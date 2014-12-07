---
layout: post
title: "彻底理解单例模式"
date: 2014-12-07 20:01:21 +0800
comments: true
categories: java 
---
在众多的设计模式中，单例模式应该是用得较多的一种模式，同时它也相对简单，但是也容易出错，特别是在多线程的情况下，因此有必要彻底研究清楚。

##什么是单例模式
单例模式，也叫单子模式，是一种常用的软件设计模式。在应用这个模式时，单例对象的类必须保证只有一个实例存在。

##为什么会有单例模式
1. 许多时候整个系统只需要拥有一个的全局对象，这样有利于我们协调系统整体的行为。比如在Android应用程序开发时，通常用一个单例对象去读取存放在配置文件中的信息，然后在Activity或者Service中用这个单例对象统一的这些信息；
2. 避免重复创建多个对象，提高性能。

##怎么写单例模式
在Java中通常有两种写法：

* 懒汉模式(艺名：双重校验锁模式)

懒汉模式，指全局的单例实例在第一次被使用时构建，如果不使用，对不起我很懒，有多懒，就是懒得创建，懒得理你；

``` java

public class Singleton {
  
    private static volatile Singleton INSTANCE = null;
    
    private Singleton() {}
    
    public static  Singleton getInstance() {
        if(INSTANCE == null){
             synchronized(Singleton.class){         
                 if(INSTANCE == null){ 
                     INSTANCE = new Singleton();
                  }
              } 
        }
        return INSTANCE;
    }
  }

```
这个写法只能在JDK1.5以后有效，原因是在1.5以后的版本关键字`volatile`才有了具体含义，至此之前，`volatile`只作为保留关键字，并没有具体含义；

`volatile`含义是：当用于一个作用域时，读和写建立了一个happens-before关系，类似于申请和释放一个互斥锁，简单点说就是：用`volatile`修饰的变量，线程在每次使用变量的时候，都会读取变量最后修改的值。

*现在我们来彻底理解这种写法：*
<!-- more -->
代码首先声明了一个`私有` `静态` `原子类型volatile`的实例变量，`私有`保证外部类不能访问，`静态`保证实例化的变量只会存在一份，关键是怎么来理解这个`原子类型volatile`；

这要从编译原理说起，所谓编译，就是把源代码“翻译”成目标代码（大多数是指机器代码）的过程。针对Java，它的目标代码不是本地机器代码，而是虚拟机代码。编译原理里面有一个很重要的内容是编译器优化。所谓编译器优化是指，在不改变原来语义的情况下，通过调整语句顺序，来让程序运行的更快。这个过程成为reorder。

要知道，JVM只是一个标准，并不是实现。JVM中并没有规定有关编译器优化的内容，也就是说，JVM实现可以自由的进行编译器优化。

下面来想一下，创建一个变量需要哪些步骤呢？一个是申请一块内存，调用构造方法进行初始化操作，另一个是分配一个指针指向这块内存。这两个操作谁在前谁在后呢？JVM规范并没有规定。那么就存在这么一种情况，JVM是先开辟出一块内存，然后把指针指向这块内存，最后调用构造方法进行初始化。

现在我们再来理解这个`volatile`关键字，假如我们不使用这个关键字，不妨假设有两个线程A和B先后调用`getInstance()`方法，线程A已经走到`INSTANCE = new Singleton();`，此时，按照上面所说，这是后先虚拟机先开辟一块内存，让`INSTANCE`这个指针(叫`引用`更为准确)指向这块内存，在这个时候线程B走到第一个判断`INSTANCE`是否null，现在此时`INSTANCE`所指并不为空，这样就造成函数直接返回了`INSTANCE`,而此时`INSTANCE`还未来得及调用构造方法进行初始化，进而引发问题。

