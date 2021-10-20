# Shein大杀器

1. 集合类的hashmap和hashtable的区别

   HashTable父类是Dictionary类，HashMap父类是AbtractMap类，但他们都实现了Map接口。

   HashTable是同步方法。

   HashTable的key和value都不允许为null。

   HashTable直接使用对象的hashCode，HashMap重新计算hash值。（高16位右移到低16位，和原来的hash值进行异或运算，得到新hash值）。

   HashTable数组初始大小为11，扩容为2倍+1，HashMap初始大小为16，扩容为2倍。

   HashTable在执行构造方法时数组会初始化，而HashMap是在第一次put的时候才初始化。

2. 进程和线程的区别

3. 快速失败和安全失败

   安全失败机制的集合容器，在遍历时不是直接在集合内容上访问的，而是先复制原有集合内容，在拷贝的集合上进行遍历。

4. 简单工厂和抽象工厂的区别

   简单工厂模式通过判断type来创建对应的对象，假如以后需要增加或者删除类，则需要修改工厂的代码，这样违反了开闭原则。

   工厂方法模式则是创建多个工厂，分别来创建对应的类对象，符合开闭原则，但是会增加系统的复杂度，类的数量会增多。

   抽象工厂模式里，一个工厂可以生产多种产品，跟简单工厂相比功能更强，但是他们都违反开闭原则，在增加产品时需要修改原有代码。

5. jdk，jre，jvm的区别

   **JDK包含了JRE，JRE包含了JVM**

   <img src="https://raw.githubusercontent.com/jjames567/picture/main/image-20210817173845126.png" alt="image-20210817173845126" style="zoom:33%;margin-left:-5px" />

   JDK：JDK里面的JRE是JDK自带的为其开发工具提供运行环境的JRE，在JDK中有很多用Java编写的开发工具（bin文件夹下，如： javac.exe、jar.exe），这些工具的实现代码在JDK下面的lib目录下的tools.jar中。

   JRE：在Java平台下，所有的Java程序都需要在JRE下才能运行。只有JVM还不能进行class的执行，因为解释class的时候，JVM需要调用解释所需要的类库lib。JRE里面有两个文件夹bin和lib，这里可以认为bin就是JVM，lib就是JVM所需要的类库，而JVM和lib合起来就称为JRE。

   <img src="https://raw.githubusercontent.com/jjames567/picture/main/image-20210817174327420.png" alt="image-20210817174327420" style="zoom:33%; margin-left:-5px" />

6. 对mysql事务的理解，mysql那个存储引擎支持事务

7. 提高查找mysql查询速度，除了索引还有其他办法吗？

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

8. epoll和select的区别

   https://mp.weixin.qq.com/s?__biz=MzUxODAzNDg4NQ==&mid=2247489558&idx=1&sn=7a96604032d28b8843ca89cb8c129154&scene=21#wechat_redirect

9. tcp连接的过程

   三次握手

10. http1.0和http1.1的区别

