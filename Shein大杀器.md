# Shein大杀器

1. 集合类的hashmap和hashtable的区别

2. 进程和线程的区别

3. 简单工厂和抽象工厂的区别

4. jdk，jre，jvm的区别

   **JDK包含了JRE，JRE包含了JVM**

   <img src="https://raw.githubusercontent.com/jjames567/picture/main/image-20210817173845126.png" alt="image-20210817173845126" style="zoom:33%;margin-left:-5px" />

   JDK：JDK里面的JRE是JDK自带的为其开发工具提供运行环境的JRE，在JDK中有很多用Java编写的开发工具（bin文件夹下，如： javac.exe、jar.exe），这些工具的实现代码在JDK下面的lib目录下的tools.jar中。

   JRE：在Java平台下，所有的Java程序都需要在JRE下才能运行。只有JVM还不能进行class的执行，因为解释class的时候，JVM需要调用解释所需要的类库lib。JRE里面有两个文件夹bin和lib，这里可以认为bin就是JVM，lib就是JVM所需要的类库，而JVM和lib合起来就称为JRE。

   <img src="https://raw.githubusercontent.com/jjames567/picture/main/image-20210817174327420.png" alt="image-20210817174327420" style="zoom:33%; margin-left:-5px" />

5. 对mysql事务的理解，mysql那个存储引擎支持事务

