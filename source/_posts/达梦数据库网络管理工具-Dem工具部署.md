title: 达梦数据库网络管理工具-Dem工具部署
author: 记得
tags: []
categories: []
date: 2019-07-11 09:36:00
---
# 搭建远程web服务管理达梦数据库——DEM服务搭建

## 1. 功能简介

DEM全称为Dameng Enterprise Manager。本工具主要提供如下功能：

- 客户端工具：用户能够通过DEM工具来进行达梦数据库的对象管理、状态监控、SQL查询与调试。

- 监控与告警：本功能是达梦DEM工具的核心功能。通过远程主机部署代理，能够实现对远程主机状态和远程主机上达梦数据库实例状态的监控。重要的是，DEM的监控不只局限于单个数据库实例，它能够对数据库集群(MPP、RAC、数据守护)进行监控和管理。

- 系统管理：DEM工具提供了工具本身的系统配置与权限管理，方便不同用户同时使用工具，并限制非admin用户的权限。

## 2. 环境搭建
### 2.1 搭建与配置后台数据库
搭建后台数据库。创建一个数据库作为DEM后台数据库, 数据库dm.ini参数配置进行优化, 推荐配置:

```
MEMORY_POOL = 200
BUFFER = 1000
KEEP = 64
MAX_BUFFER =  2000
SORT_BUF_SIZE = 50
```
配置完后重启数据库实例。

```
[root@localhost ISN]# systemctl restart DmServiceISN
[root@localhost ISN]# systemctl status DmServiceISN
● DmServiceISN.service - Dameng Database Service(DmServiceISN).
   Loaded: loaded (/usr/lib/systemd/system/DmServiceISN.service; enabled; vendor preset: disabled)
   Active: active (exited) since 三 2019-07-10 11:09:46 CST; 10s ago
  Process: 47068 ExecStop=/opt/dmdbms/bin/DmServiceISN stop (code=exited, status=0/SUCCESS)
  Process: 47114 ExecStart=/opt/dmdbms/bin/DmServiceISN start (code=exited, status=0/SUCCESS)
 Main PID: 47114 (code=exited, status=0/SUCCESS)

7月 10 11:09:31 localhost.localdomain systemd[1]: Starting Dameng Database Service(DmServiceISN)....
7月 10 11:09:31 localhost.localdomain su[47132]: (to dmdba) root on none
7月 10 11:09:31 localhost.localdomain su[47132]: pam_limits(su-l:session): invalid line 'soft stack 102400' - skipped
7月 10 11:09:31 localhost.localdomain DmServiceISN[47114]: Starting DmServiceISN: 上一次登录：二 7月  9 11:12:38 CST 2019
7月 10 11:09:46 localhost.localdomain DmServiceISN[47114]: [11B blob data]
7月 10 11:09:46 localhost.localdomain systemd[1]: Started Dameng Database Service(DmServiceISN)..
```

### 2.2 创建DEM服务相关数据表
在数据库中执行以下SQL脚本$DM_HOME/web/dem_init.sql；

执行sql有三种方式：

- 达梦管理工具
- 达梦数据迁移工具
- disql

本文使用达梦管理工具进行执行，登录后新建查询，执行sql脚本

![upload successful](/images/pasted-4.png)


执行完毕后，将在当前数据库创建模式DEM以及相应的表结构。

### 2.3 配置后台数据库的连接信息
找到达梦数据库安装目录下的web文件夹下的dem.war包，需要编辑配置文件：

```
# 服务器中该文件地址为：/opt/dmdbms/web/dem.war
# 以下是命令行操作配置文件，完成war包编辑
unzip dem.war -d dem
cd dem/WEB-INF
vi db.xml
```
修改db.xml文件，将ip、端口、用户名和密码等改为实际的值

```
cd ..
zip -r dem.war ./*
```
将重新压缩的war包移至tomcat中进行部署，启动tomcat
tomcat配置如下：

```
(1)在conf/server.xml中  <Connector port="8080" protocol="HTTP/1.1"... 追加属性字段  maxPostSize="-1"；
(2)修改jvm启动参数，
      Linux：bin/catalina.sh -> 	JAVA_OPTS="-server -Xms256m -Xmx1024m -XX:MaxPermSize=512m -Djava.library.path=/opt/dmdbms/bin"；
      Windows：bin/catalina.bat -> set java_opts= -server -Xms40m -Xmx1024m -XX:MaxPermSize=512m -Djava.library.path=c:\dmdbms\bin；
```

