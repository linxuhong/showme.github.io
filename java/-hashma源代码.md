



HashMap 
====
HashMap 是java中常用的一个类提供<Key,Value>键值对的存取，已经有很多人介绍或者进行源代码分析了
虽然没有看全，基本上已经有所了解。
new HashMap(): 
我们需要了解：HashMap是通过"拉链法"实现的哈希表。它包括几个重要的成员变量：
1.  table, size, threshold, loadFactor, modCount。
2.  table是一个Entry[]数组类型，而Entry实际上就是一个单向链表。哈希表的"key-value键值对"都是存储在Entry数组中的。 
3. 　size是HashMap的大小，它是HashMap保存的键值对的数量。 
4. threshold是HashMap的阈值，用于判断是否需要调整HashMap的容量。
>    threshold 的值="容量*加载因子"，当HashMap中存储数据的数量达到threshold时，就需要将HashMap的容量加倍。
　　loadFactor就是加载因子。 
　　modCount是用来实现fail-fast机制的。


 ![a](map.png)
 
 
    public HashMap() {
          // DEFAULT_INITIAL_CAPACITY =16 , DEFAULT_LOAD_FACTOR = 0.75
          this(DEFAULT_INITIAL_CAPACITY, DEFAULT_LOAD_FACTOR);
     }

    public HashMap(int initialCapacity, float loadFactor) {
        
        // 找到一个2的n次方值，大于初始容量，目的后面看hahsm聚会时再描述 
        int capacity = 1;
        while (capacity < initialCapacity)
            capacity <<= 1;
        this.loadFactor = loadFactor;
        // 阀值是 容量 * 因子
        threshold = (int)Math.min(capacity * loadFactor, MAXIMUM_CAPACITY + 1);
        table = new Entry[capacity];
        // 初始化一个数量，数组长度为容量 
    }
    



#  hash 的本质
Hash，一般翻译做“散列”，也有直接音译为“哈希”的，就是把任意长度的输入，通过散列算法，变换成固定长度的输出，该输出就是散列值。这种转换是一种压缩映射，
也就是，散列值的空间通常远小于输入的空间，不同的输入可能会散列成相同的输出，
所以不可能从散列值来唯一的确定输入值。
简单的说就是一种将任意长度的消息压缩到某一固定长度的消息摘要的函数。

类比 我们在数学中经常谈到函数
y = f(x),这里我们可以理解为 
 key 组成 值域{key1,key2,key3...keyn}
 y :是 这个hash的值域，当hash的在不扩容的情况下就是 {0,1,2, capacity }
 hash函数就是这个 f 
 那么问题来了，在不数据向map中put数据时肯定有多个key对应同一个y,这个时候怎么办？
  > hash函数很着急，它尽量将key能分散在bucket
  
1. 开放定址法：
    开放定址法就是一旦发生了冲突，就去寻找下一个空的散列地址，只要散列表足够大，空的散列地址总能找到，并将记录存入。
2. 链地址法
     将哈希表的每个单元作为链表的头结点，所有哈希地址为i的元素构成一个同义词链表。即发生冲突时就把该关键字链在以该单元为头结点的链表的尾部。
3. 再哈希法
      当哈希地址发生冲突用其他的函数计算另一个哈希函数地址，直到冲突不再产生为止。
4. 建立公共溢出区
    将哈希表分为基本表和溢出表两部分，发生冲突的元素都放入溢出表中
  > 多个key映射到 HashMap中的tables数组的位置，怎么处理 
 

##   hash函数设计 
  
    int hash = hash(key);
    int i = indexFor(hash, table.length);
   
    static int indexFor(int h, int length) {
           return h & (length-1);
    }
     final int hash(Object k) {
        int h = 0;
        if (useAltHashing) {
            if (k instanceof String) {
                return sun.misc.Hashing.stringHash32((String) k);
            }
            h = hashSeed;
        }

        h ^= k.hashCode();
 
        h ^= (h >>> 20) ^ (h >>> 12);
        return h ^ (h >>> 7) ^ (h >>> 4);
    }

ndexFor方法其实主要是将hash生成的整型转换成链表数组中的下标
位运算(&)效率要比代替取模运算(%)高很多，主要原因是位运算直接对内存数据进行操作，不需要转成十进制，
因此处理速度非常快

## X % 2^n = X & (2^n – 1)

2^n表示2的n次方，也就是说，一个数对2^n取模 == 一个数和(2^n – 1)做按位与运算 。
假设n为3，则2^3 = 8，表示成2进制就是1000。2^3 -1 = 7 ，即0111。

此时X & (2^3 – 1) 就相当于取X的2进制的最后三位数。
从2进制角度来看，X / 8相当于 X >> 3，即把X右移3位，此时得到了X / 8的商，
而被移掉的部分(后三位)，则是X % 8，也就是余数。

> 6 % 8 = 6 ，6 & 7 = 6
  10 & 8 = 2 ，10 & 7 = 2


###  拉链法
     ublic V put(K key, V value) {
         if (key == null)
             return putForNullKey(value);
         int hash = hash(key);
         int i = indexFor(hash, table.length);
         // 定位到数组第i个无线，然后找到 key与相等的元素，如果没有则直接加一个
         for (Entry<K,V> e = table[i]; e != null; e = e.next) {
             Object k;
             // 看到了没有    e.key) == key || key.equals(k) ，也就是说如果你
             if (e.hash == hash && ((k = e.key) == key || key.equals(k))) {
                 V oldValue = e.value;
                 e.value = value;
                 e.recordAccess(this);
                 return oldValue;
             }
         }
 
         modCount++;
         addEntry(hash, key, value, i);
         return null;
     }
 
引出一个话题 为什么Override了  equal方法，也要override方法
java中规定  一个对象如果 equals相等，那么 其 hashcode也必需相等，而正常情况下 hashcode()是
Object的一个native方法，能于同一个object的不同实例，正常情况下，hashcode返回的值是不相同 的

     static class A {
     
        String s ;
        // get set 
        
        public boolean equals(Object anObject) 
           // 类型检测略
           A  tmp = (A) anObject
           if(s == null && tmp.getS() == null) 
              return true;
            
           if(a!=null)
               return s.equal(tmp.getS())
           
            if(tmp.getS()!=null)
                return tmp.getS().equals(s);
        }
     }
     public static void main(String[] args) {
          A  a1 = new A();
         System.out.println(a1.hashCode());
         A  a2 = new A();
         System.out.println(a2.hashCode());
         
         // jvm底层实现 输出
          13729475
          31022504
          
          // 添加equal时，new A()出来两个object equals是相等的 ,但是hashcode不相等。这样就不符合规范了
          
     }
 
 
### 3   提一句 HashTable 
` 
 而HashTable中也没有indexOf方法，取而代之的是这段代码：
 int index = (hash & 0x7FFFFFFF) % tab.length;。
 也就是说，HashMap和HashTable对于计算数组下标这件事，
 采用了两种方法。HashMap采用的是位运算，
 而HashTable采用的是直接取模。
                    
>&copy; lxh_007@hotmail.com
 
  
  

