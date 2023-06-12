---
title: 重写equals()和hashCode()方法
date: 2021-07-31 09:03:12
categories: java
tags: equals()和hashCode()方法
---

# Object

> java.lang.Object类是所有的类的基类,根类,超类
>
> 所有的类如果没有明确指定父类,都会默认继承Object类.

## equals

> 1. boolean equals(Object obj);//对象之间的比较.默认仍然使用的是==
>
> **"业内认可的一种说法" - 基本类型用==比较的值,对象类型用==比较的是地址**
>
> ```java
> public boolean equals(Object obj) {
>   return (this == obj);
> }
> ```
>
> 默认的比较方式不符合实际的业务需要,因此需要重写equals方法
>
> success版本
>
> ```java
>  @Override
> public boolean equals(Object obj){
>   //1. 非空性
>   if(obj == null)
>     return false;
>   //2. 自反性
>   if(this == obj)//p1.equals(p1)
>     return true;
>   //3. 一致类型,判断具体的哪个属性是一致的,才认为返回true
>   // p1.equals(user);
>   if(obj instanceof Product){
>     Product p = (Product) obj;
>     //return this.id == p.id;
>    
>     //id和name都一致的时候,才认为是同一个对象
>     //return (this.id == p.id) && (this.name.equals(p.name));
>    
>     //需求 - 认为产品的id以及这个产品对应的产品信息的id一致的时候认为返回true
>     return this.id == p.id && this.getProductInfo().equals(p.productInfo);
>   }
>   return false;
> }
> ```



## hashCode方法

> int hashCode();//返回对象的哈希值.一般是和equals方法是成对出现的.目的是为了**提高性能.**

> 容器应用中 - 在什么时候才会去调用equals方法? - 当哈希碰撞的时候,只要哈希不冲突,不会调用equals方法
>
> 而调用equals方法执行的时机肯定会慢一点的.因为它需要比较属性值[需要更多的时间]

> **`关于数字31` - 质数 - 1. 尽可能产生更多的哈希值  2. 让哈希碰撞/冲突尽可能少.**

```java
@Override
public int hashCode() {
  int result = id != null ? id.hashCode() : 0;
  result = 31 * result + (name != null ? name.hashCode() : 0);
  result = 31 * result + (productInfo != null ? productInfo.hashCode() : 0);
  return result;
}
```

`超前知识点`

```java
package tech.aistar.day10.hashcodes;

import java.util.HashSet;
import java.util.Set;

/**
 * 本类用来演示:
 *
 * @author: success
 * @date: 2021/7/28 2:01 下午
 */
public class TestCourse {
    public static void main(String[] args) {
        Course c1 = new Course(1,"java");
        Course c2 = new Course(2,"mysql");
        Course c3 = new Course(3,"mysql");
        Course c4 = new Course(4,"mysql");
        Course c5 = new Course(5,"mysql");
        Course c6 = new Course(1,"oracle");
//        System.out.println(c1 == c2);//false
//        System.out.println(c1.equals(c2));//true

        //集合 - "容器",比数组更加强大的.
        //集合 - 里面存储的数据是无序不可重复的
        //不能存储同一个对象
        Set<Course> sets = new HashSet<>();
        //1. 当把某个对象放入到该容器之前,先调用了该对象的hashCode方法,得到一个哈希值
        //2. 根据这个哈希值就可以被分配一个内存地址
        //3. 如果这个哈希值在这之前没有出现过,说明这个位置没有被占用.那么就会直接将该对象放入到
        //   这个哈希值对应的在容器中的那个位置
        //4. 如果这个哈希值在这之前出现过.但是此时还不能说明这个对象和之前的那个对象是同一个对象?
        //   因为哈希值是通过哈希算法计算得到的,可能产生"哈希冲突" - "哈希碰撞"
        //5. 当哈希值一样的时候,那么才会去调用equals方法,如果equals方法仍然返回true,那么才
        //   最终确定这个对象曾经出现过,那么就拒绝添加.
        sets.add(c1);
        sets.add(c2);
        sets.add(c3);
        sets.add(c4);
        sets.add(c5);
        sets.add(c6);

        for (Course c : sets) {
            System.out.println(c);
        }
    }
}

```

