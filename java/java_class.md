# java class 字码码


    
     
### java源代码 class字节码文件  

1.  javac  Demo.java

   ```java
        public class Demo {
            private int a = 1111;
            protected void testMethod() {
                 int bb = 333;
                 long dddd = 6666L;
            }
         
            protected int myfinal() {
                int i = 0;
        
                try {
                    i = 2;
                    return i;
                } finally {
                    return 1;
                }
            }
}
    
``` 

2. 用subline text打开 Demo.class 文件 内容 

 ```java
   cafe babe 0000 0032 0021 0a00 0600 1c09
   0005 001d 0500 0000 0000 001a 0a07 001e
   0700 1f01 0001 6101 0001 4901 0006 3c69  
          
        
```  
  - cafe babe     字节码文件 标识 
  - 0000 0032     版本号 0000次版本号， 0032主版本号 （ 32相当于十进抽的 50）  ---jdk6 编译出来的 


### 反编译字节码  

1.  使用javap  反编译   
     > javap  -verbose  Demo


    Compiled from "Demo.java"
    public class Demo extends java.lang.Object
      SourceFile: "Demo.java"
      minor version: 0
      major version: 50
      Constant pool:
    const #1 = Method	#6.#28;	//  java/lang/Object."<init>":()V
    const #2 = Field	#5.#29;	//  Demo.a:I
    const #3 = long	6666l;
    const #5 = class	#30;	//  Demo
    const #6 = class	#31;	//  java/lang/Object
    const #7 = Asciz	a;
    const #8 = Asciz	I;
    const #9 = Asciz	<init>;
    const #10 = Asciz	()V;
    const #11 = Asciz	Code;
    const #12 = Asciz	LineNumberTable;
    const #13 = Asciz	LocalVariableTable;
    const #14 = Asciz	this;
    const #15 = Asciz	LDemo;;
    const #16 = Asciz	testMethod;
    const #17 = Asciz	bb;
    const #18 = Asciz	dddd;
    const #19 = Asciz	J;
    const #20 = Asciz	myfinal;
    const #21 = Asciz	()I;
    const #22 = Asciz	i;
    const #23 = Asciz	StackMapTable;
    const #24 = class	#30;	//  Demo
    const #25 = class	#32;	//  java/lang/Throwable
    const #26 = Asciz	SourceFile;
    const #27 = Asciz	Demo.java;
    const #28 = NameAndType	#9:#10;//  "<init>":()V
    const #29 = NameAndType	#7:#8;//  a:I
    const #30 = Asciz	Demo;
    const #31 = Asciz	java/lang/Object;
    const #32 = Asciz	java/lang/Throwable;
    
    {
    public Demo();
      Code:
       Stack=2, Locals=1, Args_size=1
       0:	aload_0
       1:	invokespecial	#1; //Method java/lang/Object."<init>":()V
       4:	aload_0
       5:	sipush	1111
       8:	putfield	#2; //Field a:I
       11:	return
      LineNumberTable: 
       line 3: 0
       line 6: 4
    
      LocalVariableTable: 
       Start  Length  Slot  Name   Signature
       0      12      0    this       LDemo;
    
    
    protected void testMethod();
      Code:
       Stack=2, Locals=4, Args_size=1
       0:	sipush	333
       3:	istore_1
       4:	ldc2_w	#3; //long 6666l
       7:	lstore_2
       8:	return
      LineNumberTable: 
       line 11: 0
       line 12: 4
       line 13: 8
    
      LocalVariableTable: 
       Start  Length  Slot  Name   Signature
       0      9      0    this       LDemo;
       4      5      1    bb       I
       8      1      2    dddd       J
    
    
    protected int myfinal();
      Code:
       Stack=1, Locals=4, Args_size=1
       0:	iconst_0
       1:	istore_1
       2:	iconst_2
       3:	istore_1
       4:	iload_1
       5:	istore_2
       6:	iconst_1
       7:	ireturn
       8:	astore_3
       9:	iconst_1
       10:	ireturn
      Exception table:
       from   to  target type
         2     6     8   any
         8     9     8   any
      LineNumberTable: 
       line 22: 0
       line 25: 2
       line 26: 4
       line 28: 6
    
      LocalVariableTable: 
       Start  Length  Slot  Name   Signature
       0      11      0    this       LDemo;
       2      9      1    i       I
    
      StackMapTable: number_of_entries = 1
       frame_type = 255 /* full_frame */
         offset_delta = 8
         locals = [ class Demo, int ]
         stack = [ class java/lang/Throwable ]
    }
    

2. 反编译代码分析。

- 字节码的文件结构  7 部分
   - 魔数与Class文件版本
   - 常量池
   - 访问标志
   - 类索引、父类索引、接口索引
   - 字段表集合
   - 方法表集合
   - 属性表集合

- class标识与版本信息
  - Class 文件的第 1 - 4 个字节代表了该文件的魔数（Magic Number）。
    - 它唯一的作用是确定这个文件是否为一个能被虚拟机接受的 Class 文件，
    - 其值固定是：0xCAFEBABE（咖啡宝贝）。
  
  - Class 文件的第 5 - 6 个字节代表了 Class 文件的次版本号（Minor Version），
    - 例子中的是16进制的     0000
  - Class 文件的第 7 - 8 个字节代表了 Class 文件的主版本号（Major Version），
    - 例子中的是16进制的    0032
   
- 常量池常量池都放些什么
  - i常量是什么有什么 
    + 字面量(Literal)
    + 和符号引用量
  
  
  
  
  
  
  
# License

* [Wiki]()

[]:https://wwww.pigasuo.com





























