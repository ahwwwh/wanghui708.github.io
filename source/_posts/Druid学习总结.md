title: Druid学习总结
author: 记得
date: 2019-07-16 14:28:24
tags:
---
# Druid 学习总结

## 1. Druid简介
Druid是一个JDBC组件，包括三部分：

- DruidDriver：代理Driver，能够提供基于Filter-Chain模式的插件体系。
- DruidDataSource：高效可管理的数据库连接池。
- SQLParser ：sql解析器 

## 2. Druid功能

-  监控数据库访问性能，Druid内置提供一个功能强大的StatFilter插件，能够详细统计SQL的执行性能，利于线上分析数据库访问性能； 
-  替换DBCP和C3P0。Druid提供了一个高效、功能强大、可扩展性好的数据库连接池。
-  数据库密码加密。DruidDruiver和DruidDataSource支持PasswordCallback。
-  SQL执行日志。Druid提供了不同的LogFilter，能够支持Common-Logging、Log4j和JdkLog。可以按需要选择相应的LogFilter，监控应用的数据库访问情况。 
-  扩展JDBC。若对JDBC层有编程的需求，可以通过Druid提供的Filter-Chain机制，方便的编写JDBC层的扩展插件。


## 3. Druid连接池
- Druid连接池是阿里巴巴开源的数据库连接池项目
- Druid连接池为监控而生，内置强大的监控功能，监控特性不影响性能。
- 能防SQL注入，内置Loging能诊断Hack应用行为。


