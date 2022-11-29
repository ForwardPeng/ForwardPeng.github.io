---
title: HBase学习(二)
date: 2020-06-20 21:43:18
categories: 
- 大数据
- 数据库
tags:
- NoSQL数据库
- 大数据框架
toc: true
---
# HBase模型
HBase 是一种列存储模式与键值对存储模式结合的 NoSQL 数据库，它具有灵活的数据模型，不仅可以基于键进行快速查询，还可以实现基于值、列名等的全文遍历和检索。HBase 可以实现自动的数据分片，用户不需要知道数据存储在哪个节点上，只要说明检索的要求，系统会自动进行数据的查询和反馈。

HBase 不支持关系模型，它可以根据用户的需求提供更灵活和可扩展的表设计。与传统的关系型数据库类似，HBase 也是以表的方式组织数据，应用程序将数据存于 HBase 的表中，HBase 的表也由行和列组成。但有一点不同的是，HBase 有列族的概念，它将一列或多列组织在一起，HBase 的每个列必须属于某一个列族。

* **表(Table)**
HBase 中的数据以表的形式存储。同一个表中的数据通常是相关的，使用表主要是可以把某些列组织起来一起访问。表名作为 HDFS 存储路径的一部分来使用，在 HDFS 中可以看到每个表名都作为独立的目录结构。
* **行(ROW)**
在 HBase 表里，每一行代表一个数据对象，每一行都以行键（Row Key）来进行唯一标识，行键可以是任意字符串。在 HBase 内部，行键是不可分割的字节数组，并且行键是按照字典排序由低到高存储在表中的。在 HBase 中可以针对行键建立索引，提高检索数据的速度。

* **列族(Colunm Family)**
HBase 中的列族是一些列的集合，列族中所有列成员有着相同的前缀，列族的名字必须是可显示的字符串。列族支持动态扩展，用户可以很轻松地添加一个列族或列，无须预定义列的数量以及类型。所有列均以字符串形式存储，用户在使用时需要自行进行数据类型转换。
* **列标识(Column Qualifier)** 
列族中的数据通过列标识来进行定位，列标识也没有特定的数据类型，以二进制字节来存储。通常以 Column Family：Colunm Qualifier 来确定列族中的某列。
* **单元格(Cell)**
列族中的数据通过列标识来进行定位，列标识也没有特定的数据类型，以二进制字节来存储。通常以 Column Family：Colunm Qualifier 来确定列族中的某列。
* **时间戳**
在默认情况下，每一个单元格插入数据时都会用时间戳来进行版本标识。读取单元格数据时，如果时间戳没有被指定，则默认返回最新的数据；写入新的单元格数据时，如果没有设置时间戳，默认使用当前时间。每一个列族的单元数据的版本数量都被 HBase 单独维护，默认情况下 HBase 保留 3 个版本数据。
## 数据模型
HBase表的逻辑模型如下表所示。HBase 中的一个表有若干行，每行有多个列族，每个列族中包含多个列，而列中的值有多个版本。 
<div align=center><img src="/image/HBaseModel.png" width="400" height="200"/></div>
关系型数据库中表的结构需要预先定义，如列名及其数据类型和值域等内容。如果需要添加新列，则需要修改表结构，这会对已有的数据产生很大影响。同时，关系型数据库中的表为每个列预留了存储空间，即表中的空白Cell数据在关系型数据库中以“NULL”值占用存储空间。因此，对稀疏数据来说，关系型数据库表中就会产生很多“NULL”值，消耗大量的存储空间。

在 HBase 中，如表中的空白Cell在物理上是不占用存储空间的，即不会存储空白的键值对。因此，若一个请求为获取 RowKey 为 0001 在 T2 时间的 Stulnfo:class 值时，其结果为空。类似地，若一个请求为获取 RowKey 为 0002 在 T1 时间的 Grades Computer值时，其结果也为空。

与面向行存储的关系型数据库不同，HBase是面向列存储的，且在实际的物理存储中，列族是分开存储的，即表中的学生信息表将被存储为 StuInfo 和 Grades 两个部分。StuInfo物理存储方式如下：
<div align=center><img src="/image/StuInfo.png" width="400" height="200"/></div>