### 总结

> 1. 如果俩个对象的hashcode值一样,那么equals比较,不一定返回true
> 2. 如果俩个对象equals返回true,那么这俩个对象的哈希值必须要一样 - 数据的完整性.

### 笔试题

> == 和 equals的区别
>
> 1. == 基本类型使用,比较的就是基本类型的数值
> 2. equals 对象类型使用.如果某个类没有重写equals方法,那么调用的是java.lang.Object中的equals方法,这个时候仍然使用到的是==比较.如果重写了equals,就看equals提供的是哪些属性一致的时候,才返回true.

## clone方法

> protected Object clone();//默认的克隆方法是浅克隆

### 浅克隆

> 浅拷贝,浅层复制,浅复制 - 默认的.
>
> protected - public
>
> ```java
> @Override
> public Object clone() throws CloneNotSupportedException {
> return super.clone();
> }
> Object中的clone方法 - native修饰的方法 - 本地方法栈中的方法 - C写的 - Java语言本地调用C程序.
> protected native Object clone() throws CloneNotSupportedException;
> ```

> 只有实现了java.lang.Cloneable接口[标记接口]的对象才能够调用clone方法,否则会抛出java.lang.CloneNotSupportedException不支持克隆的异常.

> ```java
> Product copy = (Product) p1.clone();
> 
> Product copy2 = new Product(1,"mac",1000.0d);
> 
> 为什么需要使用clone,而不直接new一个.
> clone方法它直接操作的堆里面的内存 - 效率是高于重新new的.
> ```

> ```java
> package tech.aistar.day10.obj;
> 
> /**
>  * 本类用来演示:
> *
>  * @author: success
>  * @date: 2021/7/28 2:32 下午
> */
> public class TestCloneDemo {
>  public static void main(String[] args) {
>      Product p1 = new Product(1,"mac",1000.0d);
>      ProductInfo info = new ProductInfo(1,"good");
> 
>      p1.setProductInfo(info);
> 
>      //System.out.println(p1 instanceof Cloneable);//true
> 
>      try {
>          //浅拷贝
>          Product copy = (Product) p1.clone();
> 
>          //1. 修饰基本数据类型以及字符串 - 原来的对象 - 是否对copy对象产生影响 - 不会
>          p1.setId(10);
>          p1.setName("tom");
> 
>          //2. 修改对象类型 - 会对copy的对象产生影响的
>          p1.getProductInfo().setId(100);
>          p1.getProductInfo().setInfo("bad");
> 
>          System.out.println(copy);
>          System.out.println(copy.getProductInfo());
>          //System.out.println(copy == p1);//false
> 
>      } catch (CloneNotSupportedException e) {//不能被克隆的异常
>          e.printStackTrace();
>      }
>  }
> }
> 
> ```

### 深克隆

> 深层复制,深拷贝,深复制 - 需要自己重写clone里面的具体的代码的
>
> ```java
> /**
>      * 深拷贝 - 修改原来对象中的任何类型的数据都不会对副本对象产生影响
>      * @return
>      * @throws CloneNotSupportedException
>   */
> @Override
> public Object clone() throws CloneNotSupportedException {
> //Product copy = (Product)p1.clone();
> //手动新建一个Product对象
> Product product = new Product();
> product.setId(id);
> product.setName(name);
> product.setPrice(price);
> 
> ProductInfo infos = getProductInfo();
> if(infos!=null){
>  ProductInfo pi = new ProductInfo();
>  pi.setId(infos.getId());
>  pi.setInfo(infos.getInfo());
> 
>  product.setProductInfo(pi);
> }
> return product;
> }
> ```