11. 稳定[排序](https://www.nowcoder.com/jump/super-jump/word?word=排序)和不稳定[排序](https://www.nowcoder.com/jump/super-jump/word?word=排序)的区别，有哪些稳定和不稳定[排序](https://www.nowcoder.com/jump/super-jump/word?word=排序)

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

12. 快排的思路

13. [1亿的的数字中找到最大的10个](https://www.nowcoder.com/jump/super-jump/practice?questionId=23263)

    1. 快排：通过partition找到一个数的位置，判断下标是否是length-10，假如较大的话就去左边继续找，较小的话就去右边找，直到找到刚好下标的位置，那么该数的右边就是最大的10个数。
    2. 堆排：取前10个数构建最小堆，然后遍历后面的数，比较堆顶和遍历数的大小，比堆顶小的则跳过，比堆顶大的则替换堆顶，再调整形成最小堆，直到遍历整个数组，整个最小堆就是最大的10个数。

14. [项目](https://www.nowcoder.com/jump/super-jump/word?word=项目)的难点

15. MySQL插入一条语句如何写？插入1000条？

    1. insert into values 或 insert into select批量插入时，都满足事务的原子性与一致性，但要注意insert into select的加锁问题。
    2. replace into与insert into on duplicate key update都可以实现批量的插入更新，具体是更新还是插入取决与记录中的pk或uk数据在表中是否存在。如果存在，前者是先delete后insert，后者是update。
    3. insert ignore into会忽略很多数据上的冲突与约束，平时很少使用。

16. Redis的用途

    1. 热点数据的缓存
    2. 限时业务的运用
    3. 计数器相关问题（incrby原子性）
    4. 排行榜相关问题
    5. 分布式锁
    6. 队列
    7. 分页、模糊搜索（ZSET 中的 ZRANGEBYLEX）
    8. 点赞（SET 的 SISMEMBER）

17. SpringBoot相比于Spring的优点

    1. 通过 starter 将依赖自动添加到项目中
    2. 内嵌容器支持（Tomcat、Jetty、Undertow）
    3. Actuator 监控
    4. 约定大于配置（将存在的依赖项进行自动配置）

18. ReentrantLock

19. ConcurrentHashMap与HashMap

20. 一个学生类按照性别分组用lambda怎么写？遍历[链表](https://www.nowcoder.com/jump/super-jump/word?word=链表) 用 lambda怎么写？

    ```java
    Map<String, List<Student>> singleMap = studentList.stream().collect(Collectors.groupingBy(Student::getCode));
    ```

21. 分布式事务优缺点？

22. hashmap和treemap的应用场景？

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

23. 多线程的优缺点？

    优点：

    1. 多个线程同时执行，提高线程执行效率
    2. 提高 CPU、内存利用率

    缺点：

    1. 占用内存空间
    2. CPU 线程调度开销大
    3. 程序复杂度上升
    4. 存在线程安全问题

24. 多线程的应用场景？

    1. 定时任务（定时爬虫、统计信息、上传文件）
    2. 异步任务（尽快响应，提高用户体验）
    3. 分布式计算

25. 线程越多越好吗？会有什么问题？

26. 怎么设置线程池最优线程数？最优的线程数？

    对于 CPU 密集型的场景下，线程数一般为 n + 1；（当线程因为偶尔的内存页失效或其他原因导致阻塞时， 这个额外的线程可以顶上， 从而保证CPU的利用率 ）

    对于 IO 密集型的场景下，线程数为 CPU 核心数 * (1 + IO 耗时/ CPU 耗时)；

    还有一种方式就是阻塞系数，但是不好记就不记录了。

27. 发生内存溢出的地方？

    1. 堆溢出：存在内存泄漏（可以让jvm在OOM时Dump出当前的内存堆快照）、堆分配得到的内存过小。
    2. 栈溢出：栈深度大于虚拟机所允许的最大深度 或者 扩展栈时无法申请到足够的空间。
    3. 方法区：大量的类被加载导致PermSize空间不足，可以通过-XX:PermSize 和 -XX:MaxPermSize限制方法区大小。
    4. 本机内存溢出：程序中直接或者间接的使用了nio，通过DirectByteBuffer请求内存，内存不足时也有可能产生OOM。明显特征是出现OOM时dump下来的文件很小，没有明显的异常信息。DirectMemory容量可以通过-XX：MaxDirectMemorySize指定，如果不指定跟Java堆最大值一样。

28. 怎么让本地内存溢出？具体怎么实现？

    调整 MaxDirectMemorySize 参数值，使用 nio 包下的 ByteBuffer.allocateDirect(`        `1024`        `*`        `1024`        `*`        `512`        `) 来申请本地内存。

    同时可以使用 cleaner.clean()方法来清除直接内存。

29. [redis](https://www.nowcoder.com/jump/super-jump/word?word=redis)分布式锁

30. 缓存雪崩和解决方案

31. 如何限流

32. JWT令牌和token

33. 网络协议安全

34. HTTPS加密[算法](https://www.nowcoder.com/jump/super-jump/word?word=算法)和原理

35. 面向过程和面向对象的区别，举例子

    面向过程就是分析出解决问题所需要的步骤，然后用函数把这些步骤一步一步实现，使用的时候一个一个依次调用就可以了；面向对象是把构成问题事物分解成各个对象，建立对象的目的不是为了完成一个步骤，而是为了描叙某个事物在整个解决问题的步骤中的行为。

    面向过程

    ​	优点：性能高，不需要对象实例化

    ​	缺点：不易维护，不易扩展，不易复用

    面向对象

    ​	优点：易维护、易复用、易扩展，由于面向对象有封装、继承、多态性的特性，可以设计出低耦合的系统，使系统 更加灵活、更加易于维护

    ​	缺点：性能比面向过程低

36. 数据库的一二三范式，举例子

    第一范式：列不可再分（地址可以分为省、市、详细地址等），确保不产生冗余数据。

    第二范式：属性完全依赖于主键。满足第一范式的基础上，使得每一行可以被唯一地区分，也就是要有对应的唯一id（订单id），但满足第二范式时可能会有数据冗余。

    第三范式：在满足第二范式的基础上，消除冗余字段。

    比如Student表（学号，姓名，年龄，性别，所在院校，院校地址，院校电话）这样一个表结构，就存在上述关系。 学号--> 所在院校 --> (院校地址，院校电话)。我们应该拆开来，如下：（学号，姓名，年龄，性别，所在院校）--（所在院校，院校地址，院校电话）

37. 讲一下MySQL的索引原理（B+数）

38. 你熟悉的集合有哪些？

39. ArrayList和LinkedList的区别

    ArrayList是实现了基于动态数组的数据结构，LinkedList基于双向链表的数据结构。 

    对于随机访问get和set，ArrayList觉得优于LinkedList，因为LinkedList要移动指针。 

    对于新增和删除操作add和remove，LinedList比较占优势，因为ArrayList要移动数据。

    时间复杂度：通过下标来查数据时，数组是O(1)，链表是O(n)。

    空间：ArrayList的空间浪费主要体现在在list列表的结尾预留一定的容量空间，而LinkedList的空间花费则体现在它的每一个元素都需要消耗相当的空间

    场景：在利用Collections.reverse()或者要对list进行大量的插入和删除操作时，LinkedList性能更好。

40. JVM的内存结构、回收[算法](https://www.nowcoder.com/jump/super-jump/word?word=算法)和回收器

41. 讲一下hashmap的get流程

    通过key的Hash找到唯一的桶位。寻找方法和put过程中是相同的，(capacity-1)&hash
    找到具体桶位，现在有两种情况

    如果首元素的key和目标key相同，则返回首元素。
    如果首元素的key不相同。判断有没有第二个元素
        如果没有，在该桶位处就没必要再找，直接返回null。
        如果有第二元素。判断首元素类型
            如果为链表，采用do-while循环遍历桶中链表。
            如果为红黑树，用红黑树的遍历方式getTreeNode()；
                首先找到根节点Root，从根节点向下找。
                然后，根据查找key的hash值和当前node的hash值比较
                    如果大于，就向右边找。
                    如果小于，就向左找。
                    如果相同并比较equals相同，就返回当前节点
                    如果左孩子没有了，就向右孩子找。
                    如果右孩子没有了，就向左孩子找。
                    如果没有找到就进入下一轮递归寻找。

42. 讲一下[链表](https://www.nowcoder.com/jump/super-jump/word?word=链表)和[红黑树](https://www.nowcoder.com/jump/super-jump/word?word=红黑树)两个结构

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

43. jdk1.7 和 jdk1.8 hashmap扩容机制

    https://blog.csdn.net/u012501054/article/details/103710171/

44. 线程池参数

    1. 核心线程数
    2. 最大线程数
    3. 超时释放时间
    4. 时间单位
    5. 线程工厂
    6. 等待队列
    7. 拒绝策略（抛异常、使用当前线程执行、丢弃、与最早任务竞争）

45. 线程池有哪些

    1. Executors.newSingleThreadExecutor()

       1核心，1最大，队列无限长

    2. Executors.newFixedThreadPool(5)

       指定核心，指定最大，队列无限长

    3. Executors.newCachedThreadPool()

       0核心，max最大，队列长度为0

46. innodb的底层原理

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

47. 最左匹配原则

48. 缓存与数据库数据不一致

49. 重载和重写区别

50. 面向对象三大特征

51. 接口和抽象类区别

    1. 抽象类必须用public、protect修饰，接口必须用public修饰
    2. 接口变量只能是public static final且必须给出初始值，方法必须是public

52. 数据库隔离级别

53. 间隙锁

54. mysql的默认隔离级别能否解决幻读

55. 手写sql语句。表A中有字段id和name,查询重复的name

56. spring的Aop

57. 线程池

58. session不一致情况

59. 集合类collection下有哪些集合

60. 线程安全的集合有哪些

61. CopyOnWriteArrayList原理

62. 如何对集合[排序](https://www.nowcoder.com/jump/super-jump/word?word=排序)

63. 说说实习干了啥，遇到问题怎么解决，重构了[项目](https://www.nowcoder.com/jump/super-jump/word?word=项目)有什么优点？

64. 为什么想应聘shein

65. 多线程的优缺点

66. 说说垃圾收集器，cms和g1的初始标记是否都会STW

67. cms和g1的区别，各自的应用场景

    过程不同：

    - cms初始标记->并发标记->重新标记->并发清理
    - g1初始标记->并发标记->最终标记->筛选回收

    清理方式不同：

    -  cms标记清理法
    -  g1年轻代复制算法，老年代标记整理算法

    处理错标的方法不同：

    - cms是增量更新的方式
    - g1是原始快照的方式

68. OSI七层模型和TCP/IP模型； 

69. socket是哪一层（传输层），websocket是哪一层（应用层）； 

70. 说说[redis](https://www.nowcoder.com/jump/super-jump/word?word=redis)有哪些数据结构；

71. 如何用[redis](https://www.nowcoder.com/jump/super-jump/word?word=redis)的数据结构存储用户的登录状态以及流程判断（string）；

72. 如何判断一个数是否为2的幂次；

    n & (n-1)

73. 什么是事务？事务的隔离界别？mysql默认哪一种？

74. SQL调优

75. Java集合了解哪些？ArrayList和LinkedList的区别？如何保证ArrayList线程安全？如何对集合元素进行[排序](https://www.nowcoder.com/jump/super-jump/word?word=排序)？

    线程安全：

    1. 自行加锁
    2. 使用Collections.synchronizedList(new ArrayList<>()); 里面有mutex对象，默认为this。
    3. 使用CopyOnWriteArrayList，利用写时复制技术思想，在添加元素时进行加锁，创建len+1的新数组，拷贝旧数组，再往新数组进行插入，最后赋值给原数组。get操作是不需要加锁的。

76. 如何创建线程？如何判断线程是否正在运行？线程池说一下

77. [redis](https://www.nowcoder.com/jump/super-jump/word?word=redis)的数据结构？有哪些命令？

78. Redis在[项目](https://www.nowcoder.com/jump/super-jump/word?word=项目)里怎么用的？[项目](https://www.nowcoder.com/jump/super-jump/word?word=项目)里超卖场景怎么解决？（用[redis](https://www.nowcoder.com/jump/super-jump/word?word=redis)分布式锁）

79. JVM的内存区域说一下，分别用来存什么 

80. 做过什么[项目](https://www.nowcoder.com/jump/super-jump/word?word=项目)

81. 如何解决Mysql数据库和ES数据不一致问题，如果数据库写失败了怎么办，ES写失败了怎么办

82. 树的遍历方式有哪些

83. [动态规划](https://www.nowcoder.com/jump/super-jump/word?word=动态规划)定义，核心，思想是什么

84. [排序](https://www.nowcoder.com/jump/super-jump/word?word=排序)[算法](https://www.nowcoder.com/jump/super-jump/word?word=算法)了解哪些？快排说一下 

85. Java中CMS说一下 

86. HashMap的key可以为null么 

87. HashMap 扩容，同样的key会覆盖么 

88. 了解设计模式么？ 

89. http和https的区别？讲一下https,http2.0 

90. 数据库连接池的参数 

91. 事物的特性 

92. Http，Tcp这些：包括https过程，都是什么层的协议，浏览器输入url后到显示页面的流程，tcp3次握手4次挥手及原因，tcp和udp的区别 

93. 泛型，java的接口和抽象类的区别， 

94. jvm的划分，创建一个数组，在jvm的流程，调用这个数组，在jvm的流程； 

    https://www.bilibili.com/read/cv5148400

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

     表面上是GET把参数包含在URL中，POST通过request body传递参数

     但实际上他们都是HTTP协议中的方法，而HTTP的底层是TCP/IP，所以GET和POST都是TCP连接，他们能做的事情都是一样的，在技术上，我要给GET加body，给POST带url参数都是可以的，只是这不符合HTTP的行为准则。

     在get请求加上body，可能服务器会直接忽略导致数据接收不到。

     那他们的真正区别是：GET产生一个TCP数据包；POST产生两个TCP数据包。

     对于POST，浏览器会先发送header，服务器响应100 continue，浏览器再发送data，服务器响应200 ok。但是火狐浏览器的POST请求就只会发送一次。

     有团队说建议GET代替POST，但是不可以，因为他们都有自己的语义，并且在网络好的情况下，两者时间差基本可以无视，网络差的情况下，两次TCP在数据的完整性上有很大优势。

119. tcp三次握手

120. http协议代表内容类型的字段

121. 长链接字段

122. 进程线程区别

123. 查找grep

124. 进程/线程 
     进程间通信方式 
     命名管道在linux中具体怎么实现的？

125. 你就随便说下linux中网络编程的5个io模型是怎么实现的吧。

     https://zhuanlan.zhihu.com/p/115912936

     https://baijiahao.baidu.com/s?id=1690471158702755453&wfr=spider&for=pc

126. java线程池 

127. Java有哪些数据结构 

128. AQS原理，Java有哪些锁 

129. 数据库连接池方面（只说了用了，具体不会） 

130. 数据库索引的一些原则 

131. mysql join执行的过程（不会） 

132. [redis](https://www.nowcoder.com/jump/super-jump/word?word=redis)与数据库的缓存一致问题 

133. SpringAOP用到什么设计模式 

     代理模式、适配器模式、单例模式、责任链模式、装饰器模式

134. JVM包括什么 

135. 运行时数据区包括什么 

136. 什么时候入栈，出栈 

137. Sychronized和 ReentrantLock区别 

138. Sychronized底层用什么实现的 

139. SpringBean的作用域有什么 

     Singleton、Propagate、Request、Session、Global Session

140. 单例模式可以保证bean安全吗 

     不能

141. 如何实现Bean的安全 

     1. 可以Controller类上加注解@Scope("prototype")或@Scope("request")
     2. 使用ThreadLocal会有问题，因为服务器的线程是复用的，因此不能解决线程安全
     3. 把全局变量替换成局部变量

142. 为什么要三次握手四次挥手 

143. 如果出现大量的TIME-WAIT连接是为什么 

     一个TCP/IP连接断开以后，会通过TIME_WAIT的状态保留一段时间，时间过了才会释放这个端口，当端口接受的频繁请求数量过多的时候，就会产生大量的TIME_WAIT状态的连接，这些连接占着端口，会消耗大量的资源。 

144. 为什么使用[红黑树](https://www.nowcoder.com/jump/super-jump/word?word=红黑树)，不使用B+树

     如果用B+树的话，在数据量不是很多的情况下，数据都会“挤在”一个结点里面。这个时候遍历效率就退化成了链表。

145. ConcurrentHashmap讲一下 

     1.7使用分段锁，并发量低，取决于Segment的大小；1.8使用了Synchronized+CAS来将锁的粒度减少为链表头，不使用Reentranlock的原因是Node要实现AQS，这样的话需要消耗大量无用的空间，因为仅仅是链表头的Node需要用到。

     1.7在定位下标时需要定位两次，先定位Segment下标，再定位HashEntry下标。

146. Hashmap1.7和1.8的区别 

     1. 数据结构不同。
     2. 计算hash值的扰动函数不同。
     3. 扩容后计算下标的方式不同，1.8通过规律推出，避免了重新计算。
     4. 扩容后插入链表的方式不同。
     5. 遇到哈希冲突时处理方式不同，1.8在链表长度>8并且元素大于64的时候会转化成红黑树，提高效率。

     https://blog.csdn.net/zhengwangzw/article/details/104889549?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522163443805716780274129364%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=163443805716780274129364&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~top_positive~default-1-104889549.pc_search_result_cache&utm_term=hashmap&spm=1018.2226.3001.4187

147. mysql的两种索引说一说？ 

148. mysql的两种索引分别用于什么场景？ 

149. hashmap扩容为什么要2的整数次幂

     n & (n-1)

150. [链表](https://www.nowcoder.com/jump/super-jump/word?word=链表)长度到8变成[红黑树](https://www.nowcoder.com/jump/super-jump/word?word=红黑树)，为什么是8

     因为经过计算，在hash函数设计合理的情况下，发生hash碰撞8次的几率为百万分之6，概率说话。。因为8够用了，至于为什么转回来是6，因为如果hash碰撞次数在8附近徘徊，会一直发生链表和红黑树的互相转化，为了预防这种情况的发生。

151. 为什么1.8改用cas+synchronized

     - 减少内存开销:如果使用ReentrantLock则需要节点继承AQS来获得同步支持，增加内存开销，而1.8中只有头节点需要进行同步。
     - 内部优化:synchronized则是JVM直接支持的，JVM能够在运行时作出相应的优化措施：锁粗化、锁消除、锁自旋等等。

152. 谈谈其他树，分别的应用(说了b+树，查找树，平衡树)

153. b+树为什么适合做innodb底层数据结构

154. 手撕:[二叉树](https://www.nowcoder.com/jump/super-jump/word?word=二叉树)层序遍历

155. 说说其他数据结构(栈，队列，图)

156. Redis哪些数据结构，底层分别怎么实现

157. 手撕:一个单[链表](https://www.nowcoder.com/jump/super-jump/word?word=链表)，求最大的前k个数

158. 手撕:最大子数组。一个数组[1，3，4，8，9]，另一个数组[2，3，4，8，10]，他们最长子数组是[3，4，8]。口述思路

159. AOP 原理和[源码](https://www.nowcoder.com/jump/super-jump/word?word=源码)

160. 8种基本类型 int short char byte boolean double float long

161. 遍历树的方式 介绍下前中后序遍历

162. mysql的InnoDB引擎 索引底层数据结构 MyISAM呢 

     **myisam保存表具体行数；innodb不保存**。

163. InnoDB 为什么用B+树 而不用B树 

164. 从树的方面来解释下mysql索引的最左匹配原则吗

165. TCP 三次握手 四次挥手 为什么是三次

166. 怎么遍历hashmap 还有其他方法吗

167. 怎么遍历list呢 能一边遍历 一边删除吗

168. hashMap jdk1.8 的改进

     1. 数据结构
     2. 尾插法
     3. 计算扩容后的hash值

169. 怎么实现一个线程

170. 线程池常见的参数

171. volatile关键字有什么作用 指令重排是干啥的

172. Spring 事务是怎么控制的？ 注解的实现原理知道吗 我说不知道 面试官说提示一下是AOP 我还是不会。。

     1. 基于xml的声明式事务
     2. 基于注解的声明式事务
     3. 编程式事务

173. 事务的隔离级别 分别能解决什么问题 默认是哪个隔离级别 Spring中的隔离级别 如果Spring中设置的隔离级别和数据库中的不一样 生效的会是哪个

     **生效的是spring的隔离级别。**

174. Mybatis 中 $ # 哪个能防止 sql 注入 为什么能防止

     #{} 有预编译，${} 只是替换字符，${} 不能防止 sql 注入，一般用于传递表名，数据库名等。

175. $ # 除了在防止sql注入 其他方面还有什么区别吗

176. MyBais 一级 二级缓存

     1）一级缓存 Mybatis的一级缓存是指SQLSession，一级缓存的作用域是SQlSession, Mabits默认开启一级缓存。 在同一个SqlSession中，执行相同的SQL查询时；第一次会去查询数据库，并写在缓存中，第二次会直接从缓存中取。 当执行SQL时候两次查询中间发生了增删改的操作，则SQLSession的缓存会被清空。 每次查询会先去缓存中找，如果找不到，再去数据库查询，然后把结果写到缓存中。 Mybatis的内部缓存使用一个HashMap，key为hashcode+statementId+sql语句。Value为查询出来的结果集映射成的java对象。 SqlSession执行insert、update、delete等操作commit后会清空该SQLSession缓存。

     注意！！当集成到 Spring 时，若没有开启事务的话每次调用都会新开启一个 Session ，之前的 Session 会被 close 掉，因此想要开启缓存：1. 开启事务。2. 使用二级缓存。

     2）二级缓存 二级缓存是mapper级别的，Mybatis默认是没有开启二级缓存的。 第一次调用mapper下的SQL去查询用户的信息，查询到的信息会存放代该mapper对应的二级缓存区域。 第二次调用namespace下的mapper映射文件中，相同的sql去查询用户信息，会去对应的二级缓存内取结果。

177. Redis操作list的操作

178. 什么是面向对象编程 

179. Java三大特性 

180. 事务有哪些隔离级别，Mysql默认的是什么隔离级别 

181. Java常用的集合类有哪些 

182. 你使用过哪些设计模式，挑几个重点讲一讲实现 

183. 双重校验锁如何实现？（这里我回答漏了voliate关键字） 

184. Java中的抽象类和接口有什么区别？ 

185. 抽象类中可以定义变量，编写实现吗？ 

186. JVM有了解过吗？它管理的内存区域分为哪些？ 

187. 对象创建的过程？

188. 单点登陆如何实现的？

     https://www.cnblogs.com/ZhuChangwu/p/11997499.html

189. [redis](https://www.nowcoder.com/jump/super-jump/word?word=redis)中有哪些存储类型？底层数据结构分别是什么？

190. springbean生命周期

191. springboot自动装配

192. 事务隔离级别

193. hashmap,concurrenthashmap（如何线程安全，resize，1.7，1.8的区别）

194. [redis](https://www.nowcoder.com/jump/super-jump/word?word=redis)数据结构,分布式锁

195. hashmap的结构,扩容阈值

196. 两个线程对hashmap同时扩容的后果

197. gc回收[算法](https://www.nowcoder.com/jump/super-jump/word?word=算法)，垃圾回收器g1,cms解释

198. springmvc的流程

199. 对微服务的了解，解释zoo[keep](https://www.nowcoder.com/jump/super-jump/word?word=keep)er

200. 手写代码，在10亿的商品日志中找出出现最多的一百个商品

     1. 将日志中的商品根据商品id利用哈希运算写入到多个文件中
     2. 遍历每个文件，利用map计算出每个商品的出现频率
     3. 构建数量为100的小顶堆，遍历每个每个文件中的map，若比堆顶大，则替换堆顶，因为堆顶表示是这个堆里最小的一个数，因此替换后再进行调整，直到遍历完成，堆顶为100个数里的最小值，而整个堆就是最大的100个数。

201. url的组成，一个网址如何找到对应的机器，对应的接口和服务

202. json的底层实现

203. jdk 1.8新特性，jdk最新版本是几

204. spring常用注解哪些

205. count(1)、count(column)、count(*) 的区别

     结果上来说：count(column) 会过滤空值。

     性能上来说：

     1. 有主键的情况下，count(column) 最优
     2. 没有主键的情况下，假如只有一列，count(*) 最优。假如有多列，则 count(1) 最优。

     count(1)，其实就是计算一共有多少符合条件的行。
     1并不是表示第一个字段，而是表示一个固定值。
     其实就可以想成表中有这么一个字段，这个字段就是固定值1，count(1)，就是计算一共有多少个1.

     count(*)，执行时会把星号翻译成字段的具体名字，效果也是一样的，不过多了一个翻译的动作，比固定值的方式效率稍微低一些。

二面准备：

1. 项目遇到的问题

   1. 如何限流

      1. 通过ip、全局key、url作为key来保存一个Hash，分别有当前令牌数、最大令牌数、令牌产生速率、上次计算令牌的时间。
      2. 执行脚本传入参数：桶的key、获取的token数、等待时间阈值。
      3. 执行逻辑：计算生成令牌数，与当前令牌数叠加（与令牌总数取最小值），把当前令牌数减去token，判断大小。
         - 若小于0，则说明令牌需要预支，此时再计算产生预支的令牌需要多长时间，和时间阈值作比较，大于则返回-1，小于则返回等待时间，注意这里需要把上次计算令牌时间设置为nowTime + 等待时间。
         - 若大于等于0，更新相关字段，最后返回0。
      4. 执行脚本后会返回一个微秒数，若为-1，则说明超出时间阈值，若为0则返回true，若大于0则通过sleep来睡眠对应时间后返回true。

   2. 限流细节：

      1. 利用ConcurrentHashMap记录key和TokenTong对象，高并发时通过computeIfAbsent方法返回TokenTong。
      2. 然后利用tokenTong对象调用tryAcquireToken方法。
   3. TokenTong中提前写好lua脚本，调用redisTemplate方法，传入lua脚本、返回值，key集合，可边长参数。
      4. 在脚本中，通过KEY[下标]来获取集合中的key，通过ARGV[下标]来获取对应的参数，在获取令牌和时间阈值时需要调用tonumber函数来获取，方便后面计算。其中还能通过redis.call方法调用redis相关的接口，比如redis.call('hget', key, 'tokensSec')，还有通过redis.call('time')，来返回数组参数：当前时间和当前这一秒已经逝去的微秒数。所以要两者运算相加，得到一个nowTime。

   3. 接口如何获取到用户id

      1. 通过Spring的AOP以目标注解为切入点。
   2. 获取请求头中的Authorization，解析jwt，获取用户id，通过调用joinPoint.getArgs来修改传入参数，使接口能直接拿到用户id来完成相关逻辑。
   
   4. 幂等性设计里也用到了AOP，只有标识了Idemp注解的方法才会取出请求头进行验证。
   
5. 更新数据时，怎么删除缓存
   
      删除操作放到MQ中间件中，MQ可以配置超时时间和重试次数，还能配置重试的时候在其他broker投递，假如删除失败了，还能通过重试来删除。另一种方法：通过Cannal中间件订阅binlog日志，把自己伪装成MySQL的从节点，发送dump请求，主节点就会开始推送Binlog给Cannal，Cannal解析字节流转化为结构化数据后便可以操作缓存。
   
   他们都是异步的思想。
   
   假如存在重复消息，通过数据库的唯一索引来解决。
   
   6. 缓存为空时，怎么写入缓存
   
      1. 针对获取不到锁的线程：其他线程获取不到分布式锁的时候，休眠2s，再重新获取缓存，假如还是为空，就再获取分布式锁，还是空则返回请等待一段时间重试。
      2. 针对获取到锁的线程：注意在加锁的时候执行setnx和失效时间的合并指令保证原子性，要在写入缓存之后删除key。假如缓存未设置完成，该key就过期了，很可能就会导致其他线程成功获取到锁来写入缓存。但是这个问题在文章查询里影响并不大，甚至有可能提高效率。所以并没有利用watch dog另起一个线程来续签。
      3. 同时，这个分布式锁也能避免大量io同时访问数据库。
   
   7. 权限设计：
   
      **OAuth 就是一种授权机制。数据的所有者告诉系统，同意授权第三方应用进入系统，获取这些数据。系统从而产生一个短期的进入令牌（token），用来代替密码，供第三方应用使用。**
   
      四种授权方式：
   
      1. 授权码模式
      2. 密码模式
   
2. SQL慢查询：

   1. 首先需要开启慢查询日志，修改ini的配置文件，定义慢查询的时间阈值。
   2. 利用explain关键字分析语句，可以查看查询的索引类型，使用到的索引key。
   3. 优化：
      1. 解决索引失效，比如like。。
      2. 优化数据库结构，比如把字段多的表分解成多个表。
      3. 增加中间表，对经常需要联表查询的字段插入到中间表中（反范式）。
      4. 分解关联查询，在内存中进行关联。
      5. 优化limit分页，避免偏移量非常大的时候。假如是主键自增的话可以先定位到相应的主键id，再直接查询后面的数据。
      6. 

3. 为什么想来我们公司？

   行业发展、公司规模、