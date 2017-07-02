---
layout: post
title:  "Java基础之exception"
date: 2017-06-2 22:14:23
categories: Java基础
tags:  Java 
---
* content
{:toc}  
  
对于Java 异常我们并不陌生，在对一些文件的操作时  和对一些数据库的连接操作时都用
到了，需要我们在 `try catch`捕获异常并进行相应的处理，但是对于Java的整体的异常机
制我们又好像没那么清晰。下面说说Java Exception。




### Java Exception(异常)

&emsp;&emsp; 使用异常机制它能够降低错误处理代码的复杂度，如果不使用异常，那么就必须检查特定
的错误，并在程序中的许多地方去处理它，而如果使用异常，那就不必在方法调用处进行
检查，因为异常机制将保证能够捕获这个错误，并且，只需在一个地方处理错误，即所谓
的异常处理程序中。这种方式不仅节约代码，而且把“概述在正常执行过程中做什么事”的代
码和“出了问题怎么办”的代码相分离。总之，与以前的错误处理方法相比，异常机制使代
码的阅读、编写和调试工作更加井井有条。（摘自《Think in java 》）。

&emsp;&emsp; 在《Think in java》中是这样定义异常的：**异常情形是指阻止当前方法或者作用域继续执行的问题。**

&emsp;&emsp; 那么什么时候才会出现异常呢？只有在你当前的环境下程序无法正常运行下
去，也就是说程序已经无法来正确解决问题了，这时它就会从当前环境中跳出，并抛出
异常。抛出异常后，它首先会做几件事。首先，它会使用new创建一个异常对象，然后在产
生异常的位置终止程序，并且从当前环境中弹出对异常对象的引用，这时。异常处理机制
就会接管程序，并开始寻找一个恰当的地方来继续执行程序，这个恰当的地方就是异常处
理程序，它的任务就是将程序从错误状态恢复，以使程序要么换一种方法执行，要么继续
执行下去

### 异常的体系图 

&emsp;&emsp; 以下blog提供了一个Java 异常的体系图:

``` java
                                                                            
                                                                            
                                   Throwable                                         
                                       ^                                    
                                       |
                                       |                                    
                           +-----------+------+                             
                           |                  |                             
                       Error                 Exception                                 
                           |                  |                             
             +-------------+                  |                             
             |         +---------------=------+---+------------------+      
             |         |        |                 |                  |      
          Error       Exception ClassNotFound  CloneNotSupport RuntimeExcetion
             |         ^          Exception        Exception         |
 AWTError ---+         |                                             | 
             |         |                       ClassCastException  --+ 
             |         |                                             | 
VirtualMa ---+         |                                             | 
chineError             |                       ArithmeticException --+ 
                       |                                             | 
         +-------------+                                             | 
         |                                  IllegalArgumentException-+  
    IOException                                                      | 
         |                                                           | 
         |                                  IllegalStateException ---+ 
         +--EOFException                                             |   
         |                                                           | 
         |                                 IndexOutOfBoundsException-+ 
         +--FileNotFoundException                                    |
         |                                                           | 
         |                                 NoSuchElementException ---+ 
         +--MalformedURLException                                    | 
         |                                                           | 
         |                                   NullPointerException ---+                                  
         +--UnKnowHostException

```
图中可以看出Throwable是Java语言的中错误和异常的超类，它有两个子类，Error，
Exception。
其中Error为错误，是程序无法处理的，如OutOfMemoryError、ThreadDeath等，出现这种
情况你唯一能做的就是听之任之，交由JVM来处理，不过JVM在大多数情况下会选择终止线
程。  

&emsp;&emsp; 而Exception是程序可以处理的异常。它又分为两种CheckedException（受捡
异常），一种是UncheckedException（不受检异常）。其中CheckException发生在编译阶
段，必须要使用try…catch（或者throws）否则编译不通过。而UncheckedException发生在
运行期，具有不确定性，主要是由于程序的逻辑问题所引起的，难以排查，我们一般都需
要纵观全局才能够发现这类的异常错误，所以在程序设计中我们需要认真考虑，好好写代
码，尽量处理异常，即使产生了异常，也能尽量保证程序朝着有利方向发展。

