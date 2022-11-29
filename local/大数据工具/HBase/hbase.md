---
title: HBase学习(一)
date: 2020-06-20 17:07:40
categories: 
- 大数据
- 数据库
tags:
- NoSQL数据库
- 大数据框架
toc: true
---
# HBase简介

## HBase是什么？
HBase 是一个开源的、分布式的、版本化的非关系型数据库，它利用 Hadoop 分布式文件系统（Hadoop Distributed File System，HDFS）提供分布式数据存储。HBase 是一个可以进行随机访问的存取和检索数据的存储平台，存储结构化和半结构化的数据，因此一般的网站可以将网页内容和日志信息都存在 HBase 里。

如果数据量不是非常庞大，HBase 甚至可以存储非结构化的数据。它不要求数据有预定义的模式，允许动态和灵活的数据模型，也不限制存储数据的类型。HBase 是非关系型数据库，它不具备关系型数据库的一些特点，例如，它不支持 SQL 的跨行事务，也不要求数据之间有严格的关系，同时它允许在同一列的不同行中存储不同类型的数据。

## HBase的优点
* **容量巨大**
> HBase 的单表可以有百亿行、百万列，可以在横向和纵向两个维度插入数据，具有很大的弹性。当关系型数据库的单个表的记录在亿级时，查询和写入的性能都会呈现指数级下降，这种庞大的数据量对传统数据库来说是一种灾难，而 HBase 在限定某个列的情况下对于单表存储百亿甚至更多的数据都没有性能问题。HBase 采用 LSM 树作为内部数据存储结构，这种结构会周期性地将较小文件合并成大文件，以减少对磁盘的访问。

* **列存储**
>行存储里的一张表的数据都放在一起，但在列存储里是按照列分开保存的。在这种情况下，进行数据的插入和更新，行存储会相对容易。而进行行存储时，查询操作需要读取所有的数据，列存储则只需要读取相关列，可以大幅降低系统 I/O 吞吐量。

* **稀疏性**
>通常在传统的关系性数据库中，每一列的数据类型是事先定义好的，会占用固定的内存空间，在此情况下，属性值为空（NULL）的列也需要占用存储空间。而在 HBase 中的数据都是以字符串形式存储的，为空的列并不占用存储空间，因此 HBase 的列存储解决了数据稀疏性的问题，在很大程度上节省了存储开销。所以 HBase 通常可以设计成稀疏矩阵，同时这种方式比较接近实际的应用场景。

* **扩展性强**
>HBase 工作在 HDFS 之上，理所当然地支持分布式表，也继承了 HDFS 的可扩展性。HBase 的扩展是横向的，横向扩展是指在扩展时不需要提升服务器本身的性能，只需添加服务器到现有集群即可。HBase表根据 Region 大小进行分区，分别存在集群中不同的节点上，当添加新的节点时，集群就重新调整，在新的节点启动 HBase 服务器，动态地实现扩展。这里需要指出，HBase 的扩展是热扩展，即在不停止现有服务的前提下，可以随时添加或者减少节点。

* **高可靠性**
>HBase 运行在 HDFS 上，HDFS 的多副本存储可以让它在岀现故障时自动恢复，同时 HBase 内部也提供 WAL 和 Replication 机制。WAL（Write-Ahead-Log）预写日志是在 HBase 服务器处理数据插入和删除的过程中用来记录操作内容的日志，保证了数据写入时不会因集群异常而导致写入数据的丢失；而 Replication 机制是基于日志操作来做数据同步的。当集群中单个节点出现故障时，协调服务组件 ZooKeeper 通知集群的主节点，将故障节点的 HLog 中的日志信息分发到各从节点进行数据恢复。

## HDFS的基本架构
HDFS 主要由 3 个组件构成，分别是 NameNode、SecondaryNameNode 和 DataNode。

HDFS 是以 Master/Slave 模式运行的，其中，NameNode 和 SecondaryNameNode 运行在 Master 节点 上，而 DataNode 运行在 Slave 节点上，所以 HDFS 集群一般由一个 NameNode、一个 SecondaryNameNode 和许多 DataNode 组成，其架构如下图所示。
<div align=center><img src="/image/HDFS_struct.png" width="400" height="200"/></div>
在 HDFS 中，文件是被分成块来进行存储的，一个文件可以包含许多个块，每个块存储在不同的 DataNode 中。从上图中可知，当一个客户端请求读取一个文件时，它需要先从 NameNode 中获取文件的元数据信息，然后从对应的数据节点上并行地读取数据块。

