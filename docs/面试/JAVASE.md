#### JAVA基础

##### 变量的加载顺序
先看是否静态：静态优先级更高；
若都是静态，则无论是代码块还是成员变量，谁先声明谁先执行。

优先级
  1. 静态成员变量 / 静态代码块: 他们的优先级相同,谁先声明谁先执行
  2. 普通成员变量 / 构造代码块: 他们的优先级相同,谁先声明谁先执行
  3. 构造代码块优先于构造方法执行,每创建一个对象就调用一次构造代码块和构造方法
  4. 普通方法和静态方法没有任何区别,谁先调用就谁先执行
```java
public class Test{
  
  String a=1;//变量
  static String b=2;//静态变量
  static final String c=3;//静态常量
  String d;
  String e;
  String f;

  //静态代码块
  static{
    d = 4;
  }
  //构造代码块
  {
    e = 5;
  }
  //构造方法
  public Test(){
    f = 6;
  }
  
  public static void main(String[] args){
      g= 7;
  }

}
```


##### 控制台输入、输出
```java
//输入
Scanner scan = new Scanner(System.in);
while(scan.hasNext()){
    String str = scan.next();
}

//输出
System.out.println("hello world");
System.out.printf("the number is:%f",12.3);
```



##### List、Set、Map

##### HashTable
实现了多线程安全，在几乎所有的方法上都加上了synchronized锁，而加锁的结果就是HashTable操作的效率十分低下。

##### HashSet
底层通过HashMap实现。因此也是非线程安全的。

##### HashMap
非线程安全。仅适用于单线程中。
实质是一个“链表的数组”，可以通过哈希值直接求出元素对应的下标，因此查询效率极高。

###### HashMap处理哈希冲突的方式
链地址法
###### 怎么样会触发HashMap的扩容？
size达到阙值（threshold）。threshold = capacity（数组最大长度） * loadfactor（扩容因子）
###### 插入、查找时，如何为Key定位
1. 先取hashcode
2. 根据hashcode获得数组下标
3. 根据数组下标获得Entry、遍历Entry链表，若hashcode一致、key.equal为true或key相等
###### 插入时，是插入在链表的头部还是尾部
1.8之前：头部。又名头插法。（个人想法：若插入到尾部，还需要遍历链表到尾部，增加了耗时）
1.8及1.8之后：尾部。
###### HashMap是怎么遍历的，遍历时可以插入和删除元素吗？
实现：HashMap.AbstractMapIterator
modCount：HashMap用来记录对数据进行插入、删除的次数；
expectedModCount：AbstractMapIterator用来记录对数据进行插入、删除的次数；

若在遍历时执行插入、删除操作，发现modCount和expectedModCount不一样，会抛出异常。

* 调用hashmap.remove()或hashmap.put()时，会修改modCount，但不会修改expectedModCount
* 调用Iterator.remove()时，会同时修改modCount和expectedModCount

因此遍历时，不可以插入，可以删除（但要用iterator.remove()）。

###### 什么是扰动函数？
HashMap为了减少哈希冲突而设计的函数。
在1.8版本的HashMap中，是这样取哈希值的：
```java
static final int hash(Object key) {
int h;
return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);
}
```

即HashMap的hash方法，就是扰动函数的实现。
采用了 hashcode 的高 16 位和低 16 位异或的方法，去降低 hash 碰撞。
（参考：https://blog.csdn.net/weixin_42373997/article/details/112085344）

###### 不同JAVA版本HashMap实现的区别
||1.6的HashMap|1.7的HashMap|1.8的HashMap|
|---|---|---|---|
|如何取hashcode|key.hashcode()|sun.misc.Hashing.stringHash32((String) key)|(key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16) |
|如何扩容|超过阙值，则创建一个容量为原数组2倍的新数组，然后重新计算哈希值存进去|与1.6一样|与1.6一样|
|如何取数组下标|hashcode & this.elementData.length - 1|与1.6一样||
|如何实现链表|Map.Entry|Map.Entry|HashMap.Node、HashMap.TreeNode（树化后）|
|在链表哪里插入新的元素|链表头插入|链表头插入|链表尾插入|
|迭代器|HashMap.AbstractMapIterator|HashMap.AbstractMapIterator|Spliterator（并行迭代器）|

##### LinkedHashMap
事实上LinkedHashMap是HashMap的直接子类，二者唯一的区别是LinkedHashMap在HashMap的基础上，采用双向链表(doubly-linked list)的形式将所有entry连接起来，这样是为保证元素的迭代顺序跟插入顺序相同。
```java
Entry<K,V>[] table; //存储HashMap的地方
Entry<K,V> header;//按插入顺序维护的一个链表头部的指针
Entry<K,V> tail;//链表尾部的指针
```


###### ConcurrentHashMap
线程安全的Hashmap。

**JDK1.7版本（Segment桶模式，也称为分段锁机制）**
ConcurrentHashMap 是一个 Segment 数组，Segment是一个锁， 通过继承 ReentrantLock 来进行加锁，所以每次需要加锁的操作锁住的是一个 segment，这样只要保证每个 Segment 是线程安全的，也就实现了全局的线程安全。