### 异常使用

#### 一般使用

&emsp;&emsp; 异常中try快包含着可能出现异常的代码块，catch块捕获异常后对异常进行处理。

``` java
public class ExceptionTest {
    public static void main(String[] args) {
        String file = "D:\\exceptionTest.txt";
        FileReader reader;
        try {
            reader = new FileReader(file);
            Scanner in = new Scanner(reader);  
            String string = in.next();  
            System.out.println(string + "不知道我有幸能够执行到不.....");
        } catch (FileNotFoundException e) {
            e.printStackTrace();
            System.out.println("对不起,你执行不到...");
        }  
        finally{
            System.out.println("finally 在执行...");
        }
    }
}
```
1、当程序遇到异常时会终止程序的运行（即后面的代码不在执行），控制权交由异常处理
机制处理。

2、catch捕捉异常后，执行里面的函数。

#### 自定义使用

&emsp;&emsp; Java确实给我们提供了非常多的异常，但是异常体系是不可能预见所有的
希望加以报告的错误，所以Java允许我们自定义异常来表现程序中可能会遇到的特定问题
，总之就是一句话：我们不必拘泥于Java中已有的异常类型。

&emsp;&emsp; Java自定义异常的使用要经历如下四个步骤：  
* 定义一个类继承Throwable或其子类。
* 添加构造方法(当然也可以不用添加，使用默认构造方法)。
* 在某个方法类抛出该异常。
* 捕捉该异常。

``` java
/** 自定义异常 继承Exception类 **/
public class MyException extends Exception{
    public MyException(){
        
    }
    
    public MyException(String message){
        super(message);
    }
}

public class Test {
    public void display(int i) throws MyException{
        if(i == 0){
            throw new MyException("该值不能为0.......");
        }
        else{
            System.out.println( i / 2);
        }
    }
    
    public static void main(String[] args) {
        Test test = new Test();
        try {
            test.display(0);
            System.out.println("---------------------");
        } catch (MyException e) {
            e.printStackTrace();
        }
    }
}

```

### 异常链

&emsp;&emsp; 在设计模式中有一个叫做责任链模式，该模式是将多个对象链接成一条链，
客户端的请求沿着这条链传递直到被接收、处理。同样Java异常机制也提供了这样一条链
：异常链。

&emsp;&emsp; 我们知道每遇到一个异常信息，我们都需要进行try…catch,一个还好，如果
出现多个异常呢？分类处理肯定会比较麻烦，那就一个Exception解决所有的异常吧。这样
确实是可以，但是这样处理势必会导致后面的维护难度增加。最好的办法就是将这些异常
信息封装，然后捕获我们的封装类即可。

&emsp;&emsp; 我们有两种方式处理异常，一是throws抛出交给上级处理，二是try…catch做
具体处理。但是这个与上面有什么关联呢？try…catch的catch块我们可以不需要做任何处
理，仅仅只用throw这个关键字将我们封装异常信息主动抛出来。然后在通过关键字throws
继续抛出该方法异常。它的上层也可以做这样的处理，以此类推就会产生一条由异常构成
的异常链。

&emsp;&emsp; 通过使用异常链，我们可以提高代码的可理解性、系统的可维护性和友好性
。

### 异常实践

1. 尽可能的减小try块！！！

2. 保证所有资源都被正确释放。充分运用finally关键词。

3. catch语句应当尽量指定具体的异常类型，而不应该指定涵盖范围太广的Exception类。
   不要一个Exception试图处理所有可能出现的异常。

4. 既然捕获了异常，就要对它进行适当的处理。不要捕获异常之后又把它丢弃，不予理睬

5. 在异常处理模块中提供适量的错误原因信息，组织错误信息使其易于理解和阅读。

6. 不要在finally块中处理返回值。

7. 不要在构造函数中抛出异常。
