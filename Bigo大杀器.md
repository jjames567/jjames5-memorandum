## Bigo大杀器

1. 手写多线程 两个线程循环打印奇偶数 

   ```java
   public static void main(String[] args) {
   
       ReentrantLock lock = new ReentrantLock();
       Condition condition1 = lock.newCondition();
       Condition condition2 = lock.newCondition();
   
       new Thread(()->{
           int a = 0;
           while (a < 102){
               lock.lock();
               System.out.println("线程" + Thread.currentThread().getName() + "：" + a);
               a+=2;
               try {
                   condition2.signal();
                   condition1.await();
               } catch (InterruptedException e) {
                   e.printStackTrace();
               }
               lock.unlock();
           }
           System.out.println("线程" + Thread.currentThread().getName() + "：" + "结束");
       }, "A").start();
       new Thread(()->{
           int b = 1;
           while (b < 101){
               lock.lock();
               System.out.println("线程" + Thread.currentThread().getName() + "：" + b);
               b+=2;
               try {
                   condition1.signal();
                   condition2.await();
               } catch (InterruptedException e) {
                   e.printStackTrace();
               }
               condition1.signal();
               lock.unlock();
           }
           System.out.println("线程" + Thread.currentThread().getName() + "：" + "结束");
       }, "B").start();
   }
   ```

2. List Set Map集合讲讲 

   总共有两大接口：Collection 和 Map ，一个元素集合，一个是键值对集合； 其中List和Set接口继承了Collection接口，一个是有序元素集合，一个是无序元素集合； 

3. ArrayList和LinkedList区别 

   ArrayList是实现了基于动态数组的数据结构，LinkedList基于双向链表的数据结构。 

   对于随机访问get和set，ArrayList觉得优于LinkedList，因为LinkedList要移动指针。 

   对于新增和删除操作add和remove，LinedList比较占优势，因为ArrayList要移动数据。

   时间复杂度：通过下标来查数据时，数组是O(1)，链表是O(n)。

   空间：ArrayList的空间浪费主要体现在在list列表的结尾预留一定的容量空间，而LinkedList的空间花费则体现在它的每一个元素都需要消耗相当的空间

   场景：在利用Collections.reverse()或者要对list进行大量的插入和删除操作时，LinkedList性能更好。

4. 线程池参数 

   - 核心线程数
   - 最大线程数
   - 非核心线程的最长空闲时间
   - 时间单位
   - 等待队列
   - 创建线程的工厂
   - 拒绝策略

5. 如何保证set集合唯一性 重写hashCode equals方法 

   本身Object类已经符合唯一性了，Set集合先通过hashCode去计算出下标，再通过equals方法进行比较地址，因此能保证唯一性，但我们希望有一种场景是针对new Student("201821314207")，要保证学生的唯一性，因此需要重写hashCode和equals方法：

   ​	hashCode具有唯一性，学号也是唯一的，因此hashCode方法可以返回this.number.hashCode()。

   ​	而equals方法则重写为比较学生学号的逻辑。

6. java类加载

   时机： 

   1. 遇到new、getstatic 和 putstatic 或 invokestatic  这4条字节码指令时，如果类没有进行过初始化，则需要先触发其初始化。对应场景是：使用 new 实例化对象、读取或设置一个类的静态字段（被  final 修饰、已在编译期把结果放入常量池的静态字段除外）、以及调用一个类的静态方法。
   2. 对类进行反射调用的时候，如果类没有进行过初始化，则需要先触发其初始化。
   3. 当初始化类的父类还没有进行过初始化，则需要先触发其父类的初始化。（而一个接口在初始化时，并不要求其父接口全部都完成了初始化）
   4. 虚拟机启动时，用户需要指定一个要执行的主类（包含 main() 方法的那个类），
      虚拟机会先初始化这个主类。

   过程：一般遵循双亲委派机制来加载

   1. 加载
   2. 连接
   3. 初始化

7. 垃圾回收判断

   1. 引用计数法：给这个对象增加一个引用计数器，当有一个地方引用他时就加1，当引用失效就减1，计数器为0的对象就是可以回收的。但会有循环用用的问题。
   2. 可达性分析算法：找到一些gc Roots对象，然后从这些对象进行向下搜索，搜索走过的路程称为引用链，当一个对象到gc roots没有任何引用链时，证明此对象是一个垃圾。

