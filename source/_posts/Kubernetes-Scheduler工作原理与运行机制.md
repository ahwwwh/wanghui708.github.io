title: Kubernetes Scheduler工作原理与运行机制
author: 记得
date: 2019-07-16 15:02:24
tags:
---
# Kubernetes Scheduler工作原理与运行机制
## Scheduler原理简介
负责Pod调度的重要功能模块，将待调度的pod按照特定的调度算法和调度策略绑定到合适的node，并将绑定信息写入etcd。

即：将PodSpec.NodeName为空的Pods逐个地，经过预选(Predicates)和优选(Priorities)两个步骤，挑选最合适的Node作为该Pod的Destination。

整个过程涉及三个对象：

- 待调度的pod列表
- 可用node列表
- 调度算法和策略

两大步骤：

- **预选**：根据配置的Predicates Policies（默认为DefaultProvider中定义的default predicates policies集合）过滤掉那些不满足这些Policies的的Nodes，剩下的Nodes就作为优选的输入。
- **优选**：根据配置的Priorities Policies（默认为DefaultProvider中定义的default priorities policies集合）给预选后的Nodes进行打分排名，得分最高的Node即作为最适合的Node，该Pod就Bind到这个Node。

> 如果经过优选将Nodes打分排名后，有多个Nodes并列得分最高，那么scheduler将随机从中选择一个Node作为目标Node。

随后，目标node上的kubelet进程通过api-server监听到pod绑定事件。

## Predicates Policies
Predicates Policies就是提供给Scheduler用来过滤出满足所定义条件的Nodes，并发的(最多16个goroutine)对每个Node启动所有Predicates Policies的遍历Filter，看其是否都满足配置的Predicates Policies，若有一个Policy不满足，则直接被淘汰。
> 注意：这里的并发goroutine number为All Nodes number，但最多不能超过16个，由一个queue控制。

Kubernetes提供了以下Predicates Policies的定义，你可以在kube-scheduler启动参数中添加<font color=#0099ff> --policy-config-file </font>来指定要运用的Policies集合,比如：

```
{
"kind" : "Policy",
"apiVersion" : "v1",
"predicates" : [
    {"name" : "PodFitsPorts"},
    {"name" : "PodFitsResources"},
    {"name" : "NoDiskConflict"},
    {"name" : "NoVolumeZoneConflict"},
    {"name" : "MatchNodeSelector"},
    {"name" : "HostName"}
    ],
"priorities" : [
    ...
    ]
}

```
1. **NoDiskConflict**: Evaluate if a pod can fit due to the volumes it requests, and those that are already mounted. Currently supported volumes are: AWS EBS, GCE PD, ISCSI and Ceph RBD. Only Persistent Volume Claims for those supported types are checked. Persistent Volumes added directly to pods are not evaluated and are not constrained by this policy.
2. **NoVolumeZoneConflict**: Evaluate if the volumes a pod requests are available on the node, given the Zone restrictions.
3. **PodFitsResources**: Check if the free resource (CPU and Memory) meets the requirement of the Pod. The free resource is measured by the capacity minus the sum of requests of all Pods on the node. To learn more about the resource QoS in Kubernetes, please check QoS proposal.
4. **PodFitsHostPorts**: Check if any HostPort required by the Pod is already occupied on the node.
5. **HostName**: Filter out all nodes except the one specified in the PodSpec’s NodeName field.
6. **MatchNodeSelector**: Check if the labels of the node match the labels specified in the Pod’s nodeSelector field and, as of Kubernetes v1.2, also match the scheduler.alpha.kubernetes.io/affinity pod annotation if present. See here for more details on both.
7. **MaxEBSVolumeCount**: Ensure that the number of attached ElasticBlockStore volumes does not exceed a maximum value (by default, 39, since Amazon recommends a maximum of 40 with one of those 40 reserved for the root volume – see Amazon’s documentation). The maximum value can be controlled by setting the KUBE_MAX_PD_VOLS environment variable.
8. **MaxGCEPDVolumeCount**: Ensure that the number of attached GCE PersistentDisk volumes does not exceed a maximum value (by default, 16, which is the maximum GCE allows – see GCE’s documentation). The maximum value can be controlled by setting the KUBE_MAX_PD_VOLS environment variable.
9. **CheckNodeMemoryPressure**: Check if a pod can be scheduled on a node reporting memory pressure condition. Currently, no BestEffort should be placed on a node under memory pressure as it gets automatically evicted by kubelet.
10. **CheckNodeDiskPressure**: Check if a pod can be scheduled on a node reporting disk pressure condition. Currently, no pods should be placed on a node under disk pressure as it gets automatically evicted by kubelet.

默认的DefaultProvider中选了以下Predicates Policies：

1. NoVolumeZoneConflict
2. MaxEBSVolumeCount
3. MaxGCEPDVolumeCount
4. MatchInterPodAffinity
	> 说明：Fit is determined by inter-pod affinity.AffinityAnnotationKey represents the key of affinity data (json serialized) in the Annotations of a Pod.

	> <font color=#0099ff > AffinityAnnotationKey string = "scheduler.alpha.kubernetes.io/affinity" </font>
