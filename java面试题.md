# 											java面试题

## 一、java 基础 

### 1 . 1 线程 

#### (1)什么是 串行 并行 并发 

串行：一次只能执行一个任务，并且是按顺序执行
并行：同一时刻同时执行多个任务，并且互不干扰
并发：多个任务可以交替执行

#### (2)线程创建的方式 

- 继承Thread类：自定义一个MyThread类，继承Thread类，覆写run()方法，创建Thread子类实例，通过Thread调用start()方法启动线程。

- 实现runnable接口：自定义一个MyRunnable类，实现Runnable接口，覆写run()方法，创建MyRunnable对象，将myRunnable作为参数调用Thread有参构造，创建Thread，调用start()。


- 实现Callable接口：自定义一个MyCallable类，实现Callable接口，覆写call()方法(方法返回值为Callable泛型)，创建myCallable对象实例，再创建FutureTask对象，将myCallable最为参数传入FutureTask，得到futureTask对象，最后创建Thread对象，将futureTask作为参数传入，调用start方法启动线程。(想要获取线程执行后的返回值，用futureTask调用get方法，获取返回值)


	三种方式有什么区别：
	
		1、java只支持单继承多实现，实现接口的方式扩展性更强。
	
		2、继承Thread类的方式说明这个类就是一个线程类，而实现Runnable和Callable接口说明这个类是一个线程任务，需要创建线程对象，从而执行     这个线程任务。
	
		3、Thread类和Runnable接口覆写的run()方法都没有返回值，Callable接口的call()方法有泛型返回值，可以声明异常
	   start()&run()的区别：
	        调用start()才是启动线程，如果只是调用run()或者call()方法，都只是调用实例方法，并不是启动线程。

#### (3)线程池有哪几种？线程池的核心参数有哪些？

| 方法                             | 描述                                                 |
| -------------------------------- | ---------------------------------------------------- |
| newFixedThreadPool(int nThreads) | 获取固定数量的线程池。参数：指定线程池中线程的数量。 |
| newCachedThreadPool()            | 获得动态数量的线程池，如不够则创建新的，无上限。     |
| newSingleThreadExecutor()        | 创建单个线程的线程池，只有一个线程。                 |
| newScheduledThreadPool()         | 创建固定大小的线程池，可以延迟或定时执行任务。       |

##### 线程池的核心参数：

| 参数                              | 描述                                                         |
| :-------------------------------- | ------------------------------------------------------------ |
| int corePoolSize                  | 核心线程数，即使这些线程处于空闲状态，他们也不会被销毁（除非设置了allowCoreThreadTimeOut） |
| int maximumPoolSize               | 最大线程数                                                   |
| long keepAliveTime                | 当线程数大于核心时，多于的空闲线程最多存活时间               |
| TimeUnit unit                     | 空闲线程存活时间单位                                         |
| BlockingQueue<Runnable> workQueue | 工作队列，当线程数超过核心线程数时用于保存任务的队列         |
| ThreadFactory threadFactory       | 线程工厂，执行程序创建新线程时使用的工厂                     |
| RejectedExecutionHandler  handler | 饱和策略，阻塞队列已满且线程数达到最大值所采取的饱和策略。   |



	( 4 )你在项目中有没有用到多线程？怎么用的？ 


​	
#### ( 5 )如何解决多线程的线程安全问题 

- 加锁
- 使用volatile关键字
- 建立副本ThreadLocal
- 使用线程安全的类：原子类、ConcurrentHashMap、 StringBuffer



#### ( 6 ) wait sleep 方法的作用和区别？线程的 join 礼让 打断 是怎么样实现的。

- 所属类不同：sleep来自Thread类，wait来自object类

- 使用的位置不同：sleep在代码的任意位置都可以使用，而wait只能在synchronize方法和	synchronize代码块中使用。

- 在同步代码块中释放锁：sleep不用释放锁，wait需要释放锁。

- 用法不同：sleep到时会自动恢复，wait需要使用notify(),notifyAll()直接唤醒

  

- 
  yield()：让出CPU，当前线程让出CPU给其他线程执行，但自己也会进出就绪状态参与CPU的争抢，因此调用yield方法后，任然有可能是自己继续获得CPU。

- join()：加入线程，会将调用的线程加入当前线程，等待加入的线程执行完后才会继续执行当前线程。

- 打断：

#### ( 7 ) synchronized 锁还有 lock 锁区别 

- syncchronized是java中的一个关键字，lock是java.util.concurrent.Locks下的一个接口

- synchronize使用后会自动释放锁，lock需要手动上锁，手动释放锁（在finally块中）

- lock提供了更多的实现方法，而且可响应中断，可定时；而synchronized关键字不能响应中断。

  ```
  	响应中断：A、B线程想要同时获取到锁，A获取到锁以后，B会进行等待，这时候等待着锁的线程B，会被Tread.interrupt()方法给中断等待状态，然后去执行其他的事情，而synchronized锁无法被Tread.interrupt()方法给中断。
  ```

  

- synchronized关键字是非公平锁，即，不能保证等待获取锁的那些线程们的顺序，而Lock的子类ReentrantLock也是非公平锁，但是可以通过一个参数的构造方法来实例化一个公平锁。

  ```
  公平锁与非公平锁：
  - ​	公平锁就是：先等待的线程，先获得锁。
  - ​	非公平锁就是：不能够保证 等待锁的 那些线程们的顺序，
  - ​	公平锁因为需要维护一个等待锁资源的队列，所以性能相对低下；
  ```

- synchronized无法判断是否已经获取到锁，而Lock通过tryLock()方法可以判断是否获取到锁

- 二者底层实现不一样，synchronized是同步阻塞，采用的是悲观并发策略，Lock是同步非阻塞，采用的是乐观并发策略（底层基于volatile关键字和cas算法实现）

  ```
  cas算法：即compareandset（比较并交换）。把内存值M更新成新值之前需要先比较内存值和期望值E是否相等，如果相等就把旧的内存值替换成新值，否则就什么都不做。
  ```

  



### 1 . 2 集合 

#### 	(1)集合的体系，不同集合的特性和存储原理是什么。

|  Collection体系集合   |
| :-------------------: |
| ![](Pictures\002.png) |

​	java集合可分为Collection和Map两种体系。

​	**Collection接口：单列集合，定义了存取一组对象的方法的集合**

------

​	**List接口：有序，有下标，可重复**	

------

***	LinkedList：双向链表结构实现，增删快查询慢。***	  

- void add(int index, Object o) 添加数据：

  ```
  - ​	尾部插入：
  
    ​	1、创建一个新节点，前驱结点指向原尾节点，后继节点指向null；
  
    ​	2、更新新节点为尾节点；
  
    ​	3、判断头节点是否为空？把新节点作为头节点：原尾节点的后继节点指向新节点
  
  - ​	指定位置插入：
  
    ​	1、获取指定下标的节点，并获取这个节点的前一个节点；
  
    ​	2、创建一个新节点，前驱节点指向前一个节点，后继节点指向后一个节点；
  
    ​	3、把前一个节点的后继节点指向新节点，之前获取指定下标位置节点的前驱节点指向新节点
  ```

- ​	Object get(int index) 获取指定下标元素：

  ```
  根据下标位置判断从前往后遍历还是从后往前遍历
  ```

- ​    删除数据：

  ```
  	1、获取指定要删除的节点，这个节点的前一个节点和后一个节点。
  
  ​	2、判断上一个节点是否为null？说明这个节点是头节点，把下一个节点赋值为first节点：把上一个节点的后继节点指向下一个节       	点，删除当前节点的前驱节点。
  
  ​	3、判断下一个节点是否为null？说明这个节点是尾节点，把上一个节点赋值为last节点：把下一个节点的前驱节点指向上一个节点，删除当前节点的后继节点
  ```

  ------

  **ArrayList：数组结构实现，查询快增删慢。**

- void add(int index, Object o)  存：

  ```
  	与一般的数组操作元素一样，先判断数组的大小是否足够，然后插入到相应位置。若插入到数组末尾，只需要将元素放入后size++；如若要插入到指定位置，需要将其后面的元素全部往后移一位在进行插入。
  ```

- 删：

  ```
     与一般的数组操作元素一样，只需要将其后面的元素全部往前移动一位，最后空出来的元素为空；如果要删除指定元素，需要先从头到尾全部遍历一遍，删除与之相等的元素，删除操作之后也需要将其后的元素全部往前移动一位。
  ```

------

**Set接口：无序，无下标，不可重复。**

​		**HashSet：基于hashcode实现元素不重复，当存入元素的哈希码相同时，会调用==或equals进行确认，如果返回true，就拒绝存入元素。（底层调用了各种hashmap的api来实现）**

​		**LinkedHashSet：基于链表实现的hashset，按照链表进行存储，即可保留元素的插入顺序。**