8. jvm垃圾回收算法

   1. 标记清除算法：遍历整个内存区域，对需要回收的对象打上标记，再次遍历内存，对标记过的内存进行回收。

      缺点：两次遍历、导致内存碎片，没有满足分配要求的情况下会再次出发GC。

   2. 复制算法：将内存划分为等大的两块，每次只使用其中的一块。当一块用完了，触发GC时，将该块中存活的对象复制到另一块区域，然后一次性清理掉这块没有用的内存。

      优点：解决了内存碎片的问题，效率更高，记住首尾地址复制清除，不用两次遍历。

      缺点：内存利用率不高，只能使用一半内存。

   在实际情况中，大多数对象的生命周期非常短，并且复制算法效率高，可以考虑用复制算法来作为回收算法，但不至于进行1:1划分，为了提高内存利用率，可以分代来处理。

   新生代：存放生命周期短的对象及其体积小的对象

   老年代：存放生命周期长的 ，体积大的对象

    3. 标记整理算法：对需要回收的进行标记，让存活的对象，向内存的一端移动，然后直接清理掉没有用的内存。

       通常也作为分代算法中老年代的垃圾回收算法。

9. G1回收器讲讲

   看上面垃圾回收器部分。

10. 网络OSI模型和TCP/IP模型 分层 每一层有什么协议 dns处于那一层(应用层） 

    DNS即域名系统，其作用是将字符串域名解析成相对于的服务器IP地址，免除人们记忆IP地址的单调和苦恼，属于为用户排忧解难之举，因此划归为应用层。

    网络面试题：https://mp.weixin.qq.com/s/oAZ9S6YG9gITreIrqwEstA

11. 讲讲tcp三次握手 四次挥手 为什么要三次握手

    看上面网络部分。 

12. B树和B+树区别

    B树的每个节点存储了key和data，key是一条数据记录的键值，是唯一的，data存储的是数据记录除key以外的数据。而B+树只在叶子节点存储data数据，这样非叶子节点就能存储更多的key。所以B+树相较于B树来说更加的矮胖，因为索引树很大不能一次IO读取进内存，树的深度越浅，查找数据时IO的次数就越少，效率就更快。

    B+树的每个叶子节点的指针指向相邻的叶子节点，构成一个有序链表，可以按照关键码排序的次序遍历全部记录。由于数据顺序排列并且相连，所以便于区间查找和搜索。而B树叶子节点指针为null，则需要进行每一层的递归遍历。相邻的元素可能在内存中不相邻，所以缓存命中性没有B+树好。

    **MyISAM**中，data存的是数据地址。索引是索引，数据是数据。索引放在XX.MYI文件中，数据放在XX.MYD文件中，所以也叫**非聚簇索引**。

    **InnoDB**中，data存的是数据本身。索引也是数据。数据和索引存在一个XX.IDB文件中，所以也叫**聚簇索引**。

13. 数据库事务ACID

    原子性：事务是一个原子操作单元，其对数据的修改，要么全都执行，要么全都不执行，在操作失败后不能对数据库中的数据有任何影响。

    一致性：在事务开始和完成时，数据必须保持一致状态，这意味着所有相关的数据规则都必须应用于事务的修改，以保持数据的完整性；事务结束时，所有的内部数据结构也必须是正确的。

    隔离性：数据库系统提供一定的隔离级别，保证事务在不受外部并发操作影响的“独立”环境执行。这意味着事务处理过程中的中间状态对外部是不可见的，反之亦然（注意：事务的隔离性是相对于两个事务而说的，两个事务独立执行互补干扰）。

    持久性：事务完成后，它对数据的修改是永久的，即使出现系统故障也能保持的正确性。

14. select poll epoll()区别

    https://mp.weixin.qq.com/s?__biz=MzUxODAzNDg4NQ==&mid=2247489558&idx=1&sn=7a96604032d28b8843ca89cb8c129154&scene=21#wechat_redirect

15. 零拷贝技术 

    https://mp.weixin.qq.com/s?__biz=MzUxODAzNDg4NQ==&mid=2247485624&idx=1&sn=248eca4d8dd214126fb89d75acb5f34e&chksm=f98e4c12cef9c5048810530b04d6f58cd449649ffd1372d1eef8f3c3b1592598579ef15f45e0&scene=178&cur_album_id=1408057986861416450#rd

16. 负载均衡[算法](https://www.nowcoder.com/jump/super-jump/word?word=算法)有哪些

    1. 轮询(Round-Robin，RR)：默认情况下Nginx服务器实现负载均衡的算法就是轮询，轮询策略按照顺序选择组内服务器处理请求。如果一个服务器在处理请求的过程中出现错误，请求会被顺次交给组内的下一个服务器进行处理，以此类推，直到返回正常的响应为止。但如果所有的组内服务器都出错，则返回最后一个服务器的处理结果。
    2. 加权轮询(Weighted Round-Robin,WRR)：为组内服务器设置权重，权重值高的服务器被优先用于处理请求。此时组内服务器的选择策略为加权轮询。组内所有服务器的权重默认设置为1，即采用轮询处理请求。
    3. ip_hash：ip_hash用于实现会话保持功能，将某个客户端的多次请求定向到组内同一台服务器上，保证客户端与服务器之间建立稳定的会话。只有当服务器处于无效(down)的状态时，客户端请求才会被下一个服务器接收和处理。注意：使用ip_hash后不能使用weight，ip_hash和主要根据客户端IP地址分配服务器，因此在整个系统中，Nginx服务器应该是处于最前端的服务器，这样才可以获取到客户端IP地址，否则它得到的IP地址将是位于它前面的服务器地址，从而就会产生问题。
    4. least_conn：least_conn用于为网络连接分配服务器组内的服务器，在功能上实现了最小连接数负载均衡算法，在选择组内的服务器时，考虑各服务器权重的同时，每次选择的都是当前网络连接最少的那台服务器，如果这样的服务器有多台，就采用加权轮询选择权重值大的服务器。

    第三方：

    5. fair：用于为响应时间分配服务器组内的服务器，他是按后端服务器的响应时间来分配请求，响应时间越短的越优先分配，需要第三方模块的支持[nginx-upstream-fair-master](https://codeload.github.com/gnosek/nginx-upstream-fair/zip/master)
    6. url_hash：按访问url的hash结果来分配请求，使每个url定向到同一个后端服务器，后端服务器为缓存时比较有效。注意：使用hash后不能使用weight。需要第三方模块的支持[ngx_http_upstream_hash_module](https://codeload.github.com/soenmie/ngx_http_upstream_hash_module/zip/master)

17. hash一致性[算法](https://www.nowcoder.com/jump/super-jump/word?word=算法) hash环

    一般的 hash(key) % N 在命中缓存方面会出现以下问题：

    1. 当缓存服务器数量发生变化时，会引起缓存的雪崩，可能会引起整体系统压力过大而崩溃（大量缓存同一时间失效）。
    2. 当缓存服务器数量发生变化时，几乎所有缓存的位置都会发生改变，怎样才能尽量减少受影响的缓存呢？

    这种情况下可以采取 hash环 来解决，下面这张图中，1和2会顺时针缓存到离它最近的节点，34同理，当服务器的数量发生改变时，也只会造成部分缓存失效，而不会导致所有缓存失效。

    计算公式是这样的：

    **hash（服务器的IP地址） %  2^32**

    **hash（key） %  2^32**

    为什么用 2 ^ 32？因为ip地址就是32位的。

    <img src="https://raw.githubusercontent.com/jjames567/picture/main/image-20210810213806033.png" alt="image-20210810213806033" style="zoom:33%; margin-left:-5px" />

    hash环的倾斜问题，实际情况可能是下面这样的：

    多个缓存都命中在同一个服务器上

    <img src="https://raw.githubusercontent.com/jjames567/picture/main/image-20210810214424006.png" alt="image-20210810214424006" style="zoom:33%; margin-left:-5px" />

    解决办法：**虚拟节点**

    将现有的物理节点通过虚拟的方法复制出来

    <img src="https://raw.githubusercontent.com/jjames567/picture/main/image-20210810214600073.png" alt="image-20210810214600073" style="zoom:33%;margin-left:-5px" />

    在实现方面，可以使用 TreeMap 来保存服务器hash后的值，TreeMap 的 tailMap(K fromKey) 可以找到比fromKey大的值的集合，但并不需要遍历整个数据结构。

18. 分布式相关有了解吗 

    微服务是架构设计方式（以功能拆分成一个个小的服务，可能是部署在一个服务器上），分布式是系统部署方式（模块分布在不同服务器，每个模块涉及一个或多个功能）。

19. 手写LRU 

    

20. 讲讲hashMap 

21. volatile关键字作用以及场景 

22. mysql主键索引讲讲 索引失效讲讲 

23. 多线程状态转换 

24. String c = "xx" + "yy " + a + "zz" + "mm" + b 背后的优化执行过程 

25. Integer i1 = new Integer(40); 
    Integer i2 = new Integer(40); 
    Integer i3 = new Integer(0); 
    i1 + i3 == i2 的结果和处理过程是怎样的 

26. java8中HashMap的实现有哪些变化 

27. try catch finally中各种return场景 

28. 表： 
    f1  |  f2 | 

    NULL |  1 
    1   |  NULL 
    select count(f1) from test; 
    select count(distinct f1, f2) from test; 区别？ 

29. user表中有主键，为自增ID 

    select * from user表中有主键，为自增ID 

    select * from user order by name limit N offset M 会查询多少行记录？如何优化，不通过增加索引。user order by name limit N offset M 会查询多少行记录？如何优化，不通过增加索引。 

    

30. 如果服务器上出现了大量处于CLOSE_WAIT状态的TCP链接有可能是什么原因 

31. 讲讲hashMap 

32. 数据库 覆盖索引 

33. 数据库相关 那条sql执行高效

34. 设计大量消息推送

35. 7个球 其中一个比较轻 怎么选出最轻的的那个 最少次数

36. 为什么BIO比NIO性能差？简单讲讲区别？ 

37. SpringIOC容器初始化的原理？ 

38. 一个网页，要实现最常访问有什么实现吗？

39. 100GB大小的IP地址文件大小，找出出现频率topK个，如何实现？

40. netty 原理, NIO, 多路复用, pipeline 

41. 场景设计题：获取总成绩前五名的学生信息，学生的属性有：学号、姓名、各科成绩。需要说出第一步该做什么，第二步该做什么，第三步该做什么。例如，第一步，需要封装什么类。（我说了下我的思路，然后面试官问：假如第四名、第五名和第六名的总成绩都是500分，那这不就少了些数据吗？我的回答是获取第五名学生的时候，判断第六名是不是和第五名的总成绩一样，如果一样，则加入到结果集中。面试官说假如有很多个500分呢？我就说继续判断下去，面试官似乎不是很满意我的回答） 

42. sql 语句，如何从数据库中获取前一百条数据 

43. 描述一下 Java 多态 

44. 讲一下平时开发过程中遇到过哪些 RuntimeException 异常。如果你突然遇到一个你不熟悉的异常，你会如何解决？ 

45. 接口和抽象类的区别 

46. 讲一下 final 关键字 

47. 讲一下重写，还有一个和重写挺像的（重载），讲一下这两个的区别

48. 说一下你知道的[排序](https://www.nowcoder.com/jump/super-jump/word?word=排序)[算法](https://www.nowcoder.com/jump/super-jump/word?word=算法)（我回答了：选择[排序](https://www.nowcoder.com/jump/super-jump/word?word=排序)、插入[排序](https://www.nowcoder.com/jump/super-jump/word?word=排序)、冒泡[排序](https://www.nowcoder.com/jump/super-jump/word?word=排序)、快速[排序](https://www.nowcoder.com/jump/super-jump/word?word=排序)、堆[排序](https://www.nowcoder.com/jump/super-jump/word?word=排序)、希尔[排序](https://www.nowcoder.com/jump/super-jump/word?word=排序)等），描述下选择[排序](https://www.nowcoder.com/jump/super-jump/word?word=排序)的思想 

49. 同步和异步的区别？（这题我没答到点，我讲到临界区去了，面试官通过[前端](https://www.nowcoder.com/jump/super-jump/word?word=前端)的 Ajax 来解释给我听） 

50. Map, Set, List， 说下它们的特性和使用场景

51. 介绍下 Spring 的实现？（我回答了 IOC 和 AOP，我问还要不要讲下 SpringMVC 的流程，面试官说不用） 

52. 网页[客户端](https://www.nowcoder.com/jump/super-jump/word?word=客户端)，登录页面，用户输入账号和密码，请求发送到后台接口，后台处理完返回数据的整个过程，将这个过程描述下 

53. 实现一个实时的排行榜，数据结构

54. [链表](https://www.nowcoder.com/jump/super-jump/word?word=链表)[排序](https://www.nowcoder.com/jump/super-jump/word?word=排序)，快排思想，时间复杂度O(nlgn)。

55. 从输入URL到浏览器显示页面，这个过程发生了什么？

56. 解释一下DNS解析的详细过程；

57. 介绍一下TCP握手的过程；

58. TCP的可靠性是如何保证的？

59. 线程和进程

60. 线程死锁的条件？死锁如何检测？

61. Java实现线程的方式？几种方式的区别是什么？Callable返回值的类型是什么？

62. 线程池的创建方法？有哪些参数

63. Java 基本数据类型？各个类型所占的字节大小？

64. 有一个容量4升的水桶和一个9升的水桶，如何使用这两个水桶装出6升的水？

65. [链表](https://www.nowcoder.com/jump/super-jump/word?word=链表)元素倒置：
    1>2>3>4 变成 2>1>4>3

66. 有Tread为什么还要Runnable

67. 自己的实践中，有没有线程互锁情况

68. [leetcode](https://www.nowcoder.com/jump/super-jump/word?word=leetcode) 188,股票的买卖最佳时机（限时15分钟）

69. ConcurrentSkipListMap的介绍和底层实现 

70. 介绍下spring AOP、filter和拦截器的先后顺序，request可以在filter、拦截器中被修改吗

71. 动态代理的实现方式、Mybatis的#和$的区别、Mybatis的缓存介绍和开启。 

72. 线程池有哪些参数 

73. exceute和submit的区别 

74. countDownLatch的使用场景，原理 

75. AQS详细介绍 

76. volatile的作用、原理，为什么不能保证原子性，读写谁先。 

77. synchrozied和volatile的区别 

78. synchrozied的底层原理 

79. synchrozied和reentrantlock的区别 

80. 超前引用

81. JVM分区，垃圾回收[算法](https://www.nowcoder.com/jump/super-jump/word?word=算法)，垃圾收集器G1，full GC的触发条件 

82. 数据库的隔离级别

83. 可重复读和读已提及的锁有什么区别

84. 在数据量比较大的情况下，使用limit分页会有性能问题，如何优化 

85. 覆盖索引的原理

86. 10亿个数，找出前100大。（堆[排序](https://www.nowcoder.com/jump/super-jump/word?word=排序)，被说10亿太大了，内存承受不了，那就分区堆[排序](https://www.nowcoder.com/jump/super-jump/word?word=排序)吧，每个区1亿.....）

87. 七层协议

88. TCP如何保证可靠性 

89. 介绍拥塞控制

90. 介绍流量窗口

91. 简历中的[项目](https://www.nowcoder.com/jump/super-jump/word?word=项目)所负责的工作内容

92. 超市种类很多，如何设计一个数据库方便种类的插入

93. 场景题（电影票锁定座位的实现，使用mysql的乐观锁和悲观锁如何实现，具体实现方式）

94. 每位玩家有id和分数，需要使用数据结构实现以下要求 

      排行榜的实时更新前十名， 

      按id号查询玩家的排名 

      实时更新玩家自身的排名 

95. [算法题]()，一个相邻元素不相等的数组，数组第一位元素小于第二位元素，倒数第二位元素大于最后一位元素，找出符合以下条件的元素 

      比相邻左边元素和相邻右边元素都大的元素。（大概意思是这样，不知道表达清楚没有，例如1，2，5，3，4，1，符合条件的有5和4，随便输出一个就可以）

96. 三个线程交替打印ABC

97. CMS

98. 标记整理和标记清除

99. java内存模型

100. 并发集合，线程安全的集合

101. concurrentHashMap怎么实现线程安全的？

102. 偏向锁

103. InnoDB和Mylsam

104. int（10），10表示什么 超过可以吗？

105. varchar和char

106. CLOSE_WAIT和TIME_WAIT

107. TIME_WAIT保留一段时间的原因

108. HTTPS了解吗

109. Redis了解吗？

     发布和订阅

     缓存击穿和缓存穿透，有什么方式可以解决？

     分布式锁

110. springboot请求的过程

111. 数组里找三个数加起来为0  不要另开容器 

112. [LRU实现方式](https://www.nowcoder.com/jump/super-jump/practice?questionId=1006010)

113. 三次握手 能不能两次 如果第三次没收到会怎么样 

114. [判断链表是否有环 快慢指针](https://www.nowcoder.com/jump/super-jump/practice?questionId=605)

115. hashmap的底层实现 扩容时能不能优化 

116. 了解epoll select吗 

117. java多态的原理 

118. 上来直接撕一个快排

119. 锁的升级

120. 堆[排序](https://www.nowcoder.com/jump/super-jump/word?word=排序)有哪些应用

121. 段页式存储用到了哪些[算法](https://www.nowcoder.com/jump/super-jump/word?word=算法)

122. udp和http各种差别

123. udp和http报文结构

124. ACID

125. 死锁的四个条件

126. final关键字、static关键字 

127. Java三大特性 

128. Java的String、StringBuilder、StringBuffer 

129. Java线程池 

130. Java的动态分派 

131. Java的内存模型 

132. Java类加载 

133. java的Stream 

134. Java agent 

135. 如何实现类加载自定义的Object类 

136. Java锁（synchronized、ReentrantLock、AQS、CAS、volatile） 

137. Java多线程（callable和future） 

138. 垃圾回收器（cms） 

139. 哪些对象能做GC root？ 

140. innoDB和Myisam的区别？ 

141. 索引的数据结构以及类别？ 

142. 数据库锁。 

143. MVCC

144. 数据库分表

145. TCP协议、UDP协议、HTTP协议 

146. 如何实现UDP协议的可靠性？ 

147. Zset数据结构 

148. 假设小明开了a和b两种药片（两种药片肉眼分辨不出来），各十片。小明每天需要吃a和b药片各一片。当吃到第九天的时候，小明将剩余的a药片2片和b药片的2片混在一起（也就是总的剩下4片），无法分辨。用什么方法可以使得小明能够正确的服用a和b药片？ 

149. 如何将一个长链接转换为短链接？

150. 微信红包如何设计？

151. 重复元素数组的全排列（力扣原题）

152. 单[链表](https://www.nowcoder.com/jump/super-jump/word?word=链表)快排 

153. 有一个升序数组，长度为n，里面的数为0到n，其中有一个数m是没有的，让你找出m是哪个数？

154. 有一个长度为2*n（偶数）的数组，现在让你将其分为两个长度为n的数组，其中数组满足，两个数组元素之和差值最小？

155. volatile写入 读原理

156. os 文件内存0拷贝

157. 线程池maxsize coresize 阻塞队列 协作

158. 两个线程交替打印0-100

159. 负载均衡[算法](https://www.nowcoder.com/jump/super-jump/word?word=算法) 一致性hash

160. aop怎么实现的

161. 引用逃逸

162. 哪些东西可以做GC Root

163. Java list要删除数据怎么做 （一定要把size()变小）

164. cglib jdk动态代理 哪个快

165. 多线程的AQS 重入锁 

166. sql悲观乐观锁 怎么去写sql来使用锁

167. 堆内外的优缺点 

168. tcp半连接队列

169. SYN攻击

170. csrf脚本攻击与解决方法 

171. mybatis缓存

172. 讲最有挑战性的一个[项目](https://www.nowcoder.com/jump/super-jump/word?word=项目)（怕被怼不敢讲秒杀系统，讲了一个学校的[项目](https://www.nowcoder.com/jump/super-jump/word?word=项目)）

173. [项目](https://www.nowcoder.com/jump/super-jump/word?word=项目)中有没有网络连接超时的现象

174. [项目](https://www.nowcoder.com/jump/super-jump/word?word=项目)中的身份认证是怎么做的

175. 说一说常用的 linux 的命令。答 cat, ls, cd, mkdir, 看cpu占用率用top, 查看进程用 ps， 搜索文件用 find。

176. 说几个 java 中的不可变对象。答 final 修饰的对象，String 等。追问有没有不可变的数组，回答应该有。

177. 不可变对象是线程安全的吗？

178. 单例模式懒汉式中除了双重检测还有啥？

179. try_catch_finally 的 finally 里面加 return 可以吗?

180. 软引用和弱引用了解吗，区别以及使用场景。

181. JIT了解吗？什么时候使用。答出现重复频次高的代码比如while循环里面等。

182. 举一个不是线程安全的例子。答new对象的过程。

183. ThreadLocal了解吗？原理是啥？答是一个map。追问ThreadLocal中的引用是什么引用类型，盲猜强引用，答错了。。。

184. 说一说线程池的一些参数？然后说说线程增加的过程。

185. 线程池的拒绝策略有哪些？答四种

186. 怎么把一个任务加入线程池？答了一个execute，还差submit。

187. 那execute和submit的区别？答不知道，返回future，，，

188. CountDownLatch和CyclicBarrier的区别和使用场景?CountDownLatch底层实现？

189. 怎么实现mysql数据库的乐观锁？答不知道，其实秒杀中查询库存可以加一个版本号字段实现乐观锁，没想起来。

190. 在spring事务中如果调用rpc可能会出现什么问题？答rpc会阻塞，导致业务执行慢，没答到点子上，其实是可能导致数据库连接无法正常关闭导致连接池耗尽。

191. 说一下聚簇索引和索引覆盖？

192. 索引失效的场景？答了联合索引最左匹配原则，还有索引参与计算。

193. 问mysql的隔离级别。read committed 和 repeatable read 的区别。答能不能解决不可重复读。追问还有吗？

194. 给了一个 sql ，delete from table where id = 8; 问 假设 repeatable read 隔离级别下并且 id 是主键，会加什么锁？答 record lock。那 id 是普通索引，会加什么锁？答 next-key lock。那如果 read committed 隔离级别下并且 id 不是索引呢？答 table lock。

195. Mybatis中的 $ 和 # 的区别？

196. 知道rabbit如何创建网络通信吗？答不知道，应该是在tcp基础上封装了自己的协议。其实是 amqp 协议。

197. 业务上怎么防止 kafka/rabbit 消息的重复消费呢？答rabbitmq有ack机制，其实他想问的是kafka的幂等性。

198. 问 tcp 如何保证不丢包？答超时重传，序列号，拥塞控制，流量控制啥的。

199. 那就问了RSA和AES[算法](https://www.nowcoder.com/jump/super-jump/word?word=算法)的区别?

200. 给出一个字符串数组，返回所有互为“换位词（anagrams）”的字符串的组合。（换位词就是包含相同字母，但字母顺序可能不同的字符串） 
      备注：所有的输入都是小写字母 
      例如： 
      输入["tea","nat","ate","eat","tan"] 
      返回 
      [["ate", "eat","tea"],["nat","tan"]]

201. [二叉树](https://www.nowcoder.com/jump/super-jump/word?word=二叉树)、二叉搜索树和[红黑树](https://www.nowcoder.com/jump/super-jump/word?word=红黑树)的区别

202. [红黑树](https://www.nowcoder.com/jump/super-jump/word?word=红黑树)的优势在哪？怎么实现这个优势的

203. 堆和栈的区别（不是JVM中的）

204. final关键字了解吗？说说你对他的理解和原理

205. String是不变的吗？怎么实现不变的

206. 说一下CopyonWriteArraylist线程安全的原理

207. CopyonWriteArraylist的lock用的是哪个锁

208. 说一说HashMap的扩容原理

209. 说下HashMap在JDK1.7和1.8中的区别

210. CAS的缺点，怎么解决ABA问题

211. CAS必须和自旋一起用吗

212. 说一下常用的垃圾收集[算法](https://www.nowcoder.com/jump/super-jump/word?word=算法)

213. 说一下标记-清除[算法](https://www.nowcoder.com/jump/super-jump/word?word=算法)和复制[算法](https://www.nowcoder.com/jump/super-jump/word?word=算法)以及他们的缺点

     那标记整理[算法](https://www.nowcoder.com/jump/super-jump/word?word=算法)把他们的缺点解决掉了吗

214. 说下CMS收集器用的什么[算法](https://www.nowcoder.com/jump/super-jump/word?word=算法)以及CMS的缺点

215. CMS对产生内存碎片的问题有什么解决方案吗

216. 说下G1收集器的优点

217. 说下类加载过程，初始化阶段是干什么的

218. MySQL索引的原理及实现

219. 说一下聚族索引和非聚族索引的区别

220. like关键字进行查找会用到索引吗

     为什么like后跟%不走索引

221. 说下TCP四次挥手的过程

222. 哪一方分别能进入TIME-WAIT和CLOSE-WAIT，TIME-WAIT和CLOSE-WAIT分别是做什么的

     什么原因会导致服务端一直CLOSE-WAIT

223. 说下HTTP协议以及1.0和1.1的区别

224. TCP和HTTP的[keep](https://www.nowcoder.com/jump/super-jump/word?word=keep)alive分别是什么

225. 说下Spring的IOC，IOC的实现原理

226. 说下Spring的AOP，AOP的实现原理

227. 说下Spring有哪几种作用域

228. CGLIB和JDK动态***的区别

229. Spring提供事务操作了吗？说下Spring的事务传播等级

230. 子类能够重写父类中声明为 private 的方法吗？protected 方法呢？ 

231. private 关键字的作用域

232. 父类中被 final 关键字修饰的方法可以被重写吗？ 

233. 满足重载的条件有哪些？ 

234. 重载是编译时决定的还是运行时决定的？ 

235. Java Object 类中有哪些方法？

236. wait() 和 notify() 方法的作用 

237. Thread.wait() 方***释放锁吗？

238. 有重写过 hashcode 和 equals 方法吗？重写 equals 方法时需要遵守什么原则？

239. 如果 hashcode 和 equals 方法不重写的话会有什么影响？ 

240. Hashmap 是线程安全的吗？它在什么情况下会成环 

241. HashTable 为什么是线程安全的？ 

242. 说一下 ConcurrentHashMap 

243. 说一下JVM 中的可达性分析[算法](https://www.nowcoder.com/jump/super-jump/word?word=算法)

244. JVM 启动时需要设置哪些参数？ 

245. MySQL 中事务在并发环境下会出现哪些问题？ 

246. 说一下 MySQL 事务的隔离级别 

247. 可重复读解决了幻读的问题吗？ 

248. Innodb 下如何解决幻读的问题？ 

249. 责任链模式在[项目](https://www.nowcoder.com/jump/super-jump/word?word=项目)中是怎么用的？ 

250. 说一下 session 

251. 快排[算法](https://www.nowcoder.com/jump/super-jump/word?word=算法)是稳定的吗？ 

252. 讲一下分代[算法](https://www.nowcoder.com/jump/super-jump/word?word=算法)？

253. 分区里的比例？参数？ 答：8:1:1

254. CAS

255. 线程池

     怎么终止线程

256. 大量TIME_WAIT？sysctl.conf 

257. 存储引擎？区别？？ 

258. 联合索引（A，B）查询B，能用到吗？

259. Redis list怎么生成

260. dns解析的两种方式（递归&迭代） 

261. tcp是怎么保证传输可靠 

262. 死锁发生的条件 

263. 写出最少7个spring的注解并解释 

264. 判断是否是回文[链表](https://www.nowcoder.com/jump/super-jump/word?word=链表)

265. 两个字符串 比如1.22.33和1.22.34代表两个版本号，判断那个版本更新 

266. 给定一个长度为N的数组，现在有0-N 一共N＋1个[排序](https://www.nowcoder.com/jump/super-jump/word?word=排序)的数，从中间剔除一个后放到数组内，求出缺失的数。 

267. 数组求交集 

268. 微信红包怎么设计红包额度分配[算法](https://www.nowcoder.com/jump/super-jump/word?word=算法)

269. 返回值不同可以重载吗，然后给了一个泛型擦除的例子，问我可以重载吗，为什么。

270. 问我子类重写的方法有什么要求，我说了权限比父类大，面试官立刻给我挖坑说也就是private升为protected喽，我想都没想就嗯了（直接跳坑里），幸好立刻反应过来了 

271. 问我hashmap具体的实现，key可不可以为null, 又问我null key怎么算hashcode

272. 还问了线程池，从工作流程到参数配置都问了一下。

273. 然后我也不知道怎么扯到了hashmap，就开始问我hashmap的东西，和TX一样，不停扩展，不停优化。 

274. 如果NIO有一个事件没有处理会怎么样，我就说了LT和ET模式。

275. 牛顿迭代法

276. 进程和线程之间的区别 

277. 进程之间的通信 

278. 消息队列的底层怎么实现，请设计一个BlockQueue 

279. NIO底层实现 

280. 两个数组求交集，时间复杂度和空间复杂度 

281. 归并[排序](https://www.nowcoder.com/jump/super-jump/word?word=排序)实现一下，给你多个排好序的数组，和成一个。

282. TCP拥塞控制原理，滑动窗口具体实现和作用 

283. 网络拥塞怎么控制 

284. 如何判断一个给定字符串是不是合法的IP地址？

285. 通常情况下堆的大小，栈的大小？为什么递归容易造成栈溢出？ 

286. 给一个存储有40亿个不重复的整数的文件，怎么找出一个不包含在这些数字当中的整数？ 

287. 一个行递增有序，列递增有序的二维数组，怎么判断给定的数字是否在这个数组中？[剑指offer](https://www.nowcoder.com/jump/super-jump/word?word=剑指offer)原题。 

288. Java中什么情况下会出现OutOfMemoryError？ 

289. 深拷贝，浅拷贝

290. list，set，map的区别

291. 说一下list、map的扩容

292. set保证不重复的原理

293. 垃圾收集[算法](https://www.nowcoder.com/jump/super-jump/word?word=算法)，详细介绍分代收集

294. 计算机网络五层模型

295. HTTP和HTTPS的区别

296. HTTPS的加密发生在那一层

297. tcp和UDP的区别

298. 三次握手四次挥手

299. tcp保证可靠传输的方法

300. 流量控制和拥塞控制

301. 线程和进程的区别

302. 进程的特点

303. 进程的内存

304. 操作系统的内存管理

305. 死锁

306. 多线程一定比单线程快吗

307. 程序执行的过程

308. 从1-12中取至少要取多少个数才能保证其中一定有两个数的差为4