### NameNode
NameNode 是主服务器，负责管理文件系统的命名空间以及客户端对文件的访问。当客户端请求数据时，仅仅从 NameNode 中获取文件的元数据信息，具体的数据传输不经过 NameNode，而是直接与具体的 DataNode 进行交互。这里文件的元数据信息记录了文件系统中的文件名和目录名，以及它们之间的层级关系，同时也记录了每个文件目录的所有者及其权限，甚至还记录每个文件由哪些块组成，这些元数据信息记录在文件 fsimage 中，当系统初次启动时，NameNode 将读取 fsimage 中的信息并保存到内存中。

这些块的位置信息是由 NameNode 启动后从每个 DataNode 获取并保存在内存当中的，这样既减少了  NameNode 的启动时间，又减少了读取数据的查询时间，提高了整个系统的效率。

### SecondaryNameNode
SecondaryNameNode 很容易被当作是 NameNode 的备份节点，其实不然。可以通过下图看HDFS中 SecondaryNameNode 的作用。
<div align=center><img src="/image/SeNameNode.png" width="400" height="200"/></div>
NameNode管理着元数据信息，元数据信息会定期保存到 edits 和 fsimage 文件中。其中的 edits 保存操作日志信息，在 HDFS 运行期间，新的操作日志不会立即与 fsimage 进行合并，也不会存到 NameNode 的内存中，而是会先写到 edits 中。当edits文件达到一定域值或间隔一段时间后触发 SecondaryNameNode 进行工作，这个时间点称为 checkpoint。

SecondaryNameNode的角色就是定期地合并edits和 fsimage文件，其合并步骤如下。
* 进行合并之前，SecondayNameNode会通知NameNode停用当前的editlog文件，NameNode会将新记录写入新的editlog.new
* SecondaryNameNode 从 NameNode 请求并复制 fsimage 和 edits 文件。
* SecondaryNameNode 把 fsimage 和 edits 文件合并成新的 fsimage 文件，并命名为 fsimage.ckpt
* NameNode 从 SecondaryNameNode 获取 fsimage.ckpt，并替换掉 fsimage，同时用 edits.new 文件替换旧的 edits 文件
* 更新checkpoint的时间

最终 fsimage 保存的是上一个 checkpoint 的元数据信息，而 edits 保存的是从上个 checkpoint 开始发生的 HDFS 元数据改变的信息。

### DataNode
DataNode 是 HDFS 中的工作节点，也是从服务器，它负责存储数据块，也负责为客户端提供数据块的读写服务，同时也响应 NameNode 的相关指令，如完成数据块的复制、删除等。

另外， DataNode 会定期发送心跳信息给 NameNode，告知 NameNode 当前节点存储的文件块信息。当客户端给 NameNode发送读写请求时，NameNode告知客户端每个数据块所在的DataNode信息，然后客户端直接与DataNode进行通信，减少 NameNode 的系统开销。当DataNode在执行块存储操作时，DataNode还会与其他DataNode通信，复制这些块到其他DataNode上实现冗余。

## HDFS的分块机制和副本机制
在 HDFS 中，文件最终是以数据块的形式存储的，而副本机制极大程度上避免了宕机所造成的数据丢失，可以在数据读取时进行数据校验。
### 分块机制
HDFS 中数据块大小默认为 64MB，而一般磁盘块的大小为 512B，HDFS 块之所以这么大，是为了最小化寻址开销。如果块足够大，从磁盘传输数据的时间会明显大于寻找块的地址的时间，因此，传输一个由多个块组成的大文件的时间取决于磁盘传输速率。

随着新一代磁盘驱动器传输速率的提升，寻址开销会更少，在更多情况下 HDFS使用更大的块。当然块的大小不是越大越好，因为 Hadoop中一个map任务一次通常只处理一个块中的数据，如果块过大，会导致整体任务数量过小，降低作业处理的速度。HDFS按块存储的好处如下：
* 文件可以任意大，不会受到单个节点的磁盘容量的限制。理论上讲，HDFS 的存储容量是无限的。
* 简化文件子系统的设计。将系统的处理对象设置为块，可以简化存储管理，因为块大小固定，所以每个文件分成多少个块，每个 DataNode 能存多少个块，都很容易计算。同时系统中 NameNode 只负责管理文件的元数据，DataNode 只负责数据存储，分工明确，提高了系统的效率。
* 有利于提高系统的可用性。HDFS通过数据备份来提供数据的容错能力和高可用性，而按照块的存储方式非常适合数据备份。同时块以副本方式存在多个DataNode中，有利于负载均衡，当某个节点处于繁忙状态时，客户端还可以从其他节点获取这个块的副本。