​        **TreeSet：基于排列顺序实现元素不重复；实现了SortedSet接口，对集合元素自动排序。**

------

**Map接口：双列集合，保存具有“key-value键值对”的集合**

```java
 public V put(K key , V value){
 		return putVal(hash(key), key, value, false, true);
 }
 
 static final int hash(Object key) {
        int h;
        return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);
    }
    	创建hashmap时，数组的默认长度是16。
  		1、首先hashmap方法接收到key和value时会根据put的key值进行hash运算的到key对应的hash值，是为了让key的hash值与其无符号右移					16位后的值进行运算（即将该hashcode的高位和地位全部参与到运算中，使计算出来的哈希值更加散列，减少哈希冲突）。
  		2、然后才会调用putVal方法，先将通过运算的到的哈希值与（数组长度-1）进行与运算得到一个数组的下标（二次散列，减少哈希冲突，下标					分布更加均匀）。
  		3、如果该下标是空的，那么直接把k,v封装成一个node对象存入此下标位置
  		4、如果下标元素非空，说明此下标已经存在node对象，则判断该node是不是一个红黑树节点，如果是就将k,v封装成一个红黑树节点，并添加				 到红黑树上去，这个过程还会判断红黑树中是否存在相同的key，如果存在就会更新这个key的value
  		5、如果这个小标位置上是个链表节点，那就将k，v封装成一个node对象插入到链表尾部。
  		6、插入到链表后会判断当前链表长度是否超过8个，如果超过8个就将链表转化成红黑树（这个过程会判断key是否存在，如果存在就会更新					 value）
  		7、最后会判断hashmap长度是否超过阈值，如果超过就要扩容。
  		
```

#### ( 2 ) Map中的集合有哪些，线程安全的集合有哪些， HashMap HashTable concurrentHashMap 集合的区别 

```
Map中的集合：
	HashMap：
	·线程不安全
	·数据结构为数组+链表+红黑树（jdk1.8后新加入的，为了解决链表过长导致查询效率变慢的问题）
	·创建hashmap实例后数组的初始容量为16
	·扩容机制：双倍扩容
	·扩容条件：数组长度*0.75f（负载因子）
	·储存的是k-v键值对，可以为空，插入无序，value可以重复，key不能重复，如果key重复则会进行覆盖；
	
	LinkedHashMap：
	·线程不安全
	·数据结构为哈希表
	·储存的是k-v键值对，可以为空，插入有序，value可以重复，key不能重复。
	TreeMap：
	·线程不安全
	·数据结构为红黑树
	·储存k-v键值对，key按照大小进行顺序排序
	·key不能为null，value可以为null
	·key不能重复，value可以重复
	
	线程安全的集合：
	Vector、HashTable、Properties、ConcurrentHashMap是线程安全的。
	
	HashMap HashTable concurrentHashMap 集合的区别：
	·HashMap是线程不安全的，在多线程环境中不适合使用，key允许为null。
	·HashTable和ConcurrentHashMap是线程安全的，其中HashTable在多线程环境中不推荐使用，而后者是线程中使用哈希表的最优解，这两者的	 key都不允许为null
	
	
扩展：
	原因（如下图）：HashTable在关键方法中给自身的对象加了一把大锁。
	带来的缺点：
		1、多线程修改变量中会发生锁冲突，即使不在一个哈希表。
		2、扩容速度十分慢。
		3、size属性同步时也会发生锁冲突，造成效率慢。
```

![](/Users/why/Desktop/github仓库(java面试)/Pictures/7abb1aee817f45c6920ac67a8c214171.png)



```
ConcurrentHashMap的锁策略（如下图）是为每一个哈希桶都分配一把锁，这样就只有多线程在同一个哈希桶内修改时会发生锁冲突，因为链表结构前后数据有关联，所以锁冲突大大降低了。
```

![](/Users/why/Desktop/github仓库(java面试)/Pictures/8eb5fcd5ac3f47ee8b1dc4bd7548dca8.png)

#### ( 3 )遍历迭代集合的方式有哪些？ 

- List集合：for循环，增强for循环，Iterate迭代器

- Set集合：增强for循环，Iterate迭代器

- Map：

  ```java
  	1.增强for循环遍历map的key
  	for (String key : map.keySet()) {
  		  Integer value = map.get(key);
        System.out.println(key + " -  " + value);
    }
  
  	2.增强for循环可以和value
  	for (Map.Entry<String, Integer> entry : map.entrySet()) {
        String key = entry.getKey();
        Integer value = entry.getValue();
        System.out.println(key + " - " + value);
     }
  ```

  ###### 了解

- ##### Queue队列

  ```java
  queue是一种经常使用的集合，遵循先入先出的基本原则（FIFO）
    
    1. 一般地，只允许前端删除，后端插入
  
    2. 无限队列：常见实现类LinkedList
  
    3. 有限队列：ArrayBlockingQueue(长度大小受限制)
       
  for(String s : queue) {
  	 System.out.println(s);
  }
       
  Iterator it = queue.iterator();
  while(it.hasNext()) {
  		System.out.println(it.next());
  }	
  		
  while(!queue1.isEmpty()) {
  		System.out.println(queue1.poll());
  }
  ```

- ##### Queue双端队列

  ```java
  deque队列只能一端进队，在另一端出队（FIFO）
  双端队列：允许两头都进，两头都出（Double Ended Queue）,Deque.
  
   方法一：while循环遍历同时进行出队操作
      String item = "";
  		while((item = deque.pollLast()) != null) {
  			System.out.println(item);
  		}
  		
  方法二：for each遍历（仅从队头开始）
   for(String s:deque){
  			System.out.println(s);
  		}
  
  方法三：Iterator迭代器
      Iterator it = deque.iterator();
  		while(it.hasNext()) {
  			System.out.println(it.next());
  		}
  ```

- ##### Stack栈

  ```java
  遵循后进先出原则（LIFO）
  
  1.增强for循环
  for(String s : stack) {
   		System.out.println(s);
  }
  
  2.while循环
  while(!stack.isEmpty()) {
  		System.out.println(stack.pop());
  }
  
  3.迭代器
  Iterator it = stack.iterator();
  while(it.hasNext()) {
  		System.out.println(it.next());
  }
  ```

  

###  1 . 3 String 

#### 	(1)字符串创建的方式有哪些 

- String
- StringBuilder
- StringBuffer

#### 	( 2 ) String StringBuffer StringBuilder 之间的区别 

- 从**可变性**来说：String底层使用了char数组，使用final修饰了，所以不可变，StringBuilder和StringBuffer是可变的。
- 从**安全**来说：String是不可变的，也可以理解为常量，所以线程安全。StringBuffer对方法加了同步锁或者对调用的方法加了同步锁，所以是线程安全的。StringBuilder并没有对方法加同步锁所以线程不安全。
- 从**性能**来说：每次对String类型进行修改的时候都会生成一个新的String对象，然后指针指向新的String对象。StringBuffer每次都会对StringBuffer本身进行操作，而不是生成新的对象并改变对象引用。StringBuilder效率会高一些，而StringBuffer加了同步锁虽然线程安全，但是性能有所下

#### 	( 3 )字符串 String 类能不能被继承？为什么？

- String类是不能被继承的，因为String类被final修饰。

#### 	( 4 )字符串有哪些常用的 API 

- split()：把字符串分割成字符串数组
- subString()：截取字符串（区间是左闭右开）
- Replace()：字符串替换
- charAt()：获取指定位置的字符
- indexOf()/lastIndexOf()：获取指定字符出现的索引位置/获取最后一次字符出现的索引位置
- toCharArray()：将字符串转化成char类型数组

#### 	( 5 ) JAVA 中常见的包装类有哪些？Integer 存储元素的原理是什么？Integer i = 128  ,  Integer ii = 128;  i == ii ? 

| 基本数据类型 | 包装类型            |
| ------------ | ------------------- |
| byte         | java.lang.Byte      |
| short        | java.lang.Short     |
| int          | java.lang.Integer   |
| long         | java.lang.Long      |
| float        | java.lang.Float     |
| double       | java.lang.Double    |
| boolean      | java.lang.Boolean   |
| char         | java.lang.Character |

储存原理：Integer类在对象中包装了一个基本类型int的值，也就是说每个Integer对象都包含了一个int类型的字段。

```java
Integer i= 128 , ii = 128;
System.out.printlt(i == ii); //false
Integer.class在装载时，其内部类型IntegerCache的static块即开始执行，实例化并暂存数值在-127 ~ 128之间的Integer对象。
当自动装箱int类型的值在这个范围里面，就直接返回暂存的Integer对象。
```

#### 	( 6 )值传递和引用传递之间的区别是什么 ？

- 值传递：在调用方法时将实际参数复制一份传递到函数中，这样如果在方法中对参数进行修改，将不会影响到实际参数（基本数据类型，形式参数的改变不会影响到实际参数的值）
- 引用传递：在调用方法时将实际参数的地址传递到方法中，那么方法对实际参数的修改就会影响到实际参数的值。（对于引用类型的参数传递，形式参数的修改会影响实际参数的值）