## HBase Shell及其常用命令
在 HBase 的 HMaster 主机上通过命令行输入 hbase shell，即可进入 HBase 命令行环境，在 Shell 中输入help可以获取可用命令列表，输入help commandname可获取特定命令的帮助，还可以输入各种命令查看集群、数据库和数据的各项详情。如：使用status命令查看当前集群各节点的状态，使用version命令查看当前 HBase 的版本号，输入命令exit或quit即可退出 HBase Shell。常用命令汇总：
| 命令           | 描述                                                                             |
| -------------- | -------------------------------------------------------------------------------- |
| create         | 创建指定模式的新表                                                               |
| alter          | 修改表的结构，如添加新的列族                                                     |
| describe       | 展示表结构的信息，包括列族的数量与属性                                           |
| list           | 列出HBase中已有的表                                                              |
| disable/enable | 为了删除或更改表而禁用一个表，更改完后需要解禁表                                 |
| diable_all     | 禁用所有的表，可以用正则表达式匹配表                                             |
| is_disable     | 判断一个表是否被禁用                                                             |
| drop           | 删除表                                                                           |
| truncate       | 如果只是想删除数据而不是表结构，则可用 truncate 来禁用表、删除表并自动重建表结构 |
| put            | 添加一个值到指定单元格中                                                         |
| get            | 通过表名、行键等参数获取行或单元格数据                                           |
| scan           | 遍历表并输出满足指定条件的行记录                                                 |
| count          | 计算表中的逻辑行数                                                               |
| delete         | 删除表中列族或列的数据                                                           |
## 创建表(Create命令)
HBase 使用 creat 命令来创建表，创建表时需要指明表名和列族名，如创建上表中的学生信息表 Student 的命令如下:
>create 'Student','StuInfo','Grades'

这条命令仓建了名为 Student 的表，表中包含两个列族，分别为 Stulnfo 和 Grades。在 HBase Shell 语法中，所有字符串参数都必须包含在单引号中，且区分大小写，如 Student 和 student 代表两个不同的表。另外，在上条命令中没有对列族参数进行定义，因此使用的都是默认参数，如果建表时要设置列族的参数，参考以下方式：
>create 'Student', {NAME => 'Stulnfo', VERSIONS => 3}, {NAME =>'Grades', BLOCKCACHE => true}

大括号内是对列族的定义，NAME、VERSION 和 BLOCKCACHE 是参数名，无须使用单引号，符号=>表示将后面的值赋给指定参数。例如，VERSIONS => 3是指此单元格内的数据可以保留最近的 3 个版本，BLOCKCACHE => true指允许读取数据时进行缓存。创建表结构后，可以使用exsits命令查看表是否存在，使用list命令查看数据库中所有的表，使用describe查看指定表的列族信息。

## HBase修改表(alter命令)
### 修改列族
首先修改列族的参数信息，如修改列族的版本。例如上面的 Student 表，假设它的列族 Grades 的 VERSIONS 为 1，但是实际可能需要保存最近的 3 个版本，可使用以下命令完成：
>alter 'Student', {NAME => 'Grades', VERSIONS => 3}

修改多个列族的参数，形式与create命令类似。这里要注意，修改已存有数据的列族属性时，HBase 需要对列族里所有的数据进行修改，如果数据量很大，则修改可能要占很长时间。
### 增加列族
如果需要在 Student 表中新增一个列族 hobby，使用以下命令：
>alter 'Student', 'hobby'
### 删除列族
如果要移除或者删除已有的列族，以下两条命令均可完成：
>alter 'Student', { NAME => 'hobby', METHOD => 'delete' }

>alter 'Student', 'delete' => 'hobby'

另外，HBase 表至少要包含一个列族，因此当表中只有一个列族时，无法将其删除。
## HBase删除表(disable和drop命令)
HBase 使用 drop 命令删除表，但是在删除表之前需要先使用 disable 命令禁用表。例如有一个 Student 表，删除该表的完整流程如下：
>disable 'Student'

>drop 'Student'

