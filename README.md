# MySQL

## 数据库存储引擎
------------------
MySQL中的数据用各种不同的技术存储在文件(或者内存)中。这些技术中的每一种技术都使用不同的存储机制、索引技巧、锁定水平并且最终提供广泛的不同的功能和能力。通过选择不同的技术，你能够获得额外的速度或者功能，从而改善你的应用的整体功能。 存储引擎说白了就是如何存储数据、如何为存储的数据建立索引和如何更新、查询数据等技术的实现方法。


MySQL 5.0 支持的存储引擎包括 MyISAM, InnoDB, MEMORY, ARCHIVE, MRG_MYISAM, CSV, BLACKHOLE, PERFORMANCE_SCHEMA, FEDERATED 。 

其中常用的为 InnoDB，MyISAM，MEMORY，ARCHIVE。

### InnoDB
InnoDB是一个健壮的事务型存储引擎，这种存储引擎已经被很多互联网公司使用，为用户操作非常大的数据存储提供了一个强大的解决方案。我的电脑上安装的 MySQL 5.6.13 版，InnoDB就是作为默认的存储引擎。InnoDB还引入了行级锁定和外键约束，在以下场合下，使用InnoDB是最理想的选择：

* 更新密集的表。InnoDB存储引擎特别适合处理多重并发的更新请求。
* 事务。InnoDB存储引擎是支持事务的标准MySQL存储引擎。
* 自动灾难恢复。与其它存储引擎不同，InnoDB表能够自动从灾难中恢复。
* 外键约束。MySQL支持外键的存储引擎只有InnoDB。
* 支持自动增加列AUTO_INCREMENT属性。
从5.7开始innodb存储引擎成为默认的存储引擎。

一般来说，如果需要事务支持，并且有较高的并发读取频率，InnoDB是不错的选择。

### MyISAM
MyISAM表是独立于操作系统的，这说明可以轻松地将其从Windows服务器移植到Linux服务器；每当我们建立一个MyISAM引擎的表时，就会在本地磁盘上建立三个文件，文件名就是表名。例如，我建立了一个MyISAM引擎的tb_Demo表，那么就会生成以下三个文件：

* tb_demo.frm，存储表定义。
* tb_demo.MYD，存储数据。
* tb_demo.MYI，存储索引。

MyISAM表无法处理事务，这就意味着有事务处理需求的表，不能使用MyISAM存储引擎。MyISAM存储引擎特别适合在以下几种情况下使用：

* 选择密集型的表。MyISAM存储引擎在筛选大量数据时非常迅速，这是它最突出的优点。
* 插入密集型的表。MyISAM的并发插入特性允许同时选择和插入数据。例如：MyISAM存储引擎很适合管理邮件或Web服务器日志数据。


## 常用引擎对比

不同存储引起都有各自的特点，为适应不同的需求，需要选择不同的存储引擎，所以首先考虑这些存储引擎各自的功能和兼容。

| 特性 | InnoDB | MyISAM | MEMORY | ARCHIVE |
| ---- | ----- | ------- | ------ | ------- |
| 存储限制(Storage limits) | 64TB | No | YES | No |
| 支持事物(Transactions) | Yes | No | No | No |
| 锁机制(Locking granularity) | 行锁 | 表锁 | 表锁 | 行锁 |
| B树索引(B-tree indexes) | Yes | Yes | Yes | No |
| T树索引(T-tree indexes) | No | No | No | No |
| 哈希索引(Hash indexes) | Yes | No | Yes | No |
| 全文索引(Full-text indexes) | Yes | Yes | No | No |
| 集群索引(Clustered indexes) | Yes | No | No | No |
| 数据缓存(Data caches) | Yes | No | N/A | No |
| 索引缓存(Index caches) | Yes | Yes | N/A | No |
| 数据可压缩(Compressed data) | Yes | Yes | No | Yes |
| 加密传输(Encrypted data<sup>[1]</sup>) | Yes | Yes | Yes | Yes |
| 集群数据库支持(Cluster databases support) | No | No | No | No |
| 复制支持(Replication support<sup>[2]</sup>) | Yes | No | No | Yes |
| 外键支持(Foreign key support) | Yes | No | No | No |
| 存储空间消耗(Storage Cost) | 高 | 低 | N/A | 非常低 |
| 内存消耗(Memory Cost) | 高 | 低 | N/A | 低 |
| 数据字典更新(Update statistics for data dictionary) | Yes | Yes | Yes | Yes |
| 备份/时间点恢复(backup/point-in-time recovery<sup>[3]</sup>) | Yes | Yes | Yes | Yes |
| 多版本并发控制(Multi-Version Concurrency Control/MVCC) | Yes | No | No | No |
| 批量数据写入效率(Bulk insert speed) | 慢 | 快 | 快 | 非常快 |
| 地理信息数据类型(Geospatial datatype support) | Yes | Yes | No | Yes |
| 地理信息索引(Geospatial indexing support<sup>[4]</sup>) | Yes | Yes | No | Yes |



