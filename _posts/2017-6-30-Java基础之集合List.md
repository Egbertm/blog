---
layout: post
title:  "Java基础之集合list"
date: 2017-06-30 21:30:39
categories: Java基础
tags:  Java
---
* content
{:toc}  
  
Java常用的类中集合应该是最常用的类之一了，熟练使用Java的集合也是Java程序员需要的
掌握的基本功；本节通过结合源码的例子来看看集合它是如何满足我们日常开发的需要的
。




### List集合框架

我们知道List本身它是一个接口，哪它是否有被继承者，亦或它也继承了其它的接口，我
们可以通过下面的List的框架图大致的了解到List集合的框架。

```
                        Iterable
                        |
                        +--Collection
                               |
                               |
                      Queue----+
                        |      |
                        |      |
         AbstarctQueue--+      +----List
               |        |      |      |
               |        |      |      +---AbstractList
ProrietyQueue -+        |      |               |
                        |      |               |
                        |      |               +---ArrayList
               Dqueue---+      +---Set         |
               |                    |          |
    ArrayDeque-+                    |          |
                                    |          +---Vector
                                    |          |      |
                                    |          |      +---Stack
                                    |          |
                                    |          +---AbstractSequentialList
                                    |                     |
                                    |                     |
                                    |                     +---LinkedList
                                    +---AbstarctSet
                                    |       |
                                    |       |
                                    |       +---HashSet
                                    |              |
                                    |              +--- LinkedHashSet
                                    +---SortedSet
                                            |
                                            +---NavigableSet
                                                     |
                                                     +---TreeSet

```

以上是Java中List集合框架的一个概述，下面来看看他们之间的区别。

### 简单介绍

**Collection**

Collection 它是一个接口；它代表可以存储一组Object。对于一些实现该接口的子类，有
些允许存储相同的元素，有些不行；有些要求排序，有些没有要求；因此构成了一个庞大的
集合体系。JDK不提供直接支持继承Collection的类(接口List和接口Set都扩展该接口)，
所有实现Collection接口的类都必须提供两个标准的构造函数：无参数的构造函数用于创建
一个空的Collection，有一个 Collection参数的构造函数用于创建一个新的Collection，
这个新的Collection与传入的Collection有相同的元素。后一个构造函数允许用户复制一
个Collection。

**ArrayList**

ArrayList 它的内部实现是一个可变化的动态数组，它存储的元素允许为Null,是个非线程
安全的类，常用的方法有Size，isEmpty，get，Set，remove，Iterator等，ArrayList在扩
充容量时，会损失一些性能，所以在实践的时候如果能够大概的估计出ArrayList存储的大
小可以在初始化的时候指定ArrayList的大小，这样就避免了扩充时调用Arrays.copyOf()
方法了。

**LinkedList**

LinkedList 它的内部实现是基于链表的,在该类内部定义了一个私有的静态内部节点类充当
链表的节点，它同样也运行存储的节点为Null,也是一个非线程安全的类，常用的方法有：
add,get,size,remove,同时它的内部还提供了一些诸如pop,push,peek等类似与栈的操作，
这也是因为官方不在推荐使用Stack作为栈来使用了，而希望我们用List来实现。因为
它还是Duque接口的实现类，因此也可以作为一个双端队列，或者栈来使用。



**Vector**

Vector 类似与ArrayList内部都是用数组实现的，他们之间存在一些差别，最大的差别是
Vector它是一个线程安全的类，因而通常不在并发环境下我们一般用ArrayList它的性能更优
。对于同步的Vector,当一个Iterator被创建并在使用，这时如果另一个线程试图改变
Vector的状态时，这时Iterator方法将抛出异常信息，可看如下源码：

``` java

        public E next() {
            synchronized (Vector.this) {
                checkForComodification();
                int i = cursor;
                if (i >= elementCount)
                    throw new NoSuchElementException();
                cursor = i + 1;
                return elementData(lastRet = i);
            }
        }

        final void checkForComodification() {
            if (modCount != expectedModCount)
                throw new ConcurrentModificationException();
        }

```

**Stack**

Stack它是继承自Vector类的，但同时为了适应栈的操作增加了一些方法，如
peek,pop,push,empty,search这个五个额外的方法， pop和 push 分别是出栈和入栈，peek
是获得栈顶元素，empty测试栈是否为空，search检测一个元素在栈中的位置。

**Set接口**

Set接口扩展了Collection接口，内部提供了一些诸如：Size,add,addAll,contains等方法
描述，具体的实现通过它的子类如：HashSet 和TreeSet这两个子类。但有趣的是在
HashSet和TreeSet初始化时分别是初始化HashMap和TreeMap作为内部的存储结构，它的特点
是不包含重复的元素，即对于任意两个元素通过比较 `e1.equals(e2)==false`，
必须小心操作可变对象（Mutable Object）。如果一个Set中的可变元素改变了自身状态导
致Object.equals(Object)=true将导致一些问题。

