# java
[![License](https://img.shields.io/badge/License-Apache%202.0-blue.svg)](https://github.com/weibocom/motan/blob/master/LICENSE)




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

### 字节与字

1 byte = 8 bit
1 char = 2 byte = 16 bit (Java默认UTF-16编码)


### java io

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



### sleep()和wait() 区别

 
1.Call on:
    + wait():  当前普通java对象方法
    + sleep(): 当前纯种休眠指定时间 后执行。
2. 同步 :
    + wait():  通常情况下存在多线程同步的时候使用wait'
    + sleep(): 等待条件达到时继续执行
3. 持有锁的情况:
    + wait():  释放当前纯种持 有的锁
    + sleep(): 持有锁
4.  唤醒条件:
    + wait(): 对象调用notify或者notifiall
    + sleep(): 时间到了或者被中断
5.  entryset witset ,notify notifyall唤醒的范围 ？？


### synchronized和volatile

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
         ublic class BackgroundFloobleLoader {
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
   


### 同学方法 同步代码块
1. 同步的是什么
   - 同步方法：表态方法就是同步在 class好u 普通方法就是当前实例 this
   - 同步代码块：任何非null的对象 
2. 为什么要出现同步代码块


### Lock和synchronzied区别





### Java中偏向锁，自旋锁，轻量级锁，和重量级锁
1. 重量级锁：通常情况 下批的是 syn书法家d


     
 
[Motan-PHP](https://github.com/weibocom/motan-php) is PHP client can interactive with Motan server directly or through Motan-go agent.

[Motan-openresty](https://github.com/weibocom/motan-openresty) is a Lua(Luajit) implementation based on [Openresty](http://openresty.org)
 
* [Wiki](https://github.com/weibocom/motan/wiki)
* [Wiki(中文)](https://github.com/weibocom/motan/wiki/zh_overview)

# Contributors

* maijunsheng([@maijunsheng](https://github.com/maijunsheng))

# License

Motan is released under the [Apache License 2.0](http://www.apache.org/licenses/LICENSE-2.0).

[maven]:https://maven.apache.org