## 4. Druid竞品分析
<div class="bi-table">
  <table>
    <colgroup>
      <col width="90px" />
      <col width="163px" />
      <col width="124px" />
      <col width="110px" />
      <col width="90px" />
      <col width="163px" />
      <col width="85px" />
    </colgroup>
    <tbody>
      <tr height="34px">
        <td rowspan="1" colSpan="1">
          <div data-type="p">功能类别</div>
        </td>
        <td rowspan="1" colSpan="1">
          <div data-type="p">功能</div>
        </td>
        <td rowspan="1" colSpan="1">
          <div data-type="p">Druid</div>
        </td>
        <td rowspan="1" colSpan="1">
          <div data-type="p">HikariCP</div>
        </td>
        <td rowspan="1" colSpan="1">
          <div data-type="p">DBCP</div>
        </td>
        <td rowspan="1" colSpan="1">
          <div data-type="p">Tomcat-jdbc</div>
        </td>
        <td rowspan="1" colSpan="1">
          <div data-type="p">C3P0</div>
        </td>
      </tr>
      <tr height="34px">
        <td rowspan="3" colSpan="1">
          <div data-type="p">性能</div>
        </td>
        <td rowspan="1" colSpan="1">
          <div data-type="p">PSCache</div>
        </td>
        <td rowspan="1" colSpan="1">
          <div data-type="p">是</div>
        </td>
        <td rowspan="1" colSpan="1">
          <div data-type="p">否</div>
        </td>
        <td rowspan="1" colSpan="1">
          <div data-type="p">是</div>
        </td>
        <td rowspan="1" colSpan="1">
          <div data-type="p">是</div>
        </td>
        <td rowspan="1" colSpan="1">
          <div data-type="p">是</div>
        </td>
      </tr>
      <tr height="34px">
        <td rowspan="1" colSpan="1">
          <div data-type="p">LRU</div>
        </td>
        <td rowspan="1" colSpan="1">
          <div data-type="p">是</div>
        </td>
        <td rowspan="1" colSpan="1">
          <div data-type="p">否</div>
        </td>
        <td rowspan="1" colSpan="1">
          <div data-type="p">是</div>
        </td>
        <td rowspan="1" colSpan="1">
          <div data-type="p">是</div>
        </td>
        <td rowspan="1" colSpan="1">
          <div data-type="p">是</div>
        </td>
      </tr>
      <tr height="34px">
        <td rowspan="1" colSpan="1">
          <div data-type="p">SLB负载均衡支持</div>
        </td>
        <td rowspan="1" colSpan="1">
          <div data-type="p">是</div>
        </td>
        <td rowspan="1" colSpan="1">
          <div data-type="p">否</div>
        </td>
        <td rowspan="1" colSpan="1">
          <div data-type="p">否</div>
        </td>
        <td rowspan="1" colSpan="1">
          <div data-type="p">否</div>
        </td>
        <td rowspan="1" colSpan="1">
          <div data-type="p">否</div>
        </td>
      </tr>
      <tr height="34px">
        <td rowspan="1" colSpan="1">
          <div data-type="p">稳定性</div>
        </td>
        <td rowspan="1" colSpan="1">
          <div data-type="p">ExceptionSorter </div>
        </td>
        <td rowspan="1" colSpan="1">
          <div data-type="p">是</div>
        </td>
        <td rowspan="1" colSpan="1">
          <div data-type="p">否</div>
        </td>
        <td rowspan="1" colSpan="1">
          <div data-type="p">否</div>
        </td>
        <td rowspan="1" colSpan="1">
          <div data-type="p">否</div>
        </td>
        <td rowspan="1" colSpan="1">
          <div data-type="p">否</div>
        </td>
      </tr>
      <tr height="34px">
        <td rowspan="1" colSpan="1">
          <div data-type="p">扩展</div>
        </td>
        <td rowspan="1" colSpan="1">
          <div data-type="p">扩展</div>
        </td>
        <td rowspan="1" colSpan="1">
          <div data-type="p">Filter</div>
        </td>
        <td rowspan="1" colSpan="1">
          <div data-type="p"></div>
        </td>
        <td rowspan="1" colSpan="1">
          <div data-type="p"></div>
        </td>
        <td rowspan="1" colSpan="1">
          <div data-type="p">JdbcIntercepter</div>
        </td>
        <td rowspan="1" colSpan="1">
          <div data-type="p"></div>
        </td>
      </tr>
      <tr height="34px">
        <td rowspan="3" colSpan="1">
          <div data-type="p">监控</div>
        </td>
        <td rowspan="1" colSpan="1">
          <div data-type="p">监控方式</div>
        </td>
        <td rowspan="1" colSpan="1">
          <div data-type="p">jmx/log/http</div>
        </td>
        <td rowspan="1" colSpan="1">
          <div data-type="p">jmx/metrics</div>
        </td>
        <td rowspan="1" colSpan="1">
          <div data-type="p">jmx</div>
        </td>
        <td rowspan="1" colSpan="1">
          <div data-type="p">jmx</div>
        </td>
        <td rowspan="1" colSpan="1">
          <div data-type="p">jmx</div>
        </td>
      </tr>
      <tr height="34px">
        <td rowspan="1" colSpan="1">
          <div data-type="p">支持SQL级监控</div>
        </td>
        <td rowspan="1" colSpan="1">
          <div data-type="p">是</div>
        </td>
        <td rowspan="1" colSpan="1">
          <div data-type="p">否</div>
        </td>
        <td rowspan="1" colSpan="1">
          <div data-type="p">否</div>
        </td>
        <td rowspan="1" colSpan="1">
          <div data-type="p">否</div>
        </td>
        <td rowspan="1" colSpan="1">
          <div data-type="p">否</div>
        </td>
      </tr>
      <tr height="34px">
        <td rowspan="1" colSpan="1">
          <div data-type="p">Spring/Web关联监控</div>
        </td>
        <td rowspan="1" colSpan="1">
          <div data-type="p">是</div>
        </td>
        <td rowspan="1" colSpan="1">
          <div data-type="p">否</div>
        </td>
        <td rowspan="1" colSpan="1">
          <div data-type="p">否</div>
        </td>
        <td rowspan="1" colSpan="1">
          <div data-type="p">否</div>
        </td>
        <td rowspan="1" colSpan="1">
          <div data-type="p">否</div>
        </td>
      </tr>
      <tr height="34px">
        <td rowspan="2" colSpan="1">
          <div data-type="p"></div>
        </td>
        <td rowspan="1" colSpan="1">
          <div data-type="p">诊断支持</div>
        </td>
        <td rowspan="1" colSpan="1">
          <div data-type="p">LogFilter</div>
        </td>
        <td rowspan="1" colSpan="1">
          <div data-type="p">否</div>
        </td>
        <td rowspan="1" colSpan="1">
          <div data-type="p">否</div>
        </td>
        <td rowspan="1" colSpan="1">
          <div data-type="p">否</div>
        </td>
        <td rowspan="1" colSpan="1">
          <div data-type="p">否</div>
        </td>
      </tr>
      <tr height="34px">
        <td rowspan="1" colSpan="1">
          <div data-type="p">连接泄露诊断</div>
        </td>
        <td rowspan="1" colSpan="1">
          <div data-type="p">logAbandoned</div>
        </td>
        <td rowspan="1" colSpan="1">
          <div data-type="p">否</div>
        </td>
        <td rowspan="1" colSpan="1">
          <div data-type="p">否</div>
        </td>
        <td rowspan="1" colSpan="1">
          <div data-type="p">否</div>
        </td>
        <td rowspan="1" colSpan="1">
          <div data-type="p">否</div>
        </td>
      </tr>
      <tr height="34px">
        <td rowspan="2" colSpan="1">
          <div data-type="p">安全</div>
        </td>
        <td rowspan="1" colSpan="1">
          <div data-type="p">SQL防注入</div>
        </td>
        <td rowspan="1" colSpan="1">
          <div data-type="p">是</div>
        </td>
        <td rowspan="1" colSpan="1">
          <div data-type="p">无</div>
        </td>
        <td rowspan="1" colSpan="1">
          <div data-type="p">无</div>
        </td>
        <td rowspan="1" colSpan="1">
          <div data-type="p">无</div>
        </td>
        <td rowspan="1" colSpan="1">
          <div data-type="p">无</div>
        </td>
      </tr>
      <tr height="34px">
        <td rowspan="1" colSpan="1">
          <div data-type="p">支持配置加密</div>
        </td>
        <td rowspan="1" colSpan="1">
          <div data-type="p">是</div>
        </td>
        <td rowspan="1" colSpan="1">
          <div data-type="p">否</div>
        </td>
        <td rowspan="1" colSpan="1">
          <div data-type="p">否</div>
        </td>
        <td rowspan="1" colSpan="1">
          <div data-type="p">否</div>
        </td>
        <td rowspan="1" colSpan="1">
          <div data-type="p">否</div>
        </td>
      </tr>
    </tbody>
  </table>