访问ip:port/dem（eg: http://192.168.0.1:8080/dem），默认用户名密码（admin/888888）

![upload successful](/images/pasted-1.png)



至此，就可以使用dem工具管理达梦数据库了，下面配置监控。

## 3. 启用dem监控

## 3.1 dmagent
首先，在需要进行监控的主机上启动dmagent，要求agent和dem所运行主机时间一致；

达梦数据库代理（以下简称dmagent）是DM部署工具和DM Web版管理工具DEM部署在远程主机上的代理。通过dmagent可以监控远程主机的相关信息，也可以在远程主机部署MPP、RW、DW,DMRAC等集群系统。
	
dmagent存在3种运行模式：

1. DEM Agent 
2. Deploy Agent 
3. DEM&Deploy Agent。

不同模式对应不同的功能。运行模式1:dmagent作为DEM Agent将负责远程主机的信息收集工作。运行模式2:dmagent作为Deploy Agent将负责在远程主机进行数据库节点搭建的工作。运行模式3:dmagent将同时开启运行模式1和运行模式2。
	
dmagent目录在安装目录下的tool/dmagent
	
- data目录:用于存放DEM Agent模式代理产生的临时数据。
- lib目录:存放dmagent运行所需要的jar包。
- log目录：保存dmagent生成的日志文件。
- wrapper目录：dmagent生成系统服务依赖文件。
- log4j.xml：日志配置文件。
- readme.pdf：dmagent使用说明文档。
- config.properties：dmagent配置文件。配置信息如下：

	```
	#[General]
	#1:DEM Agent 2:Deploy Agent 3:DEM&Deploy Agent
	#设置dmagent的运行模式 
	run_mode=3
	#dmagent的RMI端口号 
	rmi_port=6364

	#[DEM]
	#DEM Agent运行模式所需参数 
	#DEM系统所在主机连接信息 
	center.url=http://192.168.0.1:8080/dem
	center.agent_servlet=dem/dma_agent
	```


	```
	修改配置config.properties：
   		center.url=http://192.168.0.1:8080/dem    #DEM访问地址
   		center.agent_servlet=dem/dma_agent        #一般无需调整
	```

DMAgentRunner.bat：dmagent命令行模式运行脚本。用户如果以命令行模式运行dmagent，请直接运行DMAgentRunner.bat。

DMAgentService.bat：dmagent服务模式运行脚本。dmagent默认服务名为DMAgentService。DMAgentService.bat支持功能如下：

```
# 服务方式启动dmagent  
# Linux平台请运行同名的sh脚本
# windows下需先注册服务，才能启动。 
# linux下虽然可直接启动，但是并非通过服务启动dmagent。如果需要通过服务启动dmagent，请先注册服务。 
DMAgentService.sh start

#停止dmagent服务 
DMAgentService.sh stop

#重启dmagent服务 
DMAgentService.sh restart

#注册dmagent服务 
#默认服务为自动启动 
DMAgentService.sh install

#删除dmagent服务 
DMAgentService.sh remove

#查看dmagent服务运行状态 
DMAgentService.sh status
```

```
# 执行
DMAgentService.sh install
systemctl start DMAgentService
systemctl status DMAgentService
```

在远程主机使用dmagent，需首先手动将dmagent拷贝到远程主机。然后通过DMAgentService(服务方式)或DMAgentRunner(命令行模式)运行dmagent。在Linux下建议以非root用户运行dmagent。

DMAgent服务启动成功后，即可在DEM中的主机处即可看到被监控的主机信息：

![upload successful](/images/pasted-2.png)

之后，可以在数据库选项卡中添加对应主机上的数据库实例:

![upload successful](/images/pasted-3.png)

添加完毕后，即可对该数据库实例进行实时监控，比如常用的AWR报告生成或会话的分析。

## 3.2 dem监控配置

1. 用管理员登陆系统后，可以在"系统管理"->"系统配置"页面中对系统的其他属性进行配置，包括dmagent的监控频率、前端刷新频率、邮件手机通知告警等；
2. 若要启用邮件通知，需用管理员用户登录系统， 在系统配置中完成系统邮箱的相关配置；
3. 若需要启用短信通知，用户需要借助达梦提供的WEB-INF/lib/demsdk.jar，实现 com.dameng.dem.server.util.IPhoneNotify接口，将依赖包及实现类打包放入到WEB-INF/lib下，重启web容器，然后在系统配置中完成短信通知的相关配置即可。