另外，为什么同步锁`synchronized`要加在方法内部呢，而不是方法上岂不更好？虽然也没错，但是这样存在性能问题，要知道synchronized修饰的同步块可是要比一般的代码段慢上几倍的！如果存在很多次getInstance()的调用，那性能问题就不得不考虑了！让我们来分析一下，究竟是整个方法都必须加锁，还是仅仅其中某一句加锁就足够了，分析可知，出现多个实例的原因是检测null的操作和创建对象的操作分离了。如果这两个操作能够原子地进行，那么单例就已经保证了，所以我们只需要同步这块代码就好。

那为啥需要第二次检查null的操作呢？原因是：假如有两个线程A和B同时调用`getInstance()`方法，都同时判断过第一个是否为null的条件，这个时候如果没有第二次判断为空的条件，这会创建多个实例，单例失败！


* 饿汉模式

饿汉模式，指全局的单例实例在类装载时构建

``` java
  public class Singleton {
    private final static Singleton INSTANCE = new Singleton();
    
    private Singleton() {}
 
    public static Singleton getInstance() {
        return INSTANCE;
    }
  }
```
*现在我们来彻底理解这种写法：*

首先在类的成员变量中就声明并初始化一个私有，final，static的实例变量，这样就保证了这个实例只可能只有一份；紧接着是私有化构造方法，然后通过一个共有的静态工厂方法返回类的唯一实例；之所以才有静态工厂方法返回实例，而不是直接用过共有的成本变量呢？原因是采用静态工厂方法后期可以更好的扩展，例如下次我不想返回这个实例，也可以返回其他的，这样就方便我们修改；

虽然饿汉模式能很简洁地写出单例模式代码，但是这样带来了性能上的问题，因为这个单例实例，在类装载的时候就创建了，有可能我们根本不会用到这个方法，造成了资源浪费；那么怎么改进呢？请看下面这种改进写法：

``` java
  public class Singleton {
  
    private static class Instance {
    private final static Singleton INSTANCE = new Singleton();
    }  
        
    private Singleton() {}
 
    public static Singleton getInstance() {
        return Instance.INSTANCE;
    }
  }
```

在这一版本的单例模式实现代码中，我们使用了Java的静态内部类。这一技术是被JVM明确说明了的，因此不存在任何二义性。在这段代码中，因为Singleton没有static的属性，因此并不会被初始化。直到调用getInstance()的时候，会首先加载Instance类，这个类有一个static的Singleton实例，因此需要调用Singleton的构造方法，然后getInstance()将把这个内部类的INSTANCE返回给使用者。由于这个INSTANCE是static的，因此并不会构造多次。
 
由于Instance是私有静态内部类，所以不会被其他类知道，同样，static语义也要求不会有多个实例存在。并且，JSL规范定义，类的构造必须是原子性的，非并发的，因此不需要加同步块。同样，由于这个构造是并发的，所以getInstance()也并不需要加同步。

##总结

虽然以上两种模式，在大多数情况下都没有问题，但是在某些情况下，还是不能保证所创建的实例是唯一的，比如，如果这个类实现了序列化接口，那么就可能在反序列化的时候，通过`反射私有的构造方法`而创建实例，为了防止这样的情况发生，我们还需要在类中实现如下方法：

``` java
 private Object readResolve() {     
             return INSTANCE;     
       } 
```
此外，如果单例由不同的类装载器装入，那便有可能存在多个单例类的实例。假定不是远端存取，例如一些servlet容器对每个servlet使用完全不同的类  装载器，这样的话如果有两个servlet访问一个单例类，它们就都会有各自的实例。解决方式是：

``` java
private static Class getClass(String classname) throws ClassNotFoundException {     
       ClassLoader classLoader = Thread.currentThread().getContextClassLoader();     
       if(classLoader == null) 
           classLoader = Singleton.class.getClassLoader();     
           return (classLoader.loadClass(classname));        
 }  
 ```
Effective Java作者Josh Bloch 提倡用enum的方式，它不仅能避免多线程同步问题，而且还能防止反序列化重新创建新的对象，可谓是很坚强的壁垒啊，不过，个人认为由于1.5中才加入enum特性，这种写法不太常见，代码如下：

``` java
public enum Singleton {  
     INSTANCE;  
     public void whateverMethod() {  
     }  
 }  
```
