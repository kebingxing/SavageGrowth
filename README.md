# SavageGrowth
## 帮助程序员找到适合自己的“成长框架”
念念不忘必有回响，持续更新中...

# <架构设计>
**软件架构定义（之一）：** 软件架构式解释该系统所需的结构体的集合，其中包括：软件元素、元素之间的相互关系，以及二者各自的属性。 
* 架构是必须在项目早期作出的一组设计决策。这通常也被非正式的称为“那些在项目后期难以改变的内容”。
* 架构是对系统恰如其分的施加约束，一遍系统获得我们所需质量属性的一门艺术。
* 架构并不仅仅只是设计的宏观部分，如果细节关乎系统的整体质量，那么这些细节也应该属于架构层面的内容。
* 任何软件系统都有自己的架构，架构是客观存在的。

**参考资料：**
《恰如其分的软件架构》

### 微服务架构
**微服务定义：** 微服务是由以单一应用程序构成的小服务，自己拥有独立的进程，服务依赖业务功能的设计，以全自动的方式部署，与其他服务使用轻量级的通信（例如HTTP）。同时服务会使用最小的规模的集中管理能力，服务可以用不同的编程语言与数据库等组件实现。

微服务架构是一种架构风格。

### GoF的23种设计模式

### 面向对象设计原则
最基本的五大设计原则（SOLID）：单一职责原则、开放封闭原则、里式替换原则、接口隔离原则和依赖倒置原则。


### 业务系统设计的基本原则

**1. 可维护性是根本**
* 代码可读性很关键
* 设计权衡时优先考虑简单设计
* 打造可扩展系统

**2. 多参考业界的成功模式**
* GoF的23种设计模式
* 面向对象的X大设计原则
* 业界现有的设计案例

**3. 稳定性是底线**
* 充分了解背后的非功能性诉求
* 尽可能做到可降级
* 实现异常出口

**4. 选择最恰当的设计方案**
* 中间件的设计方式不一定适合业务系统
* 方案选型要考虑团队的技能水位
* 方案要符合项目当前的发展阶段（超前设计需谨慎）
* 重点评估方案的维护成本
* 多套方案中选择最合适的方案


## <云原生>
**定义：** 云原生技术有利于各组织在公有云、私有云和混合云等新型动态环境中，构建和运行可弹性扩展的应用。云原生的代表技术包括容器、服务网格、微服务、不可变基础设施和声明式API。这些技术能够构建容错性好、易于管理和便于观察的松耦合系统。结合可靠的自动化手段，云原生技术使工程师能够轻松地对系统作出频繁和可预测的重大变更。云原生计算基金会（CNCF）致力于培育和维护一个厂商中立的开源生态系统，来推广云原生技术。我们通过将最前沿的模式民主化，让这些创新为大众所用。

**参考资料：**
* https://github.com/cncf/toc/blob/master/DEFINITION.md
* https://jimmysong.io/kubernetes-handbook/cloud-native/cloud-native-definition.html

### Docker

### Service Mesh

### Serverless

## <存储>

### RDS

### NoSQL
NoSQL常见的四种类型：键值对型、文档型、列式存储型和图型。

#### Redis
**定义：** Redis是一种采用内存来作为数据结构存储的数据库、缓存和消息代理。<br/>
**部署说明：** Redis是用ANSI C编写，并且可以在大多数POSIX系统中使用，例如Linux，* BSD，OS X，而无需外部依赖。Linux和OS X是Redis开发和测试最多的两个操作系统，我们建议使用Linux进行部署。<br/>
**支持的数据结构：** 字符串，哈希，列表，集合，带范围查询的排序集合，位图，HyperLogLog，地理空间索引。<br/>
**持久化方案：**
Redis持久化方案分为RDB和AOF两种。<br/>
RDB：按指定的时间间隔执行数据集的时间点快照。 
AOF：会记录服务器接收的每个写入操作，这些操作将在服务器启动时再次播放，以重建原始数据集。使用与Redis协议本身相同的格式记录命令，并且采用追加方式。当日志太大时，Redis可以在后台重写日志。 

注意，可以在同一实例中同时合并AOF和RDB。在这种情况下，当Redis重新启动时，将使用AOF文件用于重建原始数据集，因为它可以保证是最完整的。

**RDB优势：**<br/>
1. RDB是Redis数据的非常紧凑的单文件时间点表示。RDB文件非常适合备份。可以在灾难发生时轻松将数据恢复到不同版本。
2. 支持文件上传来远程恢复。
3. RDB最大限度地提高了Redis的性能，因为Redis父进程为了持久化而需要做的唯一工作就是forking一个子进程，其余的都交给子进程来做，父实例将永远不会执行磁盘I/O或类似操作。
4. 与AOF相比，采用RDB方式来重启Redis将更快。

**RDB劣势：**<br/>
1. 在断电等没有正确关闭的情况下停止工作之后）存在数据丢失的可能。
2. RDB需要经常使用fork()才能使用子进程将其持久化在磁盘上。如果数据集很大，Fork()可能很耗时，如果机器CPU性能不佳，则可能导致Redis停止为客户端服务几毫秒甚至一秒钟。AOF也需要fork()，但我们可以控制重写日志的频率，而无需权衡持久性。

