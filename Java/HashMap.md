#1.HashMap简介

​	HashMap基于哈希表的Map接口实现，是以key-value储存形式存在，即主要用来存放键值对。HashMap的实现不是同步的，这意味着它不是线程安全的。它的key、value都可以为null。null的hash值为0，所以若key=null默认储存在table的第一个位置。此外，HashMap中的映射不是有序的。

​	JDK1.8之前，HashMap由==数组+链表==组成，数组是HashMap的主体，链表则是主要为了解决哈希冲突***(两个对象调用的hashCode方法计算的哈希码值一致导致计算的数组索引值相同)***而存在的("拉链法"解决冲突)。数组是长度为16的**Entry**数组。JDK1.8之后在解决哈希冲突有了较大变化，采用==数组+链表+红黑树==。**当链表的长度大于阈值(或者红黑树的边界值，默认为8)并且当前数组长度大于64时，此时此索引位置上的所有数据改为树节点使用红黑树储存。若树节点减少到6个时，转换为链表**

在JDK 1.8中，首次调用`put()`方法时才会在底层调用`resize()`创建长度为16的数组

补充:	将链表转换为红黑树前会判断，即使大于阈值，但是map总元素个数小于64，此时并不会将链表变为红黑树。而是选择进行数组扩容

==threshold(临界值)	=capcity(数组长度)	*	loadFactor(默认是0.75)	这个值是当前HashMap中存储的元素个数（*size*），若HashMap中的元素大于这个临界值并且接下来要添加的节点会发生Hash冲突（*数组在该索引有节点*），为了避免因为冲突产生链表导致后续遍历的性能下降，所以进行扩容。扩容后的HashMap容量是原来的两倍==

#2.HashMap集合底层的数据结构

在创建HashMap集合采用懒加载，是在第一次调用put()时创建数组，避免创建出哈希table而不使用消耗内存

##2.1HashMap的数据添加

​	在向集合添加数据时，会先根据key的hashCode方法计算出值在经过扰动函数使hash值更散列，然后结合数组长度采用某种算法计算出向数组中储存数据的空间的索引值。如果计算出的索引空间没有数据，则直接将键值对储存到数组中。如果有数据则会判断已存在的节点和要插入的节点的hash值是否相等，若不相等则判断是否有下一个节点，即链表节点，有则继续判断hash值是否相等，若相出现相等的hash值，则调用key的`equals()`进一步判断key是否相等，若遍历完整个链表都没有发现hash值和key的`equals()`相等的节点则在链表末尾添加一个链表节点储存这个数据，即拉链法；如果遍历比较链表的节点时，发现hash值相等，且key也相等的节点时，则用待插入节点的value替换换value并结束链表的遍历。

在JDK 1.7中，链表数据的插入采用头插法，在JDK 1.8中采用尾插法

**1.哈希表底层采用的算法:**
底层采用key的hashCode方法的值，结合数组长度进行无符号右移(>>>)、按位异或(^)、按位与(&)计算出索引，位运算的效率要高。还可以采用:	平方取中法、取余法、伪随机数发法

**2.关于JDK1.8加入红黑树:**
	JDK1.8以前HashMap的实现是数组+链表，即使哈希函数取得再好，也很难达到元素百分百均匀分布。当HashMap中有大量的元素都存放到一个桶中时，这个桶下有一条长长的链表，这时HashMap就相当于一个单链表，加入单链表有n个元素，遍历的时间复杂度就是O(n)，完全丧失了它的优势。针对这种情况，JDK1.8中引入了红黑树（查找的时间复杂度为O(logn)）来优化这个问题。当链表长度很小的时候，即使遍历，速度也非常快，但是当链表长度不断变化长，肯定会查询性能产生影响，所以才需要转成树

##2.2HashMap集合类的成员变量

1. 序列化版本号

```java
private static final long serialVersionUID = 362498820763181265L;
```

2. 集合的初始化容量（**必须是2的n次幂**）