Segment 内部是由 数组+链表 组成的。

segment 数组不能扩容，扩容的是Segment内部的数组。

默认有16个Segment。（concurrencyLevel=16）

**简单叙述ConcurrentHashMap 1.7中put操作的原理**
concurrentHashMap.put: 先取key的hashcode，根据hashcode求出所属Segment的下标（与15进行与操作）；

求得所属Segment后，调用Segment的put方法；

segment.put: 先获取独占锁，再根据key的hashcode获得node的下标；

若node没有value，直接赋值；若node有value，则比较hashcode是否一致，一致则直接覆盖，不一致则比较下个node



**JDK1.8版本（与HashMap类似的数组+链表+红黑树的方式实现）**

1.8摒弃了分段锁机制，使用与HashMap类似的数组+链表+红黑树的结构。
线程安全则是通过CAS和synchronized来实现。

**简单叙述ConcurrentHashMap 1.8中get操作的原理**
get操作全程不加锁，只通过volatile关键字修饰Node节点，保证数据的可见性、有序性。但不保证原子性。




**简单叙述ConcurrentHashMap 1.8中put操作的原理**
concurrentHashMap.put: 同样，先取key的hashcode，根据hashcode求出所属Node的下标（tabAt(tab, i = (n - 1) & hash)）；

若下标处node=null，则直接插入新值即可。插入时会进行一次cas操作，若操作失败则证明有并发操作，重新插入一次（casTabAt(...)）；

>CAS操作：即调用sun.misc.Unsafe的compareAndSwapObject()方法，传入了node数组、下标、旧值和新值。
>原理是先将旧值与node数组下标对应的值比较，若相等，则直接插入新值，返回true（表示操作成功）；
>若不相等，则表示数组已经被修改过，直接返回false（表示插入失败）。

若下标处node不为空，则用synchronized将node锁住，再遍历链表去存储值。