## 如何选择合适的存储引擎

提供几个选择标准，然后按照标准，选择对应的存储引擎即可，也可以根据[常用引擎对比](#常用引擎对比)来选择你使用的存储引擎。使用哪种引擎需要根据需求灵活选择，一个数据库中多个表可以使用不同的引擎以满足各种性能和实际需求。使用合适的存储引擎，将会提高整个数据库的性能。

决定使用什么样的存储引擎是一个问题，这里只考虑最常见的 MyISAM 和 InnoDB 两个存储引擎。 

先考虑一些问题： 

> * 数据库有外键吗？ 
> * 需要事务支持吗？ 
> * 需要全文索引吗？ 
> * 经常使用什么样的查询模式？ 
> * 数据有多大？ 

如果你需要事务处理或是外键，那么InnoDB可能是比较好的方式。如果你需要全文索引，那么通常来说MyISAM是好的选择，因为这是系统内建的，然而，我们其实并不会经常地去测试两百万行记录。所以，就算是慢一点，我们可以通过使用Sphinx从InnoDB中获得全文索引。 

数据的大小，是一个影响选择什么样存储引擎的重要因素，大尺寸的数据集趋向于选择InnoDB方式，因为其支持事务处理和故障恢复。数据库的在小决定了故障恢复的时间长短，InnoDB可以利用事务日志进行数据恢复，这会比较快。而MyISAM可能会需要 几个小时甚至几天来干这些事，InnoDB只需要几分钟。 

操作数据库表的习惯可能也会是一个对性能影响很大的因素。比如： COUNT() 在 MyISAM表中会非常快，而在InnoDB表下可能会很痛苦。而主键查询则在InnoDB下会相当相当的快，但需要小心的是如果我们的主键太长了也会导致性能问题。大批的inserts语句在MyISAM下会快一些，但是updates在InnoDB 下会更快一些——尤其在并发量大的时候。 


## 数据库索引
----------------------------
### 索引分类

* index ----普通的索引,数据可以重复

* fulltext----全文索引，用来对大表的文本域(char，varchar，text)进行索引。语法和普通索引一样。

* unique ----唯一索引,唯一索引,要求所有记录都唯一

* primary key ----主键索引,也就是在唯一索引的基础上相应的列必须为主键

### 设计原则

> 1．选择唯一性索引
> * 唯一性索引的值是唯一的，可以更快速的通过该索引来确定某条记录。例如，学生表中学号是具有唯一性的字段。为该字段建立唯一性索引可以很快的确定某个学生的信息。如果使用姓名的话，可能存在同名现象，从而降低查询速度。

> 2．为经常需要排序、分组和联合操作的字段建立索引
> * 经常需要ORDER BY、GROUP BY、DISTINCT和UNION等操作的字段，排序操作会浪费很多时间。如果为其建立索引，可以有效地避免排序操作。

> 3．为常作为查询条件的字段建立索引
> * 如果某个字段经常用来做查询条件，那么该字段的查询速度会影响整个表的查询速度。因此，为这样的字段建立索引，可以提高整个表的查询速度。

> 4．限制索引的数目
> * 索引的数目不是越多越好。每个索引都需要占用磁盘空间，索引越多，需要的磁盘空间就越大。修改表时，对索引的重构和更新很麻烦。越多的索引，会使更新表变得很浪费时间。

> 5．尽量使用数据量少的索引
> * 如果索引的值很长，那么查询的速度会受到影响。例如，对一个CHAR(100)类型的字段进行全文检索需要的时间肯定要比对CHAR(10)类型的字段需要的时间要多。

> 6．尽量使用前缀来索引
> * 如果索引字段的值很长，最好使用值的前缀来索引。例如，TEXT和BLOG类型的字段，进行全文检索会很浪费时间。如果只检索字段的前面的若干个字符，这样可以提高检索速度。

> 7．删除不再使用或者很少使用的索引
> * 表中的数据被大量更新，或者数据的使用方式被改变后，原有的一些索引可能不再需要。数据库管理员应当定期找出这些索引，将它们删除，从而减少索引对更新操作的影响。

> 8 . 最左前缀匹配原则，非常重要的原则。
> * MySQL会一直向右匹配直到遇到范围查询(>、 3 and d = 4 如果建立(a,b,c,d)顺序的索引，d是用不到索引的，如果建立(a,b,d,c)的索引则都可以用到，a,b,d的顺序可以任意调整。

> 9 .=和in可以乱序。
> * 比如a = 1 and b = 2 and c = 3 建立(a,b,c)索引可以任意顺序，mysql的查询优化器会帮你优化成索引可以识别的形式

> 10 . 尽量选择区分度高的列作为索引。
> * 区分度的公式是count(distinct col)/count(*)，表示字段不重复的比例，比例越大我们扫描的记录数越少，唯一键的区分度是1，而一些状态、性别字段可能在大数据面前区分度就 是0，那可能有人会问，这个比例有什么经验值吗？使用场景不同，这个值也很难确定，一般需要join的字段我们都要求是0.1以上，即平均1条扫描10条 记录

> 11 .索引列不能参与计算，保持列“干净”。
> * 比如from_unixtime(create_time) = ’2014-05-29’就不能使用到索引，原因很简单，b+树中存的都是数据表中的字段值，但进行检索时，需要把所有元素都应用函数才能比较，显然成本 太大。所以语句应该写成create_time = unix_timestamp(’2014-05-29’);

> 12 .尽量的扩展索引，不要新建索引。 
> * 比如表中已经有a的索引，现在要加(a,b)的索引，那么只需要修改原来的索引即可


### BTREE 索引与 HASH 索引
MySQL 支持 BTREE 索引与 HASH 索引
BTREE 索引的使用场景通常较 HASH 索引更多，原因如下：

> Hash 索引仅仅能满足"=","IN"和"<=>"查询，不能加速范围查询。
> * 由于 Hash 索引比较的是进行 Hash 运算之后的 Hash 值，所以它只能用于等值的过滤，不能用于基于范围的过滤，因为经过相应的 Hash 算法处理之后的 Hash 值的大小关系，并不能保证和Hash运算前完全一样。

> Hash 索引无法被用来避免数据的排序操作。
> * 由于 Hash 索引中存放的是经过 Hash 计算之后的 Hash 值，而且Hash值的大小关系并不一定和 Hash 运算前的键值完全一样，所以数据库无法利用索引的数据来避免任何排序运算；

> Hash 索引不能利用部分索引键查询。
> * 对于组合索引，Hash 索引在计算 Hash 值的时候是组合索引键合并后再一起计算 Hash 值，而不是单独计算 Hash 值，所以通过组合索引的前面一个或几个索引键进行查询的时候，Hash 索引也无法被利用。

> Hash 索引在任何时候都不能避免表扫描。
> * 前面已经知道，Hash 索引是将索引键通过 Hash 运算之后，将 Hash运算结果的 Hash 值和所对应的行指针信息存放于一个 Hash 表中，由于不同索引键存在相同 Hash 值，所以即使取满足某个 Hash 键值的数据的记录条数，也无法从 Hash 索引中直接完成查询，还是要通过访问表中的实际数据进行相应的比较，并得到相应的结果。

> Hash 索引遇到大量Hash值相等的情况后性能并不一定就会比B-Tree索引高。
> * 对于选择性比较低的索引键，如果创建 Hash 索引，那么将会存在大量记录指针信息存于同一个 Hash 值相关联。这样要定位某一条记录时就会非常麻烦，会浪费多次表数据的访问，而造成整体性能低下。


## 数据库事物隔离级别
------------------
|隔离级别 |脏读 |不可重复读 |幻读 |
|--------|-----|----------|----|
|未提交读（read uncommitted） |可能 |可能 |可能 | 
|已提交读(read committed) |不可能 |可能 |可能 |
|可重复读(Repeatable read) |不可能 |不可能 |可能 | 
|可串行化(Serializable) |不可能 |不可能 |不可能 |

> 脏读：一个事务读取到另一事务未提交的更新新据。当一个事务正在访问数据，并且对数据进行了修改，而这种修改还没有
提交到数据库中，这时，另外一个事务也访问这个数据，然后使用了这个数据。因为这个数据是还没有提交的数据， 那么另
外一个事务读到的这个数据是脏数据，依据脏数据所做的操作也可能是不正确的。
    
> 不可重复读：在同一事务中，多次读取同一数据返回的结果有所不同。换句话说就是，后续读取可以读到另一事务已提交的
更新数据。相反，“可重复读”在同一事务中多次读取数据时，能够保证所读数据一样，也就是，后续读取不能读到另一事务
已提交的更新数据。
            
> 幻读：事务T1执行一次查询，然后事务T2新插入一行记录，这行记录恰好可以满足T1所使用的查询的条件。然后T1又使用相同
的查询再次对表进行检索，但是此时却看到了事务T2刚才插入的新行。这个新行就称为“幻像”，因为对T1来说这一行就像突然
出现的一样。

InnoDB 默认级别： 可重复读(Repeatable read)