6. 提高查找mysql查询速度，除了索引还有其他办法吗？

   **设计类：**

   1. 表设计一定要优化，冗余数据最少，少用连接查询。如果在实际应用中，使用了极其复杂的连接，子查询，则数据表的设计得要重新考虑了。

   2. 尽量用char而不是varchar，因为固定长度得string用起来更快.在当今硬盘容量越来越大的情况下，牺牲点存储空间而换得查询速度得提升是值得的。

   3. 通过简化权限来提高查询速度。如果一个查询之前要执行很多权限验证，则查询速度会慢下来，不妨试着在mysql中用root登录与用你新建的有权限控制的用户登录的速度，就可以看出来了，root登录，一下子就进入了，而普通用户登录，总会延迟一下。

   4. 表的优化。如果一个表已经用了一段时间，随着更新和删除操作的发生，数据将会变得支离破碎，这样同样会增加在该表中进行物理搜索所花费的时间。你要知道的是，在mysql底层设计中，数据库将被映射到具有某种文件结构的目录中，而表则映射到文件。所以磁盘碎片是很有可能发生的。庆幸的是，在mysql中，我们可以通过下面的语句进行修复：

      optimize table tablename（注意会锁表）

      或

      myisamchk -r tablename

   5. 使用索引，可以在需要提高查询速度的地方使用索引，简化索引，不要创建查询不使用的索引。可通过运行explain命令分析后决定.

   6. 使用默认值，在尽可能的地方使用列的默认值，只在与默认值不同的时候才插入数据。这样可以减少执行insert语句所花费的时间

   **实操类：**

     1、应尽量避免在 where 子句中使用!=或<>操作符，否则将引擎放弃使用索引而进行全表扫描。

     2、对查询进行优化，应尽量避免全表扫描，首先应考虑在 where 及 order by 涉及的列上建立索引。

     3、应尽量避免在 where 子句中对字段进行 null 值判断，否则将导致引擎放弃使用索引而进行全表扫描，如：

     select id from t where num is null

     可以在num上设置默认值0，确保表中num列没有null值，然后这样查询：

     select id from t where num=0

     4、尽量避免在 where 子句中使用 or 来连接条件，否则将导致引擎放弃使用索引而进行全表扫

     如：

     select id from t where num=10 or num=20

     可以这样查询：

     select id from t where num=10

     union all

     select id from t where num=20

     5、下面的查询也将导致全表扫描：(俗话说后通配 ，走索引；前通配，走全表。所以不能前置百分号)

     select id from t where name like ‘%c%’

     若要提高效率，可以考虑全文检索。

     6、not in 也要慎用，否则会导致全表扫描，php的代码，数组、参数等对变量的类型没有强制统一要求，因此in条件中既有字符串又有整数，也会导致索引失效

     7、应尽量避免在 where 子句中对字段进行表达式操作，这将导致引擎放弃使用索引而进行全表扫描。如：

     select id from t where num/2=100

     应改为:

     select id from t where num=100*2

     8、应尽量避免在where子句中对字段进行函数操作，这将导致引擎放弃使用索引而进行全表扫描。如：

     select id from t where substring(name,1,3)=’abc’–name以abc开头的id

     select id from t where datediff(day,createdate,’2005-11-30′)=0–’2005-11-30′生成的id

     应改为:

     select id from t where name like ‘abc%’

     select id from t where createdate>=’2005-11-30′ and createdate

     10、**最左匹配原则**：在使用索引字段作为条件时，如果该索引是复合索引，那么必须使用到该索引中的第一个字段作为条件时才能保证系统使用该索引，否则该索引将不会被使 用，并且应尽可能的让字段顺序与索引顺序相一致。

     11、很多时候用 exists 代替 in 是一个好的选择：

   select num from a where num in(select num from b)

   用下面的语句替换：

   select num from a where exists(select 1 from b where num=a.num)

   exist是当子查询返回true时给外层进行查询，in是将整个子查询结果返回给外层，外层再进行查询。

   口诀：子表大的时候用exist，子表小的时候用in。

   比如外表有1000行数据，子表有1000行数据，exist只需要执行1000次，而in最多需要遍历1000\*1000次。当外表有1000行数据，子表有10行数据时，exist还是执行1000次，但可能还不如in遍历1000\*次，因为in是在内存中遍历的，exist则要不断去查外表。

   也因为exist是外表作为驱动表，会先遍历外表，而in是子表作为驱动表。

   在联表查询时，一般是使用小表作为驱动表，再跟大表进行对比，这样每次遍历的次数就减少了。

   结论：**给被驱动表建立索引**。

     12、可以用

   ​			select id,name from table_name where id> 866612 limit 20

   ​			代替

   ​			select id,name from table_name limit 866613, 20

     13、索引并不是越多越好，索引固然可以提高相应的 select 的效率，但同时也降低了 insert 及 update 的效率，因为 insert 或 update 时有可能会重建索引，所以怎样建索引需要慎重考虑，视具体情况而定。一个表的索引数最好不要超过6个，若太多则应考虑一些不常使用到的列上建的索引是否有必要。

     14、尽量使用数字型字段，若只含数值信息的字段尽量不要设计为字符型，这会降低查询和连接的性能，并会增加存储开销。这是因为引擎在处理查询和连接时会 逐个比较字符串中每一个字符，而对于数字型而言只需要比较一次就够了。

     15、尽可能的使用 varchar/nvarchar 代替 char/nchar ，因为首先变长字段存储空间小，可以节省存储空间，其次对于查询来说，在一个相对较小的字段内搜索效率显然要高些。

     16、任何地方都不要使用 select * from t ，用具体的字段列表代替“*”，不要返回用不到的任何字段。

     17、避免频繁创建和删除临时表，以减少系统表资源的消耗。

     18、尽量避免大事务操作，提高系统并发能力。

     20、MySQL查询只使用一个索引，因此如果where子句中已经使用了索引的话，那么order by中的列是不会使用索引的。因此数据库默认排序可以符合要求的情况下不要使用排序操作；尽量不要包含多个列的排序，如果需要最好给这些列创建复合索引。

     21、检查有无子查询，子查询会每次都扫描子查询的整表，尽量少使用子查询

     22、检查关联每个表的每个条件都有无索引

   总结：

   1. 减少字段冗余、字段尽量设置默认值

   2. 避免使用不到索引的情况

      !=、in(可能)、not in(可能)、等号左边进行了运算、like左边带%、隐式替换(age = '21')、or条件其中有一个没索引(可以使用union代替)、is null、is not null

   3. char和varchar的选择（空间和查询效率）

   4. exist和in的选择（大小表）

   5. 能用整型用整型（效率比字符高）

   6. 驱动表与被驱动表的选择（小表驱动大表）

   7. 给被驱动表加索引

   8. 避免大事务

7. epoll和select的区别

   https://mp.weixin.qq.com/s?__biz=MzUxODAzNDg4NQ==&mid=2247489558&idx=1&sn=7a96604032d28b8843ca89cb8c129154&scene=21#wechat_redirect

8. tcp连接的过程

   三次握手

9. http1.0和http1.1的区别