</div>

Druid连接池在性能、监控、诊断、安全、扩展性这些方面远远超出竞品

### 4.1 性能
#### 4.1.1 LRU
LRU是一个性能关键指标，特别Oracle，每个Connection对应数据库端的一个进程，如果数据库连接池遵从LRU，有助于数据库服务器优化，这是重要的指标。在测试中，Druid、DBCP、Proxool是遵守LRU的。BoneCP、C3P0则不是。BoneCP在mock环境下性能可能好，但在真实环境中则就不好了。

#### 4.1.2 PSCache
PSCache是数据库连接池的关键指标。在Oracle中，类似SELECT NAME FROM USER WHERE ID = ?这样的SQL，启用PSCache和不启用PSCache的性能可能是相差一个数量级的。Proxool是不支持PSCache的数据库连接池，如果你使用Oracle、SQL Server、DB2、Sybase这样支持游标的数据库，那你就完全不用考虑Proxool。

### 4.2 稳定性

ExceptionSorter是一个很重要的容错特性，如果一个连接产生了一个不可恢复的错误，必须立刻从连接池中去掉，否则会连续产生大量错误。这个特性，目前只有JBossDataSource和Druid实现。Druid的实现参考自JBossDataSource，经过长期生产反馈补充。

### 4.3 监控
- Druid连接池最初就是为监控系统采集jdbc运行信息而生的，它内置了StatFilter 功能，能采集非常完备的连接池执行信息。
- Druid连接池内置了能和Spring/Servlet关联监控的实现，使得监控Web应用特别方便。
- Druid连接池内置了一个监控页面，提供了非常完备的监控信息，可以快速诊断系统的瓶颈。
- 监控不影响性能
- SQL参数化合并监控

	实际业务中，如果SQL不是走PreparedStatement，SQL没有参数化，这时SQL需要参数化合并监控才能真实反映业务情况。如下SQL：

	```sql
	select * from t where id = 1
	select * from t where id = 2
	select * from t where id = 3
	```

	参数化后：
	
	```sql
	select * from t where id = ?
	```
	参数化合并监控是基于SQL Parser语法解析实现的，是Druid连接池独一无二的功能。

- 执行次数、返回行数、更新行数和并发监控

	StatFilter能采集到每个SQL的执行次数、返回行数总和、更新行数总和、执行中次数和和最大并发。并发监控的统计是在SQL执行开始对计数器加一，结束后对计数器减一实现的。可以采集到每个SQL的当前并发和采集期间的最大并发。
	