```java
static final int DEFAULT_INITIAL_CAPACITY = 1 << 4; //16
/*
	HashMap为了存取高效，要尽量减少碰撞，就是尽量把数据分配均匀，每个链表长度大致相同。这是通过hash%length的算法来计算索引，计算机中直接求余效率不如位运算，所以源码中做了优化，使用hash&(length-	 1),而实际上hash%length等于hasn&(length-1)的前提是length是2的n次幂
	hash&(length-1)算法减少hash碰撞:
		hash=3  数组长度=8			hash=2   数组长度=8
			3&(8-1)						2&(8-1)
		00000011   3			   00000010    2
		00000111   7			   00000111    7
	----------------------		--------------------
		00000011   3   索引        00000010    2  索引
	
	若数组长度不是2的n次幂:
		hash=3  数组长度=9			hash=2   数组长度=9
			3&(9-1)						2&(9-1)
		00000011   3			   00000010    2
		00001000   8			   00001000    8
	----------------------		--------------------
		00000000   0   索引        00000000    0  索引
*/
```

**问题：若创建集合时，指定了容器大小，且大小不为2的n次幂会怎么样**

```java
/*
	eg:	创建一个大小为10的map集合
	HashMap<String,Integer> hm = new HashMap<>(10);
	底层源码:
		cap = 10;
		int n = cap - 1;
		n |= n >>> 1;
第一步-->00000000 00000000 00000000 00001001	9
		00000000 00000000 00000000 00000100   9>>>1 变为4
	------------------------------------------------------------
		00000000 00000000 00000000 00001101   13  
		n |= n >>> 2;
第二步-->00000000 00000000 00000000 00001101   13
		00000000 00000000 00000000 00000011	  13>>>2 变为3
	------------------------------------------------------------
		00000000 00000000 00000000 00001111	  15
		n |= n >>> 4;
第三步-->00000000 00000000 00000000 00001111	15
		00000000 00000000 00000000 00000000   15>>>4 变为0
	------------------------------------------------------------
		00000000 00000000 00000000 00001111	  15
		n |= n >>> 8;
		n |= n >>> 16;
		return (n < 0) 1 : (n>=MAXIMUM_CAPACITY) ? MAXIMUM_CAPACITY : n+1;
		最终返回n=16，这个值赋给了threshold，然后通过put方法重新计算临界值
	总结:	若输入的初始容器大小不是2的n次幂，则会将其变为比输入值大的最小2的n次幂的值作为容器大小
	注: 容量最大是32bit的正数，因此最后n |= n >>> 16，最多也就32个1，但此时已经为负数了，所以在执行上述操作之前先对传入的初始大小进行了判断，如果大于MAXIMUM_CAPACITY(2^30),则取MAXIMUM_CAPACITY。30个1，加1之后
*/
```

3. **DEFAULT_LOAD_FACTOR**：默认的负载因子

```java
static final float DEFAULT_LOAD_FACTOR = 0.75f;
```

​	**说明：**用来衡量HashMap满的程度，表示HashMap的疏密程度，影响hash操作到同一个位置的概率，计算HashMap的实时加载因子的方法: **size/capacity**，而不是占用桶的数量除以capacity，capacity是桶的数量，也就是table的长度length。**不建议修改，如果负载因子过小，桶的利用率不够高却进行了耗费性能的扩容，若负载因子过大，在达到更多的桶被占用之前，很有可能某一个桶已经连接了大量的数据，最终转变为红黑树，而红黑树的增删很复杂会降低性能**

4. **MAXIMUM_CAPACITY**：集合最大容量（数组长度），2^30

```java
static final int MAXIMUM_CAPACITY = 1 << 30;
```

5. **TREEIFY_THRESHOLD**：树化阈值，链表长度大于该默认值时，转化为红黑树

```java
static final int TREEIFY_THRESHOLD = 8;
```

6. **UNTREEIFY_THRESHOLD**：树降级为链表的阈值，当Bucket中红黑树存储的Node小于该值，转换为链表

```java
static final int UNTREEIFY_THRESHOLD = 6;
```

7. table 
   	**table**在JDK1.8中HashMap是由数组+链表+红黑树组成，table就是HashMap的数组，数组类型Node<K,V>，	实现了MapEntry<K,V>

8. HashMap中存放元素的个数

```java
transient int size; //这个变量是集合中储存的实际键值对数量，不是table长度
```