10. 稳定[排序](https://www.nowcoder.com/jump/super-jump/word?word=排序)和不稳定[排序](https://www.nowcoder.com/jump/super-jump/word?word=排序)的区别，有哪些稳定和不稳定[排序](https://www.nowcoder.com/jump/super-jump/word?word=排序)

    稳定的排序：

    1. 冒泡排序
    2. 插入排序（向前的冒泡）
    3. 归并排序（两两合并）
    4. 基数排序（没有两两交换的过程）
    5. 计数排序（没有两两交换的过程）

    不稳定的排序：

    1. 选择排序（交换时把前面的放到后面了）
    2. 快速排序（左右指针相遇后与前面交换时破坏了位置）
    3. 希尔排序（特殊的插排，但在不同的步长下可能导致元素之间位置来回改变）
    4. 堆排序（堆顶和最后一个节点互换时，假如值相同则破坏了稳定性）

11. 快排的思路

12. [1亿的的数字中找到最大的10个](https://www.nowcoder.com/jump/super-jump/practice?questionId=23263)

    1. 快排：通过partition找到一个数的位置，判断下标是否是length-10，假如较大的话就去左边继续找，较小的话就去右边找，直到找到刚好下标的位置，那么该数的右边就是最大的10个数。
    2. 堆排：取前10个数构建最小堆，然后遍历后面的数，比较堆顶和遍历数的大小，比堆顶小的则跳过，比堆顶大的则替换堆顶，再调整形成最小堆，直到遍历整个数组，整个最小堆就是最大的10个数。

13. [项目](https://www.nowcoder.com/jump/super-jump/word?word=项目)的难点

14. MySQL插入一条语句如何写？插入1000条？

    1. insert into values 或 insert into select批量插入时，都满足事务的原子性与一致性，但要注意insert into select的加锁问题。
    2. replace into与insert into on duplicate key update都可以实现批量的插入更新，具体是更新还是插入取决与记录中的pk或uk数据在表中是否存在。如果存在，前者是先delete后insert，后者是update。
    3. insert ignore into会忽略很多数据上的冲突与约束，平时很少使用。

15. Redis的用途

    1. 热点数据的缓存
    2. 限时业务的运用
    3. 计数器相关问题（incrby原子性）
    4. 排行榜相关问题
    5. 分布式锁
    6. 队列
    7. 分页、模糊搜索（ZSET 中的 ZRANGEBYLEX）
    8. 点赞（SET 的 SISMEMBER）

16. SpringBoot相比于Spring的优点

    1. 通过 starter 将依赖自动添加到项目中
    2. 内嵌容器支持（Tomcat、Jetty、Undertow）
    3. Actuator 监控
    4. 约定大于配置（将存在的依赖项进行自动配置）

17. ReentrantLock

18. ConcurrentHashMap与HashMap

19. 一个学生类按照性别分组用lambda怎么写？遍历[链表](https://www.nowcoder.com/jump/super-jump/word?word=链表) 用 lambda怎么写？

    ```java
    Map<String, List<Student>> singleMap = studentList.stream().collect(Collectors.groupingBy(Student::getCode));
    ```

20. 分布式事务优缺点？

21. hashmap和treemap的应用场景？

    TreeMap 实现了 SortMap 接口，其能够根据键排序，默认是按键的升序排序，也可以指定排序的比较器，当用 Iterator 遍历 TreeMap 时得到的记录是排过序的，所以在插入和删除操作上会有些性能损耗，TreeMap 的键不能为空，其为非并发安全 Map，此外 TreeMap 基于红黑树实现。
    **key 不允许为 null，因为需要进行排序。**

    这里做一点补充：

    1. HashMap 允许 key 和 value 为 null；
    2. HashTable 都不允许 key 或者 value 为 null；（put 的时候需要计算哈希值，因此会抛空指针）
    3. ConcurrentHashMap 都不允许 key 或者 value 为 null；（put 要计算 key 的哈希值，value 的原因则为并发情况下不清楚 get() 返回的 null 是本身不存在还是其他线程 put 的 null 值）
    4. TreeMap 不允许 key 为 null；（会调用 compareTo() 方法导致空指针）

    一开始是 Map 本身就不支持 key 为 null 的，但后来因为功能需要则在 HashMap 专门给了槽位。

    总结：

    1. 只有 HashMap 支持 key 为 null；
    2. 线程安全的 Map 都不允许 value 为 null；
    3. 不允许 key 为 null 的 Map 要不就是需要计算 HashCode，要不就是需要调用 CompareTo 方法。

22. 多线程的优缺点？

    优点：

    1. 多个线程同时执行，提高线程执行效率
    2. 提高 CPU、内存利用率

    缺点：

    1. 占用内存空间
    2. CPU 线程调度开销大
    3. 程序复杂度上升
    4. 存在线程安全问题

23. 多线程的应用场景？

    1. 定时任务（定时爬虫、统计信息、上传文件）
    2. 异步任务（尽快响应，提高用户体验）
    3. 分布式计算

24. 线程越多越好吗？会有什么问题？

25. 怎么设置线程池最优线程数？最优的线程数？

    对于 CPU 密集型的场景下，线程数一般为 n + 1；

    对于 IO 密集型的场景下，线程数为 CPU 核心数 * (1 + IO 耗时/ CPU 耗时)；

    还有一种方式就是阻塞系数，但是不好记就不记录了。

26. 发生内存溢出的地方？

    1. 堆溢出：存在内存泄漏（可以让jvm在OOM时Dump出当前的内存堆快照）、堆分配得到的内存过小。
    2. 栈溢出：栈深度大于虚拟机所允许的最大深度 或者 扩展栈时无法申请到足够的空间。
    3. 方法区：大量的类被加载导致PermSize空间不足，可以通过-XX:PermSize 和 -XX:MaxPermSize限制方法区大小。
    4. 本机内存溢出：程序中直接或者间接的使用了nio，通过DirectByteBuffer请求内存，内存不足时也有可能产生OOM。明显特征是出现OOM时dump下来的文件很小，没有明显的异常信息。DirectMemory容量可以通过-XX：MaxDirectMemorySize指定，如果不指定跟Java堆最大值一样。

27. 怎么让本地内存溢出？具体怎么实现？

    调整 MaxDirectMemorySize 参数值，使用 nio 包下的 ByteBuffer.allocateDirect(`        `1024`        `*`        `1024`        `*`        `512`        `) 来申请本地内存。

    同时可以使用 cleaner.clean()方法来清除直接内存。

28. [redis](https://www.nowcoder.com/jump/super-jump/word?word=redis)分布式锁

29. 缓存雪崩和解决方案

30. 如何限流

31. JWT令牌和token

32. 网络协议安全

33. HTTPS加密[算法](https://www.nowcoder.com/jump/super-jump/word?word=算法)和原理

34. 面向过程和面向对象的区别，举例子

    面向过程就是分析出解决问题所需要的步骤，然后用函数把这些步骤一步一步实现，使用的时候一个一个依次调用就可以了；面向对象是把构成问题事物分解成各个对象，建立对象的目的不是为了完成一个步骤，而是为了描叙某个事物在整个解决问题的步骤中的行为。

    面向过程

    ​	优点：性能高，不需要对象实例化

    ​	缺点：不易维护，不易扩展，不易复用

    面向对象

    ​	优点：易维护、易复用、易扩展，由于面向对象有封装、继承、多态性的特性，可以设计出低耦合的系统，使系统 更加灵活、更加易于维护

    ​	缺点：性能比面向过程低

35. 数据库的一二三范式，举例子

    第一范式：列不可再分（地址可以分为省、市、详细地址等），确保不产生冗余数据。

    第二范式：属性完全依赖于主键。满足第一范式的基础上，使得每一行可以被唯一地区分，也就是要有对应的唯一id（订单id），但满足第二范式时可能会有数据冗余。

    第三范式：在满足第二范式的基础上，消除冗余字段。

    比如Student表（学号，姓名，年龄，性别，所在院校，院校地址，院校电话）这样一个表结构，就存在上述关系。 学号--> 所在院校 --> (院校地址，院校电话)。我们应该拆开来，如下：（学号，姓名，年龄，性别，所在院校）--（所在院校，院校地址，院校电话）

36. 讲一下MySQL的索引原理（B+数）

37. 你熟悉的集合有哪些？

38. ArrayList和LinkedList的区别

39. JVM的内存结构、回收[算法](https://www.nowcoder.com/jump/super-jump/word?word=算法)和回收器

40. 讲一下hashmap的get流程

41. 讲一下[链表](https://www.nowcoder.com/jump/super-jump/word?word=链表)和[红黑树](https://www.nowcoder.com/jump/super-jump/word?word=红黑树)两个结构

    红黑树：

    1. 对于任意节点，要么是红色，要么是黑色；
    2. 根节点是黑色的；
    3. 即不能有两个连续的红色节点；
    4. 任意节点到其下面各个空结点（后面称为nil节点，并约定其颜色为黑色）的路径上都包含相同数目的黑色节点（称为黑高）；
    5. 从根到叶子节点最长路径不会超过最短路径的2倍

    **红黑树是牺牲了严格的高度平衡的优越条件为代价，它只要求部分地达到平衡要求，降低了对旋转的要求，从而提高了性能。能够以O(log2 n)的时间复杂度进行搜索、插入、删除操作。**

    调整过程为先变色再旋转。

    AVL树：

    左右两个子树的高度差的绝对值不超过1，并且左右两个子树都是一棵平衡二叉树。

42. jdk1.7 和 jdk1.8 hashmap扩容机制

43. 线程池参数

    1. 核心线程数
    2. 最大线程数
    3. 超时释放时间
    4. 时间单位
    5. 线程工厂
    6. 等待队列
    7. 拒绝策略（抛异常、使用当前线程执行、丢弃、与最早任务竞争）

44. 线程池有哪些

    1. Executors.newSingleThreadExecutor()

       1核心，1最大，队列无限长

    2. Executors.newFixedThreadPool(5)

       指定核心，指定最大，队列无限长

    3. Executors.newCachedThreadPool()

       0核心，max最大，队列长度为0

45. innodb的底层原理

    <img src="https://raw.githubusercontent.com/jjames567/picture/main/640" alt="图片" style="zoom: 67%; margin-left: -5px;" />

    **内存：**

    1. **Buffer Pool 缓冲池：**

       MySQL 从磁盘读取数据，**组成以页为单位的链表**。

       为了提高效率，查询时会将结果放进缓冲池中，同样地，修改数据时，也会将修改页中的数据，再刷回磁盘。总体来说就是提高读写效率，减少与磁盘的io交互。

       **LRU-List：**

       Buffer Pool 里面的数据是以 LRU 链表的形式进行保存的。

       <img src="https://raw.githubusercontent.com/jjames567/picture/main/640" alt="图片" style="zoom: 50%; margin-left:-5px" />

       LRU链表被MidPoint分成了New Sublist和Old Sublist两部分，**是一种冷热分离的设计思想**。

       其中 **New Sublist大概占比5/8，Old Sublist占比3/8**。

       对于MySQL的LRU链表来说，通过MidPoint将链表分成两部分。

       从磁盘中新读出的数据会放在Old Sublist的头部。

       在 Old Sublist 中的数据，在 **innodb_old_blocks_time** 内假如再次访问时，是不会提升为热点数据的（New Sublist），同时 New SubList 也是经过优化的，如果你访问的是 New SubList 的前1/4的数据，它是不会被移动到 LRU 链表头部去的。

       **Free-List**：

       如果这个缓存页中没有存储任何数据，那么它对应的描述信息就会被维护进Free List中。这时当你想把从磁盘中读取出一个数据页放入缓存页中的话，就得先从Free List中找一个节点（Free List中的所有节点都会指向一个从未被使用过的缓存页），那接着就可以把你读取出来的这个数据页放入到该节点指向的缓存页中。

       相应的：当数据页中被放入数据之后。它对应的描述信息块会被从Free List中移出。

       而判断数据页是否存在于缓存中，是通过 hash table 来判断的，判断依据为：

       key = 表空间号+数据页号

       value = 缓存页地址

       最终会优先选择缓存页，避免磁盘随机io。

       **Flush-List：**

       为了提高写入效率，MySQL 并不会每次都将数据写入磁盘，而是会先写入缓冲区，而这个缓冲区就是 Flush-List。一旦你对内存中的缓冲页作出了修改，那该缓冲页对应的描述信息块就会添加进 Flush List。这样当Buffer Pool中的数据页不够用时，我们就可以优先将 Flush List中的脏数据页刷新进磁盘中。

       **刷盘时机：**

       1. 当Buffer Pool不够用时，根据LRU机制，MySQL会将Old SubList部分的缓存页移出LRU链表。如果被移除出去的缓存页的描述信息在Flush List中，MySQL就得将其刷新回磁盘。
       2. 后台线程异步将 Flush-List 中的脏页刷回磁盘。
       3. 脏数据页达到阈值时进行刷盘。
       4. redo log 不可用时强制刷盘。

       他们之间整体的架构是这样的：

       <img src="https://raw.githubusercontent.com/jjames567/picture/main/640" alt="图片" style="zoom:50%; margin-left:-5px" />

    2. **Change Buffer：**

       假如页不在内存中，按道理就需要去磁盘读取，但为了提高效率，会先把对应的页修改保存到 Change Buffer 中，当对应页 load 到内存中(Buffer Pool)时，就将之前的修改对页进行 merge，然后再 purge 到磁盘中。

    3. **自适应哈希索引：**

       MySQL 会自动评估使用自适应索引是否值得，如果观察到建立哈希索引可以提升速度，则建立。

    4. **Log Buffer：**

       从上面架构图可以看到，Log Buffer 里的 redo log，会被刷到磁盘里。

    **磁盘：**

    1. 表空间

       1. 系统表空间 or 共享表空间（ibdata1文件，会自动扩容）

       2. file per table 表空间（每个数据表对应一个单独表空间文件，以.ibd为后缀）

          存放内容：表数据、索引、insert buffer bitmap。

          而 undo log、insert buffer 索引页、double write buffer 等依然放在系统表空间。

          优点：各个表空间互不干扰，提高容错率，备份时不会影响其他表使用。

          缺点：会增加 fsync 次数。

    2. Doublewrite Buffer

       为了保证数据页的可靠性，MySQL在将数据刷到磁盘之前，会先数据写到 Doublewrite Buffer，写完后，才开始写到磁盘。这是为了防止将内存总的数据页刷到磁盘时突然断电，这时页只刷到了一半，即使有 redo log，但磁盘中的页数据是不完整的，因此需要用到 Doublewrite Buffer 来修复磁盘里的数据。

    3. 

46. 最左匹配原则

47. 缓存与数据库数据不一致

48. 重载和重写区别

49. 面向对象三大特征

50. 接口和抽象类区别

51. 数据库隔离级别

52. 间隙锁

53. mysql的默认隔离级别能否解决幻读

54. 手写sql语句。表A中有字段id和name,查询重复的name

55. spring的Aop

56. 线程池

57. session不一致情况

58. 集合类collection下有哪些集合

59. 线程安全的集合有哪些

60. CopyOnWriteArrayList原理

61. 如何对集合[排序](https://www.nowcoder.com/jump/super-jump/word?word=排序)

62. 说说实习干了啥，遇到问题怎么解决，重构了[项目](https://www.nowcoder.com/jump/super-jump/word?word=项目)有什么优点？

63. 为什么想应聘shein

64. 多线程的优缺点

65. 说说垃圾收集器，cms和g1的初始标记是否都会STW

66. cms和g1的区别，各自的应用场景

67. OSI七层模型和TCP/IP模型； 

68. socket是哪一层（传输层），websocket是哪一层（应用层）； 

69. 说说[redis](https://www.nowcoder.com/jump/super-jump/word?word=redis)有哪些数据结构；

70. 如何用[redis](https://www.nowcoder.com/jump/super-jump/word?word=redis)的数据结构存储用户的登录状态以及流程判断（string）；

71. 如何判断一个数是否为2的幂次；

72. 什么是事务？事务的隔离界别？mysql默认哪一种？

73. SQL调优

74. Java集合了解哪些？ArrayList和LinkedList的区别？如何保证ArrayList线程安全？如何对集合元素进行[排序](https://www.nowcoder.com/jump/super-jump/word?word=排序)？

75. 如何创建线程？如何判断线程是否正在运行？线程池说一下

76. [redis](https://www.nowcoder.com/jump/super-jump/word?word=redis)的数据结构？有哪些命令？

77. Redis在[项目](https://www.nowcoder.com/jump/super-jump/word?word=项目)里怎么用的？[项目](https://www.nowcoder.com/jump/super-jump/word?word=项目)里超卖场景怎么解决？（用[redis](https://www.nowcoder.com/jump/super-jump/word?word=redis)分布式锁）

78. JVM的内存区域说一下，分别用来存什么 

79. 做过什么[项目](https://www.nowcoder.com/jump/super-jump/word?word=项目)

80. 如何解决Mysql数据库和ES数据不一致问题，如果数据库写失败了怎么办，ES写失败了怎么办

81. 树的遍历方式有哪些

82. [动态规划](https://www.nowcoder.com/jump/super-jump/word?word=动态规划)定义，核心，思想是什么

83. [排序](https://www.nowcoder.com/jump/super-jump/word?word=排序)[算法](https://www.nowcoder.com/jump/super-jump/word?word=算法)了解哪些？快排说一下 

84. Java中CMS说一下 

85. HashMap 和 HashTable区别 

86. HashMap的key可以为null么 

87. HashMap 扩容，同样的key会覆盖么 

88. 了解设计模式么？ 

89. http和https的区别？讲一下https,http2.0 

90. 数据库连接池的参数 

91. 事物的特性 

92. Http，Tcp这些：包括https过程，都是什么层的协议，浏览器输入url后到显示页面的流程，tcp3次握手4次挥手及原因，tcp和udp的区别 

93. 泛型，java的接口和抽象类的区别， 

94. jvm的划分，创建一个数组，在jvm的流程，调用这个数组，在jvm的流程； 

95. 垃圾回收相关[算法](https://www.nowcoder.com/jump/super-jump/word?word=算法)，新生代和老年代的[算法](https://www.nowcoder.com/jump/super-jump/word?word=算法)及空间划分 

96. 新生代如何晋升到老年代 

97. synchronized和 ReentrantLock的原理 

98. JMM是什么

99. volatile的原理 

100. CAS乐观锁 

101. ReentrantLock公平锁和非公平锁的流程（这个公平锁的流程我只说了一半，只看到过一次，没记住） 

102. 锁的优化过程 

103. 线程池的参数和流程 

104. 索引的类型（聚簇和非聚簇） 

105. 为什么用b+树 

106. 如何优化索引 

107. 怎么查询是否走索引，explain相关的参数，要看哪些 

108. [算法](https://www.nowcoder.com/jump/super-jump/word?word=算法)就让说了下快排的步骤

109. java多线程，循环打印abc

110. java内存模型

111. 抽象类接口区别

112. 数据库哪些索引，作用

113. 介绍b+树

114. epoll模型，没回答好

115. mysql你了解多少

116. 你的[项目](https://www.nowcoder.com/jump/super-jump/word?word=项目)并发量怎么算，你了解qps和tps吗

117. http和https的区别

118. post和get的区别

119. tcp三次握手

120. http协议代表内容类型的字段

121. 长链接字段

122. 进程线程区别

123. 查找grep

124. 进程/线程 
     进程间通信方式 
     命名管道在linux中具体怎么实现的？

125. 你就随便说下linux中网络编程的5个io模型是怎么实现的吧。

126. java线程池 

127. Java有哪些数据结构 

128. AQS原理，Java有哪些锁 

129. 数据库连接池方面（只说了用了，具体不会） 

130. 数据库索引的一些原则 

131. mysql join执行的过程（不会） 

132. [redis](https://www.nowcoder.com/jump/super-jump/word?word=redis)与数据库的缓存一致问题 

133. SpringAOP用到什么设计模式 

134. JVM包括什么 

135. 运行时数据区包括什么 

136. 什么时候入栈，出栈 

137. Sychronized和 ReentrantLock区别 

138. Sychronized底层用什么实现的 

139. SpringBean的作用域有什么 

140. 单例模式可以保证bean安全吗 

141. 如何实现Bean的安全 

142. 为什么要三次握手四次挥手 

143. 如果出现大量的TIME-WAIT连接是为什么 

144. HashMap讲一下 

145. 为什么使用[红黑树](https://www.nowcoder.com/jump/super-jump/word?word=红黑树)，不使用B+树

146. 数据库使用过什么引擎 

147. InnoDB使用什么索引

148. B树索引和B+树索引有什么区别 

149. ConcurrentHashmap讲一下 

150. Hashmap1.7和1.8的区别 

151. mysql的两种索引说一说？ 

152. mysql的两种索引分别用于什么场景？ 

153. arraylist ，linkedlist

154. map用过吗，hashmap说一下

155. hashmap扩容为什么要2的整数次幂

156. [链表](https://www.nowcoder.com/jump/super-jump/word?word=链表)长度到8变成[红黑树](https://www.nowcoder.com/jump/super-jump/word?word=红黑树)，为什么是8

157. comcurrenthashmap说一下

158. 为什么1.8改用cas+synchronized

159. 刚刚说到[红黑树](https://www.nowcoder.com/jump/super-jump/word?word=红黑树)，[红黑树](https://www.nowcoder.com/jump/super-jump/word?word=红黑树)说一说

160. 分析[红黑树](https://www.nowcoder.com/jump/super-jump/word?word=红黑树)的[算法](https://www.nowcoder.com/jump/super-jump/word?word=算法)复杂度(没答上来，忘了，小哥哥建议我之后去了解)

161. 谈谈其他树，分别的应用(说了b+树，查找树，平衡树)

162. b+树为什么适合做innodb底层数据结构

163. 手撕:[二叉树](https://www.nowcoder.com/jump/super-jump/word?word=二叉树)层序遍历

164. 说说其他数据结构(栈，队列，图)

165. Redis哪些数据结构，底层分别怎么实现

166. 手撕:一个单[链表](https://www.nowcoder.com/jump/super-jump/word?word=链表)，求最大的前k个数

167. 手撕:最大子数组。一个数组[1，3，4，8，9]，另一个数组[2，3，4，8，10]，他们最长子数组是[3，4，8]。口述思路

168. AOP 原理和[源码](https://www.nowcoder.com/jump/super-jump/word?word=源码)

169. [红黑树](https://www.nowcoder.com/jump/super-jump/word?word=红黑树)

170. 缓存机制

171. 学习Java的方式、看的书

172. 8种基本类型

173. 遍历树的方式 介绍下前中后序遍历

174. mysql的InnoDB引擎 索引底层数据结构 MyISAM呢 

175. InnoDB 为什么用B+树 而不用B树 

176. 从树的方面来解释下mysql索引的最左匹配原则吗

177. TCP 三次握手 四次挥手 为什么是三次

178. 怎么遍历hashmap 还有其他方法吗

179. 怎么遍历list呢 能一边遍历 一边删除吗

180. hashMap jdk1.8 的改进

181. 怎么实现一个线程

182. 线程池常见的参数

183. volatile关键字有什么作用 指令重排是干啥的

184. Spring 事务是怎么控制的？ 注解的实现原理知道吗 我说不知道 面试官说提示一下是AOP 我还是不会。。

185. 事务的隔离级别 分别能解决什么问题 默认是哪个隔离级别 Spring中的隔离级别 如果Spring中设置的隔离级别和数据库中的不一样 生效的会是哪个

186. Mybatis 中 $ # 哪个能防止 sql 注入 为什么能防止

     #{} 有预编译，${} 只是替换字符，${} 不能防止 sql 注入，一般用于传递表名，数据库名等。

187. $ # 除了在防止sql注入 其他方面还有什么区别吗

188. MyBais 一级 二级缓存

     1）一级缓存 Mybatis的一级缓存是指SQLSession，一级缓存的作用域是SQlSession, Mabits默认开启一级缓存。 在同一个SqlSession中，执行相同的SQL查询时；第一次会去查询数据库，并写在缓存中，第二次会直接从缓存中取。 当执行SQL时候两次查询中间发生了增删改的操作，则SQLSession的缓存会被清空。 每次查询会先去缓存中找，如果找不到，再去数据库查询，然后把结果写到缓存中。 Mybatis的内部缓存使用一个HashMap，key为hashcode+statementId+sql语句。Value为查询出来的结果集映射成的java对象。 SqlSession执行insert、update、delete等操作commit后会清空该SQLSession缓存。

     注意！！当集成到 Spring 时，若没有开启事务的话每次调用都会新开启一个 Session ，之前的 Session 会被 close 掉，因此想要开启缓存：1. 开启事务。2. 使用二级缓存。

     2）二级缓存 二级缓存是mapper级别的，Mybatis默认是没有开启二级缓存的。 第一次调用mapper下的SQL去查询用户的信息，查询到的信息会存放代该mapper对应的二级缓存区域。 第二次调用namespace下的mapper映射文件中，相同的sql去查询用户信息，会去对应的二级缓存内取结果。

189. Redis操作list的操作

190. 什么是面向对象编程 

191. Java三大特性 

192. 事务有哪些隔离级别，Mysql默认的是什么隔离级别 

193. Java常用的集合类有哪些 

194. 你使用过哪些设计模式，挑几个重点讲一讲实现 

195. 双重校验锁如何实现？（这里我回答漏了voliate关键字） 

196. Java中的抽象类和接口有什么区别？ 

197. 抽象类中可以定义变量，编写实现吗？ 

198. JVM有了解过吗？它管理的内存区域分为哪些？ 

199. 对象创建的过程？

200. 单点登陆如何实现的？

     问题一：因为不同域名下会存在跨域问题，假如用cookie存的话，在A站点登录了以后，发送请求到B站点时是不会带上cookie的，因此是不知道用户是否已登录的。

     解决办法：使用同一个认证服务器，当站点发现用户未登录时，会发送请求到认证服务器，当认证服务器也没有记录时，说明真的没登录，会跳转到登录页面。用户登录后，会从站点转发到认证服务器，认证服务器会生成sessionid并存到redis，最后重定向回站点，之后每次访问站点都会带上token，站点就会查redis判断是否登录。而在不同站点下，站点发现未登录时也会转发到认证服务器，此时因为之前跟认证服务器有session交互，因此认证服务器通过后会带上sessionid重定向回站点，之后这个站点也有token了。同时这个方法也解决了重复授权的问题。

201. [redis](https://www.nowcoder.com/jump/super-jump/word?word=redis)中有哪些存储类型？底层数据结构分别是什么？

202. springbean生命周期

203. springboot自动装配

204. 事务隔离级别

205. hashmap,concurrenthashmap（如何线程安全，resize，1.7，1.8的区别）

206. [redis](https://www.nowcoder.com/jump/super-jump/word?word=redis)数据结构,分布式锁

207. hashmap的结构,扩容阈值

208. 两个线程对hashmap同时扩容的后果

209. gc回收[算法](https://www.nowcoder.com/jump/super-jump/word?word=算法)，垃圾回收器g1,cms解释

210. springmvc的流程

211. 对微服务的了解，解释zoo[keep](https://www.nowcoder.com/jump/super-jump/word?word=keep)er

212. 手写代码，在10亿的商品日志中找出出现最多的一百个商品

     1. 将日志中的商品根据商品id利用哈希运算写入到多个文件中
     2. 遍历每个文件，利用map计算出每个商品的出现频率
     3. 构建数量为100的小顶堆，遍历每个每个文件中的map，若比堆顶大，则替换堆顶，因为堆顶表示是这个堆里最小的一个数，因此替换后再进行调整，直到遍历完成，堆顶为100个数里的最小值，而整个堆就是最大的100个数。

213. url的组成，一个网址如何找到对应的机器，对应的接口和服务

214. json的底层实现

215. jdk 1.8新特性，jdk最新版本是几

216. spring常用注解哪些

217. count(1)、count(column)、count(*) 的区别

     结果上来说：count(column) 会过滤空值。

     性能上来说：

     1. 有主键的情况下，count(column) 最优
     2. 没有主键的情况下，假如只有一列，count(*) 最优。假如有多列，则 count(1) 最优。
     
     count(1)，其实就是计算一共有多少符合条件的行。
     1并不是表示第一个字段，而是表示一个固定值。
     其实就可以想成表中有这么一个字段，这个字段就是固定值1，count(1)，就是计算一共有多少个1.
     
     count(*)，执行时会把星号翻译成字段的具体名字，效果也是一样的，不过多了一个翻译的动作，比固定值的方式效率稍微低一些。