- 慢查监控

	缺省执行耗时超过3秒的被认为是慢查，统计项中有包括每个SQL的最后发生的慢查的耗时和发生时的参数。

- Exception监控

	如果SQL执行时抛出了Exception，SQL统计项上会Exception有最后的发生时间、堆栈和Message，根据这些信息可以很容易定位错误原因。
	
- 区间分布

	SQL监控项上，执行时间、读取行数、更新行数都有区间分布，将耗时分布成8个区间：

	1. 0 - 1 耗时0到1毫秒的次数
	2. 1 - 10 耗时1到10毫秒的次数
	* 10 - 100 耗时10到100毫秒的次数
	* 100 - 1,000 耗时100到1000毫秒的次数
	* 1,000 - 10,000 耗时1到10秒的次数
	* 10,000 - 100,000 耗时10到100秒的次数
	* 100,000 - 1,000,000 耗时100到1000秒的次数
	* 1,000,000 -   耗时1000秒以上的次数

### 4.4 诊断支持

Druid连接池内置了[LogFilter](https://github.com/alibaba/druid/wiki/%E9%85%8D%E7%BD%AE_LogFilter)，将Connection/Statement/ResultSet相关操作的日志输出，可以用于诊断系统问题，也可以用于Hack一个不熟悉的系统。

LogFilter可以输出连接申请/释放，事务提交回滚，Statement的Create/Prepare/Execute/Close，ResultSet的Open/Next/Close，通过LogFilter可以详细诊断一个系统的Jdbc行为。

LogFilter有Log4j、Log4j2、Slf4j、CommsLog等实现。

### 4.5 防SQL注入
Druid连接池内置了[WallFilter](https://github.com/alibaba/druid/wiki/%E7%AE%80%E4%BB%8B_WallFilter) 提供防SQL注入功能，在不影响性能的同时防御SQL注入攻击。

#### 4.5.1 基于语意的防SQL注入
Druid连接池内置了一个功能完备的SQL Parser，能够完整解析mysql、sql server、oracle、postgresql的语法，通过语意分析能够精确识别SQL注入攻击。

#### 4.5.2 极低的漏报率和误报率
基于SQL语意分析，大量应用和反馈，使得Druid的防SQL注入拥有极低的漏报率和误报率。

#### 4.5.3 防注入对性能影响极小
内置参数化后的Cache、高性能手写的Parser，使得打开防SQL注入对应用的性能基本不受影响。

## 5. SpringBoot+Druid
### 5.1 使用
1. 在 Spring Boot 项目中加入```druid-spring-boot-starter```依赖

    ```Maven```
    
    ```xml
    <dependency>
       <groupId>com.alibaba</groupId>
       <artifactId>druid-spring-boot-starter</artifactId>
       <version>1.1.10</version>
    </dependency>
    ```
    
    ```Gradle```
    
    ```xml
    compile 'com.alibaba:druid-spring-boot-starter:1.1.10'
    
    ```
2. 添加配置
	 
	```xml
    spring.datasource.url= 
    spring.datasource.username=
    spring.datasource.password=
    # ...其他配置（可选，不是必须的，使用内嵌数据库的话上述三项也可省略不填）
    ```

### 5.2 配置属性
Druid Spring Boot Starter 配置属性的名称完全遵照 Druid，你可以通过 Spring Boot 配置文件来配置Druid数据库连接池和监控，如果没有配置则使用默认值。

|配置|缺省值|说明|
| --- | --- | --- |
| name ||配置这个属性的意义在于，如果存在多个数据源，监控的时候可以通过名字来区分开来。如果没有配置，将会生成一个名字，格式是："DataSource-" + System.identityHashCode(this). 另外配置此属性至少在1.0.5版本中是不起作用的，强行设置name会出错。
| url ||连接数据库的url，不同数据库不一样。例如：<br />mysql : jdbc:mysql://10.20.153.104:3306/druid2<br /> oracle : jdbc:oracle:thin:@10.20.149.85:1521:ocnauto|
| username ||连接数据库的用户名|
| password ||连接数据库的密码。如果你不希望密码直接写在配置文件中，可以使用ConfigFilter。
| driverClassName | 根据url自动识别 |这一项可配可不配，如果不配置druid会根据url自动识别dbType，然后选择相应的driverClassName|
| initialSize |0| 初始化时建立物理连接的个数。初始化发生在显示调用init方法，或者第一次getConnection时|
| maxActive |8|最大连接池数量|
| maxIdle | 8	|已经不再使用，配置了也没效果|
| minIdle ||最小连接池数量|
| maxWait ||获取连接时最大等待时间，单位毫秒。配置了maxWait之后，缺省启用公平锁，并发效率会有所下降，如果需要可以通过配置useUnfairLock属性为true使用非公平锁。|
| poolPreparedStatements | false |是否缓存preparedStatement，也就是PSCache。PSCache对支持游标的数据库性能提升巨大，比如说oracle。在mysql下建议关闭。|
| maxPoolPreparedStatementPerConnectionSize |-1|要启用PSCache，必须配置大于0，当大于0时，poolPreparedStatements自动触发修改为true。在Druid中，不会存在Oracle下PSCache占用内存过多的问题，可以把这个数值配置大一些，比如说100|
| validationQuery ||用来检测连接是否有效的sql，要求是一个查询语句，常用select 'x'。如果validationQuery为null，testOnBorrow、testOnReturn、testWhileIdle都不会起作用。|
| validationQueryTimeout ||单位：秒，检测连接是否有效的超时时间。底层调用jdbc Statement对象的void setQueryTimeout(int seconds)方法|
| testOnBorrow | true |申请连接时执行validationQuery检测连接是否有效，做了这个配置会降低性能。|
| testOnReturn | false |归还连接时执行validationQuery检测连接是否有效，做了这个配置会降低性能。|
| testWhileIdle | false |建议配置为true，不影响性能，并且保证安全性。申请连接的时候检测，如果空闲时间大于timeBetweenEvictionRunsMillis，执行validationQuery检测连接是否有效。|
| keepAlive | false<br/>（1.0.28） | 连接池中的minIdle数量以内的连接，空闲时间超过minEvictableIdleTimeMillis，则会执行keepAlive操作。|
| timeBetweenEvictionRunsMillis |1分钟（1.0.14）|有两个含义：<br />1) Destroy线程会检测连接的间隔时间，如果连接空闲时间大于等于minEvictableIdleTimeMillis则关闭物理连接。<br />2) testWhileIdle的判断依据，详细看testWhileIdle属性的说明|
| numTestsPerEvictionRun |30分钟（1.0.14）|不再使用，一个DruidDataSource只支持一个EvictionRun|
| minEvictableIdleTimeMillis ||连接保持空闲而不被驱逐的最小时间|
| connectionInitSqls ||物理连接初始化的时候执行的sql|
| exceptionSorter |根据dbType自动识别|当数据库抛出一些不可恢复的异常时，抛弃连接|
| filters ||属性类型是字符串，通过别名的方式配置扩展插件，常用的插件有：<br />监控统计用的filter:stat<br />日志用的filter:log4j<br />防御sql注入的filter:wall|
|proxyFilters||类型是List<com.alibaba.druid.filter.Filter>，如果同时配置了filters和proxyFilters，是组合关系，并非替换关系|



