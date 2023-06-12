---
title: String和StringBuilder
date: 2021-07-31 08:58:01
categories: java
tags: java
---

# java.lang.String

> Java中提供的处理字符串的类.
>
> 1. 不可变的字符串
>
> ```java
> String s = "abc";
> s = "eef";//ok,"abc"就变成了垃圾对象.
> ```
>
> 
>
> 2. 字符串本质上就是一个**字符数组**
>
> ```java
> //final修饰的类不可被继承,
> public final class String
>     implements java.io.Serializable, Comparable<String>, CharSequence {
>     
>     //维护的是不可变的字符数组
>     //String s = "abc";//将"abc"打散,保存成value[]数组中.
>     private final char value[];   	
> }
> ```

**虽然String底层是final修饰的字符数组, 但本质上说的是不可改变的是其地址, 可以改变的是其里面的值**

**实例**

~~~java
final char[] arr2 = {1,2};
for (int i = 0; i < arr2.length; i++) {
    arr2[i] = 'a';
}
System.out.println(arr2);
~~~



## 字符串构建的方式

> 1. 通过new关键字来构建
>
> ```java
> //创建了几个对象?
> //1个或者2个
> //①通过new关键字在堆里面创建一个字符串对象"abc"
> //②同时在"串池"里面扔一个对象"abc"[前提是串池中之前不存在"abc"]
> String s1 = new String("abc");
> ```
>
> 2. 直接通过"串池"/"字符串常量池"的方式来构建
>
> ```java
> //创建了几个对象?
> 
> //它会在"串池"中创建一个"abc"字符串对象,然后将地址给了s1
> String s1 = "abc";
> //"串池"中的特点:优先先到串池中查找是否已经存在"abc"对象,如果有直接返回地址给s2
> //否则才会继续创建新的字符串对象
> String s2 = "abc";
> System.out.println(s1 == s2);//true
> ```



## 关于字符串的拼接

> 字符串的拼接使用+,但是使用+的拼接效率非常低.
>
> ```java
> package tech.aistar.day09;
> 
> import java.util.Date;
> 
> /**
>  * 本类用来演示: 字符串拼接效率低
> *
>  * @author: success
>  * @date: 2021/7/27 2:15 下午
> */
> public class StringPlusDemo {
>  public static void main(String[] args) {
>      test02();//append
> 
>      test01();//+
>  }
> 
>  public static void test01(){
> 
>      long t1 = System.currentTimeMillis();//获取当前系统时间的毫秒数
> 
>      String str = "";
> 
>      for (int i = 0; i < 100000; i++) {
>          str+=i;//str = str + i;
>      }
> 
>      long t2 = System.currentTimeMillis();
> 
>      System.out.println("str拼接时间:"+(t2-t1)+"ms");
>  }
> 
>  public static void test02(){
>      //java.lang.StringBuilder
>      StringBuilder builder = new StringBuilder();
> 
>      long t1 = System.currentTimeMillis();//获取当前系统时间的毫秒数
> 
>      for (int i = 0; i < 100000; i++) {
>         builder.append(i);//拼接方式
>      }
> 
>      long t2 = System.currentTimeMillis();
>      System.out.println("append拼接时间:"+(t2-t1)+"ms");
>  }
> }
> ```

`拼接效率的原因`

> 1. +底层每次都会产生一个StringBuilder对象
> 2. +底层仍然是调用StringBuilder中的append方法

```java
字符串使用+拼接的过程中,会产生新的对象 - 拼接效率低

String s1 = "abc";

String s2 = new String("abc");
  
admindeMacBook-Pro:Desktop admin$ javap -c HelloStr
Compiled from "HelloStr.java"
public class HelloStr {
  public HelloStr();
    Code:
       0: aload_0
       1: invokespecial #1                  // Method java/lang/Object."<init>":()V
       4: return

  public static void main(java.lang.String[]);
    Code:
       0: ldc           #2                  // String abc  放在了"串池中"
       2: astore_1
       3: new           #3                  // class java/lang/String
       6: dup                               // "abc" -> 堆里面
       7: ldc           #2                  // String abc  "串池中已经出现了"
       9: invokespecial #4                  // Method java/lang/String."<init>":(Ljava/lang/String;)V
      12: astore_2
      13: return
}
```

