## 当 g（111）遇上了 g(double d) 和 void g(Integer i) 


### 有一道测试题目如下 
 
> 某个类有两个重载方法：
>  void g(double d) 和 void g(Integer i)，
>  那么 g(1) 的会调用哪个方法？
>      A. 前者
>       B. 后者
>      C. 随机调用
>      D. 编译出错
     


### 写了几个例子
 
```java

     
public class DemoFun {
	public static void main(String[] args) {
        // a为int ,会调用 g的哪个方法
		int a = 111;
		g(a);
		
        // myList(ArrayList ls)
		ArrayList ls = new ArrayList<>();
		myList(ls);
        
        // myList(List ls)
        LinkedList linked =  new LinkedList();
		myList(linked);
	}

	static void g(double d) {
		System.out.println("double " +d);
	}

	static void g(float d) {
		System.out.println("float " +d);
	}

	
}

```
 

### int a = 111; g(a)能否编译通过

- 1 如果编译通过
   - 会调用哪个方法 g(double d)  g(float d)
   - 如果只保留其中一个方法能否调用 成功?
   - 为什么可以调用成功?
```java

        int a = 111;
 		g(a);
        
        static void g(double d) {
     		System.out.println("double " +d);
     	}
     
     	static void g(float d) {
     		System.out.println("float " +d);
     	}

 ```  
- 调用   g(new Integer(111))
   - 是否成功
   - 会调用 哪个方法？
   
   

### 继续看一个习以为常的例子 List 参数调用

方法的参数类型为List,传入ArrayList,LinkedList都是可以成功的。
  		
```java
        // myList(ArrayList ls)
		ArrayList ls = new ArrayList<>();
		myList(ls);
        
        // myList(List ls)
        LinkedList linked =  new LinkedList();
		myList(linked);

        static void myList(List ls) {
        		System.out.println("haha ");
        }
        	
        static void myList(ArrayList ls) {
        }

```

### 回到最初的问题

g(111) 是可以调用成功的
- g(float d)

- 如果此时 我们增加一个 g(Integer d)方法，结果如何

- 如果此时 我们增加一个 g(int d)方法，结果如何？？

- 在只有 g(float d),g(double d)的情况下
  - 会调用  g(float d)

- 在只有 g(float d),g(double d) g(Integer d) 的情况下
  - 为什么还是会调用  g(float d)


### 在g(111)调用啥情况？ 欢迎讨论！


 

 