**Queue接口**

Queue用于模拟队列这种数据结构。队列通常是指“先进先出（FIFO）”的容器。队列的头
部保存在队列中存放时间最长的元素，尾部保存存放时间最短的元素。新元素插入到队
列的尾部，取出元素会返回队列头部的元素。通常，队列不允许随机访问队列中的元素。

### 关系及区别

#### ArrayList 和 LinkedList

ArrayList 底层是基于数组实现的，它具有数组的一些通用特点，能够快速的定位到一个元
素，而相比而言 LinkedList它的内部是基于链表实现的，它通过遍历访问特定的元素，因
而就查找而言访问速度ArrayList优于LinkedList,但是插入元素的时候，ArrayList 需要复
制元素并且移动元素，而LinkedList改变元素引用的指向即可，这一点而言LinkedList优于
ArrayList,在实际的使用中需要特定的场景具体分析和使用。ArrayList和LinkedList在性
能上各有优缺点，都有各自所适用的地方，总的说来可以描述如下： 

1. 对ArrayList和LinkedList而言，在列表末尾增加一个元素所花的开销都是固定的。对
ArrayList而言，主要是在内部数组中增加一项，指向所添加的元素，偶尔可能会导致对数
组重新进行分配；而对LinkedList而言，这个开销是统一的，分配一个内部Entry对象。

2. 在ArrayList的中间插入或删除一个元素意味着这个列表中剩余的元素都会被移动；
而在LinkedList的中间插入或删除一个元素的开销是固定的。

3. LinkedList不支持高效的随机元素访问。

4. ArrayList的空间浪费主要体现在在list列表的结尾预留一定的容量空间，而LinkedList
的空间花费则体现在它的每一个元素都需要消耗相当的空间 

#### TreeSet 和 HashSet

**HashSet**

当向HashSet集合中存入一个元素时，HashSet会调用该对象的hashCode()方法来得到该对象
的hashCode值，然后根据 hashCode值来决定该对象在HashSet中存储位置。简单的说，HashSet
集合判断两个元素相等的标准是两个对象通过equals方法比较相等，并且两个对象的hashCode()
方法返回值相等注意，如果要把一个对象放入HashSet中，重写该对象对应类的equals方法，
也应该重写其hashCode()方法。其规则是如果两个对象通过equals方法比较返回true时，
其hashCode也应该相同。另外，对象中用作equals比较标准的属性，都应该用来计算hashCode的值。

**TreeSet**

TreeSet是SortedSet接口的唯一实现类，TreeSet可以确保集合元素处于排序状态。TreeSet支持两
种排序方式，自然排序 和定制排序，其中自然排序为默认的排序方式。向TreeSet中加入的应该是同一个类的对象。
TreeSet判断两个对象不相等的方式是两个对象通过equals方法返回false，或者通过CompareTo方法比较没有返回0
自然排序使用要排序元素的CompareTo（Object obj）方法来比较元素之间大小关系，然后将元素按照升序排列。
Java提供了一个Comparable接口，该接口里定义了一个compareTo(Object obj)方法，该方法返回一个整数值，
实现了该接口的对象就可以比较大小。obj1.compareTo(obj2)方法如果返回0，则说明被比较的两个对象相等，
如果返回一个正数，则表明obj1大于obj2，如果是 负数，则表明obj1小于obj2。
如果我们将两个对象的equals方法总是返回true，则这两个对象的compareTo方法返回应该返回0
自然排序是根据集合元素的大小，以升序排列，如果要定制排序，应该使用Comparator比较器接
口，实现 `int compare(T o1,T o2)`方法。

#### PriorityQueue 和 ArrayDqueue

**PriorityQueue**

PriorityQueue是一种比较标准的队列实现类，而不是绝对标准的。这是因为PriorityQueue保存
队列元素的顺序不是按照元素添加的顺序来保存的，而是在添加元素的时候对元素的大小排序后
再保存的。因此在PriorityQueue中使用peek()或pool()取出队列中头部的元素，取出的不是最
先添加的元素，而是最小的元素。
PriorityQueue不允许插入null元素，它还需要对队列元素进行排序，PriorityQueue
有两种排序方式：实现一个comparable接口，或者传入一个实现comparator比较器。

**ArrayDqueue**

ArrayDqueue 是一个双端队列，同时用这个双端队列可以很方便的实现栈操作，它提供了一
些方法，如栈的方法，push,pop,以及peek等；作为双端队列它可以通过addFirst增加元素
到队列头部，也可以通过addLast增加元素到双端队列的尾部等。