### 副本机制
 HDFS 中数据块的副本数默认为 3，当然可以设置更多的副本数，这些副本分散存储在集群中，副本的分布位置直接影响 HDFS 的可靠性和性能。一个大型的分布式文件系统都是需要跨多个机架。

 如果把所有副本都存放在不同的机架上，可以防止机架故障从而导致数据块不可用，同时在多个客户端访问文件系统时很容易实现负载均衡。如果是写数据，各个数据块需要同步到不同机架上，会影响写数据的效率。在 HDFS 默认 3 个副本情况下，会把第一个副本放到机架的一个节点上，第二副本放在同一个机架的另一个节点上，第三个副本放在不同的机架上。这种策略减少了跨机架副本的个数，提高了数据块的写性能，也可以保证在一个机架出现故障时，仍然能正常运转。

 ## HDFS的读写机制
 客户端读、写文件是与 NameNode 和 DataNode 通信的，下面详细介绍 HDFS 中读写文件的过程。
 ### 读文件
 HDFS 通过 RPC 调用 NameNode 获取文件块的位置信息，并且对每个块返回所在的 DataNode 的地址信息，然后再从 DataNode 获取数据块的副本。，读过程如下图所示：
 <div align=center><img src="/image/hdfs_read.png" width="400" height="200"/></div>
 步骤如下：

 1. 客户端发起文件读取的请求。
 2. NameNode将文件对应的数据块信息及每个块的位置信息，包括每个块的所有副本的位置信息（即每个副本所在的 DataNode的地址信息）都传送给客户端。
 3. 客户端收到数据块信息后，直接和数据块所在的 DataNode 通信，并行地读取数据块。

在客户端获得 NameNode 关于每个数据块的信息后，客户端会根据网络拓扑选择与它最近的 DataNode 来读取每个数据块。当与 DataNode 通信失败时，它会选取另一个较近的 DataNode，同时会对出故障的 DataNode 做标记，避免与它重复通信，并发送 NameNode 故障节点的信息。
### 写文件
当客户端发送写文件请求时，NameNode 负责通知 DataNode 创建文件，在创建之前会检查客户端是否有允许写入数据的权限。通过检测后，NameNode 会向 edits 文件写入一条创建文件的操作记录。写文件过程如下图：
<div align=center><img src="/image/hdfs_write.png" width="400" height="200"/></div>
操作步骤如下：

* 客户端在向 NameNode 发送写请求之前，先将数据写入本地的临时文件中。
* 待临时文件块达到系统设置的块大小时，开始向 NameNode 请求写文件。
* NameNode在此步骤中会检查集群中每个 DataNode状态信息，获取空闲的节点，并在检查客户端权限后创建文件，返回客户端一个数据块及其对应 DataNode的地址列表。列表中包含副本存放的地址。
* 客户端在获取 DataNode 相关信息后，将临时文件中的数据块写入列表中的第一个 DataNode，同时第一个 DataNode 会将数据以副本的形式传送至第二个 DataNode，第二个节点也会将数据传送至第三个 DataNode。DataNode 以数据包的形式从客户端接收数据，并以流水线的形式写入和备份到所有的 DataNode 中，每个 DataNode 收到数据后会向前一个节点发送确认信息。 最终数据传输完毕，第一个 DataNode 会向客户端发送确认信息
* 当客户端收到每个 DataNode 的确认信息时，表示数据块已经持久化地存储在所有 DataNode 当中，接着客户端会向 NameNode 发送确认信息。如果在第（4 ）步中任何一个 DataNode 失败，客户端会告知 NameNode，将数据备份到新的 DataNode 中。

## HDFS的特点与使用场景
### 适合存储超大文件
HDFS 支持 GB 级别甚至 TB 级别的文件，每个文件大小可以大于集群中任意一个节点的磁盘容量，文件的所有数据块存在不同节点中，在进行大文件读写时采用并行的方式提高数据的吞吐量。HDFS 不适合存储大量的小文件，这里的小文件指小于块大小的文件。因为 NameNode 将文件系统的元数据信息存在内存当中，所以 HDFS 所能存储的文件总数受到 NameNode 内存容量的限制。