5. NoDiskConflict
6. GeneralPredicates 
	1. PodFitsResources
		1. pod, in number
		2. cpu, in cores
		3. memory, in bytes
		4. alpha.kubernetes.io/nvidia-gpu, in devices。截止V1.4，每个node最多只支持1个gpu  
	2. PodFitsHost
	3. PodFitsHostPorts
	4. PodSelectorMatches
7. PodToleratesNodeTaints
8. CheckNodeMemoryPressure
9. CheckNodeDiskPressure
		 		

## Priorities Policies
经过预选策略甩选后得到的Nodes，会来到优选步骤。在这个过程中，会并发的根据每个Node分别启动一个goroutine，在每个goroutine中会根据对应的policy实现，遍历所有的预选Nodes，分别进行打分，每个Node每一个Policy的打分为0-10分，0分最低，10分最高。待所有policy对应的goroutine都完成后，根据设置的各个priorities policies的权重weight，对每个node的各个policy的得分进行加权求和作为最终的node的得分。
> <font color=#0099ff > finalScoreNodeA = (weight1 * priorityFunc1) + (weight2 * priorityFunc2) </font>
> 
> 注意：这里的并发goroutine number为All Nodes number，但最多不能超过16个，由一个queue控制。
思考：如果经过预选后，没有一个Node满足条件，则直接返回FailedPredicates报错，不会再触发Prioritizing阶段，这是合理的。但是，如果经过预选后，只有一个Node满足条件，同样会触发Prioritizing，并且所走的流程和多个Nodes一样。实际上，如果只有一个Node满足条件，在优选阶段，可以直接返回该Node作为最终scheduled结果，无需跑完整个打分流程。

如果经过优选将Nodes打分排名后，有多个Nodes并列得分最高，那么scheduler将随机从中选择一个Node作为目标Node。

Kubernetes提供了以下Priorities Policies的定义，你可以在kube-scheduler启动参数中添加<font color=#0099ff > --policy-config-file </font>来指定要运用的Policies集合，比如：

```
{
"kind" : "Policy",
"apiVersion" : "v1",
"predicates" : [
    ...
    ],
"priorities" : [
    {"name" : "LeastRequestedPriority", "weight" : 1},
    {"name" : "BalancedResourceAllocation", "weight" : 1},
    {"name" : "ServiceSpreadingPriority", "weight" : 1},
    {"name" : "EqualPriority", "weight" : 1}
    ]
}
```

1. **LeastRequestedPriority**: The node is prioritized based on the fraction of the node that would be free if the new Pod were scheduled onto the node. (In other words, (capacity - sum of requests of all Pods already on the node - request of Pod that is being scheduled) / capacity). CPU and memory are equally weighted. The node with the highest free fraction is the most preferred. Note that this priority function has the effect of spreading Pods across the nodes with respect to resource consumption.
2. **BalancedResourceAllocation**: This priority function tries to put the Pod on a node such that the CPU and Memory utilization rate is balanced after the Pod is deployed.
3. **SelectorSpreadPriority**: Spread Pods by minimizing the number of Pods belonging to the same service, replication controller, or replica set on the same node. If zone information is present on the nodes, the priority will be adjusted so that pods are spread across zones and nodes.
4. **CalculateAntiAffinityPriority**: Spread Pods by minimizing the number of Pods belonging to the same service on nodes with the same value for a particular label.
5. **ImageLocalityPriority**: Nodes are prioritized based on locality of images requested by a pod. Nodes with larger size of already-installed packages required by the pod will be preferred over nodes with no already-installed packages required by the pod or a small total size of already-installed packages required by the pod.
6. **NodeAffinityPriority**: (Kubernetes v1.2) Implements preferredDuringSchedulingIgnoredDuringExecution node affinity; see here for more details.

默认的DefaultProvider中选了以下Priorities Policies：

1. SelectorSpreadPriority, 默认权重为1；
2. InterPodAffinityPriority, 默认权重为1；

	> 1. pods should be placed in the same topological domain (e.g. same node, same rack, same zone, same power domain, etc.)
	> 2. as some other pods, or, conversely, should not be placed in the same topological domain as some other pods.
	> 3. AffinityAnnotationKey represents the key of affinity data (json serialized) in the Annotations of a Pod.	

	>  <font color=#0099ff> scheduler.alpha.kubernetes.io/affinity="..." </font>

3. LeastRequestedPriority, 默认权重为1;
4. BalancedResourceAllocation, 默认权重为1
5. NodePreferAvoidPodsPriority, 默认权重为10000
	
	> 说明：这里权重设置足够大（10000），如果得分不为0，那么加权后最终得分将很高，如果得分为0，那么意味着相对其他得搞很高的，注定被淘汰,分析如下：
	
	> 如果Node的Anotation没有设置key-value: 
	> <font color=#0099FF > scheduler.alpha.kubernetes.io/preferAvoidPods="..." </font> 
	> 则该node对该policy的得分就是10分，加上权重10000，那么该node对该policy的得分至少10W分。
	> 如果Node的Anotation设置了<font color=#0099FF > scheduler.alpha.kubernetes.io/preferAvoidPods="..." </font>
	> 如果该pod对应的Controller是ReplicationController或ReplicaSet，则该node对该policy的得分就是0分，那么该node对该policy的得分相对没有设置该Anotation的Node得分低的离谱了。也就是说这个**Node一定会被淘汰**！