[值传递和引用传递之间的区别](https://blog.csdn.net/mengfanshaoxia/article/details/121128992?ops_request_misc=&request_id=&biz_id=102&utm_term=%E5%80%BC%E4%BC%A0%E9%80%92%E5%92%8C%E5%BC%95%E7%94%A8%E4%BC%A0%E9%80%92%E4%B9%8B%E9%97%B4%E7%9A%84%E5%8C%BA%E5%88%AB%E6%98%AF%E4%BB%80%E4%B9%88%20&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduweb~default-1-121128992.142^v67^control,201^v3^control_2,213^v2^t3_control1&spm=1018.2226.3001.4187)

#### 	( 7 )什么是对象的浅拷贝？什么是深拷贝？浅拷贝和深拷贝之间的区别？ 

- 浅拷贝：是会将对象的每个属性进行依次复制，，但是当对象的属性是引用类型时，实际上复制的是其引用，当引用指向的的值改变时也会跟着改变。

- 深拷贝：复制变量值，对于引用数据，则递归至基本类型后，再复制。深拷贝后的对象和原来的对象是完全隔离的，互不影响，对一个对象的修改并不会影响到另一个对象。

  浅拷贝和深拷贝之间的区别：通俗来讲，浅拷贝拷贝的是其引用，当引用指向的值发生改变时也会随之发生改变；深拷贝则是与原来对象完全隔离，互不影响。

  

  思维导图：

  ![](/Users/why/Desktop/github仓库(java面试)/Pictures/20191127092041200.png)

### 1 . 4  io

#### 	 ( 1 )常见的 io流有哪些？

|                           主要io流                           |
| :----------------------------------------------------------: |
| ![](/Users/why/Desktop/github仓库(java面试)/Pictures/20160522165107051.png) |

[java常用io流](https://blog.csdn.net/u013733643/article/details/123933847?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522167012788916782428687467%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=167012788916782428687467&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduend~default-2-123933847-null-null.142^v67^control,201^v3^add_ask,213^v2^t3_esquery_v2&utm_term=java常见的io流&spm=1018.2226.3001.4187)

#### 	( 2 )序列化反序列化操作为什么要实现serializable 接口？

- 序列化：把对象转化成字节序列的过程。

- 反序列化：把字节序列恢复成对象

  serializable的作用：

  1.存储对象在存储介质中，以便在下次使用时很快捷的重建一个副本

  2.以便数据传输，尤其是在远程调用的时候

  3.serializable接口是启用其序列化的接口。实现serializable接口的类是可序列化的。没有实现此接口的类将不能使他们的任意状态被序列化和反序列化。

### 二、Mysql 数据库

[mysql面试题](https://blog.csdn.net/ThinkWon/article/details/104778621?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522167022963516782425625298%2522%252C%2522scm%2522%253A%252220140713.130102334.pc%255Fall.%2522%257D&request_id=167022963516782425625298&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~first_rank_ecpm_v1~hot_rank-2-104778621-null-null.142^v67^control,201^v4^add_ask,213^v2^t3_esquery_v2&utm_term=mysql存储引擎的区别&spm=1018.2226.3001.4187)

### 	 (1) sql 优化的问题。你在工作中是如何优化 sql 的 

[sql优化](https://blog.csdn.net/weixin_38361347/article/details/122267201)

- 优化select：
  1. 创建索引/索引优化
  2. 用union all 代替union
  3. 在批量insert的时候少用在后端for循环的方式添加数据，而是提供一个批量插入数据的sql语句
  4. select语句务必指明字段名称（不要使用select *）
  5. 能用between...and就不用in()

#### (2)索引的问题,如何创建索引?如何查看索引?索引的分类?索引的数据结构?

```mysql
-- 创建索引
CREATE INDEX indexname ON table(col,col...);
-- 查看索引
SHOW INDEX FROM table;
-- 按数据结构分类可分为：B+tree索引、Hash索引、Full-text索引。
-- 按物理存储分类可分为：聚簇索引、二级索引（辅助索引）。
-- 按字段特性分类可分为：主键索引、普通索引、前缀索引。
-- 按字段个数分类可分为：单列索引、联合索引（复合索引、组合索引）。

```

[mysql索引问题](https://blog.csdn.net/qq_38785977/article/details/126809064)



#### (3) explain 关键字有没有了解过？怎么用的？ 

explain：作用是获取MYSQL如何执行select语句的信息，包括在select语句执行过程中如何连接和连接的顺序。

使用方法：在select语句前加explain关键字即可。

| 字段          | 含义                                                         |
| ------------- | ------------------------------------------------------------ |
| id            | id列的编号是select的序列号，有几个select就有几个id，并且id的大小是根据select出现的先后顺序增长的。id数值越大越先执行，id相同则从上往下执行，id为null的最后执行。 |
| select_type   | ①simple：简单查询，不包含子查询和union的查询。②primary：复杂查询中最外层的select。③subquery：包含在select中的子查询（不在from字句中）。④derived：包含在from子句中的子查询。mysql会将结果放在一个临时表中，也叫派生表。⑤union：在union中的第二个和随后的select。 |
| table         | 输出结果集的表                                               |
| type          | 这一列表示关联类型或者访问类型，也可以理解成mysql是如何决定查找表中的行，查找数据行的大概范围。性能从优到差分别为：system > const > eq_ref > ref > range > index > ALL，一般来说需要保证查询达到range级别，最好达到ref。 |
| possible_keys | 这一列显示查询可能使用哪些索引来查找。                       |
| key           | 表示实际使用的key                                            |
| key_len       | 索引字段的长度                                               |
| rows          | 扫描行的数量                                                 |
| extra         | 执行情况的说明和描述                                         |



#### 	(4 )索引失效的情况有哪些？举例说明。 

1. 对索引中所有列都制定具体值（如果字段顺序发生改变，也是没有问题的）
2. 最左前缀法则：如果索引了多列，就要遵循最左前列法则。指的是查询从索引的最左前列开始，且不跳过索引中的列。如果符合最左前缀法则，但是出现跳过了某一列的，只有最左列索引生效。
3. 范围查询右边的列，不能使用索引。
4. 不要在索引列上做运算操作，索引将失效。
5. 字符串不加单引号，索引将失效。
6. 尽量使用覆盖索引，避免select *，如果查询列超出索引列，也会降低性能。
7. 用or分割开的条件，如果or前的列有索引，后面的没有索引，那么涉及到的索引都会失效
8. 以%开头的模糊查询，索引失效，如果只是在尾部模糊查询，则不会失效
9. is null , is not null有时索引失效。如果有一个address字段中所有的数据都不为null， 那么使用is null索引就不会失效；使用is not null索引就会失效，因为mysql解析器会认为走全表的效率更高。
10. in走索引， not in不走索引
11. 尽量使用复合索引，少用单列索引
12. mysql评估走全表比走索引更快，就不会走索引

#### 	(5 ) mysql 的锁具体的特性是什么

相对其他数据库而言，MySQL的锁机制比较简单，最显著的特点是不同的存储引擎支持不同的锁机制。

![](/Users/why/Desktop/github仓库(java面试)/Pictures/图片1.png)

InnoDB默认支持行级锁。

- MySQL的锁的特性大致有如下三点：

![](/Users/why/Desktop/github仓库(java面试)/Pictures/图片2.png)

#### (6) mysql 存储引擎有哪些， innodb myisam 存储引擎的区别 

![](/Users/why/Desktop/github仓库(java面试)/Pictures/mysql数据引擎.png)

#### 	(7) mysql 数据类型有哪些 char varchar 区别是什么 

- 长度是否可变：varchar的长度是可变的，而char是固定的。char 类型是一个定长的字段，以 char(10) 为例，不管真实的存储内容多大或者是占了多少空间，都会消耗掉 10 个字符的空间

  坦通俗来讲，当定义为 char(10) 时，即使插入的内容是 `'abc'` 3 个字符，它依然会占用 10 个字节，其中包含了 7 个空字节。

- 储存长度。char最大长度为255个字符，varchar是65536个字符。

- 检索效率方面。char类型的查找效率比较高，varchar类型的查询效率比较低。

#### 	(8) mysql 的行列转换问题 

|                        原数据结构如下                        |
| :----------------------------------------------------------: |
| ![](/Users/why/Desktop/github仓库(java面试)/Pictures/行列转换原表.png) |
|                 **现在需要将结果转换成如下**                 |
| ![](/Users/why/Desktop/github仓库(java面试)/Pictures/行列转换后.png) |

```mysql
SELECT  NAME AS 姓名,
SUM(CASE WHEN SUBJECT = '语文' THEN fraction END) AS 语文,
SUM(CASE WHEN SUBJECT = '数学' THEN fraction END) AS 数学,
SUM(CASE WHEN SUBJECT = '英语' THEN fraction END) AS 英语,
SUM(fraction)AS 总分
FROM t_score GROUP BY NAME 
UNION ALL
SELECT NAME AS NAME,SUM(语文),SUM(数学),SUM(英语),SUM(总分) FROM (
  SELECT 'total' AS NAME,
	SUM(CASE WHEN SUBJECT = '语文' THEN fraction END) AS 语文,
	SUM(CASE WHEN SUBJECT = '数学' THEN fraction END) AS 数学,
	SUM(CASE WHEN SUBJECT = '英语' THEN fraction END) AS 英语,
	SUM(fraction) AS 总分
	FROM t_score GROUP BY NAME 
) AS t GROUP BY NAME
```

​	

#### (9) mysql 语句的执行顺序？什么是DDL,DML,DQL,DCL

- mysql 语句的执行顺序：

  1. from
  2. Where
  3. Group by
  4. Having 
  5. Select 
  6. Order by
  7. Limit 

- DDL：数据定义语言。用来创建数据库的各种对象------表，视图，索引，同义词，聚簇等。如：create table/view/index/syn/cluster。DDL是隐形提交，不能rollback！

- DML：数据操纵语言。insert、delete、update。

- DQL：数据查询语言。基本结构是select子句，from子句，where子句组成的查询块。

- DCL：数据控制语言。用来授予或回收访问数据库的某种特权，并控制数据库操纵事务发生的时间及效果，对数据库进行监视等。

  如：
  GRANT：授权。
  ROLLBACK [WORK] TO [SAVEPOINT]：回退到某一点。回滚—ROLLBACK回滚命令使数据库状态回到上次最后提交的状态。其格式为：SQL>ROLLBACK;
  COMMIT [WORK]：提交。在数据库的插入、删除和修改操作时，只有当事务在提交到数据库时才算完成。在事务提交前，只有操作数据库的这个人才能有权看到所做的事情，别人只有在最后提交完成后才可以看到。提交数据有三种类型：显式提交、隐式提交及自动提交。下面分别说明这三种类型。
  (1) 显式提交
  用COMMIT命令直接完成的提交为显式提交。其格式为：SQL>COMMIT；
  (2) 隐式提交
  用SQL命令间接完成的提交为隐式提交。这些命令是：ALTER，AUDIT，COMMENT，CONNECT，CREATE，DISCONNECT，DROP，EXIT，GRANT，NOAUDIT，QUIT，REVOKE，RENAME。
  (3) 自动提交
  若把AUTOCOMMIT设置为ON，则在插入、修改、删除语句执行后，系统将自动进行提交，这就是自动提交。其格式为：SQL>SET AUTOCOMMIT ON；

#### 	(10)mysql 的事务,什么是事务？事务的特性？事务的隔离级别？事务的传播行为？

- 事务是一个原子操作。是一个最小执行单元。可以由一个或多个sql语句组成，在同一个事务中，所有的sql语句都执行成功时，整个事务成功，有一个sql执行失败，整个事务都执行失败。

- 事务的特性：

  1. atomicity(原子性)：一个事务不可分割，要么一起成功，要么一起失败。
  2. consistency(一致性)：事务执行的前后数据要保持一致。
  3. isolation(隔离性)：一个事务的操作不能影响到另一个事务的执行。
  4. durability(持久性)：一个事务操作完成对数据库的影响是永久的。

- 事务的隔离级别：

  | 事务隔离级别                 | 脏读 | 不可重复读 | 幻读 |
  | ---------------------------- | ---- | ---------- | ---- |
  | 读未提交（read-uncommitted） | 是   | 是         | 是   |
  | 不可重复读（read-committed） | 否   | 是         | 是   |
  | 可重复读（repeatable-read）  | 否   | 否         | 是   |
  | 串行化（serializable）       | 否   | 否         | 否   |

- 事务的传播行为：事务传播行为（propagation behavior）指的就是当一个事务方法被另一个事务方法调用时，这个事务方法应该如何运行。

  Spring在TransactionDefinition接口中规定了7种类型的事务传播行为。
  事务传播行为是Spring框架独有的事务增强特性。
  7种：(required / supports / mandatory / requires_new / not supported / never / nested)

  - PROPAGATION_REQUIRED：如果当前没有事务，就创建一个新事务，如果当前存在事务，就加入该事务，这是最常见的选择，也是Spring默认的事务传播行为。(required需要，没有新建，有加入)

  - PROPAGATION_SUPPORTS：支持当前事务，如果当前存在事务，就加入该事务，如果当前不存在事务，就以非事务执行。（supports支持，有则加入，没有就不管了，非事务运行）

  - PROPAGATION_MANDATORY：支持当前事务，如果当前存在事务，就加入该事务，如果当前不存在事务，就抛出异常。（mandatory强制性，有则加入，没有异常）

  - PROPAGATION_REQUIRES_NEW：创建新事务，无论当前存不存在事务，都创建新事务。（requires_new需要新的，不管有没有，直接创建新事务）

  - PROPAGATION_NOT_SUPPORTED：以非事务方式执行操作，如果当前存在事务，就把当前事务挂起。（not supported不支持事务，存在就挂起）

  - PROPAGATION_NEVER：以非事务方式执行，如果当前存在事务，则抛出异常。（never不支持事务，存在就异常）

  - PROPAGATION_NESTED：如果当前存在事务，则在嵌套事务内执行。如果当前没有事务，则按REQUIRED属性执行。（nested存在就在嵌套的执行，没有就找是否存在外面的事务，有则加入，没有则新建）

    对事务的要求程度可以从大到小排序：mandatory / supports / required / requires_new / nested / not supported / never

    

### 三 、redis

#### 	 (1) redis 的常用数据类型及其命令有哪些 ?

| 结构类型   | 介绍                                                         |
| ---------- | ------------------------------------------------------------ |
| key-string | 一个key对应一个值。存放一个字符串信息。                      |
| key-hash   | 一个key对应一个map或Object。一般用于存放对象数据。           |
| key-list   | 一个key对应一个列表。实现队列或者栈。                        |
| key-set    | 一个key对应一个集合。去重存放数据或者进行交叉并计算。        |
| key-zset   | 一个key对应一个有序集合。排行榜等有序数据。                  |
| HyperLog   | 计算近似值。统计计数。                                       |
| GEO        | 经纬度地理位置。坐标。                                       |
| BIT        | 一般储存的也是一个字符串，储存的是一个byte[]。通过byte位操作来记录一些数据，减少空间占用。 |

|                   五种常用的存储数据结构图                   |
| :----------------------------------------------------------: |
| ![](/Users/why/Desktop/github仓库(java面试)/Pictures/1586759101828.png) |

#### 	(2)缓存穿透、缓存击穿、缓存雪崩的概念以及对应的解决方案

| 缓存穿透                                                     |
| :----------------------------------------------------------- |
| ![](/Users/why/Desktop/github仓库(java面试)/Pictures/1586949401099.png) |
| 问题出现的原因：查询的数据redis中没有，数据库中也没有。<br /><br />1.根据id查询时，如果id是自增，将id的最大值放到redis中，在查询数据库之前直接比较一下id。<br /><br />2.如果id不是整型，可以将id全部放到set中，在用户查询数据之前，可以先去set中查询一下是否有这个id。<br /><br />3.获取客户端的IP地址，可以将IP的访问添加权限。 |

| 缓存击穿                                                     |
| ------------------------------------------------------------ |
| ![](/Users/why/Desktop/github仓库(java面试)/Pictures/1586949585287.png) |
| 问题出现的原因：缓存中的热点数据突然到期，造成大量的数据请求访问数据库，导致服务器崩溃<br /><br />1.在访问缓存中没有的数据时，直接添加一个锁，让几个请求去访问数据库，避免数据库宕机。<br /><br />2.热点数据的生存时间去掉 |

| 缓存雪崩                                                     |
| :----------------------------------------------------------- |
| ![](/Users/why/Desktop/github仓库(java面试)/Pictures/1586949725602.png) |
| 问题出现的原因：我们缓存的数据并不是每次请求才放进去的，实际上很多数据是在服务器启动的时候同步进去的，我们写的代码放进去设置有效期，因为编码的问题写死了一个时间，导致这些数据其实会在非常短的时间内同时到期，最终大量的请求同时去访问数据库，造成服务器宕机。<br /><br />1.将缓存中的数据的生存时间，设置为30-60的一个随机时间 |

#### 	 (2)redis的持久化机制 

- RDB：redis的默认的持久化机制。

  - RDB持久化文件，速度比较快，而且储存的是一个二进制文件，传输起来方便。

  - RDB持久化的时机：变化的越多，保存的越快。

    save 3600 1：在3600秒内，有1个key改变了，就执行RDB持久化。

    save 300 10：在300秒内，有10个key改变了，就执行RDB持久化。

    save 60 10000：在60秒内，有10000个key改变了，就执行RDB持久化。

  - RDB无法保证数据的安全。

- AOF：AOF持久化机制默认是关闭的，redis官方推荐同事开启RDB和AOF持久化，更安全，避免数据丢失。

  - AOF存的是执行过的增删改的命令，而不是数据，在每次执行命令之后都可以存储一次。

  - AOF的持久化速度，相比RDB较慢，存储的是一个文本文件，到了后期文件会比较大，传输困难。

  - AOF的持久化时机：

    appendfsync always：每执行一个写操作，立即持久化到AOF文件中，性能比较低。
    appendfsync everysec：每秒执行一次持久化。
    appendfsync no：会根据你的操作系统不同，环境的不同，在一定时间内执行一次持久化。

  - AOF相对RDB更安全，推荐同时开启AOF和RDB。

- 同时开启的注意事项：

  - 如果同时开启了RDB和AOF的持久化，那么在redis宕机重启之后，需要加载一个持久化文件，优先选择AOF文件。
  - 如果先开启了RDB，再次开启AOF，如果RDB进行了持久化，那么RDB文件中的内容会被AOF覆盖掉。

#### 	(3) redis 的事务命令有哪些？ redis 事务和 mysql 事务的区别 ？

**redis事务和MySQL事务的区别：**

- MySQL和Redis事务的命令区别：

  MySQL：

  ```mysql
  - begin：开启事务。
  - commit：提交事务，事务执行完成后对数据库的影响是永久性的。
  - rollback：回滚事务，结束用户的事务，并撤销正在进行的所有未提交的写操作。
  ```

  Redis：

  ```shell
  #开启事务
  multi
  
  ##正常输入命令
  
  #执行事务的commands队列
  exec
  
  #结束事务，并清除commands队列
  discard
  #redis的一次事务操作，该成功的成功，该失败的失败。即redis的事务不能保证原子性。
  #redis开启事务，执行一些列的命令，但是命令不会立即执行，而是会放在一个队列中，如果执行了exec命令，那么队列中的命令会全部执行，如果#discard，那么队列中的命令会全部取消。
  ```

- 取消事务的区别：

  - MySQL事务的回滚(rollback)是指一次事务中，如果一个sql执行出错，那么所有的sql全部执行失败，全部sql执行成功整个事务才会成功。执行rollback后所有语句造成的影响消失。
  - Redis的discard只是结束本次事务，正确的命令造成的影响仍然存在。即：
    - 如果在一个事务中的命令出现错误，那么所有的命令都不会执行。
    - 如果在一个事务中出现运行错误，那么正确的命令会被执行。

​	[redis 事务和 mysql 事务的区别](https://blog.csdn.net/weixin_44685869/article/details/104420566?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522167072687516800182715633%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=167072687516800182715633&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduend~default-1-104420566-null-null.142^v68^control,201^v4^add_ask,213^v2^t3_esquery_v2&utm_term=redis事务和mysql事务有什么区别&spm=1018.2226.3001.4187)

#### (4) Redis 的主从复制有没有了解?是如何实现的 ?redis 哨兵是什么?解决了什么问题?

- redis的主从复制：指将一台Redis服务器的数据，复制到其他的Redis服务器中。前者称为主节点（Master/Leader），后者称为从节点（slave/follower），数据的复制是单向的！只能有主节点复制到从节点（主节点已写为主也可以读，从节点已读为主）。Redis的主从复制是异步复制，异步分为两个方面：1.master服务器在将数据同步到slave时是异步的，因此master服务器依然可以接收其他请求2.slave在接受同步数据的时候也是异步的。
- 主从复制实现原理：Redis的主从复制包括全量复制和增量复制。全量复制是发生在初始化阶段，从节点会主动向主节点发起一个同步请求，主节点接收到请求以后会生成一份当前数据的快照，发送给从节点，从节点接收到数据进行加载之后完成全量复制。增量复制是发生在每一次master节点发生数据变更的一个场景里面，会把变化的增量数据同步给从节点，增量复制是通过维护offset这样一个复制偏移量来实现的。
- redis哨兵解决了主从架构的单点故障问题。
- redis哨兵：哨兵是Redis的高可用方式，哨兵节点是特殊的Redis服务，不提供读写服务，主要用来监控Redis实例节点。当Redis的主节点挂掉时，哨兵会第一时间感知到，并且会在slave节点中重新选出一个新的master节点。（即使以后原来的master重启服务以后也不会变为master节点，而是会作为slave节点。）

#### 	(5)Redis的集群有没有搭建过如何搭建的

#### 	(6)Redis 的分布式锁有没有了解过 setnx 

- setnx + expire

  ```java
  /*
  setnx：如果当前key不存在，就存入key-value，否则就什么都不做。
  expire：设置key的生存时间，单位为秒。
  **/
  if (setnx(key, 1) == 1){
      expire(key, 10)
      try {
          //TODO 业务逻辑
      } finally {
          del(key)
      }
  }
  ```


[Redis的分布式锁](https://blog.csdn.net/q66562636/article/details/124739036?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522167100174816782412558274%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=167100174816782412558274&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~top_click~default-1-124739036-null-null.142^v68^control,201^v4^add_ask,213^v2^t3_esquery_v2&utm_term=redis分布式锁面试题&spm=1018.2226.3001.4187)

#### (7)Redis的常用客户端你知道哪些 jedis、redisTemplate 

- jedis：是Redis官方推荐的面向java操作Redis的客户端开发jar包

- jedisPool：线程安全的网络连接池预先生成一批jedis连接对象放入连接池中，当需要对Redis进行操作时从连接池中借用jedis对象，操作完成后归还。减少资源的开销。

- RedisTemplate：springboot集成Redis的客户端方式，spring框架对jedisAPI进行了高度封装。springboot还提供了redistemplate和stringRedisTemplate两个模板。第一个是需要指定数据的泛型，第二个是默认操作的都是字符串格式，通过不同类型的数据的ops执行增删改查操作。

  

### 四、 linux操作系统

#### 	(1)linux 操作系统的常用命令 

```shell
#列出目录
ls [-ald] [目录名]
#创建目录
mkdir [-p] 目录名
#只能删除空目录
rmdir 目录名
# 删除非空目录
# -r：代表递归删除目录下的全部内容
# -f：不询问，直接删除
rm [-rf] 目录名
#复制目录下的全部内容
cp -r 来源目录 目标目录
# 如果第二个参数指定的路径不存在，就是重命名，如果第二个参数的路径存在，就是移动
mv 目录名 新目录名 | 路径
#创建空文件
touch 文件名1 文件名2 ……
#编辑文件，后期最常的命令之一
vi 文件名 				# 查看文件。（查看模式）

i | a | o   		  # 进入编辑模式。（编辑模式）
                      # i：在当前光标处，进入编辑模式。 
                      # a：在当前光标后一格，进入编辑模式。 
                      # o：在当前光标下一行，进入编辑模式。
I |A |O
                      # I：在当前光标所在行的行首，进入编辑模式。 
                      # A：在当前光标所在行的行尾，进入编辑模式。 
                      # O：在当前光标所在行的上一行，进入编辑模式。
                      
esc				      # 退出编辑模式，回到查看模式。

:				      # 从查看模式进入到底行命令模式。（底行命令模式）
                      # 在底行命令模式下，输入wq回车保存并退出。输入q!回车不保存并退出
                      # 在查看模式下，摁ZZ，可以快速保存并退出。
# 查看文件，直接展示到最后一行
cat 文件名
#解压压缩包
# -z： 代表压缩包后缀是.gz的
# -x： 代表解压
# -v： 解压时，打印详细信息
# -f： -f选项必须放在所有选项的最后，代表指定文件名称
# -C 路径： 代表将压缩包内容解压到指定路径
tar [-zxvf] 压缩包名称 [-C 路径]
#查看指定目录磁盘使用情况
df [选项] [文件]
df -h /data/
#查看内存使用情况
free
#查看进程的瞬间信息
ps
#持续的监视进程的信息
top
#linux查看日志信息 
tail
cat
```

#### (6)linux的文件系统有没有了解过

inux采用的是树形结构。linux中没有盘符，最上层目录是根目录（/），其他所有的目录都是从根目录出发而生成的。

![](/Users/why/Desktop/github仓库(java面试)/Pictures/1586239342796.png)

| 目录 | 介绍                                                  |
| ---- | ----------------------------------------------------- |
| root | 该目录为系统管理员root的home目录                      |
| bin  | 该目录下放着经常使用的命令                            |
| boot | 该目录放的是启动linux时的一些核心文件                 |
| etc  | 该目录放的是系统管理所需要的配置文件和子目录          |
| home | 普通用户的home目录                                    |
| usr  | 默认安装软件的目录，类似Windows中的programme file目录 |
| opt  | 主机额外安装软件访问的目录                            |

### 五、SSM

###  (1)spring

[spring面试题](https://blog.csdn.net/a745233700/article/details/80959716?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522167117977016782429747385%2522%252C%2522scm%2522%253A%252220140713.130102334.pc%255Fall.%2522%257D&request_id=167117977016782429747385&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~first_rank_ecpm_v1~hot_rank-1-80959716-null-null.142^v68^control,201^v4^add_ask,213^v2^t3_esquery_v2&utm_term=spring管理bean的方式&spm=1018.2226.3001.4187)

#### 1 . 1 spring 的核心思想有哪些？ ioc aop 

- IOC(Inverse Of Controll)：控制反转。创建对象的控制权转移给spring框架进行管理，并由spring根据配置文件去创建实例和和管理各个实例的依赖关系。对象与对象之间解耦合，也利于功能的复用。
- AOP(Aspect Oriented Programming)：面向切面。讲那些影响了多个类的公共行为或逻辑封装到一个可重用模块，这个模块被称为切面“Aspect”，减少系统中的重复代码，降低了模块之间的耦合度，提高系统的可维护性。

#### 1 . 2 在 spring 中管理 bean 的方式有哪些？ 

- 使用xml的方式来声明bean的定义，spring容器在启动的时候会加载并解析这个xml，把bean装在到ioc容器中。
- 使用@compontscan来扫描申明了@controller、@service、@repository、@component注解的类
- 使用@configuration声明配置类，并使用@bean注解实现bean的定义，这种方式其实是xml配置方式的一种演变，是spring迈入无配置时代的里程碑。

##### 以下方式了解即可

- 使用@import注解，导入配置类或者普通的bean
- 使用FectoryBean工厂bean，动态构建一个bean实例，springCloud openFeign里面的动态代理实例就是使用FectoryBean来实现的
- 实现ImportBeanDefinition-Register接口可以动态注入bean实例，这个在springboot里面的启动注解有用到
- 实现ImportSelector接口动态批量注入配置类或者bean对象，这个在springboot里面的自动装配机制有用到。

#### 	1 . 3 什么是依赖注入？依赖注入的方式又有哪些 

DI(dependency injection)：IOC的一个重点就是在程序运行时，动态的向某个对象提供他所需要的其他对象，这一点就是通过DI实现的。即应用程序在运行时依赖IOC容器来动态注入对象所需的外部依赖。而spring的DI具体是通过反射实现注入的，反射允许程序在运行时动态的生成对象，执行对象方法，改变对象的属性。

```java
//依赖注入的类型
/*
1.字段注入
这种注入方式存在三个明显缺陷：
·对象的外部可见性：也就是脱离了Spring容器那么这个对象就不会被注入进去。
·循环依赖：字段注入不会被检测是否出现依赖循环。比如A类中注入B类，B类中又注入了A类。
·无法设置注入对象为final：因为final的成员变量必须在实例化时同时赋值。
  **/
@Autowire 
private ExampleService exampleServiceImpl;

/*
2. 构造器注入
·因为用了构造器所以保证一定可以独立使用，不依赖Spring容器。
·可以检测循环依赖。
·如果注入的很多，那么构造方法要写很多参数，代码结构不是很清楚。
**/
private ExampleService exampleServiceImpl;

@Autowire
public ExampleController (ExampleService exampleServiceImpl){
	this.exampleServiceImpl = exampleServiceImpl;
}
/*
3.setter注入
·利用set来注入保证不依赖Spring容器。
·每个set单独注入某个对象，便于控制，并且可以实现选择性注入。
·可以检测循环依赖。
**/
private ExampleService exampleServiceImpl;

@Autowire
public setExampleServiceImpl (ExampleService exampleServiceImpl){
	this.exampleServiceImpl = exampleServiceImpl;
}
```

#### 	1 . 4 spring 中有哪些常用的注解？管理bean的注解、依赖注入的注解、生命周期的注解、作用域的注解 

[spring的常用注解](https://blog.csdn.net/guorui_java/article/details/107347754?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522167133046016782388076842%2522%252C%2522scm%2522%253A%252220140713.130102334.pc%255Fall.%2522%257D&request_id=167133046016782388076842&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~first_rank_ecpm_v1~hot_rank-1-107347754-null-null.142^v68^control,201^v4^add_ask,213^v2^t3_esquery_v2&utm_term=spring的常用注解&spm=1018.2226.3001.4187)

```java
//管理bean的注解
@Component //普通的类

@Controller //表现层,其功能与@Component相同。

@Service //业务层,其功能与@Component相同。

@Repository //持久层,其功能与@Component相同。

//依赖注入的注解
//用于对Bean的属性变量，属性的Set方法以及构造方法进行标注，配合对应的注解处理器完成Bean的自动配置工作。默认按照Bean的类型（type）进行装配。
@Autowired 
//与@Autowired注解配合使用，会将默认的按照Bean类型注入改为按照Bean的实例名称装配，Bean的实例名称由@Qualifier注解的参数指定。
@Qualifier
/*
作用与@Autowired一样。区别在于@Autowired默认按照Bean类型（type）注入，而@Resource默认按照Bean实例名称进行装配

@Resource 中有两个重要属性：name 和 type。

Spring 将 name 属性解析为 Bean 实例名称，type 属性解析为 Bean 实例类型。如果指定 name 属性，则按实例名称进行装配；如果指定 type 属性，则按 Bean 类型进行装配。

如果都不指定，则先按 Bean 实例名称装配，如果不能匹配，则再按照 Bean 类型进行装配；如果都无法匹配，则抛出 NoSuchBeanDefinitionException 异常。
**/
@Resource

//生命周期的注解
//通过@Bean注解指定初始化和销毁方法
@PostConstruct //初始化 
@PreDestroy //销毁

//作用域的注解
/*
1.Spring Bean的作用域分为四种

单例（singleton）：在整个应用中，只创建bean的一个实例
原型（prototype）：每次注入或者通过Spring应用上下文获取的时候，都会创建一个新的bean实例
会话（session）：在web应用中，为每个会话创建一个bean实例
请求（request）：在web应用中，为每个请求创建一个bean实例
**/
@Scope

```

#### 1 . 5 @Autowired和@Resource注解的区别 

[Autowired注解与Resource注解的区别](https://blog.csdn.net/NoviceZ/article/details/120208241)

- 两者用法：
  - 两个注解的作用是一样的，都是在做bean注入，在使用过程中两个注解有时候可以交替使用。
- 两者共同点：
  - @Autowired和@Resource注解都可以用作bean注入
  - 在接口只有一个实体类的时候，两个注解可以交替使用
- 两者不同点：
  - @Resource是Java自身的注解，@Autowired是spring的注解
  - @Resource有两个重要的属性，name和type。如果name属性有值，则使用byName的自动注入策略，将值作为需要注入bean的名字；如果type有值，则使用byType自动注入策略，将值作为需要注入bean的类型。如果两者都不指定，这时将使用反射机制，使用byName的自动注入策略。即：@Resource默认按照名称进行匹配，名称可以通过name属性进行指定，如果没有设置name属性，，当注解写在字段上时，默认取字段名，按照名称查找，当找不到与名称匹配的bean时才会按照类型进行装配，需要注意的是，如果name属性指定了值，就只会按照名称进行匹配。
  - @Autowired是 spring的注解，此注解只根据type进行注入，不回去匹配name，但如果根据type无法辨别注入对象时，就需要配合@Qualifier注解使用。

#### 1 . 6 什么是 aop ？spring 是如何整合 aop 的？你在项目中是如何使用 aop 的？

#### 1 . 7 spring 如何管理事务？ 

[spring管理事务](https://blog.csdn.net/ChineseSoftware/article/details/122562677)

- spring管理事务的两种方式：
  - 编程式事务管理：侵入到了业务代码里面，但是提供了更加详细的事务管理。编程式事务使用 TransactionTemplate 或者直接使用底层的 PlatformTransactionManager。对于编程式事务管理，Spring 推荐使用 TransactionTemplate。
  - 声明式事务管理：基于AOP，既能管理事务，又不影响业务代码。本质是对方法前后进行拦截，然后在目标方法开始之前创建或者加入一个事务，在目标方法执行完后根据执行情况提交或者回滚事务。最大的优点就是不需要通过编程的方式管理事务，这样就不需要在业务逻辑代码中掺杂事务管理的代码，只需在配置文件中做相关的事务规则声明(或基于@Transactional的方式)，便可以将事务规则应用到业务逻辑中。

#### 1 . 8 spoing 中的代理思想 jdk 动态代理和 cglib 字节码代理的区别 	

- 两者都可以实现产生代理对象
- JDK动态代理是Java自带的，cglib是第三方的
- JDK动态代理是代理对象和目标对象之间实现同一个接口
- cglib代理是代理对象时目标对象的子类

#### 1 . 9 spring 的 bean 的生命周期和 spring 中如何解决循环依赖的问题

- spring的bean的生命周期：

> **单例bean：**singleton（当spring容器创建的时候，对象就会被创建。当spring容器被关闭的时候，类的对象就会被销毁。）
>
> 随工厂启动[创建]() ==》  [构造方法]()  ==》 [set方法(注入值)]()  ==》 [init(初始化)]()  ==》 [构建完成]() ==》[随工厂关闭销毁]()

> **多例bean：**prototype（spring容器创建的时候，并不会初始化，而是什么时候使用，什么时候初始化。spring容器销毁，并不会销毁类的对象，而是交给Java内存回收机制。）
>
> 被使用时[创建]() ==》  [构造方法]()  ==》 [set方法(注入值)]()  ==》 [init(初始化)]()  ==》 [构建完成]() ==》JVM垃圾回收销毁

- spring解决循环依赖问题：

    [spring如何解决循环依赖](https://blog.csdn.net/DQWERww/article/details/126128229)

  - spring设计了三级缓存，分别是一级缓存：singletonObjects；二级缓存：earlySingletonObjects；三级缓存：singletonFactories。



### 2 . mybatis & mybatisplus 

#### 	2 . 1 mybatis 中的核心类有哪些 

- Resource：[mybatis](https://so.csdn.net/so/search?q=mybatis&spm=1001.2101.3001.7020)中来的类主要用来加载配置文件的

  ```java
  InputStream in = Resources.getResourceAsStream(conf);
  ```

  

- SqlSessionFactoryBuilder：创建SqlSessionFactory

  ```java
  SqlSessionFactoryBuilder sqlSessionFactoryBuilder = new SqlSessionFactoryBuilder();
  ```

  

- SqlSessionFactory：SqlSessionFactory ，是重量级对象，使用资源多，比较费劲，整个项目有一个就可以了，仅创建一次，创建SqlSession

  ```java
  SqlSessionFactory sqlSessionFactory = sqlSessionFactoryBuilder.build(in);
  ```

  

- SqlSession：非线程安全的，需要在方法中使用，使用前获取，使用后关掉

  ```java
  //非自动提交
  SqlSession sqlSession = sqlSessionFactory.openSession();
  //自动提交
  SqlSession sqlSession = sqlSessionFactory.openSession(true);
  //非自动提交
  SqlSession sqlSession = sqlSessionFactory.openSession(false);
  ```

  

#### 	2 . 2 mybatis 框架中都用到了哪些核心的设计模式？

[mybatis 框架核心设计模式](https://blog.csdn.net/qq5621/article/details/126162680?ops_request_misc=&request_id=&biz_id=102&utm_term=mybatis%20框架中都用到了哪些核心的设计模式？&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduweb~default-0-126162680.142^v68^control,201^v4^add_ask,213^v2^t3_esquery_v2&spm=1018.2226.3001.4187)

| Mybatis中的核心设计模式                                      |
| ------------------------------------------------------------ |
| ![](/Users/why/Desktop/github仓库(java面试)/Pictures/a020ddeb34c7efbc5edf8ba9e492003a.png)​**Mybatis** 中使用了10种设计模式，其中创建型模式3种（工厂、单例、建造者），结构型模式4种（适配器、代理、组合、装饰器），行为型模式3种（模板、策略、迭代器) |

#### 2 . 3 mybatis 主键自增如何实现 

- 方式1：通过selectKey标签将查询的id回填到指定属性中

  标签：< selectKey id="" parameterType="" order="AFTER|BEFORE">

  ```xml
  <!--
       keyProperty 主键的属性
       order：在执行之前获取还是在执行之后获取
       resultType 主键的返回值类型
  -->
  <insert id="insertUser1" >
      <selectKey keyProperty="id" resultType="int" order="AFTER">
          select last_insert_id();
      </selectKey>
      insert into tb_user (username,password) values (#{username},#{password})
  </insert>
  ```

- 方式2：使用useGeneratedKeys使用生成的id，并回填写到指定的属性中

  ```xml
  <!--  
    	useGeneratedKeys:使用自动生成的id
      keyProperty：将生成的id添加到指定的属性中,id属性值表示的是实体类中的属性名
  -->
  <insert id="insertUser1" useGeneratedKeys="true" keyProperty="id">
      insert into tb_user (username,password) values (#{username},#{password})
  </insert>
  ```

  

#### 2 . 4 mybatis 接口传递多个参数如何解决

- 顺序传参：此时[mybatis](https://so.csdn.net/so/search?q=mybatis&spm=1001.2101.3001.7020)会将参数放在map集合中，以两种方式存储数据 以arg0,arg1..为键，以参数为值 以param1,param2..为key,以参数为value（这种方法不推荐，SQL层表达不直观，一旦调整顺序容易出错）

  ```java
  public interface UserMapper { 
    User checkLogin(String username,String password); 
  }
  <select id="checkLogin" resultType="User"> 
    select * from user where username=#{param1}and password=#{param1} 
  </select>
   //或者
  <select id="checkLogin" resultType="User"> 
    select * from user where username=#{arg1} and password=#{arg0} 
  </select>
  ```

- 利用Map集合：此时可以通过#{}和${}一任意的内容获取参数值，一定要注意${}的单引号问题

  ```java
  public interface UserMapper { 
    // 验证登录（以map集合作为参数） 
    User checkLoginByMap(Map<String,Object> map); 
  } 
  <select id="checkLoginByMap" resultType="User"> 
    select * from user where username=#{username} and password=#{password} 
  </select>
  ```

- 利用@Param注解：此时mybatis会将这些参数放在map中，以两种方式进行存储

  1：以@param注解的value属性值为键

  2：以param1,param2....为键，以参数为值 此时可以通过#{}和${}访问实体类的属性名，就可以获取相应的属性值一定要注意${}的单引号问题

  当接口中只有一个参数(并且没有用@Param())时候，需要在xml中添加响应的参数类型parameterType；

  如果是多个参数(每个参数都是用@Param())的时候，就不会去读参数类型parameterType，
  直接取得参数里面的值。

  ```java
  public interface UserMapper { //验证登录 
    User checkLoginByParam(@Param("username") String username,@Param("password") String password); 
  }
  <select id="checkLoginByParam" resultType="User"> 
    select * from user where username=#{username} and password=#{password} 
  </select>
  ```

- 创建实体类： 此时可以通过#{}和${}访问实体类的属性名，就可以获取相应的属性值一定要注意${}的单引号问题（这种方法需要创建实体类，扩展性不是很好）

  ```java
  public interface UserMapper { 
    // 添加用户信息 
    void insertUser(User user); 
  }
  <insert id="insertUser"> 
    insert into user values(null,#{username},#{password},#{age},#{gender},{email}) 
  </insert>
  ```

  

#### 2 . 5 mybatis #{}和${}的区别是什么？

${}是字符串替换，#{}是预处理；使用#{}可以有效防止sql注入，提高系统安全性。

Mybatis在处理${}时，就是把${}直接替换成变量的值；而Mybatis在处理#{}时，会将#{}替换成？，调用prepareStatement的set方法来赋值。

#### 2 . 6 mybatis 前后字段不一致怎么解决？ resultType resultMap 之间的区别 

- 为字段起别名，使之与属性名一致

  ```xml
  <select id="getEmpByEmpIdOld" resultType="Emp">
          select emp_id empId,emp_name empName,age,gender from t_emp where emp_id = #{empId}
  </select>
  ```

- 可以在MyBatis的核心配置文件中设置一个全局配置、mapUnderscoreT-oCamelCase，可以在查询表中数据时，自动将_类型的字段名转换为驼峰。

  例如：字段名user_name，设置了mapUnderscoreToCamelCase，此时字段名就会转换为userName

  [注意：此方法仅适用于字段名的下划线转化为驼峰后恰好与类的属性名一致的情况]()

  ```xml
  <settings>
          <!--将下划线映射为驼峰-->
          <setting name="mapUnderscoreToCamelCase" value="true"/>
  </settings>
  
  <select id="getEmpByEmpIdOld" resultType="Emp">
          select * from t_emp where emp_id = #{empId}
  </select>
  ```

- 使用resultmap来处理

  resultMap：设置自定义的映射关系
  id：唯一标识
  type：处理映射关系的实体类的类型
  常用的标签：
  id：处理主键和实体类中属性的映射关系
  result：处理普通字段和实体类中属性的映射关系
  association：处理多对一的映射关系（处理实体类类型的属性）
  collection：处理一对多的映射关系（处理集合类型的属性）
  column：设置映射关系中的字段名，必须是sql查询出的某个字段
  property：设置映射关系中的属性的属性名，必须是处理的实体类类型中的属性名

  ```xml
      <resultMap id="empResultMap" type="Emp">
          <id column="emp_id" property="empId"></id>   
          <result column="emp_name" property="empName"></result>
          <result column="age" property="age"></result>
          <result column="gender" property="gender"></result>
      </resultMap>
   
      <!--Emp getEmpByEmpId(@Param("empId") Integer empId);-->
      <select id="getEmpByEmpId" resultMap="empResultMap">
          select * from t_emp where emp_id = #{empId}
      </select>
  ```

  

#### 2 . 7 mybatis 如何开启批处理？

[Mybatis批处理](https://blog.csdn.net/csucsgoat/article/details/116724221?ops_request_misc=&request_id=&biz_id=102&utm_term=mybatis%20如何开启批处理？&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduweb~default-1-116724221.142^v68^control,201^v4^add_ask,213^v2^t3_esquery_v2&spm=1018.2226.3001.4187)



#### 2 . 8 有没有了解过 mybatis 的一级缓存二级缓存是如何实现的？ 



#### 2 . 9 mybatis 中延时加载策略有没有了解过怎么实现的 ？



#### 2 . 10 mybatis 的动态 sql 是如何实现的？ 

```xml
<!--
< sql >
定义SQL片段：抽取公共的SQL语句  
sql标签的作用是提取公共的sql代码片段
sql id属性：唯一标识
include refid属性：参照id
-->
<sql id="productSql">
    p_id pid,t_id tid,p_name name,p_time time,p_price price ,
    p_state state ,p_image image, p_info info,isdel del
</sql>
<!-- 引用片段 -->
<select id="selectProductByCondition" resultType="product">
        select  <include refid="p_col"/>  from product
</select>

<!--
< if >
if标签的作用适用于条件判断
test 属性：判断条件(必填)
注意：
    1、在test中获取属性值的时候，不需要加上#{},在sql语句中获取属性值要加上#{}
    2、在sql语句中进行使用特殊字符，最好不要使用 > 或者 <,应该使用 &gt;  &lt;
-->
<select id="selectByCondition" resultType="product">
    select <include refid="productSql"/> from product  where 1 = 1
    <if test="name != null">
        and p_name like concat('%',#{name},'%')
    </if>
    <if test="time != null">
        and p_time  &gt; #{time}
    </if>
    <if test="price != null">
        and p_price &gt; #{price}
    </if>
    <if test="state != null">
        and p_state &gt; #{state}
    </if>
</select>

<!--
where标签用于添加where条件
特点:
    1、如果where有条件自动加上where关键字，如果没有则不会where关键字
    2、会自动去除前面的and或者or等关键字
-->
<select id="selectByCondition" resultType="product">
    select <include refid="productSql"/> from product
    <where>
        <if test="name != null">
            and p_name like concat('%',#{name},'%')
        </if>
        <if test="time != null">
            and p_time  &gt; #{time}
        </if>
        <if test="price != null">
            and p_price &gt; #{price}
        </if>
        <if test="state != null">
            and p_state &gt; #{state}
        </if>
    </where>
</select>

<!--  
set标签用于添加修改的字段值
特点:
    1、如果set有条件自动加上set关键字，如果没有则不会set关键字
    2、会自动去除后面的.
-->
<update id="updateByCondition">
    update product
    <set>
        <if test="name != null">
            p_name = #{name},
        </if>
        <if test="time != null">
            p_time = #{time},
        </if>
        <if test="state != null">
            p_state = #{state},
        </if>
        <if test="price != null">
            p_price = #{price},
        </if>
        p_id = #{pid}
    </set>
    where p_id = #{pid}
</update>

<!--
    choose、when、otherwise标签用于多值判断使用类似于java中的switch...case
-->
<select id="selectOrderByCondition" resultType="product">
    select <include refid="productSql"/> from product  order by
    <choose>
        <when test="price != null">
            p_price desc
        </when>
        <when test="time != null">
            p_time desc
        </when>
        <when test="state != null">
            p_state desc
        </when>
        <otherwise>
            p_id desc
        </otherwise>
    </choose>
</select>

<!--
trim:用灵活的定义任意的前缀和后缀，以及覆盖多余前缀和后缀
< trim prefix="" suffix="" prefixOverrides="" suffixOverrides="" >代替< where > 、< set >
prefix 前缀
suffix 后缀
prefixOverrides 前缀覆盖
suffixOverrides 后缀覆盖
-->
<update id="updateByCondition">
    update product
    <trim prefix="set" suffixOverrides=",">
        <if test="name != null">
            p_name = #{name},
        </if>
        <if test="time != null">
            p_time = #{time},
        </if>
        <if test="state != null">
            p_state = #{state},
        </if>
        <if test="price != null">
            p_price = #{price},
        </if>
        p_id = #{pid}
    </trim>
    where p_id = #{pid}
</update>

<!--
 foreach 标签的作用是遍历集合或者数组
参数	描述	取值
collection	容器类型	list、array、map(可以在形参上加注解改变名称)
open	起始符	(
close	结束符	)
separator	分隔符	,
index	下标号	从0开始，依次递增
item	当前项	任意名称（循环中通过 #{任意名称} 表达式访问）
-->
<!-- insert into 表名  (字段名1,字段名2,...)  values (值1,...),(值1,...) ,(值1,...)  -->
<insert id="insertProduct">
    insert into product (p_name,p_time,p_state,p_price) values
    <foreach collection="productList" item="product" separator=",">
        (#{product.name},#{product.time},#{product.state},#{product.price})
    </foreach>
</insert>

<!-- delete from 表名 where p_id in (id1,id2,..)   -->
<delete id="deleteProduct">
    delete from product where p_id in
    <foreach collection="ids" item="id" open="(" close=")" separator=",">
        #{id}
    </foreach>
</delete>
```



#### 2 . 11 mybatisplus 中的插件有哪些？具体怎么用的？分页插件、 SQL性能分析插件、乐观锁插件 



### 3 、springmVc

​	 3 . 1 springmvc 中的 mvc 思想是什么 
​	3 . 2 springmvc 的执行流程 
​	3 . 3 springmvc 的常用注解有哪些 
​	3 . 4 springmvc 如何进行异常处理 
​	3 . 5 springmv 。如何配置拦截器拦截器和过滤器之间的区别是什么？六、 springboot 
​	1 、什么是 5 pringboot spr 主 ngboot 和 spring 之间有什么区别 	2 、什么是微服务微服务和 springb 。。 t 之间的区别是什么 
​	3 、 springboot 有哪些常用的注解 
​	4 、 springbcot 如何开启自动配置 
​	5 、 springb 。。 t 配置文件有哪两种类型优先级是什么 
​	6 、 springboot 常见的启动器（ starter )有哪些？如何自定义启动器 
​	7 、 springboot 中如何开启监控如何配置热部署 s 、＠ Restcontroller 和＠ controller 之间的区别是什么

### 六、Springcloud 

​	1 、 springoloud 有哪些常用的组件这些组件的区别有哪些 
​	2 、 5 pringcloud 中的常见的注册中心有哪些 eureka nacos consul zookeeper 之间的区别是什么 
​	3 、什么是 CAP 理论 CP Ap 原则 
​	4 、负载均衡的组件是什么负载均衡的配置策略有哪些 
​	5 、 feign openfeign 有没有了解过两者之间的区别是什么？ 
​	6 、 5 pringoloud 中的容错组件有哪些服务降级如何实现服务熔断是什么断路器的原理是什么 
​	7 、 springcloud 配置中心有哪些如何实现的

### 七、rabbitmq 

#### 1 、rabbitmq 的作用是什么 ？

rabbitmq是什么：MQ全称 Message Queue（[消息队列](https://so.csdn.net/so/search?q=消息队列&spm=1001.2101.3001.7020)），是在消息的传输过程中保存消息的容器。多用于分布式系统之间进行通信，abbitmq是一款开源的，Erlang编写的，消息队列中间件； 最大的特点就是消费并不需要确保提供方存在,实现了服务之间的高度解耦 

rabbitmq的作用：

1. 服务间解耦

2. 实现异步通信

3. 流量削峰

   主要实现了消费者和生产者之间的解耦，发送异步消息，高并发访问解决流量削峰的问题。实现高性能，高可用，可伸缩和最终一致性的框架。是大型分布式系统不可缺少的中间件。

   常见的使用场景：用户订单，库存处理；用户注册，发送手机短信邮件；商品秒杀和抢购等...

#### 2 、rabbitmq  中常见的消息模型有哪些 

​	3 、rabbitmq  AMQ协议是什么
​	4、理解如何避免重复消费
​	5、如何保证消息的可靠性投递
​	6、如何保证消息消费的幂等性
​	7、死信队列有没有了解过 
​	8 、消息的发布确认机制有没有了解 
​	9 、什么是延迟队列 le 、。。 b bitmq 集群有没有搭建镜像队列是什么
​	分布式事务的实现方式有哪些？seata 可靠消息最终一致性需要