下面通过举例来计算同等容量的单个大文件和多个小文件所占的文件块的个数。假设 HDFS 中块的大小为 64MB，备份数量为3，—般情况下，一条元数据记录占用 200B 的内存。那么，对于 1GB 的大文件，将占用 1GB/64MB x 3 个文件块；对于 1024 个 1MB 的小文件，则占用 1024 x3 个文件块。可以看到，存储同等大小的文件，单个文件越小，所需的元数据信息越大，占用的内存越大，因此 HDFS 适合存储超大文件。
### 适用于流数据访问
HDFS 适用于批量数据的处理，不适用于交互式处理。它设计的目标是通过流式的数据访问保证高吞吐量，不适合对低延迟用户响应的应用。可以选择 HBase 满足低延迟用户的访问需求。
### 支持简单的一致性模型
HDFS 中的文件支持一次写入、多次读取，写入操作是以追加的方式添加在文件末尾，不支持多个写入者的操作，也不支持对文件的任意位置进行修改。
### 计算向数据靠拢
在 Hadoop 系统中，对数据进行计算时，采用将计算向数据靠拢的方式，即选择最近的数据进行计算，减少数据在网络中的传输延迟。

# HBase的组件和功能
HBase 是一个高可靠、高性能、面向列、可伸缩的分布式数据库，底层基于 Hadoop 的 HDFS 来存储数据。本节将介绍 HBase 的系统架构以及每个组件的功能。下图为HBase的系统架构，包括客户端、ZooKeeper服务器、HMaster主服务器和RegionServer。Region是HBase中数据的物理切片，每个Region中记录了全局数据的一小部分，不同的Region之间的数据不重复。
## 客户端
客户端包含访问 HBase 的接口，是整个 HBase 系统的入口，使用者直接通过客户端操作HBase。客户端使用 HBase的RPC机制与HMaster和RegionServer进行通信。

在一般情况下，客户端与 HMaster 进行管理类操作的通信，在获取 RegionServer 的信息后，直接与 RegionServer 进行数据读写类操作。而且客户端获取 Region 的位置信息后会缓存下来，用来加速后续数据的访问过程。客户端可以用Java语言来实现，也可以使用 Thtift、Rest等客户端模式，甚至MapReduce也可以算作一种客户端。
## Zookeeper
ZooKeeper 是一个高性能、集中化、分布式应用程序协调服务，主要是用来解决分布式应用中用户经常遇到的一些数据管理问题，例如，数据发布/订阅、命名服务、分布式协调通知、集群管理、Master 选举、分布式锁和分布式队列等功能。其中，Master 选举是 ZooKeeper最典型的应用场景。ZooKeeper 主要用于实现高可靠性（High Availability, HA），包括 HDFS 的 NameNode 和 YARN 的 ResourceManager 的 HA。 以 HDFS 为例，NameNode 作为 HDFS 的主节点，负责管理文件系统的命名空间以及客户端对文件的访问，同时需要监控整个 HDFS 中每个 DataNode 的状态，实现负载均衡和容错。

为了实现 HA，必须有多个NameNode 并存，并且只有一个 NameNode 处于活跃状态，其他的则处于备用状态。当活跃的 NameNode 无法正常工作时， 处于备用状态的 NameNode 会通过竞争选举产生新的活跃节点来保证 HDFS 集群的高可靠性
<div align=center><img src="/image/zookeeper.png" width="400" height="200"/></div>

### Master选举
与HDFS 的 HA 机制一样，HBase 集群中有多个 HMaster 并存，通过竞争选举机制保证同一时刻只有一个 HMaster 处于活跃状态，一旦这个 HMaster 无法使用，则从备用节点中选出一个顶上，保证集群的高可靠性。
### 系统容错
在 HBase 启动时，每个 RegionServer 在加入集群时都需要到 ZooKeeper 中进行注册，创建一个状态节点，ZooKeeper 会实时监控每个 RegionServer 的状态，同时 HMaster 会监听这些注册的  RegionServer。