9. **modCount**：HashMap扩容和结构改变的次数
10. **MIN_TREEIFY_CAPACITY**：桶中的Node被树化时最小的hash表容量。当桶中的Node的数量大到需要变红黑树时，若hash表容量小于**MIN_TREEIFY_CAPACITY**时，会执行**resize**扩容操作

##2.3HashMap中的方法

1.扰动函数:	==(h = key.hashCode()) ^ (h >>> 16)==

```java
/*
	因为在路由运算中，hash值通常是int值，二进制数共32位，当table长度较小时，转换为的二进制数位数较少，为了让hash值高16位的数也参与运算就需要通过扰动函数，将已经计算得到的hash值无符号右移16位，在进行原hash值和右移后的hash值异或运算，让hash的低16位具有高16位的特性
	eg:	假设table长度为16
		1111 1111 1111 1111 1111 0000 1110 1010	 h
	^	0000 0000 0000 0000 1111 1111 1111 1111  h >>>16  扰动
	-----------------------------------------------------------
		1111 1111 1111 1111 0000 1111 0001 0101  扰动后的hash
	&	0000 0000 0000 0000 0000 0000 0000 1111  15  (length-1)  16-1
	------------------------------------------------------------
		0000 0000 0000 0000 0000 0000 0000 0101   索引值为5
	若不使用扰动函数
		第一个key的索引:
		1111 1111 1111 1111 1111 0000 1110 1010	 h
	&	0000 0000 0000 0000 0000 0000 0000 1111  15  (length-1)  16-1
	-----------------------------------------------------------
		0000 0000 0000 0000 0000 0000 0000 1010  10  索引值为10
		第二个key的索引:(只有高16为发生)
		1001 1001 0000 1111 1111 0000 1110 1010  h
	&	0000 0000 0000 0000 0000 0000 0000 1111  15  (length-1)  16-1
	-----------------------------------------------------------
		0000 0000 0000 0000 0000 0000 0000 1010  10  索引值为10
	当hash只有高位发生改变时，实际有效的位只有与数组长度相同的位才会参与到路由运算，不使用扰动函数会增大	hash碰撞的概率
*/
```

2.构造方法
	**初始容量 = (需要存的元素个数 / 负载因子) + 1**

3.resize方法  (扩容方法)
	扩容机制:	进行扩容会伴随一次重新hash分配，并且会遍历hash表中的所有元素，非常耗时。HashMap在进行扩容时，使用的rehash方式非常巧妙，因为每次扩容都是翻倍，与原数组长度相比，只是多了一个bit位，所以扩容后的节点要么就在原来的位置，要么就被分配到**原位置+旧容量**这个位置。

```java
/*
	示例:
		数组长度:	16	(n-1)&hash计算索引
			0000 0000 0000 0000 0000 0000 0000 1111  15
hash(key1)  1111 1111 1111 1111 0000 1111 0000 0101
	------------------------------------------------------------------
			0000 0000 0000 0000 0000 0000 0000 0101  索引值为5
			
			0000 0000 0000 0000 0000 0000 0000 1111  15
hash(key2)  1111 1111 1111 1111 0000 1111 0001 0101
	------------------------------------------------------------------
			0000 0000 0000 0000 0000 0000 0000 0101  索引值为5
		
		数组扩容为原来的2倍:	32	(n-1)&hash计算索引
			0000 0000 0000 0000 0000 0000 0001 1111  31
hash(key1)  1111 1111 1111 1111 0000 1111 0000 0101
	------------------------------------------------------------------
			0000 0000 0000 0000 0000 0000 0000 0101  索引值为5
			
			0000 0000 0000 0000 0000 0000 0001 1111  31
hash(key2)  1111 1111 1111 1111 0000 1111 0001 0101
	------------------------------------------------------------------
			0000 0000 0000 0000 0000 0000 0001 0101  索引值为5 + 16(旧数组的长度)
若数组长度的二进制高位多出的那一位通过&hash运算后，多出的那一位是0则是原位置，若是1则是"原位置+旧容量"的位置，所以扩容后不再需要重新进行路由运算，只需根据情况判断是否在原位置+旧容量即可
*/
```