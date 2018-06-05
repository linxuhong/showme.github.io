线和 wait notify notifyAll  synchronized
====
# wait notify 
一段最笑意的wait notify 代码

 ~~~java
 
 public class Wait {
 	public static void main(String[] args) {
 		final Object lock = new Object();
 		new Thread(new Runnable() {
 			@Override
 			public void run() {
 				System.out.println("thread A is waiting to get lock");
 				synchronized (lock) {
 					try {
 						System.out.println("thread A get lock");
 						TimeUnit.SECONDS.sleep(1);
 						System.out.println("thread A do wait method");
 						lock.wait();
 						System.out.println("wait end");
 					} catch (InterruptedException e) {
 						e.printStackTrace();
 					}
 				}
 			}
 		}).start();
 
 		new Thread(new Runnable() {
 			@Override
 			public void run() {
 				System.out.println("thread B is waiting to get lock");
 				synchronized (lock) {
 					System.out.println("thread B get lock");
 					try {
 						TimeUnit.SECONDS.sleep(5);
 					} catch (InterruptedException e) {
 						e.printStackTrace();
 					}
 					lock.notify();
 					System.out.println("thread B do notify method");
 				}
 			}
 		}).start();
 	}
 }

 ~~~

## 1 引出下面几个问题
     ，
 1. 为什么wait notify 要放在 synchronized
 2. sleep 与wait 有什么区别
 3. 我们锁宝的时什么？
 4. wait ,notify 究竟做了什么
 5. JVM是怎么实现实现的synchronized

 
 
###  Q1 为什么wait notify 要放在 synchronize
- wait是Object 的方法，其实实现，任务一个普通Java对象都有这个方法
   > public final native void wait(long var1) throws InterruptedException;
   > 实际上最终调用 的是一个native方法 wait () 
   
-  wait 与notify 的方法说明,以wait为例 JavaDoc文档注释如下：
          Causes the current thread to wait until another thread invokes the
          `In other words, this method behaves exactly as if it simply
          The current thread must own this object's monitor` .(请注释这段，wait的前台是你已经得到了锁) 
          The thread releases ownership of this monitor and waits until another thread
          notifies threads waiting on this object's monitor to wake up
          either through a call to the {@code notify} method or the
          {@code notifyAll} method. The thread then waits until it can
          re-obtain ownership of the monitor and resumes execution.
    
- 锁什么？
  1. public ovid synchronized methodName() {}
     > 锁定当前对象
  2. public void method() {
        synchronized(this){
           // code
        }
     }
     > 锁定当前对象
     
  3. public ``static `` ovid synchronized methodName() {}
     > 锁定的 ClassName.calss 

  4.  public void method() {
         synchronized(Object){
                // code
             }
         }
      > 锁定Object对象

  `   ` 
  
  
  

### 继续...
  
  
  
  
  
  
  
  
  
  
  
  


 
 

                 
                     
 
 
                    
>&copy; lxh_007@hotmail.com
 
  
  

