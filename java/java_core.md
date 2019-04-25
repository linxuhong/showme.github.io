# java
[![License](https://img.shields.io/badge/License-Apache%202.0-blue.svg)]()




### 1。 Static方法是否能被覆盖？
- 什么是静态方法？为什么要用静态方法 如何使用静态方法？
  1. 堆，方法区，程序计数器，栈、本地方法栈
  2. 静态方法是java类中以static 修改的方法，
  
- 何时加载类
  1. 调用Class.forName的时候。相当于调用Class.forName(className, true, currentLoader)},如果将true改为false,表示类不会进行初始化，那么静态块也就不会执行。
  2. 当new一个类的新的实例的时候。因为new 一个新实例相当于：Class.forName("").newInstance()
  3. 调用类的静态方法 Test.display()
  4. 调用类的静态变量  Teset.X  注意： 调用类的静态常量就不会调用静态块比如：Test.Y
    `为什么调用静态常量不会进行类加载？`
  
- 静态代码块  --》普通代码块 --》构造函数
   
  
 ```java
    class Father{
    
        public static int field = 1 ;
    
        {
            System.out.println("--- father call common block  111 ");
        }
        public Father(){
            System.out.println("-- father call init ");
        }
        static {
            System.out.println("-- father call static");
            System.out.println("-- static field "+field);
        }
    
        {
            System.out.println("--- father call   common block 222 ");
        }
    }
    public class Base {
        public static void main(String[] args) {
            System.out.println("hwllo ");
            System.out.println(Father.field);
            System.out.println(new Father());
            //   hwllo 
            //   -- father call static
            //   -- static field 1
            //   1
            //   --- father call common block  111 
            //   --- father call   common block 222 
            //  -- father call init 
        }
    }
    
```
    

- 如何理解方法的覆盖 

  1. 常量池
  2. 类变量（静态变量）
  3. 字段信息
  4. 方法信息
  5. 类的父类信息
  6. 类的访问权限信息等
  
  一个叫做方法表的东西。这个方法表是实现java多态的一个关键,只包括实例方法，并且final,private方法不饮食其中
  
- 结论是 不能覆盖静态方法







### 2。 集合不用泛型会造成什么问题？比如List list = new ArrayList();？

- 什么是范型  范型的范围
 1. 泛型，即“参数化类型”。一提到参数，最熟悉的就是定义方法时有形参，然后调用此方法时传递实参。那么参数化类型怎么理解呢？
 
 2. 顾名思义，就是将类型由原来的具体的类型参数化，类似于方法中的变量参数，此时类型也定义成参数形式（可以称之为类型形参），然后在使用/调用时传入具体的类型（类型实参）。
 
 3. 泛型的本质是为了参数化类型（在不创建新的类型的情况下，通过泛型指定的不同类型来控制形参具体限制的类型）。也就是说在泛型使用过程中，
 
 4. 操作的数据类型被指定为一个参数，这种参数类型可以用在类、接口和方法中，分别被称为泛型类、泛型接口、泛型方法。
 
 5. 上界<? extends T>不能往里存，只能往外取
 6. 下界<? super T>不影响往里存，但往外取只能放在Object对象里
 
 7 PECS（Producer Extends Consumer Super）原则，已经很好理解了：
 
 - 频繁往外读取内容的，适合用上界Extends。
 - 经常往里插入的，适合用下界Super。
 
 ```java

// 范型化接口
interface Generator<T> {
    public T next();
}


// 实现类

class FruitGenerator implements Generator<String> {

    private String[] fruits = new String[]{"Apple", "Banana", "Pear"};

    @Override
    public String next() {
        try {
            genericMethod(String.class);
        } catch (InstantiationException e) {
            e.printStackTrace();
        } catch (IllegalAccessException e) {
            e.printStackTrace();
        }
        
        Random rand = new Random();
        return fruits[rand.nextInt(3)];

    }

    public static  <T> T genericMethod(Class<T> tClass) throws InstantiationException, IllegalAccessException {
        T instance = tClass.newInstance();
        return instance;
    }
}

public class Geeneric {

//   范型化方法
    public static <T extends Comparable> void printAall(T[] a) {
        for (T item : a) {
            System.out.println(item);
        }
    }


//   使用范型的情况
    private static void comm() {
        List<String> a = new ArrayList<String>();
        a.add("1");
        a.add("b");
        a.add("c");
        System.out.println(a);
    }

//    不使用范型的情况，会报 classcastexception
    private static void notuser() {
        List list = new ArrayList();
        list.add("qqyumidi");
        list.add("corn");
        list.add(100);

        for (int i = 0; i < list.size(); i++) {
            String name = (String) list.get(i); // 1
            System.out.println("name:" + name);

        }
        System.out.println("---------");
    }

}

```



### 3。 java io   字条流，字节流

#### 字节与字

1 byte = 8 bit
1 char = 2 byte = 16 bit (Java默认UTF-16编码)


#### java io

1. 字节流继承于InputStream    OutputStream

 ```java
  class a {
    OutputStream out = new BufferedOutputStream(
            new ObjectOutputStream(
                    new FileOutputStream("/var/test/filename"));
  }
  
```
  - 还有一点是流最终写到什么地方必须要指定，
  - 要么是写到磁盘要么是写到网络中，其实从上面的类图中我们发现，
  - 写网络实际上也是写文件，只不过写网络还有一步需要处理就是底层操作系统再将数据传送到其它地方而不是本地磁盘。

2. 字节转换字符
  - InputStreamReader 类是字节到字符的转化桥梁，
  - InputStream 到 Reader 的过程要指定编码字符集，否则将采用操作系统默认字符集，很可能会出现乱码问题。 
  - StreamDecoder正是完成字节到字符的解码的实现类。也就是当你用如下方式读取一个文件时 
  

2. 字符流继承于InputStreamReader    OutputStreamWriter

```java
   class a {
       try { 
                  StringBuffer str = new StringBuffer(); 
                  char[] buf = new char[1024]; 
                  FileReader f = new FileReader("file"); 
                  while(f.read(buf)>0){ 
                      str.append(buf); 
                  } 
                  str.toString(); 
       } catch (IOException e) {}
   
   }
   
   ```
3。磁盘IO工作机制 磁盘io

  - inputstream outputstream ,reader write 只是定义 了读写接口，而最终这些字节需要落地在哪里？
    
    + 数据持久化到磁盘是一个很重要的方式
    + 数据在磁盘的唯一最小描述就是文件，也就是说上层应用程序只能通过文件来操作磁盘上的数据，文件也是操作系统和磁盘驱动器交互的一个最小单元
    + 通常情况 下java冲对应 的file对象 是一个与路径想着的虚拟对象，可能是文件或者目录。但我们并不关心它是否存在。 
    + 当我们使用 FileInputStream 对象时，
      + 会创建一个 FileDescriptor 这个fd是jvm封装的与平台无关的文件对象，
      + 所有的操作都是通过 这个执行 fd。sync（） 刷新盘。
      + 需要读取的是字符格式，所以需要 StreamDecoder 类将 byte 解码为 char 格式，
      + 至于如何从磁盘驱动器上读取一段数据，由操作系统帮我们完成。
      + 操作系统是如何将数据持久化到磁盘以及如何建立数据结构需要根据当前操作系统使用何种文件系统来回答，
       
    
3。java socket 工作机制 网络io
  — socket是什么
    + Socket 这个概念没有对应到一个具体的实体，它是描述计算机之间完成相互通信一种抽象功能。
      + 打个比方，可以把 Socket 比作为两个城市之间的交通工具，有了它，就可以在城市之间来回穿梭了。交通工具有多种，每种交通工具也有相应的交通规则。
      + Socket 也一样，也有多种。大部分情况下我们使用的都是基于 TCP/IP 的流套接字，它是一种稳定的通信协议
  - 创建过程
    + 在创建 Socket 实例的构造函数正确返回之前，
      + 将要进行 TCP 的三次握手协议，TCP 握手协议完成后，
      + Socket 实例对象将创建完成，否则将抛出 IOException 错误。
    
    + 与之对应的服务端将创建一个 ServerSocket 实例，ServerSocket 创建比较简单只要指定的端口号没有被占用，
      + 一般实例创建都会成功，同时操作系统也会为 ServerSocket 实例创建一个底层数据结构，
      + 这个数据结构中包含指定监听的端口号和包含监听地址的通配符，通常情况下都是“*”即监听所有地址。
        + 之后当调用 accept() 方法时，
        + 将进入阻塞状态，等待客户端的请求。当一个新的请求到来时，将为这个连接创建一个新的套接字数据结构，
    + 数据传输
      + 当连接已经建立成功，服务端和客户端都会拥有一个 Socket 实例，
        + 每个 Socket 实例都有一个 InputStream 和 OutputStream，
        + 正是通过这两个对象来交换数据。同时我们也知道网络 I/O 
        + 都是以字节流传输的。当 Socket 对象创建时，
      + 操作系统将会为 InputStream 和 OutputStream 分别分配一定大小的缓冲区，
        + 数据的写入和读取都是通过这个缓存区完成的。
        + 写入端将数据写到 OutputStream 对应的 SendQ 队列中，
        + 当队列填满时，数据将被发送到另一端 InputStream 的 RecvQ 队列中，
          + 如果这时 RecvQ 已经满了，那么 OutputStream 的 write 方法将会阻塞
          + 直到 RecvQ 队列有足够的空间容纳 SendQ 发送的数据。，
          + 值得特别注意的是 这个缓存区的大小以及写入端的速度和读取端的速度非常影响这个连接的数据传输效率，
      + 由于可能会发生阻塞，
        + 所以网络 I/O 与磁盘 I/O 在数据的写入和读取还要有一个协调的过程，
        + 如果两边同时传送数据时可能会产生死锁，在后面 NIO 部分将介绍避免这种情况。

4. BIO NIO
   - BIO
     + IO 即阻塞 I/O，不管是磁盘 I/O 还是网络 I/O，数据在写入 OutputStream 或者从 InputStream 读取时都有可能会阻塞。
   - NIO
     + Channel 和 Selector，它们是 NIO 中两个核心概念。
     + 这里的 Channel 要比  Socket 更加具体，它可以比作为某种具体的交通工具，如汽车或是高铁等，
      + 而 Selector 可以比作为一个车站的车辆运行调度系统，它将负责监控每辆车的当前运行状态：是已经出战还是在路上等等，也就是它可以轮询每个 Channel 的状态。
      + 这里还有一个 Buffer 类，它也比 Stream 更加具体化，将它比作为车上的座位，
      + Channel 是汽车的话就是汽车上的座位，高铁上就是高铁上的座位，它始终是一个具体的概念，与 Stream 不同。
        + Stream 只能代表是一个座位，至于是什么座位由你自己去想象，也就是你在去上车之前并不知道，
        + 这个车上是否还有没有座位了，也不知道上的是什么车，因为你并不能选择，
          + 这些信息都已经被封装在了运输工具（Socket）里面了，对你是透明的。
     + NIO 引入了 Channel、Buffer 和 Selector 就是想把这些信息具体化，让程序员有机会控制它们，
       + 如：当我们调用 write() 往 SendQ 写数据时，当一次写的数据超过 SendQ 长度是需要按照 
       + SendQ 的长度进行分割，这个过程中需要有将用户空间数据和内核地址空间进行切换，而这个切换不是你可以控制的。
       + 而在 Buffer 中我们可以控制 Buffer 的 capacity，并且是否扩容以及如何扩容都可以控制。
        ```java
        class a {
           public void selector() throws IOException {
                   ByteBuffer buffer = ByteBuffer.allocate(1024);
                   Selector selector = Selector.open();
                   ServerSocketChannel ssc = ServerSocketChannel.open();
                   ssc.configureBlocking(false);//设置为非阻塞方式
                   ssc.socket().bind(new InetSocketAddress(8080));
                   ssc.register(selector, SelectionKey.OP_ACCEPT);//注册监听的事件
                   while (true) {
                       Set selectedKeys = selector.selectedKeys();//取得所有key集合
                       Iterator it = selectedKeys.iterator();
                       while (it.hasNext()) {
                           SelectionKey key = (SelectionKey) it.next();
                           if ((key.readyOps() & SelectionKey.OP_ACCEPT) == SelectionKey.OP_ACCEPT) {
                               ServerSocketChannel ssChannel = (ServerSocketChannel) key.channel();
                            SocketChannel sc = ssChannel.accept();//接受到服务端的请求
                               sc.configureBlocking(false);
                               sc.register(selector, SelectionKey.OP_READ);
                               it.remove();
                           } else if 
                           ((key.readyOps() & SelectionKey.OP_READ) == SelectionKey.OP_READ) {
                               SocketChannel sc = (SocketChannel) key.channel();
                               while (true) {
                                   buffer.clear();
                                   int n = sc.read(buffer);//读取数据
                                   if (n <= 0) {
                                       break;
                                   }
                                   buffer.flip();
                               }
                               it.remove();
                           }
                       }
                   }
           }
         }
   ```
      + 上面的这段程序中，是将 Server 端的监听连接请求的事件和处理请求的事件放在一个线程中，但是在实际应用中，
      + 我们通常会把它们放在两个线程中，一个线程专门负责监听客户端的连接请求，而且是阻塞方式执行的；
      + 另外一个线程专门来处理请求，这个专门处理请求的线程才会真正采用 NIO 的方式    
       

5. 磁盘io优化 io wait  @%
   + 增加缓存，减少磁盘访问次数
   + 优化磁盘的管理系统，设计最优的磁盘访问策略，以及磁盘的寻址策略，这里是在底层操作系统层面考虑的。
   + 设计合理的磁盘存储数据块，以及访问这些数据块的策略，这里是在应用层面考虑的。如我们可以给存放的数据设计索引，通过寻址索引来加快和减少磁盘的访问，还有可以采用异步和非阻塞的方式加快磁盘的访问效率。
   + 应用合理的 RAID 策略提升磁盘 IO，每种 RAID 的区别我们可以用下表所示

6，网络 I/O 优化
  + 一个是减少网络交互的次数：要减少网络交互的次数通常我们在需要网络交互的两端会设置缓存，
    + 比如 Oracle 的 JDBC 驱动程序，就提供了对查询的 SQL 结果的缓存，在客户端和数据库端都有，可以有效的减少对数据库的访问。
    + 合并访问请求：如在查询数据库时，我们要查 10 个 id，我可以每次查一个 id，也可以一次查 10 个 id。
    + 一个页面时通过会有多个 js 或 css 的文件，我们可以将多个 js 文件合并在一个 HTTP 链接中，每个文件用逗号隔开，然后发送到后端 Web 服务器根据这个 URL 链接，再拆分出各个文件，
    
  + 减少网络传输数据量的大小：减少网络数据量的办法通常是将数据压缩后再传输，如 HTTP 请求中，通常 Web 服务器将请求的 Web 页面 gzip 压缩后在传输给浏览器。 
  + 尽量减少编码：通常在网络 I/O 中数据传输都是以字节形式的，尽量直接以字节形式发送。也就是尽量提前将字符转化为字节，或者减少字符到字节的转化过程。



### 4 sleep()和wait() 区别

 
1. Call on:
        + wait():  当前普通java对象方法
        + sleep(): 当前纯种休眠指定时间 后执行。
2.  同步 :
        + wait():  通常情况下存在多线程同步的时候使用wait'
        + sleep(): 等待条件达到时继续执行
3.  持有锁的情况:
        + wait():  释放当前纯种持 有的锁
        + sleep(): 持有锁
4.  唤醒条件:
        + wait(): 对象调用notify或者notifiall
        + sleep(): 时间到了或者被中断
5.  entryset witset ,notify notifyall唤醒的范围 ？？


### 5 synchronized和volatile

1. volatile : 
   + 可变的易变的。锁的我互斥和可见性，这样一次就只能有一个线程个性共享数据
   + volatile 亦是： 轻量级synchronized 保证可见性。不保证原子性，在读多玩定的情况 下
     - volatile可以提供更高的性能，它并不阻塞纯种
     - volatile阻止指令重新排序，但是其有两个使用条件
       - 变量的写操作不依赖于当前值。volatile int a = 1； a = a+1；
       - 该变量没有包含在具有其他变量的不变式中
   + 使用场景 ：
       + 模式 #1：状态标志
       
         volatile boolean shutdownRequested;
          
         public void shutdown() { shutdownRequested = true; }
          
         public void doWork() { 
             while (!shutdownRequested) { 
                 // do stuff
             }
         }
       
       - 模式 #2：一次性安全发布（one-time safe publication）
         public class BackgroundFloobleLoader {
             public volatile Flooble theFlooble;
          
             public void initInBackground() {
                 // do lots of stuff
                 theFlooble = new Flooble();  // this is the only write to theFlooble
             }
         }
          
         public class SomeOtherClass {
             public void doWork() {
                 while (true) { 
                     // do some stuff...
                     // use the Flooble, but only if it is ready
                     if (floobleLoader.theFlooble != null) 
                         doSomething(floobleLoader.theFlooble);
                 }
             }
         }
        
       - 模式 #2：一次性安全发布（one-time safe publication）
      
2. jvm层面的volatile
   MM对volatile变量定义的特殊规则假定T表示一个线程，V和W分别表示两个volatile变量，
   那么在进行read、load、use、assign、store、write操作时需要满足以下规则：
   + 1、load + use 必须成对出现。
       + 这条规则要求在工作内存中，
       + 每次使用V前都必须先从主内存中刷新最新的值，用于保证能看见其他线程对变量V所做的修改后的值。
   + 2、assign + store 必须成对出现。
       + 这条规则要求在工作内存中，
       + 每次修改V后都必须立刻同步回主内存中，用于保证其他线程可以看到自己对变量V的修改
   


### 6 同学方法 同步代码块
1. 同步的是什么
   - 同步方法：表态方法就是同步在 class 普通方法就是当前实例 this
   - 同步代码块：任何非null的对象 
2. 为什么要出现同步代码块


### 7 Lock和synchronzied区别





### 8 Java中偏向锁，自旋锁，轻量级锁，和重量级锁
1. 锁的类型： 锁的状态总共有四种：无锁状态、偏向锁、轻量级锁和重量级锁。随着锁的竞争
> 重量级锁通常是 synchronized 依赖于操作系统的mutex lock

2. 偏向锁 ： -XX:-UseBiasedLocking来禁用偏向锁。锁的状态保存在对象的头文件中，以32位的JDK为例：

3. 轻量级锁
   - 轻量级锁并不是用来代替重量级锁的，它的本意是在没有多线程竞争的前提下，
     - 减少传统的重量级锁使用产生的性能消耗。在解释轻量级锁的执行过程之前，
     - 先明白一点，轻量级锁所适应的场景是线程交替执行同步块的情况，
     - 如果存在同一时间访问同一锁的情况，就会导致轻量级锁膨胀为重量级锁。
4. 轻量级锁
   - （1）在代码进入同步块的时候，
         - 如果同步对象锁状态为无锁状态（锁标志位为“01”状态，是否为偏向锁为“0”），
         - 虚拟机首先将在当前线程的栈帧中建立一个名为锁记录（Lock Record）的空间，用于存储锁对象目前的Mark Word的拷贝，官方称之为 Displaced Mark Word。
          
   - （2）拷贝对象头中的Mark Word复制到锁记录中。
   = （3）拷贝成功后，虚拟机将使用CAS操作尝试将对象的Mark Word更新为指向Lock Record的指针，并将Lock record里的owner指针指向object mark word。
         - 如果更新成功，则执行步骤（3），
         - 否则执行步骤（4）。
   - （4）如果这个更新动作成功了，那么这个线程就拥有了该对象的锁，并且对象Mark Word的锁标志位设置为“00”，即表示此对象处于轻量级锁定状态，这时候线程堆栈与对象头的状态如图2.2所示。
   - （5） 如果这个更新操作失败了，虚拟机首先会检查对象的Mark Word是否指向当前线程的栈帧，如果是就说明当前线程已经拥有了这个对象的锁，那就可以直接进入同步块继续执行。否则说明多个线程竞争锁，轻量级锁就要膨胀为重量级锁，锁标志的状态值变为“10”，Mark Word中存储的就是指向重量级锁（互斥量）的指针，后面等待锁的线程也要进入阻塞状态。 
         而当前线程便尝试使用自旋来获取锁，自旋就是为了不让线程阻塞，而采用循环去获取锁的过程。

5、偏向锁获取过程：

　　-（1）访问Mark Word中偏向锁的标识是否设置成1，锁标志位是否为01——确认为可偏向状态。

　　-（2）如果为可偏向状态，则测试线程ID是否指向当前线程，如果是，进入步骤（5），否则进入步骤（3）。

　　-（3）如果线程ID并未指向当前线程，则通过CAS操作竞争锁。如果竞争成功，则将Mark Word中线程ID设置为当前线程ID，然后执行（5）；如果竞争失败，执行（4）。

　　-（4）如果CAS获取偏向锁失败，则表示有竞争。当到达全局安全点（safepoint）时获得偏向锁的线程被挂起，偏向锁升级为轻量级锁，然后被阻塞在安全点的线程继续往下执行同步代码。

6. 锁优化
   - 1 适应性自旋（Adaptive Spinning）
   - 2 锁粗化（Lock Coarsening）
   - 3 锁消除（Lock Elimination）
   
7. 比较

| 锁   |      优点      |  缺点 |   适用场景   |      |
|----------|:-------------:|------:|------:------:
| 偏向锁   |  加锁和解锁不需要额外的消耗，和执行非同步方法比仅存在纳秒级的差距 | 如果线程间存在锁竞争，会带来额外的锁撤销的消耗。 |适用于只有一个线程访问同步块场景 |  
| 轻量级锁 |    竞争的线程不会阻塞，提高了程序的响应速度。   |   如果始终得不到锁竞争的线程使用自旋会消耗CPU |追求响应时间。同步块执行速度非常快 | 
| 重量级锁 | 线程竞争不使用自旋，不会消耗CPU |    线程阻塞，响应时间缓慢 |追求吞吐量 同步块执行速度较长 | 
 
简书著作权归作者所有，任何形式的转载都请联系作者获得授权并注明出处。

    
### 8 无锁化编程的途径
1. 什么是无锁
  
   - 无锁，英文一般翻译为lock-free，是利用处理器的一些特殊的原子指令来避免传统并行设计中对锁的使用

2. 为什么要无锁?
   - 首先是性能考虑。
     - 通信项目一般对性能有极致的追求，这是我们使用无锁的重要原因。当然，无锁算法如果实现的不好，性能可能还不如使用锁，所以我们选择比较擅长的数据结构和算法进行lock-free实现，比如Queue，对于比较复杂的数据结构和算法我们通过lock来控制，比如Map（虽然我们实现了无锁Hash，但是大小是限定的，而Map是大小不限定的）。
     - 对于性能数据，后续文章会给出无锁和有锁的对比。
   - 其次是避免锁的使用引起的错误和问题。
     - 死锁(dead lock)：两个以上线程互相等待
     - 锁护送(lock convoy)：多个同优先级的线程反复竞争同一个锁，抢占锁失败后强制上下文切换，引起性能下降
     - 优先级反转(priority inversion)：低优先级线程拥有锁时被中优先级的线程抢占，而高优先级的线程因为申请不到锁被阻塞

3. 系统层面支持
   - 比较 A 与 V 是否相等。（比较）
   - 如果比较相等，将 B 写入 V。（交换）
   - 返回操作是否成功。
4. cas  存在 AB的总是ß
     ```java
     class AtomicInteger {
       int  incrementAndGet() {
           public final int getAndAddInt(Object var1, long var2, int var4) {
               int var5;
               do {
                   var5 = this.getIntVolatile(var1, var2);
               } while(!this.compareAndSwapInt(var1, var2, var5, var5 + var4));
       
               return var5;
            }
       }
     }
     
     ```
     
    - CAS需要在操作值的时候检查下值有没有发生变化，如果没有发生变化则更新，但是如果一个值原来是A，变成了B，又变成了A，
    - 那么使用CAS进行检查时会发现它的值没有发生变化，但是实际上却变化了。这就是CAS的ABA问题

    
 
  
### 9 Java线程池corePoolSize, maximuPoolSize, workQueue的含义

1. 为什么要用线程池

   - new Thread的弊端：
        
       - 每次new Thread新建对象性能差。
       - 线程缺乏统一管理，可能无限制新建线程，相互之间竞争，及可能占用过多系统资源导致死机或oom。
       - 缺乏更多功能，如定时执行、定期执行、线程中断。
       
   - 线程池好处
       - 重用存在的线程，减少对象创建、消亡的开销，性能佳。
       - 可有效控制最大并发线程数，提高系统资源的使用率，同时避免过多资源竞争，避免堵塞。
       - 提供定时执行、定期执行、单线程、并发数控制等功能
2. java core中线程池的介绍：Java通过Executors提供四种线程池：
   
   
   - newCachedThreadPool创建一个可缓存线程池，如果线程池长度超过处理需要，可灵活回收空闲线程，若无可回收，则新建线程。
   
   - newFixedThreadPool 创建一个定长线程池，可控制线程最大并发数，超出的线程会在队列中等待。
   
   - newScheduledThreadPool 创建一个定长线程池，支持定时及周期性任务执行。
   
   - newSingleThreadExecutor 创建一个单线程化的线程池，它只会用唯一的工作线程来执行任务，保证所有任务按照指定顺序(FIFO, LIFO, 优先级)执行。

3，能用作法：一般需要根据任务的类型来配置线程池大小
    
   - 如果是CPU密集型任务，就需要尽量压榨CPU，参考值可以设为  cpu核数+1
   - 如果是IO密集型任务，参考值可以设置为2*cpu核数
   >> 当然，这只是一个参考值，具体的设置还需要根据实际情况进行调整，比如可以先将线程池大小设置为参考值，再观察任务运行情况和系统负载、资源利用率来进行适当调整。

4. 关闭线程池
   - 我们知道如果使用shutdownNow()方法终止线程池的时候，有可能会抛出异常，
   - 而且他会将那些已添加到队列中的任务抛弃。
   - 所以我们一般会使用shutdown()方法来终止线程池。
     - 但是只使用该方法的话，我们没办法办法知道线程池是否已经被终止了，
     - 比如，有个任务阻塞了，那就会导致线程池关闭不了。
   - 所以我们最好在调用了shutdown（）方法后再调用awaitTermination()方法来查看线程池关闭的状态，注意，该方法只是用来查看线程池关闭的状态，并不能完成终止线程的任务。

5. 工程队列与线程池的关系
   
   - 构造方法：ThreadPoolExecutor(int corePoolSize, int maximumPoolSize, long keepAliveTime, TimeUnit unit, BlockingQueue<Runnable> workQueue, ThreadFactory threadFactory, RejectedExecutionHandler handler)
   
       - corePoolSize：核心线程数即一直保留在线程池中的线程数量，即使处于闲置状态也不会被销毁。要设置 allowCoreThreadTimeOut 为 true，才会被销毁。
       - maximumPoolSize：线程池中允许存在的最大线程数
       - keepAliveTime：非核心线程允许的最大闲置时间，超过这个时间就会本地销毁。
       - workQueue：用来存放任务的队列。
   
   
   - 工作队列BlockingQueue的存取策略：
       - 当工作线程数量小于corePoolSize时，新提交的任务总是会由新创建的工作线程执行，不入队列
       - 当工作线程数量大于corePoolSize，  如果工作队列没满，新提交的任务就入队列
       - 当工作线程数量大于corePoolSize，小于MaximumPoolSize时，如果工作队列满了，新提交的任务就交给新创建的工作线程，不入队列
       - 当工作线程数量大于MaximumPoolSize，并且工作队列满了，那么新提交的任务会被拒绝执行。具体看采用何种拒绝策略
         


### 10 hashnap原理 







### 11 ConcurrentHashMap 原理 










### 12 ThreadLocal 






###  ClassLoader继承关系和过程？

1. 加载
 - 加载过程是JVM类加载的第一步，如果JVM配置中打开-XX:+TraceClassLoading，我们可以在控制台观察到类似

  - [Loaded chapter7.SubClass from file:/E:/EclipseData-Mine/Jvm/build/classes/]
 
  - 加载过程是作为程序猿最可控的一个阶段，因为你可以随意指定类加载器，甚至可以重写loadClass方法，当然，在jdk1.2及以后的版本中，loadClass方法是包含双亲委派模型的逻辑代码的，所以不建议重写这个方法，而是鼓励重写findClass方法。
  - 类加载的二进制字节码文件可以来自jar包、网络、数据库以及各种语言的编译器编译而来的.class文件等各种来源。
  - 加载过程主要完成如下三件工作：
    - 1 通过类的全限定名（包名+类名）来获取定义此类的二进制字节流
    - 2 将字节流所代表的静态存储结构转化为运行时数据结构存储在方法区
    - 3 为类生成java.lang.Class对象，并作为该类的唯一入口

    - 这里涉及到一个概念就是类的唯一性，书上对该概念的解释是：在类的加载过程中，
         - 一个类由类加载器和类本身唯一确定。也就是说，如果一个JVM虚拟机中有多个不同加载器，
         - 即使他们加载同一个类文件，那得到的java.lang.Class对象也是不同的。
         - 因此，只有在同一个加载器中，一个类才能被唯一标识，这叫做类加载器隔离。
         
2. 验证 验证过程主要包含四个验证过程
    ：
    - 1 文件格式验证
      四个验证过程中，只有格式验证是建立在二进制字节流的基础上的。
      格式验证就是对文件是否是0xCAFEBABE开头、class文件版本等信息进行验证，
      确保其符合JVM虚拟机规范。
    - 2 元数据验证
    元数据验证是对源码语义分析的过程，验证的是子类继承的父类是否是final类；如果这个类的父类是抽象类，是否实现了起父类或接口中要求实现的所有方法；子父类中的字段、方法是否产生冲突等，这个过程把类、字段和方法看做组成类的一个个元数据，然后根据JVM规范，对这些元数据之间的关系进行验证。所以，元数据验证阶段并未深入到方法体内。
    - 3 字节码验证
        既然元数据验证并未深入到方法体内部，那么到了字节码验证过程，这一步就不可避免了。
        字节码主要是对方法体内部的代码的前后逻辑、关系的校验，例如：字节码是否执行到了方法体以外、类型转换是否合理等。
        当然，这很复杂。所以，即使是到了如今jdk1.8，也还是无法完全保证字节码验证准确无遗漏的。而且，
        如果在字节码验证浪费了大量的资源，似乎也有些得不偿失。
    - 4 符号引用验证
      符号引用的验证其实是发生在符号引用向直接引用转化的过程中，而这一过程发生在解析阶段。
      因为都是验证，所以一并在这讲。符号引用验证做的工作主要是验证字段、
      类方法以及接口方法的访问权限、根据类的全限定名是否能定位到该类等。具体过程会在接下来的解析阶段进行分析。
      好了，验证阶段的工作基本就是以上四类，下面我们来看下一个阶段。

3. 准备
    相信经历过艰辛的验证阶段的磨练，JVM和我们都倍感疲惫。所以，接下来的准备阶段给我们提供了一个相对轻松的休息阶段。
    准备阶段要做的工作很简单，他瞄准了类变量这个元数据，把他放进了方法区并进行了初始化，这里的初始化并不是<init>或者<clinit>操作，准备阶段只是将这些可爱的类变量置零。
    图片描述

4. 解析
    这一部分我画了几个图，内容有些多，放在另一篇文章里：解析

5. 初始化
    - 初始化阶段是我们可以大搞实验的一块实验田。首先，初始化阶段做什么？这个阶段就是执行<clinit>方法。而<clinit>方法是由编译器按照源码顺序依次扫描类变量的赋值动作和static代码块得到的。
    - 那么问题来了，啥时候才会触发一个类的初始化的操作呢？答案有且只有五个：
        - 1 在类没有进行过初始化的前提下，当执行new、getStatic、setStatic、invokeStatic字节码指令时，类会立即初始化。对应的java操作就是new一个对象、读取/写入一个类变量（非final类型）或者执行静态方法。
        - 2 在类没有进行过初始化的前提下，当一个类的子类被初始化之前，该父类会立即初始化。
        - 3 在类没有进行过初始化的前提下，当包含main方法时，该类会第一个初始化。
        - 4 在类没有进行过初始化的前提下，当使用java.lang.reflect包的方法对类进行反射调用时，该类会立即初始化。
        - 5 在类没有进行过初始化的前提下，当使用JDK1.5支持时，如果一个java.langl.incoke.MethodHandle实例最后的解析结果REF_getStatic、REF_putStatic、REF_invokeStatic的方法句柄，并且这个方法句柄所对应的类没有进行过初始化，则需要先触发其初始化。
        
    - 以上五种情况被称作类的五种主动引用，除此之外的任何情况都被相应地叫做被动引用。以下是集中常见的且容易迷惑人心智的被动引用的示例：


### 克隆

1. Cloneable 实现这个接口，虽然啥也不做！如果不实现，则直接报错。
2. Teacher 克隆后，其中的 teacher成员，与原始对象指向的是同一个 ??? 

    ```java
    public class Clone {
        public static void main(String[] args) throws CloneNotSupportedException {
            Student p = new Student();
            p.setId("111");
            Teacher t = new Teacher();
            t.setId("----00000----");
    
            p.setTeacher(t);
            Student target = (Student) p.clone();
    
            // output  111
            System.out.println(target.getId());
    
            // output  ----00000----
            System.out.println(target.getTeacher().getId());
    
            // true
            System.out.println(p.getTeacher() == target.getTeacher());
        }
    }
    
 
    class Teacher {
        public String getName() {  return name; }
        public void setName(String name) {     this.name = name; }
        public String getId() {  return id; }
        public void setId(String id) {    this.id = id; }
    
        private String name;
        private String id;
    }
    
    class Student implements Cloneable {
    
        public Teacher getTeacher() { return teacher; }
        public void setTeacher(Teacher teacher) {  this.teacher = teacher; }
        public String getName() { return name;  }
        public void setName(String name) { this.name = name; }
        public String getId() { return id; }
        public void setId(String id) { this.id = id;}
    
        private String name;
        private String id;
        private Teacher teacher;
    
        public Object clone() throws CloneNotSupportedException {
            return super.clone();
        }
    }
    
    
    ```
### 锁 （相对悲观锁而言，乐观锁机制采取了更加宽松的加锁机制）

。
1. 悲观锁大多数情况下依靠数据库的锁机制实现，以保证操作最大程度的独占性。但随之而来的就是数据库性能的大量开销，特别是对长事务而言，这样的开销往往无法承受。
2. 而乐观锁机制在一定程度上解决了这个问题。乐观锁，大多是基于数据版本（ Version ）记录机制实现。
   > 何谓数据版本？即为数据增加一个版本标识，在基于数据库表的版本解决方案中，一般是通过为数据库表增加一个 “version” 字段来实现。
     读取出数据时，将此版本号一同读出，之后更新时，对此版本号加一。此时，将提交数据的版本数据与数据库表对应记录的当前版本信息进行比对，
     如果提交的数据版本号大于数据库表当前版本号，则予以更新，否则认为是过期数据。


3. 在InnoDB中，会在每行数据后添加两个额外的隐藏的值来实现MVCC。
   - 一个值记录这行数据何时被创建
   - 一个值记录这行数据何时过期（或者被删除） 
   - 在实际操作中，存储的并不是时间，而是事务的版本号，每开启一个新事务，事务的版本号就会递增。 在可重读Repeatable reads事务隔离级别下：

     - SELECT时，读取创建版本号<=当前事务版本号，删除版本号为空或>当前事务版本号。
     - INSERT时，保存当前事务版本号为行的创建版本号
     - DELETE时，保存当前事务版本号为行的删除版本号
     - UPDATE时，插入一条新纪录，保存当前事务版本号为行创建版本号，同时保存当前事务版本号到原来删除的行

     > 通过MVCC，虽然每行记录都需要额外的存储空间，更多的行检查工作以及一些额外的维护工作，但可以减少锁的使用，大多数读操作都不用加锁，读数据操作很简单，性能很好，并且也能保证只会读取到符合标准的行，也只锁住必要行。

4. 如何避免

   - 以固定的顺序访问表和行。简单方法是对某一列先排序，后执行，这样就避免了交叉等待锁的情形。
   - 大事务拆小。大事务更倾向于死锁，如果业务允许，将大事务拆小。
   - 在同一个事务中，尽可能做到一次锁定所需要的所有资源，减少死锁概率。
   - 降低隔离级别。如果业务允许，将隔离级别调低也是较好的选择，比如将隔离级别从RR调整为RC，可以避免掉很多因为gap锁造成的死锁。
   - 为表添加合理的索引。可以看到如果不走索引将会为表的每一行记录添加上锁，死锁的概率大大增大。

5. 分析
   - show processlist; #查看正在执行的sql （show full processlist;查看全部sql）

   - mysql> kill id #杀死sql进程；
   - 如果进程太多找不到，就重启mysql
    - /ect/init.d/mysql restart
    - 或/ect/init.d/mysql stop（如果关不掉就直接kill -9 进程id）  再/ect/init.d/mysql start
   - mysql日志文件是否保存死锁日志：
     - 常用目录：/var/log/mysqld.log；（该目录还有其它相关日志文件就都看看）


5. 不周情况下的锁级别
   - 在RR级别中，通过MVCC机制，虽然让数据变得可重复读，但我们读到的数据可能是历史数据，是不及时的数据，不是数据库当前的数据！这在一些对于数据的时效特别敏感的业务中，就很可能出问题。
   - 对于这种读取历史数据的方式，我们叫它快照读 (snapshot read)，而读取数据库当前版本数据的方式，叫当前读 (current read)。很显然，在MVCC中：

     - 快照读：就是select
      > select * from table ....;
     
     - 当前读：特殊的读操作，插入/更新/删除操作，属于当前读，处理的都是当前的数据，需要加锁。
       - select * from table where ? lock in share mode;
       - select * from table where ? for update;
       - insert;
       - update;
       - delete;

### SSl


### java程序性能优化


### jps

1. jps功能

  jps [options] [hostid]  
  如果不指定hostid就默认为当前主机或服务器。
  
  命令行参数选项说明如下：
  
  -q 不输出类名、Jar名和传入main方法的参数  
  -m 输出传入main方法的参数  
  -l 输出main类或Jar的全限名  
  -v 输出传入JVM的参数  
  
  $ jps -ml  
  
  
  jstack
  jstack主要用来查看某个Java进程内的线程堆栈信息。语法格式如下
  
  ps -ef | grep wordcount | grep -v grep  
  ps -Lfp 2860 
  
  
### 代理

1 静态代理  ：暂时不讨论
2 动态代理
  - 接口类
  - 业务类，实现接口
  - InvocationHandler实现类
  - Proxy.newProxyInstance生成 一个接口类的子类，并且实现业务接口类，
    - 该动态生成的代理类，会实现每个业务接口方法，
    - 代理类通过反射来统一代码invoke方法来实现对业务实现类的代理与加持。
  - 其实本质就是根据业务接口的metadata，生成class的二进制数据，写入内存中
    - ProxyGenerator.generateProxy 这个是生成 字节能码的关键类。
    - 我debug了一下，发现代理类的包与，接口在同一个包下面。（目前接口与实现灰在同个）
3. 到了最后 还是要对jvm class 字节码要熟悉。spring的aop依旧如此，只不过是用了相应 的框架来实现

   ```java
    public class TestProxy {
        public static void main(String[] args) throws IOException {
            Dynamic proxy = new Dynamic();
            IBiz biz = (IBiz) proxy.bind(new BizImp());
            String rs = biz.doBiz("aa");
            System.out.println(rs);
    
            // Proxy.newProxyInstance 究竟是啥 内容
            byte[] proxyClass = ProxyGenerator.generateProxyClass(biz.getClass().getSimpleName(), biz.getClass().getInterfaces());
    
            // 我们将字节码，保存到 本地，然后反编译查看
            FileOutputStream outputStream = new FileOutputStream(new File("$Proxy0.class"));
            outputStream.write(proxyClass);
            outputStream.flush();
            outputStream.close();
    
        }
    }
    
    
    class Dynamic implements InvocationHandler {
        // 目标对象
        private Object target;
    
        public Object bind(Object delegate) {
            this.target = delegate;
            return Proxy.newProxyInstance(delegate.getClass().getClassLoader(), delegate.getClass().getInterfaces(), this);
        }
    
        @Override
        public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
            System.out.println("--begin---");
            Object rs = method.invoke(target, args);
            System.out.println("---end----");
            return rs;
        }
    }
    
    
    interface IBiz {
        String doBiz(String id);
    }
 
    class BizImp implements IBiz {
        @Override
        public String doBiz(String id) {
            return " " + id;
        }
    }
    
    
    // 运行时生成 的中间代理类
    public final class $Proxy0 extends Proxy implements IBiz {
            private static Method m1;
            private static Method m3;
            private static Method m2;
            private static Method m0;
        
            // invoke handle 实际作为代理类的构造参数
            public $Proxy0(InvocationHandler var1)    {
                super(var1);
            }
        
            public final String doBiz(String var1)    {
                // 看到了么 ，这里通过invoke来调用i invoke handle的代理类
                return (String)super.h.invoke(this, m3, new Object[]{var1});
            }
       
            static {
                try {
                    m1 = Class.forName("java.lang.Object").getMethod("equals", Class.forName("java.lang.Object"));
                    m3 = Class.forName("http.aop.IBiz").getMethod("doBiz", Class.forName("java.lang.String"));
                    m2 = Class.forName("java.lang.Object").getMethod("toString");
                    m0 = Class.forName("java.lang.Object").getMethod("hashCode");
                } catch (NoSuchMethodException var2) {
                    throw new NoSuchMethodError(var2.getMessage());
                } catch (ClassNotFoundException var3) {
                    throw new NoClassDefFoundError(var3.getMessage());
                }
            }
        }

   ```
 



### spring  事务

1 REQUIRED：如果当前存在事务，则加入该事务；如果当前没有事务，则创建一个新的事务。这是默认值。

2 REQUIRES_NEW：创建一个新的事务，如果当前存在事务，则把当前事务挂起。

3 SUPPORTS：如果当前存在事务，则加入该事务；如果当前没有事务，则以非事务的方式继续运行。

4 NOT_SUPPORTED：以非事务方式运行，如果当前存在事务，则把当前事务挂起。

5 NEVER：以非事务方式运行，如果当前存在事务，则抛出异常。

6 MANDATORY：如果当前存在事务，则加入该事务；如果当前没有事务，则抛出异常。

NESTED：如果当前存在事务，则创建一个事务作为当前事务的嵌套事务来运行；如果当前没有事务，则该取值等价于REQUIRED。 
### java class
 
   
     
# License

* [Wiki]()

[]:https://maven.apache.org





