使用 disable 禁用表以后，可以使用 is_disable 查看表是否禁用成功。另外，如果只是想清空表中的所有数据，使用truncate命令即可，此命令相当于完成禁用表、删除表，并按原结构重新建立表操作：
>truncate 'Student'
## HBase put命令：插入数据
put向表中增加一个新行数据，或覆盖指定行的数据。写法如下：
>put 'Student', '0001', 'Stulnfo:Name', 'Tom Green', 1

* 第一个参数Student为表名；
* 第二个参数0001为行键的名称，为字符串类型；
* 第三个参数StuInfo:Name为列族和列的名称，中间用冒号隔开。列族名必须是已经创建的，否则 HBase 会报错；列名是临时定义的，因此列族里的列是可以随意扩展的；
* 第四个参数Tom Green为单元格的值。在 HBase 里，所有数据都是字符串的形式；
* 最后一个参数1为时间戳，如果不设置时间戳，则系统会自动插入当前时间为时间戳。

不考虑时间戳的情况下，执行put语句，则可对数据进行更新操作，如果在初始创建表时，已经设定了列族 VERSIONS 参数值为 n，则 put 操作可以保存 n 个版本数据，即可查询到行键为 0001 的学生的 n 个版本的姓名数据。

## HBase删除数据(delete命令)
删除Student表中行键为0002的Grades列族的所有数据：
>delete 'Student','0002','Grades'

delete 操作并不会马上删除数据，只会将对应的数据打上删除标记（tombstone），只有在合并数据时，数据才会被删除。另外，delete 命令的最小粒度是单元格（Cell）。例如，执行以下命令将删除 Student表中行键为0001，Grades 列族成员为Math，时间戳小于等于2的数据：
>delete 'Student', '0001', 'Grades:Math', 2

如需删除表中所有列族在某一行上的数据，即删除上表中一个逻辑行，则需要使用 deleteall 命令,如下：
>deleteall 'Student','0001'

## HBase get命令：从表中获取数据
get 命令必须设置表名和行键名，同时可以选择指明列族名称、时间戳范围、数据版本等参数。获取 Student 表中行键为 0001 的所有列族数据， get命令使用了限定词 VERSIONS=>2，因此 get 得到的数据只显示了 Stulnfo:Name 最小的两个版本。：
>get 'student', '0001'

## HBase scan命令：查询全表数据
使用时只需指定表名即可查询数据：
>scan 'Student'

还可以指定列族和列的名称，或指定输出行数，甚至指定输出行键范围。注意指定列族和列名称使用 COLUMN 限定符；指定输出行键范围使用 STARTROW 和 ENDROW 限定符，此时输出行不包括 ENDROW 行。例如，上图中 ENDROW=>0003，只会输出行键为 0002 的记录，不会输出 0003 记录。

上述限定条件也可以联合使用，中间用逗号隔开即可。在 HBase 中，具有相同行键的单元格，无论其属于哪个列族，都可以将整体看作一个逻辑行， 使用 count 命令可以计算表的逻辑行数。在关系型数据库中，有多少条记录就有多少行，表中的行数很容易统计。而在 HBase 里，计算逻辑行需要扫描全表的内容，重复的行键是不纳入计数的，且标记为 tombstone 的删除数据也不纳入计数。

