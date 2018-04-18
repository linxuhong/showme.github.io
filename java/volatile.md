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
   
  逻辑CPU个数 = cpu物理 * 核心
  而所有的对象 new Object(),实际分配在主存，本质上多线程的时候，访问主存对象时，其实是将主存的数据加载到cpu缓存中
  实际上线程在不同核心运行时，如果操作同一主存对象时，每个线程理论上是访问的cpu cache中的数据，而这个数据与请在中的实际状态并不相同
  实际上还是依赖于cpu指令集
  
   问题，a,b,c三个变量，其中c是volatile的，a，b是普通变量，
   a = 1, b = 2, c = 3, 
   c写入之后，a,b的值也会被刷入缓存吗，
   + 还是 c 写入之前所有在cpu缓存的数据都会被刷入内存，
   + 还是只刷入和 c 在同一个缓存行的数据？
         
   `一开始，我的认知是后者，只刷入同一个缓存行的数据。`
         
   答
   -  要按 happens before 来考虑这种问题，不需要考虑（cache），
   -  在JMM中，如果一个操作执行的结果需要对另一个操作可见，那么这两个操作之间必须存在happens-before关系   -  Java的修正过的内存模型其实基本点很简单，
   -  同一线程内的副作用按程序顺序发生，
   -  所以a、b、c的赋值如果是在同一线程内按这个顺序写的，
   -  实际执行就要按照这个顺序发生（至少表象上要按照这个顺序；
   -  在程序无法感知顺序差异时可以作弊）
   -  这样就是a赋值happens before b赋值，b赋值happens before c赋值，
   -  而不同线程之间的操作则是没有happens before关系的，
   -  除非有volatile或者synchronized等带有跨线程happen before关系的操作。
   -  不同线程之间的非 volatile、非 synchronized 操作直接要想有传递的happens before关系的话，
   -  中间就肯定得有能产生 happens before 关系的volatile或者synchronized操作。
   -  什么寄存器啊、缓存啊啥的不必扯进来。
   
  
  ```java
   class MyObject{
	   private int a;
	   private volatile  int b;
	   private int  c;
   }
   
```
  
  
### 3.3 happen-before
 
happens-before原则定义如下：

1. 如果一个操作happens-before另一个操作，那么第一个操作的执行结果将对第二个操作可见，而且第一个操作的执行顺序排在第二个操作之前。 
2. 两个操作之间存在happens-before关系，并不意味着一定要按照happens-before原则制定的顺序来执行。如果重排序之后的执行结果与按照happens-before关系来执行的结果一致，那么这种重排序并不非法。


下面是happens-before原则规则：

1. 程序次序规则：一个线程内，按照代码顺序，书写在前面的操作先行发生于书写在后面的操作；
    > 怎么理解：其实就是想象只有一个单线程执行程序，所以的动作按照你程序的顺序执行
2. 锁定规则：一个unLock操作先行发生于后面对同一个锁额lock操作；
   > 对象加锁： 先unlock，然后才能lock  
3.  volatile变量规则：对一个变量的写操作先行发生于后面对这个变量的读操作；
   > 某线程修改 volatile 变量，其他线程立马可以读取最新值 它标志着volatile保证了线程可见性
4. 传递规则：如果操作A先行发生于操作B，而操作B又先行发生于操作C，则可以得出操作A先行发生于操作C；
5. 线程启动规则：Thread对象的start()方法先行发生于此线程的每个一个动作；
6. 线程中断规则：对线程interrupt()方法的调用先行发生于被中断线程的代码检测到中断事件的发生；
7. 线程终结规则：线程中所有的操作都先行发生于线程的终止检测，我们可以通过Thread.join()方法结束、Thread.isAlive()的返回值手段检测到线程已经终止执行；
8. 对象终结规则：一个对象的初始化完成先行发生于他的finalize()方法的开始；




  
  
  
   
  
   
   
   

     
 

                 
                     
 
 
                    
>&copy; lxh_007@hotmail.com
 
  
  

