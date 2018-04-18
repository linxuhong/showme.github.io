volatile 
====
# volatile特性
内存可见性：通俗来说就是
线程A对一个volatile变量的修改，
对于其它线程来说是可见的，即线程每次获取volatile变量的值都是最新的。

## 1 volatile的使用场景
通过关键字 synchronize 可以防止多个线程进入同一段代码，
在某些特定场景中，volatile相当于一个轻量级的 synchronize，
因为不会引起线程的上下文切换，但是使用volatile必须满足两个条件：
1. 对变量的写操作不依赖当前值，如多线程下执行a++，是无法通过volatile保证结果准确性的；
2. 该变量没有包含在具有其它变量的不变式中，这句话有点拗口，看代码比较直观

```java

   class Demo {
	 
	private volatile  int i;
	void increment(){
		i++;  // 实际上是先 load  i , i++ store ，并蜚线程安全的。
	}
   }
```
 
### 1.1 使用场景 --- 状态标记量
```java

    public class Demo {
        private volatile boolean flag;
        public void run() {
            if (flag) {
               //其他逻辑
            } else {
              //正常逻辑
            }
        }
        public void setFlag(boolean flag) {
            this.flag = flag;
        }
    }
 
    
    
```
> 如果需要开启，可以通过后台设置，具体实现可以发送一个请求，
  调用 setFlag 方法并设置flag为true，由于setFlag是volatile修饰的，
  所以一经修改，其他线程都可以拿到isopen的最新值，用户请求就可以执行促销逻辑了。

` 暂时考虑考虑所有的程序在同一个虚拟机中 ?`  `   ` 



## 2 如何保证内存可见性

  在java虚拟机的内存模型中，有主内存和工作内存的概念，
  每个线程对应一个工作内存， 并共享主内存的数据，
  下面看看操作普通变量和volatile变量有什么不同：
  
  1. 对于普通变量：读操作会优先读取工作内存的数据，
     如果工作内存中不存在，则从主内存中拷贝一份数据到工作内存中；
     写操作只会修改工作内存的副本数据，
     这种情况下，其它线程就无法读取变量的最新值。
  
  2. 对于volatile变量，读操作时JMM会把工作内存中对应的值设为无效，
     要求线程从主内存中读取数据；写操作时JMM会把工作内存中对应的数据刷新到主内存中，这种情况下，其它线程就可以读取变量的最新值。
  
  3. volatile变量的内存可见性是基于内存屏障(Memory Barrier)实现的，

## 3  内存可见性涉及到哪些内容
  1. JVM 内存模型 与内存结构
  2. 计算机的CPU与内存是是如何交互的
  3。 指令重排序与内存屏障(Memory Barrier)
  
  为什么要提到这三个问题，这将给我们

### 3.1 JVM内存模型与内存结构
1. Java内存模型即Java Memory Model，简称JMM。JMM是隶属于JVM的JMM定义了Java 虚拟机(
    JVM)在计算机内存(RAM)中的工作方式。JVM是整个计算机虚拟模型，
   

2. 线程之间的通信
   `线程的通信`是指线程之间以何种机制来交换信息。
   在命令式编程中，线程之间的通信机制有两种`共享内存`和`消息传递`。

   在`共享内存`的并发模型里，线程之间共享程序的公共状态，
   线程之间通过写-读内存中的公共状态来隐式进行通信，典型的共享内存通信方式就是通过共享对象进行通信。
    > 如何 理解  Object obj = new Object(); // System.out.print(obj) // output 是对象的@一个地址位
    无论在何时\何地使用这个obj，只要你不重新赋值obj,它将一直指向内存的某块区域
    如果他被垃圾回收obj为null，你可以将这个obj(对象指针）传递到任务方法，线程中使用它。
    

   在`消息传递`的并发模型里，线程之间没有公共状态，   线程之间必须通过明确的发送消息来显式进行通信，
   在java中典型的消息传递方式就是wait()和notify()。
   
 ![a](a.png)
 ![a](mapping.jpg)
      
   Java线程之间的通信总是隐式进行，整个通信过程对程序员完全透明。
   如果编写多线程程序的Java程序员不理解隐式进行的线程之间通信的工作机制，
   很可能会遇到各种奇怪的内存可见性问题
   > 怎么样理解这句话：
     1 Object obj = new Object();如果每个线各单独new一个实例对象，是不所以的线程通讯问题的，因此object 是基local thread object
       想到了什么?  JDK中的`ThreadLocal`类的作用，对象伴随 local thread 
     > 2 当有多个thread调用 obj 的时候 ，就会出现隐藏通信问题 
      new Thread(new Runnable(){ 
          obj.setValue(xxx);
      }).start();
      new Thread(new Runnable(){ 
         if(obj.getValue() == 1 ) {
         } else {
         }
      }).start();

   1. 程序顺序规则：一个线程中的每个操作，happens- before 于该线程中的任意后续操作。
   2. 监视器锁规则：对一个监视器锁的解锁，happens- before 于随后对这个监视器锁的加锁。
   3. volatile变量规则：对一个volatile域的写，happens-before 于任意后续对这个volatile域的读。
   4. 传递性：如果A happens- before B，且B happens- before C，那么A happens- before C。
    
### 3.2 指令重排序
   1. 编译器优化重排序：编译器在不改变单线程程序语义的前提下，可以重新安排语句的执行顺序。
   2. 指令级并行的重排序：如果不存l在数据依赖性，处理器可以改变语句对应机器指令的执行顺序。
   3. 内存系统的重排序：处理器使用缓存和读写缓冲区，这使得加载和存储操作看上去可能是在乱序执行。


### 3.3 计算机CPU与Memory的交互
   
  ![CPU](CPU.jpg)
   
   
   
   
   1. top  
     PID USER      PR  NI  VIRT  RES  SHR S %CPU %MEM    TIME+  COMMAND                                                                     
     6764 root      20   0 2428m 1.1g  11m S 190.0 28.3  36:38.55 java      
   2. ps -mp 6764 -o THREAD,tid,time
      USER     %CPU PRI SCNT WCHAN  USER SYSTEM   TID     TIME
      root     44.6  19    - futex_    -      -  6766 00:23:32
      root     44.6  19    - futex_    -      -  6767 00:23:32
   3. printf "%x\n" 6766
      1a6e
   4. jstack pid |grep tid
      "GC task thread#0 (ParallelGC)" prio=10 tid=0x00007ffeb8016800 nid=0x1a6e runnable
      "GC task thread#0 (ParallelGC)" prio=10 tid=0x00007ffeb8016800 nid=0x1a6e runnable 
      "GC task thread#1 (ParallelGC)" prio=10 tid=0x00007ffeb8016800 nid=0x1a6e runnable  
      "VM Periodic Task Thread" prio=10 tid=0x00007ffeb8016800 nid=0x3700 waiting on condition 
    5. jstat -gcutil 6764 2000 10
      S0     S1     E      O      P     YGC     YGCT    FGC    FGCT     GCT   
    6. 使用jmap命令导出heapdump文件，然后拿到本地使用jvisualvm.exe分析
    7. 使用jmap -dump:format=b,file=dump.bin 6764
        jstack -l 6764 >> jstack.out
    
 

     

## 6 工程打包
	 
 

### 6.5 开发配置不同环境的jsf分组名
 
### 6.6 默认打包类型
 

                 
                      
                         
              
                       
 
 
                    
>&copy; linxuhong
 
  
  