- JDBC 配置

```xml
spring.datasource.druid.url= # 或spring.datasource.url= 
spring.datasource.druid.username= # 或spring.datasource.username=
spring.datasource.druid.password= # 或spring.datasource.password=
spring.datasource.druid.driver-class-name= #或 spring.datasource.driver-class-name=
```

- 连接池配置

```
spring.datasource.druid.initial-size=
spring.datasource.druid.max-active=
spring.datasource.druid.min-idle=
spring.datasource.druid.max-wait=
spring.datasource.druid.pool-prepared-statements=
spring.datasource.druid.max-pool-prepared-statement-per-connection-size= 
spring.datasource.druid.max-open-prepared-statements= #和上面的等价
spring.datasource.druid.validation-query=
spring.datasource.druid.validation-query-timeout=
spring.datasource.druid.test-on-borrow=
spring.datasource.druid.test-on-return=
spring.datasource.druid.test-while-idle=
spring.datasource.druid.time-between-eviction-runs-millis=
spring.datasource.druid.min-evictable-idle-time-millis=
spring.datasource.druid.max-evictable-idle-time-millis=
spring.datasource.druid.filters= #配置多个英文逗号分隔
....//more
```

- 监控配置

```
# WebStatFilter配置，说明请参考Druid Wiki，配置_配置WebStatFilter
spring.datasource.druid.web-stat-filter.enabled= #是否启用StatFilter默认值false
spring.datasource.druid.web-stat-filter.url-pattern=
spring.datasource.druid.web-stat-filter.exclusions=
spring.datasource.druid.web-stat-filter.session-stat-enable=
spring.datasource.druid.web-stat-filter.session-stat-max-count=
spring.datasource.druid.web-stat-filter.principal-session-name=
spring.datasource.druid.web-stat-filter.principal-cookie-name=
spring.datasource.druid.web-stat-filter.profile-enable=

# StatViewServlet配置，说明请参考Druid Wiki，配置_StatViewServlet配置
spring.datasource.druid.stat-view-servlet.enabled= #是否启用StatViewServlet（监控页面）默认值为false（考虑到安全问题默认并未启动，如需启用建议设置密码或白名单以保障安全）
spring.datasource.druid.stat-view-servlet.url-pattern=
spring.datasource.druid.stat-view-servlet.reset-enable=
spring.datasource.druid.stat-view-servlet.login-username=
spring.datasource.druid.stat-view-servlet.login-password=
spring.datasource.druid.stat-view-servlet.allow=
spring.datasource.druid.stat-view-servlet.deny=

# Spring监控配置，说明请参考Druid Github Wiki，配置_Druid和Spring关联监控配置
spring.datasource.druid.aop-patterns= # Spring监控AOP切入点，如x.y.z.service.*,配置多个英文逗号分隔
```

