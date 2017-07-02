---
layout: post
title:  "Java基础之Static"
date: 2017-06-17 15:08:47
categories: Java基础
tags:  Java
---
* content
{:toc}  
  
在Java程序中我们往往会遇到一些用static关键字描述的变量，方法，甚至是类；我们把
static定义的变量称为类变量，static 定义的方法为类方法，static定义的类静态类(常见
于静态的嵌套类)。我们知道在一个类中可能包含代码块，静态代码块，方法，变量等。那
么虚拟机对于这些启动的顺序是什么样的？先看个小例子：




### 引例

一个虚拟机启动的顺序的小例子：  

``` java
public class StaticTest{

    private int court=0;
    public static boolean b;

    public static void main (String[] args) {
        StaticTest s = new StaticTest();
        new Test3().display();
    }
}
class Test2{
    int a;
    static {
        System.out.println("this test2 static block"); 
    }
    {
        System.out.println("this test2 block"); 
    }
    public Test2(){
       a = 100; 
       System.out.println("test2 construt");
    }
}

class Test3 extends Test2{
    {
        System.out.println("this is test3 block"); 
    }
    static {
        
       System.out.println("this is test3 static block"); 
    }
    public Test3(){
    super();
    System.out.println("test3 construt");
    }
    public void display(){
    System.out.println("subclass"+a); 
    }
}

```

以下是程序的输出：  

> Press ENTER or type command to continue
> this test2 static block
> this is test3 static block
> this test2 block
> test2 construt
> this is test3 block
> test3 construt
> subclass100

通过实验我们发现在类进行实例化的过程中,遇到类的静态代码块的时候是优先被虚拟机处
理并初始化的，且对于父类的初始化要比子类早

### static用法

static 可以用于修饰一个变量，一个方法，一个类，也可以单独的修饰一块代码。分别有
不同的作用。

1. static变量

static变量是静态变量，我们也称之为类变量，它不依赖于任何一个实例，对于同一个类
变量，所有的实例共享这个类变量，也可以通过类名来直接访问。在JVM启动时，会在虚拟
机的元数据区为静态变量分配内存并初始化的，这个过程要早于类的实例化过程。

2. static方法  

static 修饰的方法是类方法或叫静态方法，它也是从属于类的，不依附于具体的实例，在
JVM启动时，类方法的一些信息也存储在虚拟机的元数据区，可以直接调用，因而它不能和
abstract一同修饰同一个方法。常见的在工厂方法模式中我们用到了static 方法，而且在
《effective java》中作者推荐使用静态工厂方法来替代构造器构造对象。用处很大。

3. static块

在引例中我们看到了static块它的加载在创建实例之前，对于这一点，我们可以利用它的
这一特性配置一些类需要的初始化启动项等等。在Singlton单例模式中我们也曾见过这种
饿汉式单例模式。  

``` java

public class Singleton{
    private static Singleton instance;
    static {
    
       instance = new Singleton(); 
    }
    private Singleton(){}
    public static Singleton getInstance(){
    
       return instance; 
    }

    public static void main (String[] args) {
        
        Singleton s1 = Singleton.getInstance();
        Singleton s2 = Singleton.getInstance();
        System.out.println(s1==s2);
    }
}

```

4. static类

static类描述的类是静态类，对于静态类中，它只能包含静态变量，静态方法，对于一切
非静态方法和变量在编译时通不过编译。我们见到的一些static类也很多，还是用单例模
式来说，其中就有一种使用的是静态内部类实现的单例模式。如下：

``` java

public class Singleton{

    private static class InnerClass{
    
        public static final Singleton singleton = new Singleton();
    }
    private Singleton(){}

    public static final Singleton getInstance(){

        return InnerClass.singleton;
    }
    public static void main (String[] args) {
        Singleton s1 = Singleton.getInstance();
        Singleton s2 = Singleton.getInstance();
        System.out.println(s1==s2);
    }
} 

```

### static局限

1. statci只能调用static变量

2. 不能以任何形式引用this,super

3. static变量在定义时必须要进行初始化，且初始化时间要早于非静态变量。

总结：无论是变量，方法，还是代码块，只要用static修饰，就是在类被加载时就已经"准
备好了",也就是可以被使用或者已经被执行，都可以脱离对象而执行。反之，如果没有
static，则必须要依赖于对象实例。