当某个 RegionServer 挂断的时候，ZooKeeper 会因为一段时间内接收不到它的心跳信息而删除该 RegionServer 对应的状态节点，并且给 HMaster 发送节点删除的通知。这时，HMaster 获知集群中某节点断开，会立即调度其他节点开启容错机制。
### Region元数据管理
在 HBase 集群中，Region元数据被存储在 Meta 表中。每次客户端发起新的请求时，需要查询 Meta 表来获取 Region 的位置，而 Meta 表是存在 ZooKeeper 中的。当 Region 发生变化时，例如，Region 的手工移动、进行负载均衡的移动或 Region 所在的 RegionServer 出现故障等，就能够通过 ZooKeeper 来感知到这一变化，保证客户端能够获得正确的 Region 元数据信息。
### Region状态管理
HBase 集群中 Region 会经常发生变更，其原因可能是系统故障，配置修改，或者是 Region 的分裂和合并。只要 Region 发生变化，就需要让集群的所有节点知晓，否则就会出现某些事务性的异常。

而对于 HBase 集群，Region的数量会达到10万，甚至更多。如此规模的Region状态管理如果直接由HMaster来实现，则 HMaster的负担会很重，因此只有依靠 ZooKeeper系统来完成。
### 提供Meta表存储位置
在 HBase 集群中，数据库表信息、列族信息及列族存储位置信息都属于元数据。这些元数据存储在 Meta 表中，而 Meta 表的位置入口由 ZooKeeper 来提供。
## HMaster主服务器
负责监控集群中的所有RegionServer，并且是所有元数据更改的接口。在分布式集群中，HMaster 服务器通常运行在 HDFS 的 NameNode上，HMaster 通过 ZooKeeper 来避免单点故障，在集群中可以启动多个 HMaster，但 ZooKeeper 的选举机制能够保证同时只有一个 HMaster 处于 Active 状态，其他的 HMaster 处于热备份状态。

* 管理用户对Meta表的增、删、改、查操作，HMaster提供了表中基于元数据方法的接口。

| 接口          | 功能                                |
| ------------- | ----------------------------------- |
| HBase表       | 创建表、删除表、启用/失效表、修改表 |
| HBase列表     | 添加列、修改列、移除列              |
| HBase表Region | 移动Region、分配和合并Region        |
* 管理RegoinServer的负载均衡，调整Region的分布
* Region的分配和移除
* 处理RegionServer的故障转移

当某台 RegionServer 出现故障时，总有一部分新写入的数据还没有持久化地存储到磁盘中，因此在迁移该 RegionServer 的服务时，需要从修改记录中恢复这部分还在内存中的数据，HMaster 需要遍历该 RegionServer 的修改记录，并按 Region 拆分成小块移动到新的地址下。

另外，当 HMaster 节点发生故障时，由于客户端是直接与 RegionServer 交互的，且 Meta 表也是存在于 ZooKeeper 当中，整个集群的工作会继续正常运行，所以当 HMaster 发生故障时，集群仍然可以稳定运行。

但是 HMaster 还会执行一些重要的工作，例如，Region 的切片、RegionServer 的故障转移等，如果 HMaster 发生故障而没有及时处理，这些功能都会受到影响，因此 HMaster 还是要尽快恢复工作。 ZooKeeper 组件提供了这种多 HMaster 的机制，提高了 HBase 的可用性和稳健性。

## RegionServer
在 HDFS 中，DataNode 负责存储实际数据。RegionServer 主要负责响应用户的请求，向 HDFS 读写数据。一般在分布式集群中，RegionServer 运行在 DataNode 服务器上，实现数据的本地性。每个 RegionServer 包含多个 Region，它负责的功能如下：

* 处理分批给它的 Region。
* 处理客户端读写请求。
* 刷新缓存到 HDFS 中。
* 处理 Region 分片。
* 执行压缩。

RegionServer 是 HBase 中最核心的模块，其内部管理了一系列 Region 对象，每个 Region 由多个HStore组成，每个HStore对应表中一个列族的存储。HBase 是按列进行存储的，将列族作为一个集中的存储单元，并且HBase将具备相同I/O特性的列存储到一个列族中，这样可以保证读写的高效性。

RegionServer 最终将 Region 数据存储在 HDFS 中，采用 HDFS 作为底层存储。HBase 自身并不具备数据复制和维护数据副本的功能，而依赖HDFS为HBase 提供可靠和稳定的存储。当然，HBase也可以使用本地文件系统或云计算环境中的Amazon S3。