## HBase过滤器
使用 show_filter 命令可以查看当前 HBase 支持的过滤器类型，一般需要配合比较运算符或比较器使用，如下表所示。
|                        |                  |
| ---------------------- | ---------------- |
| BinaryComparator       | 匹配完整字节数组 |
| BinaryPrefixComparator | 匹配字节数组前缀 |
| BitComparator          | 匹配比特位       |
| NullComparator         | 匹配空值         |
| RegexStringComparator  | 匹配正则表达式   |
| SubstringComparator    | 匹配子字符串     |
用过滤器的语法格式如下所示：
>scan '表名', { Filter => "过滤器(比较运算符, '比较器') }

在上述语法中，Filter=> 指明过滤的方法，整体可用大括号引用，也可以不用大括号。过滤的方法用双引号引用，而比较方式用小括号引用。

### 行键过滤器
RowFilter 可以配合比较器和运算符，实现行键字符串的比较和过滤。例如，匹配行键中大于 0001 的数据，可使用 binary 比较器；匹配以 0001 开头的行键，可使用 substring 比较器，注意 substring 不支持大于或小于运算符。针对行键进行匹配的过滤器还有 PrefixFilter、KeyOnlyFilter、FirstKeyOnlyFilter 和 InclusiveStopFilter。其中，FirstKeyOnlyFilter 过滤器可以用来实现对逻辑行进行计数的功能，并且比其他计数方式效率高。
| 行键过滤器          | 描述                                               | 示例                                                                                                                                                 |
| ------------------- | -------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------- |
| PrefixFilter        | 行键前缀比较器，比较行键前缀                       | scan 'Student', FILTER => "PrefixFilter('0001')" 同 scan 'Student', FILTER => "RowFilter(=,'substring:0001')"                                        |
| KeyOnlyFilter       | 只对单元格的键进行过滤和显示，不显示值             | scan 'Student', FILTER => "KeyOnlyFilter()"                                                                                                          |
| FirstKeyOnlyFilter  | 只扫描显示相同键的第一个单元格，其键值对会显示出来 | scan 'Student', FILTER => "FirstKeyOnlyFilter()"                                                                                                     |
| InclusiveStopFilter | 替代 ENDROW 返回终止条件行                         | scan 'Student', { STARTROW => '0001', FIILTER => "InclusiveStopFilter('binary:0002')" }  同 scan 'Student', { STARTROW => '0001', ENDROW => '0003' } |
## 列族与列过滤器
针对列族进行过滤的过滤器为 FamilyFilter，其语法结构与 RowFilter 类似，不同之处在于 FamilyFilter 是对列族名称进行过滤的。例如，以下命令扫描Student表显示列族为 Grades 的行。
>scan 'Student', FILTER=>" FamilyFilter(= , 'substring:Grades')"

过滤器结合比较运算符和比较器进行列族或列的扫描过滤：
| 列过滤器                   | 描述                               | 示例                                                                    |
| -------------------------- | ---------------------------------- | ----------------------------------------------------------------------- |
| QualifierFilter            | 列标识过滤器，只显示对应列名的数据 | scan 'Student', FILTER => "QualifierFilter(=,'substring:Math')"         |
| ColumnPrefixFilter         | 对列名称的前缀进行过滤             | scan 'Student', FILTER => "ColumnPrefixFilter('Ma')"                    |
| MultipleColumnPrefixFilter | 可以指定多个前缀对列名称过滤       | scan 'Student', FILTER => "MultipleColumnPrefixFilter('Ma','Ag')"       |
| ColumnRangeFilter          | 过滤列名称的范围                   | scan 'Student', FILTER => "ColumnRangeFilter('Big',true,'Math',false')" |

MultipleColumnPrefixFilter 过滤器是对 ColumnPrefixFilter 的延伸，可以一次过滤多个列前缀。ColumnRangeFilter过滤器则可以扫描出符合过滤条件的列范围，起始和终止列名用单引号引用，true 和 false 参数可指明结果中包含的起始或终止列。
### 值过滤器
在 HBase 的过滤器中也有针对单元格进行扫描的过滤器，即值过滤器，如下表：
| 值过滤器                       | 描述                                 | 示例                                                                                                                              |
| ------------------------------ | ------------------------------------ | --------------------------------------------------------------------------------------------------------------------------------- |
| ValueFilter                    | 值过滤器，找到符合值条件的键值对     | scan 'Student', FILTER => "ValueFilter(=,'substring:curry')"同get 'Student', '0001', FILTER => "ValueFilter(=,'substring:curry')" |
| SingleColumnValueFilter        | 在指定的列族和列中进行比较的值过滤器 | scan 'Student', Filter => "SingleColumnValueFilter('StuInfo', 'Name', =, 'binary:curry')"                                         |
| SingleColumnValueExcludeFilter | 排除匹配成功的值                     | scan 'Student', Filter => "SingleColumnValueExcludeFilter('StuInfo', 'Name', =, 'binary:curry')"                                  |
# HBase Region管理(拆分＋合并＋负载均衡)
当 HBase中 表的容量非常庞大时，用户就需要将表中的内容分布到多台机器上。那么，需要根据行键的值对表中的行进行划分，每个行区间构成一个 Region，一个 Region 包含了位于某个阈值区间的所有数据。
## HFile合并
每个RegionServer包含多个Region，而每个 Region 又对应多个 Store，每一个 Store 对应表中一个列族的存储，且每个 Store 由一个 MemStore 和多个 StoreFile 文件组成。

StoreFile 在底层文件系统中由 HFile 实现，也可以把 Store 看作由一个 MemStore 和多个 HFile 文件组成。MemStore 充当内存写缓存，默认大小 64MB，当 MemStore 超过阈值时，MemStore 中的数据会刷新到一个新的 HFile 文件中来持久化存储。每个 Store 中的 HFile 文件会越来越多，I/O 操作的速度也随之变慢，读写也会延时，导致慢操作。因此，需要对 HFile 文件进行合并，让文件更紧凑，让系统更有效率。HFile的合并分为两种类型，分别是 Minor 合并和 Major 合并。这两种合并都发生在 Stow 内部，不是 Region 的合并
<div align=center><img src="/image/Minor.png" width="400" height="200"/></div>

## Minor合并
Minor 合并是把多个小 HFile 合并生成一个大的 HFile。执行合并时，HBase 读出已有的多个 HFile 的内容，把记录写入一个新文件中。然后把新文件设置为激活状态，并标记旧文件为删除。

在 Minor 合并中，这些标记为删除的旧文件是没有被移除的，仍然会出现在 HFile 中，只有在进行 Major 合并时才会移除这些旧文件。对需要进行 Minor 合并的文件的选择是触发式的，当达到触发条件才会进行 Minor 合并，而触发条件有很多，例如， 在将 MemStore 的数据刷新到 HFile 时会申请对 Store下符合条件的 HFile 进行合并，或者定期对 Store 内的 HFile 进行合并。选择合并HFile条件如下表：
| 参数名           | 配置项                           | 默认值            | 备注                                           |
| ---------------- | -------------------------------- | ----------------- | ---------------------------------------------- |
| minFileToCompact | hbase.hstore.compaction.min      | 3                 | 至少需要三个满足条件的 HFile 才启动合并        |
| minFileToCompact | hbase.hstore.compaction.max      | 10                | 一次合并最多选择 10 个                         |
| maxCompactSize   | hbase.hstore.compaction.max.size | Long.MAX_VALUE    | HFile 大于此值时被排除合并，避免对大文件的合并 |
| minCompactSize   | hbase.hstore.compaction.min.size | MemStoreFlushSize | HFile 小于 MemStore 的默认值时被加入合并队列   |
## Major合并
Major 合并针对的是给定 Region 的一个列族的所有 HFile，如图 1 所示。它将 Store 中的所有 HFile 合并成一个大文件，有时也会对整个表的同一列族的 HFile 进行合并，这是一个耗时和耗费资源的操作，会影响集群性能。一般情况下都是做 Minor 合并，不少集群是禁止 Major 合并的，只有在集群负载较小时进行手动 Major 合并操作，或者配置 Major 合并周期，默认为 7 天。另外，Major 合并时会清理 Minor 合并中被标记为删除的 HFile。

## Region拆分
Region 拆分是 HBase 能够拥有良好扩展性的最重要因素。一旦 Region 的负载过大或者超过阈值时，它就会被分裂成两个新的 Region，由RegionServer完成，流程如下：
* 将需要拆分的 Region下线，阻止所有对该 Region 的客户端请求，Master 会检测到 Region 的状态为 SPLITTING。
* 将一个 Region 拆分成两个子 Region，先在父 Region下建立两个引用文件，分别指向 Region 的首行和末行，这时两个引用文件并不会从父 Region 中复制数据。
* 之后在 HDFS 上建立两个子 Region 的目录，分别复制上一步建立的引用文件，每个子 Region 分别占父 Region 的一半数据。复制登录完成后删除两个引用文件。
* 完成子 Region 创建后，向 Meta 表发送新产生的 Region 的元数据信息。
* 将 Region 的拆分信息更新到 HMaster，并且每个 Region 进入可用状态。

## Region合并
从 Region 的拆分过程中可以看到，随着表的增大，Region 的数量也越来越大。如果有很多 Region，它们中 MemStore 也过多，会频繁出现数据从内存被刷新到 HFile 的操作，从而会对用户请求产生较大的影响，可能阻塞该 Region 服务器上的更新操作。过多的 Region 会增加 ZooKeeper 的负担。因此，当 Region 服务器中的 Region 数量到达阈值时，Region 服务器就会发起 Region 合并，其合并过程如下。
* 客户端发起 Region 合并处理，并发送 Region 合并请求给 Master。
* Master 在 Region 服务器上把 Region 移到一起，并发起一个 Region 合并操作的请求。
* Region 服务器将准备合并的 Region下线，然后进行合并。
* 从 Meta 表删除被合并的 Region 元数据，新的合并了的 Region 的元数据被更新写入 Meta 表中。
* 合并的 Region 被设置为上线状态并接受访问，同时更新 Region 信息到 Master。

## Region负载均衡
当 Region 分裂之后，Region 服务器之间的 Region 数量差距变大时，Master 便会执行负载均衡来调整部分 Region 的位置，使每个 Region 服务器的 Region 数量保持在合理范围之内，负载均衡会引起 Region 的重新定位，使涉及的 Region 不具备数据本地性。Region 的负载均衡由 Master 来完成，Master 有一个内置的负载均衡器，在默认情况下，均衡器每 5 分钟运行一次，用户可以配置。负载均衡操作分为两步进行：首先生成负载均衡计划表， 然后按照计划表执行 Region 的分配。

执行负载均衡前要明确，在以下几种情况时，Master 是不会执行负载均衡的。
* 均衡负载开关关闭。
* Master 没有初始化。
* 当前有 Region 处于拆分状态。
* 当前集群中有 Region 服务器出现故障。

Master 内部使用一套集群负载评分的算法，来评估 HBase 某一个表的 Region 是否需要进行重新分配。这套算法分别从 Region 服务器中 Region 的数目、表的 Region 数、MenStore 大小、 StoreFile 大小、数据本地性等几个维度来对集群进行评分，评分越低代表集群的负载越合理。确定需要负载均衡后，再根据不同策略选择 Region 进行分配，负载均衡策略有三种，如下表所示。
| 策略                | 原理                                                                                           |
| ------------------- | ---------------------------------------------------------------------------------------------- |
| RandomRegionPicker  | 随机选出两个 Region 服务器下的 Region 进行交换                                                 |
| LoadPicker          | 获取 Region 数目最多或最少的两个 Region 服务器，使两个 Region 服务器最终的 Region 数目更加平均 |
| LocalityBasedPicker | 选择本地性最强的 Region                                                                        |
根据上述策略选择分配 Region 后再继续对整个表的所有 Region 进行评分，如果依然未达到标准，循环执行上述操作直至整个集群达到负载均衡的状态。

## HBase数据写入流程
* 客户端访问ZooKeeper，从Meta表得到写入数据对应的Region信息和相应的Region服务器
* 客户端访问相应的 Region 服务器，把数据分别写入 HLog 和 MemStore。MemStore 数据容量有限，当达到一个阈值后，则把数据写入磁盘文件 StoreFile 中，在 HLog 文件中写入一个标记，表示 MemStore 缓存中的数据已被写入 StoreFile 中。如果 MemStore 中的数据丢失，则可以从 HLog 上恢复。
* 当多个 StoreFile 文件达到阈值后，会触发 Store.compact() 将多个 StoreFile 文件合并为一个 大文件。

## HBase数据读取流程
* 客户端先访问 ZooKeeper，从 Meta 表读取 Region 信息对应的服务器。

* 客户端向对应 Region 服务器发送读取数据的请求，Region 接收请求后，先从 MemStore 查找数据；如果没有，再到 StoreFile 上读取，然后将数据返回给客户端。

## 运维管理
在集群运行时，有些操作任务是必需的，包括移除和增加节点。
### 移除Region服务器节点
当集群由于升级或更换硬件等原因需要在单台机器上停止守护进程时，需要确保集群的其他 部分正常工作，并且确保从客户端应用来看停用时间最短。满足此条件必须把这台 Region 服务器服务的 Region 主动转移到其他 Region 服务器上，而不是让 HBase 被动地对此 Region 服务器的下线进行反应。在指定节点的HBase目录下使用hbase-damon.sh stop命令来停止集群中的一个 Region 服务器。执行此命令后，Region 服务器先将所有 Region 关闭，然后再把自己的进程停止，Region 服务器在 ZooKeeper 中对应的临时节点将会过期。

Master 检测到 Region 服务器停止服务后，将此Region服务器上的Region重新分配到其他机器上。这种停止服务器方法的坏处是，Region 会下线一段时间，时间长度由 ZooKeeper 超时时间来决定，而且会影响集群性能，同时整个集群系统会经历一次可用性的轻微降级。HBase也提供了脚本来主动转移 Region 到其他 Region 服务器，然后卸掉下线的 Region 服务器，这样会让整个过程更加安全。在 HBase 的 bin 目录下提供了 gracefiil_stop.sh 脚本，可以实现这种主动移除节点的功能。此脚本停止一个 Region 服务器的过程如下。
* 关闭 Region 均衡器。
* 从需要停止的 Region 服务器上移出 Region，并随机把它们分配给集群中其他服务器。
* 停止 Region 服务器进程。

graceful_stop.sh 脚本会把 Region 从对应服务器上一个个移出以减少抖动，并且会在移动下一 个 Region 前先检测新服务器上的 Region 是否已经部署好。此脚本关闭了需要停止的 Region 服务器，Master 会检测到停止服务的 Region 服务器，但此时 Master 无须再来转移 Region。同时，由于 Region 服务器关闭时已经没有 Region 了，所以不会执行 WAL 拆分的相关操作。
### 增加Master备份节点
为了增加 HBase 集群的可用性，可以为 HBase 增加多个备份Master。当 Master出现故障后， 备份Master可以自动接管整个HBase的集群。配置备份Master的方式是在 HBase 的 conf 下增加文件 backup-masters，然后通过 hbase-damon.sh start 命令启动。Master 进程使用 ZooKeeper 来决定哪一个是当前活动的进程。当集群启动时，所有进程都会去竞争作为主 Master 来提供服务，其他 Master 会轮询检测当前主 Master 是否失效；如果失效，则会触发新的 Master 选举。
##　数据管理
在使用 HBase 集群时，需要处理一张或多张表中的大量数据，例如，备份数据时移动全部数据或部分数据到归档文件中
### 数据的导出
在HBase集群中，有时候需要将表进行导出备份，HBase提供了自带的工具Export，可以将表的内容输出为HDFS的序列化文件，在HBase安装目录的bin目录下执行 hbase org.apache.hadoop.hbase.mapreduce.Export 命令，参数-D设定键值类型配置属性，可以使用正则表达式或过滤器过滤掉部分数据，如下：
| 名字          | 描述                                                            |
| ------------- | --------------------------------------------------------------- |
| tablename     | 准备导出的表名                                                  |
| outputdir     | 导出数据存放在 HDFS 中的路径                                    |
| version       | 每列备份的版本数量，默认值为 1                                  |
| starttime     | 开始时间                                                        |
| endtime       | 扫描所使用的时间范围的结束时间                                  |
| regexp/prefix | 以 ^ 开始表示该选项被当做表达式类匹配行键，否则被当做行键的前缀 |
### 数据的导入
HBase提供了自带的工具Import，可以将数据加载到HBase当中，在bin目录下执行hbase org.apache.hadoop.hbase.mapereduce.Import。
### 数据迁移
在 HBase 系统中，有时候需要在同集群内部或集群之间复制表的部分或全部数据，可使用 HBase 自带 CopyTable 工具来完成此功能。其中，根据 -peer.adr 参数可以区分集群内部还是集群间的复制，当设置为与当前运行命令的集群相同时为集群内复制，否则为集群间复制。另外，复制时还可以只复制部分数据，如用 -families 来表示要复制的列族。