```java
String s1 = "abc";

String s2 = new String("def");

s1 = s1 + s2;//会产生一个新的对象

admindeMacBook-Pro:Desktop admin$ javap -c HelloStr
Compiled from "HelloStr.java"
public class HelloStr {
  public HelloStr();
    Code:
       0: aload_0
       1: invokespecial #1                  // Method java/lang/Object."<init>":()V
       4: return

  public static void main(java.lang.String[]);
    Code:
       0: ldc           #2                  // String abc
       2: astore_1
       3: new           #3                  // class java/lang/String
       6: dup
       7: ldc           #4                  // String def放在了堆里面
       9: invokespecial #5                  // Method java/lang/String."<init>":(Ljava/lang/String;)V
      12: astore_2
      13: new           #6                  // class java/lang/StringBuilder
        							                      //字符串每次拼接+,都会产生一个新的StringBuilder对象
      16: dup
      17: invokespecial #7                  // Method java/lang/StringBuilder."<init>":()V
        									                  //StringBuilder builder = new StringBuilder();
      20: aload_1
      21: invokevirtual #8                  // Method java/lang/StringBuilder.append:
        	                                  //(Ljava/lang/String;)Ljava/lang/StringBuilder;
        
        																		//字符串的+,底层仍然是去调用StringBuilder类中提供的append方法
      24: aload_2
      25: invokevirtual #8                  // Method java/lang/StringBuilder.append:(Ljava/lang/String;)Ljava/lang/StringBuilder;
      28: invokevirtual #9                  // Method java/lang/StringBuilder.toString:()Ljava/lang/String;
      31: astore_1
      32: return
}
```

## 字符串的比较

```java
package tech.aistar.day09;

/**
 * 本类用来演示: 字符串的各种比较
 *
 * @author: success
 * @date: 2021/7/27 2:35 下午
 */
public class StringCompare {
    public static void main(String[] args) {
        //s1默认指向的是堆里面的字符串对象
        String s1 = new String("abc");
        String s2 = "abc";
        System.out.println(s1 == s2);//false

        //intern()强制让s1指向串池的对象
        System.out.println(s1.intern() == s2);//true

        String s3 = "b";
        String s4 = "a" + s3;//产生一个StringBuilder对象, 
        //因为s3是一个变量,不确定因数, 所以只能把转化为StringBuilder在用append拼接
        String s5 = "a" + "b";//因为此处的"a"和"b"就是字符串常量
                              //jvm对待字符串常量的拼接 - "a"和"b"的拼接
                              //当成"ab"的整体,直接扔到常量池中.

        System.out.println(s4 == s5);//false

        //字符串使用final修饰
        final String s6 = "b";
        //"a"+s6 => "ab"扔到常量池,没有产生新的对象.
        String s7 = "a" + s6;
        System.out.println(s5 == s7);//true


        String x1 = new String("abc");
        String x2 = new String("abc");
        System.out.println(x1 == x2);//false

        System.out.println(x1);

        //希望字符串的值是一样的,就希望返回true
        System.out.println(x1.equals(x2));//true

    }
}
```

## 字符串常用方法

> 构造方法
>
> String();
>
> String(String val);
>
> String(StringBuilder builder);//StringBuilder对象转换成String对象

> ```java
> package tech.aistar.day09;
> 
> /**
>  * 本类用来演示: 字符串常用方法
> *
>  * @author: success
>  * @date: 2021/7/27 3:02 下午
> */
> public class StringMethod {
>  public static void main(String[] args) {
>      String s = new String("abc");
>      // java.lang.StringIndexOutOfBoundsException
>      System.out.println(s.charAt(1));//b
>      System.out.println(s.length());//3
> 
>      String result = s.concat("de");//原来的字符串的末尾添加新的字符串
>      System.out.println(result);//abcde
> 
>      //判断字符串中是否包含某个序列
>      System.out.println(s.contains("bc"));//true
> 
>      //判断前缀或者后缀
>      System.out.println("xxx.gif".endsWith(".gif"));//true
> 
>      System.out.println("javaScript".startsWith("java"));//true
> 
>      System.out.println("abc".equals(s));//true
> 
>      //忽略大小写的比较
>      System.out.println("ABc".equalsIgnoreCase("abc"));//true
> 
>      //转换大写和小写
>      System.out.println("abc".toUpperCase());//ABC
> 
>      System.out.println("ABC".toLowerCase());//abc
> 
>      //返回指定字符第一次出现的字符串内的索引。
>      System.out.println("abcabc".indexOf("b"));//1
> 
>      System.out.println("abcabc".lastIndexOf("b"));//4
> 
>      //判断字符串是否为空
>      System.out.println("".isEmpty());//长度为0的字符串 - 返回true
> 
>      //替换
>      System.out.println("I Love Girl".replace("Girl","Java"));
> 
>      //字符串的切割
>      String ip = "192:168:3:90";
> 
>      String[] arr = ip.split("\\:");
>      for (String s1 : arr) {
>          System.out.println(s1);
>      }
> 
>      //将字符串转换成char类型数组
>      char[] arr2 = "白日依山尽".toCharArray();
>      for (char c : arr2) {
>          System.out.println(c);
>      }
> 
>      String x1 = "abcde";
>      String x2 = x1.substring(1,3);//[1,3)
>      System.out.println(x2);
> 
>      System.out.println(x1.substring(1));//从1一直截取到最后bcde
> 
>      //trim() - 去除字符串左右两边的空格
>      System.out.println("  ab c  ".trim().length());//4
> 
>      System.out.println("       ".trim().length());//0
> 
>      //int->String
>      String o = String.valueOf(10);
>      System.out.println(o+10);
>  }
> }
> 
> ```