##### TreeMap
以红黑树作为存储结构。
###### Treemap是有序的吗？
是的。默认排序规则：按照key的字典顺序来排序（升序）。
也可以自定义排序规则（Comparator接口）
###### 是线程安全类吗？
不是线程安全的。若想线程安全，可通过如下方法：
```java
SortedMap m = Collections.synchronizedSortedMap(new TreeMap(...));
```
###### 红黑树的原理？
逻辑上是一棵“2-3-4树”。下图可知，2-3-4树是一棵“多叉树”，一个节点里可包含1~3个值。
包含一个值的叫2-节点（2个子树），对应红黑树的黑节点；
包含两个值的叫3-节点（3个子树），对应红黑树的一个黑根、一个红子；
包含三个值的叫4-节点（4个子树），对应红黑树的一个黑根、两个红子；
![331b6c466d9c52098902ba9ca263a71c.png](en-resource://database/963:1)

###### 为什么有二叉搜索树（BST），还要有平衡二叉树（AVL树）？为什么又要有红黑树？为什么又会有B-树？为什么又会有B+树？
* 二叉搜索树（BST）：
  查找最坏情况为O(N)，即树成链表状的时候
* 平衡二叉树（AVL）：
  在二叉搜索树的基础上，加上了“自平衡”的逻辑（左右子树树高超过1则对树进行旋转），避免树成为链表的形状；
  查找的平衡和最坏情况均为O(logN)；
* 红黑树（RBT）：
  红黑树也是一棵二叉搜索树，查找的时间复杂度最坏情况是O(2logN)，比平衡二叉树稍差，但因为插入、删除操作更简单，旋转操作更少，所以总体性能优于平衡二叉树
* B-树：
  B-树是一棵多路搜索树，通过增加阶数（增加子树数量），使树变得更“矮”，查询效率就可以比红黑树更高。
* B+树：
   B+树也是一棵多路搜索树，只不过B+树的数据只存储在叶子节点，且叶子节点之间增了指针相连，方便遍历和范围搜索。
   现在大多数数据库的数据以及索引都默认通过B+树来存储。可参考Mysql的数据及索引存储结构。


##### 队列、栈
###### Stack类（官方不建议使用）
* peek()
* pop()
* push(E)
###### Queue接口

**从尾部进队，从头部出队**  

* offer(e) 
* remove()  
* poll() 
* element()
* peek()

###### Duque接口（双向队列。既可当栈用，又可当队列用）
继承了Queue接口。  

**常用实现类**  
最常用：        LinkedList  
线程安全：      LinkedBlockingQueue
限制队列长度：  ArrayBlockingQueue  
有优先级的：    PriorityBlockingQueue  


***API***  

|   |第一个元素（头部）  |                |第一个元素（尾部）  |               | 对应Queue方法              |                   |
|---|---               |---             |---                |---            |---                        |---              |
|    |抛出异常          |特殊值（0或null）|抛出异常           |特殊值（0或null）|抛出异常                   |特殊值（0或null）|
|插入|addFirst          |offerFirst     |addLast            |offerLast      |add = addLast             |offer           |
|移除|removeFirst       |pollFirst      |removeLast         |pollLast       |remove = removeFirst         |poll            |
|检查|getFirst          |peekFirst      |getLast            |peekLast       |element = getFirst         |peek            |


* 当队列（Queue）用：
    * offerLast(e)（入队，也可以用offer(e)、add()、addLast(e)）
    * E pollFirst() （出队，也可以用poll()、remove()、removeFirst()）
    * E peekFirst()（获取头节点（first），也可以用peek()、getFirst()、element()）

* 当栈（Stack）用：
    * addFirst(e)（进栈）
    * removeFirst()（出栈）
    * peekFirst()（获取栈顶元素）

总结：
1. add、remove、get方法（Collection接口的方法）若失败，均会抛出异常；
2. offer、poll、peek方法（Queue接口的方法）若失败，则会返回null或false；
3. 后缀为First、Last的均为Deque接口的方法（因为是双端队列）；

***实现：***   
***非线程安全：LinkedList、ArrayDeque***   
***LinkedList***  
解决方法：Collections.synchronizedList()  
底层是链表：Node(first)-> Node-> Node-> Node-> Node(last)  

***ArrayDeque***  
变为线程安全：Collections.synchronizedList()  
底层是数组+head指针+tail指针：  
![d4f9e92ce7d22f49ae7f98d5c3062057.png](en-resource://database/957:1)  
如何扩容：  
申请一个更大的数组(原数组的两倍)，然后将原数组复制过去  

***线程安全：ConcurrentLinkedQueue（继承Queue接口）、BlockingQueue接口***

***ConcurrentLinkedQueue类***  
通过CAS操作操作（UNSAFE）实现资源同步（无锁）；  
队列中无数据时出队、或者队列满了时入队，都会操作失败，但不会阻塞线程，而是返回null或false；想阻塞的话可使用BlockingQueue的实现。  
特点：HOPS(延迟更新的策略)的设计  

***BlockingQueue接口及其实现***  
***BlockingQueue在入队出队失败后，应对方式比ConcurrentLinkedQueue灵活。***  
与ConcurrentLinkedQueue类一样，队列中无数据时出队、或者队列满了时入队，都会操作失败；  
但BlockingQueue为操作失败之后提供了多种应对方法：  

||抛异常 |特定值 |阻塞 |超时 |
|---|---|---|---|---|
|入队 |add(o) |offer(o) |put(o) |offer(o, timeout, timeunit) |
|出队| remove(o)| poll(o) |take(o) |poll(timeout, timeunit) |
|检查| element(o)| peek(o)||

此外还有双端队列BlockingDeque（继承了BlockingQueue）

***BlockingQueue的实现***  
|类名                   |描述                                               |初始大小                         |是否自动扩容|最大大小|
|---                    |---                                                |---                            |---         |---|
|ArrayBlockingQueue     |有界数组阻塞队列                                    |需在new对象时传入大小            |×           |初始大小|
|PriorityBlockingQueue  |有界优先级阻塞队列（插入的元素需要继承Comparable接口） |11                              |√           |2147483639(Integer.MAX_VALUE)|
|DelayQueue             |无界延迟队列（本质是PriorityQueue）                 |11                              |√           |Integer.MAX_VALUE - 8 |
|LinkedBlockingQueue    |有界链阻塞队列                                     |Integer.MAX_VALUE               |×           |初始大小|
|SynchronousQueue       |同步队列（内部同时只能够容纳单个元素）                 |                               |           |       |



#### JAVA事务
##### 1.事务的概念
事务（Transaction）：一般是指要做的或所做的事情。在计算机术语中是指访问并可能更新数据库中各种数据项的一个程序执行单元(unit)。

##### 2.事务四个属性（ACID）
1. atomicity 原子性  
原子性：操作这些指令时，要么全部执行成功，要么全部不执行。只要其中一个指令执行失败，所有的指令都执行失败，数据进行回滚，回到执行指令前的数据状态。
2. consistency 一致性  
 一致性：事务的执行使数据从一个状态转换为另一个状态，但是对于整个数据的完整性保持稳定。
3. isolation 隔离性  
隔离性：在该事务执行的过程中，无论发生的任何数据的改变都应该只存在于该事务之中，对外界不存在任何影响。只有在事务确定正确提交之后，才会显示该事务对数据的改变。其他事务才能获取到这些改变后的数据。
4. durability 持久性  
持久性：当事务正确完成后，它对于数据的改变是永久性的。

##### 3.并发事务导致的问题
在许多事务处理同一个数据时，如果没有采取有效的隔离机制，那么并发处理数据时，会带来一些的问题。

1. 脏读(Dirty Read)  
当一个事务读取另一个事务尚未提交的修改时，产生脏读。  
同一事务内不是脏读。 一个事务开始读取了某行数据，但是另外一个事务已经更新了此数据但没有能够及时提交。这是相当危险的，因为很可能所有的操作都被回滚，也就是说读取出的数据其实是错误的。

2. 不可重复读(Nonrepeatable Read)  
一个事务对同一行数据重复读取两次，但是却得到了不同的结果。同一查询在同一事务中多次进行，由于其他提交事务所做的修改或删除，每次返回不同的结果集，此时发生不可重复读。

3. 幻读(Phantom Reads)  
事务在操作过程中进行两次查询，第二次查询的结果包含了第一次查询中未出现的数据（这里并不要求两次查询的SQL语句相同）。这是因为在两次查询过程中有另外一个事务插入数据造成的。
当对某行执行插入或删除操作，而该行属于某个事务正在读取的行的范围时，会发生幻像读问题。

4. 丢失修改(Lost Update)  
第一类：当两个事务更新相同的数据源，如果第一个事务被提交，第二个却被撤销，那么连同第一个事务做的更新也被撤销。  
第二类：有两个并发事务同时读取同一行数据，然后其中一个对它进行修改提交，而另一个也进行了修改提交。这就会造成第一次写操作失效。  

##### 4.事务隔离级别
为了兼顾并发效率和异常控制，在标准SQL规范中，定义了4个事务隔离级别.

1. 读未提交(Read Uncommitted)
直译就是"读未提交"，意思就是即使一个更新语句没有提交，但是别的事务可以读到这个改变。  
Read Uncommitted允许脏读。  

2. 读已提交(Read Committed)
直译就是"读提交"，意思就是语句提交以后，即执行了 Commit 以后别的事务就能读到这个改变，只能读取到已经提交的数据。Oracle等多数数据库默认都是该级别。  
【原理】读已提交就是改变了释放锁的时机，让事务完成提交后再去释放锁。这样就解决了脏读问题。  
Read Commited 不允许脏读，但会出现非重复读。


3. 可重复读(Repeatable Read)：  
直译就是"可以重复读"，这是说在同一个事务里面先后执行同一个查询语句的时候，得到的结果是一样的。  
【原理】MVCC（行级锁的升级版，略）  
Repeatable Read 不允许脏读，不允许非重复读，但是会出现幻象读。  

4. 串行读(Serializable)  
直译就是"序列化"，意思是说这个事务执行的时候不允许别的事务并发执行。完全串行化的读，每次读都需要获得表级共享锁，读写相互都会阻塞。  
Serializable 不允许不一致现象的出现。  

通过不同的隔离级别，可以防止一些并发事务问题，同时级别越高则相应性能越低，这个设置需要根据实际场景进行设置。

常见商用数据库的默认隔离级别：
* MYSQL 可重复读（RR）
* ORACLE 读已提交（RC）
* SQL SERVER 读已提交（RC）

###### 不同隔离级别会遇到的问题
（✔为没有解决，✖为可解决）
| 隔离级别| 脏读| 不可重复读|幻读|
|---|---|---|---|
|读未提交|✔|✔|✔|
|读已提交|✖|✔|✔|
|可重复读|✖|✖|✔|
|串行读|✖|✖|✖|

###### Oracle、Sql Server 既然默认的隔离级别为“读已提交”，那么它们是怎么解决幻读和不可重复读的？
解决不可重复读：MVCC机制；  
解决幻读：MVCC机制+Next-key Lock锁；  



#### JAVA多线程

##### Java新建线程有哪几种方式？
* Thread
 ```java
 Thread t = new Thread();
 t.run();
 ```
 
 * Runnable接口
 ```java
  public static void main(String[] args) {
  Thread thread = new Thread(new MyRunnable());
  thread.start();
 }
public class MyRunnable implements >Runnable {
    @Override
    public void run() {
        // ...
    }
}
 
 ```
 * Callable接口
 ```java
 public static void main(String[] args) {
  FutureTask task = new FutureTask(new MyCallable());
  Thread thread = new Thread(task);
  thread.join(); //在thread.start之后，等待线程执行完之后，再继续往下执行
  System.out.println("join!");
  thread.start();
  System.out.println("after start!");
  System.out.println("result="+task.get());
  //最后输出顺序是：join! -> after start! -> generate 123! -> result=123
 }
 public class MyCallable implements 
  Callable<Integer> {
     public Integer call() {
     System.out.println("generate 123!");
     Thread.currentThread().sleep(5000);
     return 123;
     }
 }
 ```

##### 线程池ThreadPoolExecutor

问题：
* 创建线程的方式有哪些
* 实现runnable和callable的区别
* sleep和wait方法的区别
* 为什么不推荐用Executors里提供的线程池初始化的方法？


###### 线程池的核心参数
```java
//线程池实现类&核心参数
public class ThreadPoolExecutor extends AbstractExecutorService{

private final HashSet<ThreadPoolExecutor.Worker> workers;//线程集合
private final BlockingQueue<Runnable> workQueue;//等待被执行的任务队列
private final ReentrantLock mainLock;
private final Condition termination;
//RejectedExecutionHandler默认实现是ThreadPoolExecutor.AbortPolicy
private volatile RejectedExecutionHandler handler;//入任务队列失败的处理策略
private volatile ThreadFactory threadFactory;//创建线程的工具

}
```

###### 线程池的使用
1. 生成线程池
```java
/**
 【自定义线程池】
 参数：
 1、corePoolSize：核心线程数
        * 核心线程会一直存活，即使没有任务需要执行
        * 当线程数小于核心线程数时，即使有线程空闲，线程池也会优先创建新线程处理
        * 设置allowCoreThreadTimeout=true（默认false）时，核心线程会超时关闭
2、maxPoolSize：最大线程数
        * 当线程数>=corePoolSize、当前没有空闲线程、且任务队列已满时，线程池会创建新线程来处理任务
        * 当线程数=maxPoolSize、当前没有空闲线程、且任务队列已满时，线程池会拒绝处理任务而抛出异常
3、 keepAliveTime：线程空闲时间
        * 当线程空闲时间达到keepAliveTime时，线程会退出，直到线程数量=corePoolSize
        * 如果allowCoreThreadTimeout=true，则会直到线程数量=0
4.  线程空闲时间单位
5.  workQueue 工作队列，用于存储任务在任务被执行之前
6.  threadFactory 线程创建工厂，用于创建线程
7.  RejectedExecutionHandler 当workQueue工作队列达到容量上限时，对任务进行的拒绝策略。
**/
public ThreadPoolExecutor(int arg0, int arg1, long arg2, TimeUnit arg4,
BlockingQueue<Runnable> arg5, ThreadFactory arg6,
RejectedExecutionHandler arg7)

/**
ExecutorService是接口，ThreadPoolExecutor是该接口的实现
Executors.newCachedThreadPool() 等同于以下写法
**/
ExecutorService service =new ThreadPoolExecutor(0, Integer.MAX_VALUE,
60L, TimeUnit.SECONDS,  
new SynchronousQueue<Runnable>())  
 
```


2. 执行任务  
执行任务也有很多API可以用：  

    * 直接使用线程池  
      service.execute() 不支持返回结果

    ```java
        //新建线程池
        ExecutorService service =new ThreadPoolExecutor(0, Integer.MAX_VALUE,  
                                          60L, TimeUnit.SECONDS,  
                                          new SynchronousQueue<Runnable>());
        //无回参
        service.execute(new Runnable(){//...});
    ```

    * Future接口  
      service.submit() 支持返回结果，结果封装为Future  
      
    ```java
        //新建线程池
        ExecutorService service =new ThreadPoolExecutor(0, Integer.MAX_VALUE,  
                                          60L, TimeUnit.SECONDS,  
                                          new SynchronousQueue<Runnable>());
        //有回参的写法
        Future future = service.submit(new Callable(){//...});

        //获取执行结果（阻塞）
        future.get();

        //获取执行结果（等待一段时间，超时则抛出异常）
        //future.get(3,TimeUnit.SECONDS);
    ```

    * FutureTask类  
      Future的实现类，使用起来跟Future差不多  

        ```java

            public static void main(String[] args) {
                List<FutureTask> resultList = test();
                for(FutureTask task:resultList){
                    System.out.println((String)task.get());
                }
            }

            public List<FutureTask> test(){
                List<FutureTask> result = new ArrayList<>();
                //新建线程池
                ExecutorService service =new ThreadPoolExecutor(0, Integer.MAX_VALUE,  
                                                  60L, TimeUnit.SECONDS,  
                                                  new SynchronousQueue<Runnable>());

                for(int i=0;i<10;i++){
                    FutureTask<String> task = new FutureTask<String>(new Callable<String>() {
                        @Override
                        public String call() throws Exception {
                            return "hello";
                        }
                    });

                    service.submit(task);
                    result.add(task);
                }

                return result;
            }
            
        ```


    * CompletionService接口（JDK1.8前推荐用这个）
      有一个实现类为ExecutorCompletionService  
      推荐理由：可以使得先完成的任务先被取出，减少了不必要的等待时间
    
        ```java 
            //创建线程池，并且包一层CompletionService
            ExecutorService service =new ThreadPoolExecutor(0, Integer.MAX_VALUE,60L, TimeUnit.SECONDS,
            new SynchronousQueue<Runnable>());
            CompletionService<Integer> cService = new ExecutorCompletionService<Integer>(service);

            // 向里面扔10个任务
            for (int i = 0; i < 10; i++) {
                //这边不是直接通过线程池来提交任务，而是通过CompletionService来提交
                cService.submit(new WorkTask("ExecTask" + i));  
            }

            // 检查线程池任务执行结果
            for (int i = 0; i < 10; i++) {
                
                //获取执行结果。10个任务中，最先计算出结果的就最先被获得。
                int sleptTime = cService.take().get();
                System.out.println(" slept "+sleptTime+" ms ...");
            }
        ```

    * CompletableFuture类（JDK1.8后推荐用这个）  
      自带线程池(ForkJoinPool.commonPool())，无需手工创建。  
      当然也可以使用手动创建的线程池 

        ```java
        CompletableFuture comFuture = new CompletableFuture();
        //从线程池获取一个线程并执行任务（无返回值）
        Runnable runnable = new Runnable() {
            @Override
            public void run() {
                Integer intResult = 1+1;
            }
        };
        CompletableFuture.runAsync(runnable);

        //从线程池获取一个线程并执行任务（有返回值）
        try {
            Integer result = CompletableFuture.supplyAsync(()->{
                Integer intResult = 1+1;
                return intResult;
            }).get();
        } catch (InterruptedException e) {
            throw new RuntimeException(e);
        } catch (ExecutionException e) {
            throw new RuntimeException(e);
        }

        //多任务
        CompletableFuture
                .supplyAsync(()->1+1) //获取一个线程并执行
                .exceptionally((exception)->{ System.out.println(exception.getMessage()); return 0;})//若抛异常则这样处理
                .thenApply((result)->x+2)  //使用上一步的线程，对上一步的结果进行处理
                .handle((result,exception)->x+3) //使用上一步的线程，对上一步的结果进行处理（若抛了异常也可处理）
                .whenComplete((result,exception)->System.out.println("x="+x)); //最后一步处理
        
        //多任务2
        CompletableFuture
                .supplyAsync(()->1+1) //获取一个线程并执行
                .thenApplyAsync((x)->x+2);  //获取一个新线程，处理上一步的结果

        //生成UUID：谁先计算出结果，就用谁的
        CompletableFuture<String> future1 = CompletableFuture
                .supplyAsync(()->"123456");

        CompletableFuture<String> future2 = CompletableFuture
                .supplyAsync(()->"567890");

        String myuuid = (String)CompletableFuture.anyOf(future1,future2).get();

        //使用自定义线程池
        ExecutorService service =new ThreadPoolExecutor(0, Integer.MAX_VALUE,60L, TimeUnit.SECONDS,
                new SynchronousQueue<Runnable>());
        CompletableFuture<Integer> future3 = CompletableFuture.supplyAsync(()->1+1,service);
        Integer result3 = future3.get();
        ```

3. 关闭线程池

```java
service.shutdown();
```

###### 任务队列满后的拒绝策略
    * AbortPolicy（直接抛出异常，任务没有被执行。默认策略）
    * DiscardOldestPolicy（放弃队列中最旧的任务，然后将新任务入队）
    * DiscardPolicy（直接放弃任务，什么都不做）
    * CallerRunsPolicy（会调用当前线程池的所在的线程去执行被拒绝的任务，缺点是会阻塞主线程）

##### 线程池创建工具：Executors类（已不建议使用，建议直接new ThreadPoolExecutor）

|线程池|corePoolSize|maxPoolSize|workQueue|workQueue初始化大小|拒绝策略|描述|
|---|---|---|---|---|---|---|
|Executors.newFixedThreadPool()       |new对象时传入|new对象时传入     |LinkedBlockingQueue |Interger.MAX_VALUE|AbortPolicy| 定长线程池。LinkedBlockingQueue默认size为Integer.MAX_VALUE，过多任务进队时会导致OOM|
|Executors.newCachedThreadPool()      |0           |Integer.MAX_VALUE|SynchronousQueue    |                  |AbortPolicy||
|Executors.newScheduledThreadPool()   |new对象时传入|Integer.MAX_VALUE|DelayedWorkQueue    |16                |AbortPolicy|DelayedWorkQueue可变长，即队列是无界的，过多任务进队时会导致OOM|
|Executors.newSingleThreadExecutor()  |1           |1                |LinkedBlockingQueue |Interger.MAX_VALUE|AbortPolicy|只有一个线程。LinkedBlockingQueue默认size为Integer.MAX_VALUE，过多任务进队时会导致OOM|


(1). 获取线程池
 * 可变长线程池
```java
//生成一个可变长线程池
ExecutorService pool = Executors.newCachedThreadPool()
```

* 定长线程池
 ```java
 //生成一个定长线程池
 ExecutorService pool = Executors.newFixedThreadPool()
```

* 轮询线程池
 ```java
 //生成一个定长线程池（支持定时、周期性任务）
 ExecutorService pool = Executors.newScheduledThreadPool()
```

 (2).获取线程执行任务
 ```java
 //
 Future future = pool.execute(new Runnable(){});
 ```


##### SpringBoot的异步执行方法

通过@EnableAsync + @Async配置，即可使用SpringBoot维护的线程池

```java
//在启动类增加@EnableAsync注解
@EnableAsync
@SpringBootApplication
public class ManageApplication {
    //...
}
```


```java
@Service
public MyService{
    //定义一个异步执行的函数，被调用时使用默认的线程池执行
    @Async 
    public void test(){//...}

    //使用名称为pool2的自定义线程池
    @Async(value="pool2")
    public void test2(){//...}
}

```

默认的线程池配置：
```
#核心线程数
spring.task.execution.pool.core-size=8
#最大线程数
spring.task.execution.pool.max-size=Integet.MAX_VALUE
#空闲线程保留时间
spring.task.execution.pool.keep-alive=60s
#队列容量
spring.task.execution.pool.queue-capacity=Integet.MAX_VALUE
```



##### volatile Integer 和AtomicInteger的区别？

AtomicInteger： 本身即可满足线程安全的三个条件：原子性、可见性、有序性；  
  
volatile Integer：只能满足可见性和有序性，所以遇到 i++这种操作，还是需要先加锁再操作。  


##### JAVA里线程（Thread）的生命周期？

新建（new）  
就绪（runnable）  
运行 （running）  
阻塞（blocking）  
睡眠（time waiting，等待通知或等待一段时间）  
等待（waiting，等待通知）  
结束（dead）  


##### Thread里的函数解析

```java
/**在调用interrupt时，若thread正被阻塞（等待IO、或在synchronized代码块前等待锁），则可让thread停止阻塞，抛出异常。
若thread没有被阻塞，则不会抛出异常。
若想在运行时也可以中断，可在thread的run()中加入isInterruped()的判断。
**/
thread.interrupt();
/**
开始线程
**/
thread.start();
/**
线程休眠（不会交出占用的锁）
**/
thread.sleep(long millis);
/**
线程是否活跃
**/
thread.isAlive();
/**
thread线程阻塞当前线程，当thread执行完后，当前线程才继续执行
**/
thread.join();
```
##### Thread如何从一个生命周期进入另一个生命周期？

新建（new）
```java
public static void main(String[] args){
//此时进入新建态
Thread thread = new MyThread();
}
```
新建（new） --> 就绪（runnable）
就绪（runnable） --> 运行 （running）
```java
thread.start(); 
//调用start()后获取到CPU时间片，start()再调用run()，此时才算正式进入运行态
```

运行 （running） --> 阻塞（blocking）
```java
//1.遇到同步代码块时，thread便会被阻塞
public class MyThread {
    public void run(){
        synchronize(this){
           // ...
        }
        
    
    }
}
```

运行 （running） --> 睡眠（time waiting）
运行 （running） --> 等待（waiting）
```java
//第一种：lock.wait()
public class MyThread {
    Object lock = new Object();
    public void run(){
        //为什么wait()、notify()、notifyAll()需要在synchronized里执行？
        //语法上：不写在synchronized内，运行时会抛出异常
        //原理上：当前线程必须拥有对象的monitor（内置锁），调用对象的wait()、notify()、notifyAll()方法时才能有锁可以释放
        synchronized( lock ){
            try {
                    // ...
                    //释放lock对象，线程等待1s，1s后重新竞争lock对象，竞争到lock对象后继续往下走
                    lock.wait(1000);
                } catch (InterruptedException e) {
                      e.printStackTrace();
                }

        }
    }
}
```
```java
//第二种：thread.join() (实质是调用了thread.wait())
public static void main(String[] args){
//此时进入新建态
Thread thread = new MyThread();
//等待thread执行完后，主线程再执行（等于异步变同步）
thread.join(1000);
}
```
```java
//第三种：
public class MyThread {
public void run(){
    LockSupport.pack(this);//当前线程等待，直至其他线程调用LockSupport.unpack(this)
}
//等待thread执行完后，主线程再执行（等于异步变同步）
thread.join();
}
```
睡眠（time waiting） --> 就绪（runnable）
```sql
/**
1. lock.nofity() or lock.notifyAll() ; //对应第一种方式
2. LockSupport.unpack(this) //对应第三种方式
**/
```


##### 有哪些常见的锁？

|类型|可重入|悲观锁|可中断锁|公平锁|互斥锁|独占锁|
|---|---|---|---|---|---|---|
|synchronized|√|√|×|×|√|√|
|ReentrantLock|√|√|√|都可，默认非公平|√|√|
|ReentrantReadWriteLock|√|√|×|都可，默认非公平|×(读写锁)|读锁是共享锁，写锁是独占锁|
|Unsafe.compareAndSwap()|-|×|-|-|×(自旋锁)|-|
|Semaphore|-|√|-|都可，默认非公平|-|×|
|CountDownLatch|-|√|-|都可，默认非公平|-|×|


* 可重入？
当某个线程已调用该锁并持有，在被锁的代码段里面仍可以继续调用锁，则该锁是可重入锁；
换言之，当某线程调用锁，进入代码，然后再次调用锁，若此时不能继续进入该代码，则是不可重入锁。

* 悲观or乐观？
当读、写都要调用锁，悲观锁；
读写都不锁，只有在写时检测是否有冲突，有冲突则返回错误给用户，乐观锁；
* 可中断？
当线程A想要的锁一直被另一线程B占据，若线程A需要一直阻塞等待锁，即是不可中断锁；
反之，若线程A可等待一段时间后返回失败，则是可中断锁。
* 公平？
若线程A最先请求锁，最终亦是线程A最先获得锁，那么该锁便是公平锁；
反之，若线程A不一定最先获得锁，便是不公平锁。
* 互斥or读写？
当读、写都要调用锁，互斥锁；
当读时，大家可以共用该锁；写时，只有一人可以占用该锁，读写锁；
* 互斥or自旋？
互斥锁(mutex)：竞争锁失败时，线程会挂起，进入阻塞态；等待解锁后，重新竞争CPU时间片
自旋锁(spin)：竞争锁失败时，线程不会挂起，而是一直轮询锁是否能用一段时间；一段时间内还获取不到，才挂起线程，进入阻塞态。
* 独占or共享？
独占锁（写锁）：一个资源只能被线程上一次独占锁
共享锁（读锁）：一个资源能被多个线程上多次共享锁



##### 乐观锁的优点



##### 关于Java乐观锁、自旋锁：Unsafe类
像原子类（AtomicInteger等类）、AbstractQueuedSynchronizer（AQS，ReentrantLock、CountDownLatch的底层实现），都是通过Unsafe类的CAS操作（乐观锁操作）实现。

> 既然底层是通过乐观锁实现，那为什么说ReentrantLock是悲观锁？
> 


如何实例化Unsafe对象
> 由Unsafe类的源码可知，只有JDK自带的类（即BootstrapClassLoader加载的类），才能使用getUnsafe()获取unsafe类
> 若开发者想自己获取unsafe类，只能通过反射方式获取（如下示例）：
```java
    public static void main(String[] args)
        throws NoSuchFieldException, IllegalAccessException {
        Field theUnsafe = Unsafe.class.getDeclaredField("theUnsafe");
        theUnsafe.setAccessible(true);
        Unsafe unsafe = (Unsafe) theUnsafe.get(null);
        System.out.println(unsafe);
    }
```

Unsafe类核心代码
```java
public final class Unsafe {
    private static final Unsafe theUnsafe;

    private Unsafe() {
    }

    @CallerSensitive
    public static Unsafe getUnsafe() {
        Class var0 = Reflection.getCallerClass();
        if (!VM.isSystemDomainLoader(var0.getClassLoader())) {
            throw new SecurityException("Unsafe");
        } else {
            return theUnsafe;
        }
    }

    //...
}
    
```


##### CAS操作
Unsafe提供了3个CAS函数，来实现乐观锁。
所谓CAS函数，即compareAndSwapObject()、compareAndSwapInt()、compareAndSwapLong()。
它包含3个入参：要修改变量的内存位置、预期原值、要修改为的值
比如，有一个student对象，要使用自旋锁的方式，修改它的name属性。
CAS操作，若成功则返回true，失败则返回false
```java

    public void test(Student student,String newName){
        //1.获取unsafe，略
        try {
            //2.使用Unsafe类，实现上自旋锁修改name属性
            long valueOffset = unsafe.objectFieldOffset(Student.class.getDeclaredField("name"));
            String oldName = unsafe.getAndSetObject(student, valueOffset, newName);
            //getAndSetObject()是自旋锁实现，实际也是调用compareAndSwapObject()方法
        } catch (Exception var1) {
            throw new Error(var1);
        }
    }
```




##### JUC简介
![e3197fc4e1ad1393da309bdad29de4d8.png](en-resource://database/959:1)

###### AQS
* 什么是AQS？

抽象的队列式的同步器。

ReentrantLock/Semaphore/CountDownLatch的底层实现。


##### Lock接口
为什么有synchronized，还需要再提出Lock接口？

因为synchronized无法解决线程死锁的问题。
产生线程死锁需要满足以下条件：
1. 互斥条件（一段时间内某一资源仅为一线程所持有）
2. 请求和保持条件（当线程请求资源而阻塞时，不会释放自己拥有的资源）
3. 不可抢夺条件（线程为使用完的资源不能被剥夺，只能在使用完时释放）
2. 环路等待条件（在发生死锁时，必然存在一个进程--资源的环形链，如下）
线程1占有锁1，等待锁2；
线程2占有锁2，等待锁3；
...
线程N占有锁N，等待锁1。

解决线程死锁，一般方法是破坏条件3，即“不可抢夺条件”。

想破坏“不可抢夺条件”，有如下方法：
1. 线程能响应中断（对应lock.lockInterruptibly()）
2. 线程可以非阻塞地获取锁（对应lock.tryLock()，获取不到立即返回false）
3. 支持超时（对应lock.tryLock(long time)）

从上面可看到，JDK1.5后发布的Lock接口有对应的API支持。

###### ReentrantLock
三个内部类：Sync、NonfairSync、FairSync。NonfairSync、FairSync集成Sync，而且都是AQS的实现类。

FairSync类：采用公平策略获取锁
NonfairSync：采用非公平策略获取锁（默认）


#####  JUC Tools
###### Semaphore
Semaphore称为计数信号量，它允许n个任务同时访问某个资源，可以将信号量看做是在向外分发使用资源的许可证，只有成功获取许可证，才能使用资源。
```java
//假设只能允许10个用户同时在线
Semaphore semaphore=new Semaphore(10);
//用户登录，占用1个信号量
boolean isLogin= semaphore.tryAcquire();
//查看剩余多少用户可以登录
int restCount=semaphore.availablePermits();

```
###### CountDownLatch
CountDownLatch典型的用法是将一个程序分为n个互相独立的可解决任务，并创建值为n的CountDownLatch。当每一个任务完成时，都会在这个锁存器上调用countDown，等待问题被解决的任务调用这个锁存器的await，将他们自己拦住，直至锁存器计数结束。
```java
//定义了一个计数器，当前计数为1
CountDownLatch begin = new CountDownLatch(1);
//阻塞当前线程，等待计数器到0
begin.await();
//（在另外的线程）任务完成，计数-1
begin.countDown();
```
###### LockSupport




