---
title: static和单例模式
date: 2021-07-31 08:56:49
categories: java
tags: java
---

# static关键字

> 1. **static修饰的成员是在JVM加载类的时候就会被立即分配空间以及初始化 - 只有1次**,内存的实例只有1份
> 2. 非静态的成员的是在每次创建对象的时候才会被分配空间以及初始化



## 作用

> 1. static修饰符类 - 静态内部类[过]
> 2. 修饰的属性 - 静态属性
> 3. 方法 - 静态方法
> 4. 代码块 - 静态代码块



## JVM加载类进内存的活动顺序

0. JVM加载类进内存

1. 给所有的静态属性分配空间以及初始化

2. 给所有的静态方法分配空间以及初始化

3. 调用静态代码块

4. 如果创建了对象

   4-1. 给这个对象的所有的非静态属性分配空间以及初始化

   4-2. 给这个对象的所有的非静态方法分配空间

   4-3. 调用普通代码块

   4-4. 调用构造块



# static关键字应用

> 设计模式 - 单例模式
>
> 如何保证某个类的对象在内存中永远只有1份.后期,某些类的创建和销毁的时间成本比较高.这些类有必要设计成单例的.
>
> 比如jdbc中的连接池对象[重量级的对象]



## 饿汉模式 多线程安全

```java
package tech.aistar.design.singleton.version01;

/**
 * 本类用来演示: 将Singleton01设置成饿汉模式 - 单例的
 *
 * @author: success
 * @date: 2021/7/27 9:28 上午
 */
public class Singleton01 {
    //2. 初始化一个变量,该变量就是该类的唯一实例[对象]
    private static Singleton01 instance = new Singleton01();

    //1. 私有化构造
    private Singleton01(){
        //比较费时费力的代码,可能需要更多的时间
        System.out.println("Singleton01...");
    }

    //3. 提供一个公开的静态的方法来返回这个类的唯一实例
    public static Singleton01 getInstance(){
        return instance;
    }
}
class TestSingleton01{
    public static void main(String[] args) {
        //获取类的实例
        Singleton01 s1 = Singleton01.getInstance();
        Singleton01 s2 = Singleton01.getInstance();
        System.out.println(s1 == s2);//true
    }
}
```

## 懒汉模式 多线程不安全

```java
package tech.aistar.design.singleton.version02;

/**
 * 本类用来演示: 懒汉
 *
 * @author: success
 * @date: 2021/7/27 9:35 上午
 */
public class Singleton02 {
    private static Singleton02 instance;

    private Singleton02(){
        System.out.println("私有化构造");
    }

    public static Singleton02 getInstance(){
        return instance == null?instance = new Singleton02():instance;
    }
}
class TestSingleton02{
    public static void main(String[] args) {
//        Singleton02 s1 = Singleton02.getInstance();
//        Singleton02 s2 = Singleton02.getInstance();
//        System.out.println(s1 == s2);
    }

}
```

## 双重检查锁 - 笔试脱手写的版本

> 懒汉模式下 - 仍然是多线程安全的
>
> ```java
> package tech.aistar.design.singleton.version03;
> 
> /**
>  * 本类用来演示:双重检查锁 - 笔试脱手写的版本
> *
>  * @author: success
>  * @date: 2021/7/27 10:07 上午
> */
> public class Singleton03 {
>  private static Singleton03 instance;
> 
>  private Singleton03(){
>      System.out.println("私有化构造");
>  }
> 
>  public static Singleton03 getInstance(){
>      //因为本身"锁"资源就是一个比较昂贵的资源,为了避免跑得慢的线程去抢这个把锁,所以此处也要进行非空判断.
>      if(instance == null){
>          //跑得快的线程都会进入此处A,B
>          //A,B线程就会去争抢锁资源,谁抢到锁资源,谁就会进入
>          //同步代码块去执行里面的代码
>          //假设A线程抢到了,B就会在等待
>          synchronized (Singleton03.class){
>              //A进入判断instance==null
> 
>              //只要抢到"锁"的线程,如果此处没有非null判断
>              //那么进入到这的线程都会去执行instance = new Singleton03();
>              if(instance == null){
>                  //由A线程进行实例的初始化
>                  //A继续执行,等A执行完毕之后,那么A就会释放锁资源
>                  //其他线程继续争抢锁资源.
>                  instance = new Singleton03();
>              }
>          }
>      }
>      return instance;
>  }
> }
> ```

## volatile

> 1. 可见性 - 忽略
> 2. 禁止JVM的指令重排
>
> ```java
> int i=10;//原子性操作
> 
> Student s = new Student();//不是一个原子性操作
> ①给s对象分配内存空间
> ②调用构造块,执行构造函数 - 初始化动作
> ③将引用s指向内存中的那个对象.
>   
> 但是JVM内部会对这些指令进行一个优化操作.
> 本来你的顺序应该是①-②-③,经过JVM指令重排之后,顺序有可能
> 变成①-③-②
> ```
>
> 

```java
package tech.aistar.design.singleton.version03;

/**
 * 本类用来演示:双重检查锁 - 笔试脱手写的版本
 *
 * @author: success
 * @date: 2021/7/27 10:07 上午
 */
public class Singleton03 {
    private volatile static Singleton03 instance;
    //private static Singleton03 instance;

    private Singleton03(){
        System.out.println("私有化构造");
    }

    public static Singleton03 getInstance(){
       
        //② - 跑得很慢的线程刚刚到达此处,其他线程判断instance是不为null的
        //但是instance可能指向的是一个"半成品" - A线程还没有完成构造方法.
        //其他线程可能会使用到这个"半成品"对象,肯定会有问题.
        if(instance == null){
           
            synchronized (Singleton03.class){
              
                if(instance == null){
                    //① - A线程可能执行到2步骤的时候,instance已经不为null
                  
                    //不是一个原子性操作,可能经过JVM指令重排
                    //1. 给instance对象分配空间
                    //2. 将对象的内存地址赋值给了instance
                    //3. 调用构造方法
                    instance = new Singleton03();
                }
            }
        }
        return instance;
    }
}
```