### 5.3 日志输出

#### 5.3.1. pom.xml中springboot版本依赖

```
<!--Spring-boot中去掉logback的依赖-->
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-web</artifactId>
	<exclusions>
		<exclusion>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-logging</artifactId>
		</exclusion>
	</exclusions>
</dependency>

<!--日志-->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-log4j2</artifactId>
</dependency>

<!--数据库连接池-->
<dependency>
	<groupId>com.alibaba</groupId>
	<artifactId>druid-spring-boot-starter</artifactId>
	<version>1.1.6</version>
</dependency>

<!--其他依赖-->
```



#### 5.3.2. log4j2.xml文件中的日志配置

```
<?xml version="1.0" encoding="UTF-8"?>
<configuration status="OFF">
    <appenders>

        <Console name="Console" target="SYSTEM_OUT">
            <!--只接受程序中DEBUG级别的日志进行处理-->
            <ThresholdFilter level="DEBUG" onMatch="ACCEPT" onMismatch="DENY"/>
            <PatternLayout pattern="[%d{HH:mm:ss.SSS}] %-5level %class{36} %L %M - %msg%xEx%n"/>
        </Console>

        <!--处理DEBUG级别的日志，并把该日志放到logs/debug.log文件中-->
        <!--打印出DEBUG级别日志，每次大小超过size，则这size大小的日志会自动存入按年份-月份建立的文件夹下面并进行压缩，作为存档-->
        <RollingFile name="RollingFileDebug" fileName="./logs/debug.log"
                     filePattern="logs/$${date:yyyy-MM}/debug-%d{yyyy-MM-dd}-%i.log.gz">
            <Filters>
                <ThresholdFilter level="DEBUG"/>
                <ThresholdFilter level="INFO" onMatch="DENY" onMismatch="NEUTRAL"/>
            </Filters>
            <PatternLayout
                    pattern="[%d{yyyy-MM-dd HH:mm:ss}] %-5level %class{36} %L %M - %msg%xEx%n"/>
            <Policies>
                <SizeBasedTriggeringPolicy size="500 MB"/>
                <TimeBasedTriggeringPolicy/>
            </Policies>
        </RollingFile>

        <!--处理INFO级别的日志，并把该日志放到logs/info.log文件中-->
        <RollingFile name="RollingFileInfo" fileName="./logs/info.log"
                     filePattern="logs/$${date:yyyy-MM}/info-%d{yyyy-MM-dd}-%i.log.gz">
            <Filters>
                <!--只接受INFO级别的日志，其余的全部拒绝处理-->
                <ThresholdFilter level="INFO"/>
                <ThresholdFilter level="WARN" onMatch="DENY" onMismatch="NEUTRAL"/>
            </Filters>
            <PatternLayout
                    pattern="[%d{yyyy-MM-dd HH:mm:ss}] %-5level %class{36} %L %M - %msg%xEx%n"/>
            <Policies>
                <SizeBasedTriggeringPolicy size="500 MB"/>
                <TimeBasedTriggeringPolicy/>
            </Policies>
        </RollingFile>

        <!--处理WARN级别的日志，并把该日志放到logs/warn.log文件中-->
        <RollingFile name="RollingFileWarn" fileName="./logs/warn.log"
                     filePattern="logs/$${date:yyyy-MM}/warn-%d{yyyy-MM-dd}-%i.log.gz">
            <Filters>
                <ThresholdFilter level="WARN"/>
                <ThresholdFilter level="ERROR" onMatch="DENY" onMismatch="NEUTRAL"/>
            </Filters>
            <PatternLayout
                    pattern="[%d{yyyy-MM-dd HH:mm:ss}] %-5level %class{36} %L %M - %msg%xEx%n"/>
            <Policies>
                <SizeBasedTriggeringPolicy size="500 MB"/>
                <TimeBasedTriggeringPolicy/>
            </Policies>
        </RollingFile>

        <!--处理error级别的日志，并把该日志放到logs/error.log文件中-->
        <RollingFile name="RollingFileError" fileName="./logs/error.log"
                     filePattern="logs/$${date:yyyy-MM}/error-%d{yyyy-MM-dd}-%i.log.gz">
            <ThresholdFilter level="ERROR"/>
            <PatternLayout
                    pattern="[%d{yyyy-MM-dd HH:mm:ss}] %-5level %class{36} %L %M - %msg%xEx%n"/>
            <Policies>
                <SizeBasedTriggeringPolicy size="500 MB"/>
                <TimeBasedTriggeringPolicy/>
            </Policies>
        </RollingFile>

        <!--druid的日志记录追加器-->
        <RollingFile name="druidSqlRollingFile" fileName="./logs/druid-sql.log"
                     filePattern="logs/$${date:yyyy-MM}/api-%d{yyyy-MM-dd}-%i.log.gz">
            <PatternLayout pattern="[%d{yyyy-MM-dd HH:mm:ss}] %-5level %L %M - %msg%xEx%n"/>
            <Policies>
                <SizeBasedTriggeringPolicy size="500 MB"/>
                <TimeBasedTriggeringPolicy/>
            </Policies>
        </RollingFile>
    </appenders>

    <loggers>
        <root level="DEBUG">
            <appender-ref ref="Console"/>
            <appender-ref ref="RollingFileInfo"/>
            <appender-ref ref="RollingFileWarn"/>
            <appender-ref ref="RollingFileError"/>
            <appender-ref ref="RollingFileDebug"/>
        </root>

        <!--记录druid-sql的记录-->
        <logger name="druid.sql.Statement" level="debug" additivity="false">
            <appender-ref ref="druidSqlRollingFile"/>
        </logger>
        <logger name="druid.sql.Statement" level="debug" additivity="false">
            <appender-ref ref="druidSqlRollingFile"/>
        </logger>

        <!--log4j2 自带过滤日志-->
        <Logger name="org.apache.catalina.startup.DigesterFactory" level="error" />
        <Logger name="org.apache.catalina.util.LifecycleBase" level="error" />
        <Logger name="org.apache.coyote.http11.Http11NioProtocol" level="warn" />
        <logger name="org.apache.sshd.common.util.SecurityUtils" level="warn"/>
        <Logger name="org.apache.tomcat.util.net.NioSelectorPool" level="warn" />
        <Logger name="org.crsh.plugin" level="warn" />
        <logger name="org.crsh.ssh" level="warn"/>
        <Logger name="org.eclipse.jetty.util.component.AbstractLifeCycle" level="error" />
        <Logger name="org.hibernate.validator.internal.util.Version" level="warn" />
        <logger name="org.springframework.boot.actuate.autoconfigure.CrshAutoConfiguration" level="warn"/>
        <logger name="org.springframework.boot.actuate.endpoint.jmx" level="warn"/>
        <logger name="org.thymeleaf" level="warn"/>
    </loggers>
</configuration>
```



#### 5.3.3. 配置application.properties

```
# 配置日志输出
spring.datasource.druid.filter.slf4j.enabled=true
spring.datasource.druid.filter.slf4j.statement-create-after-log-enabled=false
spring.datasource.druid.filter.slf4j.statement-close-after-log-enabled=false
spring.datasource.druid.filter.slf4j.result-set-open-after-log-enabled=false
spring.datasource.druid.filter.slf4j.result-set-close-after-log-enabled=false
```


[参考文献: github/druid](https://github.com/alibaba/druid)