**AOF优势：**<br/>
1. 使用AOF Redis更加持久：您可以使用不同的fsync策略：完全没有fsync，每秒fsync，每个查询fsync。使用fsync的默认策略，每秒的写入性能仍然很好（fsync是使用后台线程执行的，并且当没有fsync进行时，主线程将尽力执行写入操作。）但是您只能损失一秒钟的写入时间。
2. AOF以易于理解和解析的格式包含所有操作的日志。您甚至可以轻松导出AOF文件，在某些情况下，便于我们分析Redis的所有操作。

**AOF劣势：**<br/>
1. 对于同一数据集，AOF文件通常大于等效的RDB文件。
2. 精确的fsync策略可能会导致AOF可能比RDB慢。通常情况下，设置每秒一次fsync性能仍然很高，并且在禁用fsync的情况下，即使在高负载下，它也应与RDB一样快。即使在巨大的写负载的情况下，RDB仍然能够提供有关最大延迟的更多保证。
3. AOF通过像MySQL或MongoDB那样增量地更新现有状态来工作，而RDB快照一次又一次地创建所有内容，从概念上讲，它更健壮。

如果您要增加一个计数器100次，最终将在数据集中包含最终值的键只有一个，而在AOF中却包含100个条目。不需要其中的99个条目来重建当前状态。因此，Redis支持一个有趣的功能：它能够在后台重建AOF，而不会中断对客户端的服务。每当您发出BGREWRITEAOF时， Redis都会编写最短的命令序列来重建内存中的当前数据集。如果您将AOF与Redis 2.2一起使用，则需要不时运行BGREWRITEAOF。Redis 2.4能够自动触发日志重写。

**复本机制：**<br/>
1. Redis支持主从模式，默认采用异步复制的方式，如果对强一致性有要求，可以使用同步复制。
2. Redis支持将多个副本连接到同一主副本，并且副本还可以按级联结构连接到其他副本。从Redis 4.0开始，所有子副本将从主服务器接收完全相同的数据流。
3. Redis复制时在Master上是无阻塞的。这意味着当一个或多个副本执行初始同步或部分重新同步时，主服务器将继续处理查询。


**高可用：**<br/>
Redis官方提供的高可用方案分为Redis Sentinel和Redis Cluster两种。<br/>
Redis Sentinel<br/>
这是宏观上Sentinel功能的完整列表（即，大图）：
* 监控。Sentinel会不断检查您的主实例和副本实例是否按预期工作。
* 通知。Sentinel可以通过API通知系统管理员或其他计算机程序，其中一个受监视的Redis实例出了问题。
* 自动故障转移。如果主服务器未按预期工作，则Sentinel可以启动故障转移过程，在该过程中将副本升级为主服务器，将其他附加副本重新配置为使用新的主服务器，并通知使用Redis服务器的应用程序要使用的新地址。连接时。
* 配置提供程序。Sentinel充当客户端服务发现的权威来源：客户端连接到Sentinels，以询问负责给定服务的当前Redis主服务器的地址。如果发生故障转移，Sentinels将报告新地址。

1. 当多个哨兵就给定的主机不再可用这一事实达成共识时，将执行故障检测。这降低了误报的可能性。
2. 即使不是所有的Sentinel进程都在工作，Sentinel仍能正常工作，从而使系统能够应对故障。毕竟，拥有故障转移系统本身就是一个单点故障，这没有任何意思。
3. 一个健壮的部署至少需要三个Sentinel实例。
4. Sentinels，Redis实例（主服务器和副本）以及连接到Sentinel和Redis的客户端的总和也是具有特定属性的大型分布式系统。




**扩展知识点：** HyperLogLog、
**参考资料：** 
* https://redis.io/
* http://doc.redisfans.com/


## <基础框架>
### Spring
Spring中最核心的接口是BeanFactory，它是我们访问Spring Bean容器的根接口，里面定义了很多通过名字或类型来获取Bean的方法。<br />
Spring中另一个核心接口是ApplicationContext



## <常见工具>
### Arthas


## < JVM >
### 运行时数据区域
线程私有的内存区域有程序计数器、Java虚拟机栈和本地方法栈，程序计数器是Java虚拟机规范中唯一没有要求OOM的地方；线程公用的有堆和方法区，方法区在JDK8之前的HotSpot叫做永久代，在JDK8以及之后叫做元数据区，元数据区作为直接内存来管理，只受操作系统内存的限制。

### 垃圾收集算法和收集器
主要是标记-清除、标记-整理和标记-复制三种。<br />
**标记-清除** 算法有两个缺点，一个是如果大量对象都需要标记和清除的话，开销会比较大，另一个是会导致碎片化严重，导致给新对象分配内存空间时需要进行碎片整理，增加了运行时内存分配的成本。


### 垃圾收集器
大多数垃圾收集器都遵循分代收集理论（G1除外），一般把堆分为新生代和老年代。
新生
新生代收集器Parallel Scavenge


  
**参考资料：**
深入理解Java虚拟机：JVM高级特性与最佳实践（第3版）

## < IO >
### 零拷贝

**参考资料：**
https://developer.ibm.com/articles/j-zerocopy/
https://www.linuxjournal.com/article/6345

### 伪共享