# java.lang.StringBuilder

> 它是一个可变的字符串 - final修饰,性能最高的.默认的容量16.线程不安全的
>
> StringBuilder();
>
> StringBuilder(String val);//String类型转换成StringBuilder类型

## 扩容机制

> 研究append方法
>
> ```java
> StringBuilder x = new StringBuilder("abc");
> x.append("def");
> 
> StringBuilder类的父类AbstractStringBuilder
> 
> public AbstractStringBuilder append(String str) {
> if (str == null)
>  return appendNull();
> int len = str.length();//len = 3
> //count+len
> //count - 原来数组中已经存在的字符的长度
> //len - 需要存入的新的字符串长度
> ensureCapacityInternal(count + len);//3
> str.getChars(0, len, value, count);
> count += len;
> return this;
> }
> 
> 扩容方法
> private void ensureCapacityInternal(int minimumCapacity) {
> // overflow-conscious code
> if (minimumCapacity - value.length > 0)
>  expandCapacity(minimumCapacity);
> }
> 
> //真正的扩容方法
> void expandCapacity(int minimumCapacity) {
> int newCapacity = value.length * 2 + 2;//扩容,得到新的长度
> if (newCapacity - minimumCapacity < 0)//扩容得不够,不够长
>  newCapacity = minimumCapacity;//直接补充到最长的.
> if (newCapacity < 0) {//长度会溢出.
>  if (minimumCapacity < 0) // overflow
>    throw new OutOfMemoryError();
>  newCapacity = Integer.MAX_VALUE;
> }
> value = Arrays.copyOf(value, newCapacity);
> }
> 
> ```

## 常用方法

```java
package tech.aistar.day09;

/**
 * 本类用来演示: 常用方法 - 可变的字符串
 *
 * @author: success
 * @date: 2021/7/27 4:20 下午
 */
public class StringBuilderMethod {
    public static void main(String[] args) {
        StringBuilder x = new StringBuilder("abcd");//String->StringBuilder
        System.out.println(x);

        StringBuilder result = x.delete(1,3);//[1,3), 使用delete的原字符串也会发生改变
        System.out.println("result:"+result);//ad
        System.out.println("x:"+x);//ad

        System.out.println(x.deleteCharAt(0));//d 根据下标进行删除操作, 原数组也会改变

        x.insert(0,"管管");
        System.out.println(x);//管管d

        //反转字符串
        System.out.println(x.reverse());//d管管

        int n = 6723;
        System.out.println(String.valueOf(n).length());
//String.valueOf(n) int -> String
        StringBuilder y = new StringBuilder(String.valueOf(n));
//new StringBuilder(String.valueOf(n)) int->String->StringBuilder
        System.out.println(y.reverse());

        //StringBuilder->String
        System.out.println(x.toString());

    }
}

```



# 总结

> String类型和StringBuilder类型之间的相互转换问题
>
> StringBuilder->String类型
>
> 1. java.lang.StringBuilder中提供的String toString();
> 2. java.lang.String类中提供的构造方法String(StringBuilder builder);
> 3. java.lang.String类中提供的static String valueOf(Object obj);//面向父类编程    

> String->StringBuilder
>
> 1. java.lang.StringBuilder中提供的构造StringBuilder(String val);

# java.lang.StringBuffer

> 它的使用方式和StringBuilder是一模一样的.唯一区别是它是多线程安全的.可变的字符串
>
> 因为它里面的方法都使用**synchronized**关键字进行了修饰,重量级的对象.
>
> 性能低于StringBuilder



# 笔试题

> String,StringBuilder,StringBuffer三者的区别? - 查,**补充完整** - 作业
>
> 1. 三者之间都是使用final修饰的,都是不可被继承的,jdk处于安全角度考虑的 - 共同点
> 2. 不同点
>
> 2-1. String它是不可变的字符串,线程安全的.比较耗系统资源和内存的.性能是三个当中最低的.
>
> 2-2. StringBuilder可变的字符串 - 线程不安全的,效率是最高的.
>
> 2-3. StringBuffer可变的字符串 - 线程安全的.效率低于StringBuilder,但是高于String



# 作业

> 两道笔试题
>
> ```java
> //从oldStr中删除指定的target字符串
> //比如"abcdebcef","bc" -> 返回"adeef"
> //禁止使用replace方法
> //递归算法
> public static String delByStr(String oldStr,String target){
> 
> }
> 
> ```
>
> ```java
> //找出俩个字符串中的最大长度的公串
> //abcbcedfdfdepopoqqq
> //fdfdbcbceoifdfdeqqa
> 
> //bcbce fdfde
> 
> ```























