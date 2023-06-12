---
title: IO流
date: 2021-08-05 20:13:04
categories: java
tags: java
---

# IO流

> 流是一种抽象概念，它代表了数据的**无结构化传递**。**按照流的方式[数据在传输之前,需要进行转换,转换成无结构的字节/字符序列]**进行**有序的**输入输出，**数据被当成无结构的字节序或字符序列**。从流中取得数据的操作称为提取操作，而向流中添加数据的操作称为插入操作。用来进行输入输出操作的流就称为IO流。换句话说，IO流就是以流的方式进行输入(Input)输出(Output).........![](D:/%E7%AE%A1%E7%88%B8%E7%88%B8%E7%9A%84%E8%B5%84%E6%96%99/cnm/j03s_tech/note/imgs/io01.jpeg) 

`流想象成是数据传输的那个管道`

`磁盘中数据加载/读入到内存中 - 输入`

![](D:/%E7%AE%A1%E7%88%B8%E7%88%B8%E7%9A%84%E8%B5%84%E6%96%99/cnm/j03s_tech/note/imgs/io02.png) 

`java内存中数据写出去到磁盘中 - 输出`

![](D:/%E7%AE%A1%E7%88%B8%E7%88%B8%E7%9A%84%E8%B5%84%E6%96%99/cnm/j03s_tech/note/imgs/io03.png)  

## 流的分类

> 1. 按照流的方向 - 输入流和输出流
> 2. 按照流的读取的单位不同 - 字节流[按照单个字节单个字节进行读写,文本文件或者二进制文件]和字符流[单个字符进行读写,只能操作文本字符文件]
> 3. 按照流的功能 - 节点流[基础流]和拓展流[过滤流或者包装流] - 采用了"装饰器设计模式思想"
>
> **节点流才具备真正操作文件的能力.**拓展流只是让流的功能更加强大而已,拓展流脱离了节点流的话,那么是不允许的.
>
> 拓展流的使用必须要建立在节点流的基础之上.

## 字节流

### 字节输入流

> 1. java.io.InputStream[C] - 字节输入流的顶级的抽象的父类
>    - java.io.FileInputStream[C] - 文件字节输入流 - 操作文件的读操作. - **典型的节点流**
>    - java.io.ObjectInputStream[C] - 操作对象类型的字节输入流 - **典型的包装流**
>    - java.io.FilterInputStream[C] - 过滤流
>      - java.io.BufferedInputStream[C] - 带缓存功能的流 - 缓冲流
>      - java.io.DataInputStream[C] - 操作基本数据类型的字节输入流

### 字节输出流

> java.io.OutputStream[C] - 字节输出流的顶级的抽象的父类
>
> - java.io.**FileOutputStream[C]** - 文件字节输出流 - 操作文件的写操作. - **典型的节点流**
> - java.io.ObjectOutputStream[C] - 操作对象类型的字节输出流 - **典型的包装流**
> - java.io.FilterOutputStream[C] - 过滤流
>   - java.io.**BufferedOutputStream[C]** - 带缓存功能的流 - 缓冲流
>   - java.io.DataOutputStream[C] - 操作基本数据类型的字节输出流

# InputStream常用方法

> 1. void close();//关闭流,流本身就是一个对象[占内存 - 昂贵的资源]
> 2. abstract  int read();//单个字节单个字节的读取,如果读不到,返回-1
> 3. **int read(byte[] b);//从输入流读取一些字节数，并将它们存储到缓冲区 b** 。

# OutputStream常用方法

> 1. void close();//关闭
> 2. abstract void write(int b);将指定的字节写入此输出流。
> 3. **void write(byte[] b, int off, int len);**

# IO流的编程步骤

> 1. 确定流 - 选什么流
> 2. 确定源头和目标
> 3. 循环读取,循环写出 - while
> 4. 关闭流

# 缓冲流BufferedInputStream

> 在它出来之前,可以可以单个字节单个字节进行读写操作 - 弊端:java程序和磁盘的IO交互比较频繁,性能就会低下.
>
> 采取了自定义的数组的方式.从磁盘文件中读取一定数量的字节先放入到缓冲数组中.然后再从缓冲数组中一次性写出到磁盘中.
>
> 减少了java程序和磁盘的IO交互 - 性能提高.

> JDK中发现了这点,提供了缓冲流BufferedInputStream
>
> ```java
> private static int DEFAULT_BUFFER_SIZE = 8192;//默认的容量8kb
> 
> protected volatile byte buf[];//内置的缓冲区
> 
> public BufferedInputStream(InputStream in) {
> this(in, DEFAULT_BUFFER_SIZE);
> }
> 
> public BufferedInputStream(InputStream in, int size) {
> super(in);
> if (size <= 0) {
>  throw new IllegalArgumentException("Buffer size <= 0");
> }
> buf = new byte[size];//初始化内置的缓冲区数组 - 大小是8kb
> }
> ```



> 底层利用了"装饰器"思想.这个流属于包装流,拓展流,过滤流,本身不具备真正的文件的读写能力.
>
> 构建带缓冲功能的流,必须要以节点流作为支撑

> `构架一个带缓冲功能的能够读取文件的字节输入流`
>
> BufferedInputStream in = new BufferedInputStream(new FileInputStream("路径"));
>
> BufferedOutputStream out = new BufferedOutputStream(new FileOutputStream("路径"));



## 缓冲数组

> BufferedInputStream本身已经在底层定义了8kb的字节数组,那么为什么程序中还自定义了一个3kb的字节数组[可以不定义,read()方法],自定义的byte[]和内置的byte[]各自的作用是什么?

> **内置的8kb的byte[] - 在一开始或者后面空闲的时候,由JVM去装载磁盘上的数据,提前放入到这个内置的byte[]中的.**

```java
package tech.aistar.day13;

import java.io.*;

/**
 * 本类用来演示: 缓冲流
 *
 * @author: success
 * @date: 2021/8/5 2:18 下午
 */
public class BufferDemo {
    public static void main(String[] args) {
        BufferedInputStream in = null;

        BufferedOutputStream out = null;

        try {
            //jvm加载磁盘上的数据到内置的byte[]中
            in = new BufferedInputStream(new FileInputStream("src/tech/aistar/day13/fast.gif"));
            out = new BufferedOutputStream(new FileOutputStream("src/tech/aistar/day13/fast_副本.gif"));

            //自定义一个byte[]
            //真正的读
            byte[] buf = new byte[3*1024];

            int len = -1;

            while(true){
                len = in.read(buf);
                if(len==-1)
                    break;
                out.write(buf,0,len);
            }
            System.out.println("文件拷贝成功!");

        } catch (FileNotFoundException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}

```

## 读过程

> 1. 当初始化好缓冲流之后,jvm会立即读取磁盘中的数据到内置的缓冲数组中
> 2. 当程序中循环读取到的时候 len = in.read(buf); = 程序是直接从内置的缓冲区[内置的缓冲数组中]
> 3. 一旦内置的数组中的8kb数据被取完,那么jvm在空闲的时候,继续加载文件中下一个8kb的字节到内置的缓冲区.



# 语法糖

> jdk7.0提供的写法
>
> 作用 - 把流的关闭交给JVM

> ```java
> try(资源对象的声明1;资源对象的声明2){
> 
> }catch(..){
> ...
> }
> ```

```java
package tech.aistar.day13;

import java.io.*;

/**
 * 本类用来演示: 语法糖 - JVM去关闭流
 *
 * @author: success
 * @date: 2021/8/5 2:39 下午
 */
@SuppressWarnings("all")
public class StreamAutoCloseDemo {
    public static void main(String[] args) {
        //自动关闭这些资源
        try(BufferedInputStream in = new BufferedInputStream(new FileInputStream("src/tech/aistar/day13/fast.gif"));
            BufferedOutputStream out = new BufferedOutputStream(new FileOutputStream("src/tech/aistar/day13/fasts.gif"))){
            //自定义一个byte[]
            byte[] buf = new byte[3*1024];

            int len = -1;

            while(true){
                len = in.read(buf);
                if(len==-1)
                    break;
                out.write(buf,0,len);
            }
            System.out.println("文件拷贝成功!");

        } catch (FileNotFoundException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

# ObjectInputStream/ObjectOutputStream

> 操作对象类型的字节输入流和字节输出流,拓展流,必须要以节点流作为支撑
>
> `构建一个能够读写对象类型的文件字节输入流/输出流`
>
> ```java
> ObjectInputStream in = new ObjectInputStream(new FileInputStream("path"))
> ```

`构建一个能够读写对象类型的并且带缓冲功能的文件字节输入流`

```java
ObjectInputStream in = new ObjectInputStream(new BufferedInputStream(new FileInputStream("path")));
```

利用"装饰器"思想,我们构建出功能更加强大的流,但是又不会对原来的流的结构产生任何影响.

## 对象的序列化

> 也叫做 - 数据的持久化的过程. - 把内存中的java对象存储到磁盘的文件的过程.
>
> 注意点:
>
> 1. 如果保存的是单个对象,这个对象的实体类必须要实现java.io.Serializable序列化接口
>
> 如果没有实现这个接口,进行序列化的时候,抛出一个java.io.NotSerializableException不可被序列化的异常
>
> 2. 保存的是数组/集合,那么集合中的每个对象对应的实体类也是需要实现这个序列化的接口
>
> 关于追加写入,设置FileOutputStream(String path,boolean append);//append设置true,但是不支持分批次读取.

## 对象的反序列化

> java对象已经存储到磁盘的文件中,希望再从文件中将这些对象数据加载到内存中[映射到java对象上].



# CURD操作①

> 实体类 - 接口 - 实现类 - 单元测试

> 操作文件中的数据
>
> - tech.aistar.day13.prj
>   - entity - Phone.java[id,name,price]
>   - dao - data access object [IPhoneDao.java]数据对象访问 - 数据持久层 - 和文件进行打交道的一层 - 接口
>     - impl - 接口的实现类PhoneDaoImpl.java
>   - test - 单元测试

## 磁盘IO/网络IO

  `IO`以不同的维度划分，可以被分为多种类型，比如可以从工作层面划分成磁盘`IO`（本地`IO`）和网络`IO`：

- 磁盘`IO`：指计算机本地的输入输出，从本地读取一张图片、一段音频、一个视频载入内存，这都可以被称为是磁盘`IO`。
- 网络`IO`：指计算机网络层的输入输出，比如请求/响应、下载/上传等，都能够被称为网络`IO`。

也可以从工作模式上划分，例如常听的`BIO、NIO、AIO`，还可以从工作性质上分为阻塞式`IO`与非阻塞式`IO`，亦或从多线程角度也可被分为同步`IO`与异步`IO`。

## 流程

一般程序的IO操作都在用户态，但是IO有些操作需要内核态中运行，这对操作系统是很危险的，所以操作系统提供了两个read（）、write（）两个函数供用户态使用。操作系统是不会百分百将这些功能开放给用户的，因为这样很危险。IO的工作流程：

![](https://s3.bmp.ovh/imgs/2023/05/25/297be5a49331d522.png)

- ① 首先肯定是在本地存储中或者网络的网卡上获取到数据，然后调用read（）函数。
- ② 然后从用户态的数据读取到内核态
- ③ 内核态读取完数据之后向CPU发送一个中断请求，通知CPU对数据进行一个处理
- ④ 然后CPU将内核区的数据复制到（写入到）用户态中，也就是本地存储中或者网络的网卡上的缓冲区。
- ⑤ 全部写入到缓冲区后，程序就可以对数据进行一个处理。

在上述`IO`工作过程中，其实大体可分为两部分：**准备阶段和复制阶段**，准备阶段是指数据从网络网卡或本地存储器读取到内核的过程，而复制阶段则是将内核缓冲区中的数据拷贝至用户态的进程缓冲区。常听的`BIO、NIO、AIO`之间的区别，就在于这两个过程中的操作是同步还是异步的，是阻塞还是非阻塞的。

## IO模型

### BIO

**同步阻塞IO（Blocking-IO），在准备阶段和复制阶段会一直阻塞。**

![1685013087483](C:\Users\qinfeng\AppData\Roaming\Typora\typora-user-images\1685013087483.png)

**优点：**开发简单，在阻塞期间，用户线程基本不会去占用CPU。

**缺点：**当有多个请求过来时，内核态就会多一条线程去执行，当高并发情况下，一个线程占用1M，线程资源是有限的，一旦超了，可能会导致系统的崩溃，而且频繁的上下文切换会导致效率进一步下降。

![](https://s3.bmp.ovh/imgs/2023/05/25/765ccc7672c18a3a.png)

> 拓展：但在`Java`常用的`Tomcat`服务器中，`Tomcat7.x`版本以下默认的`IO`类型也是`BIO`，但似乎并未碰到过：并发请求创建大量线程导致系统崩溃的情况出现呢？这是由于`Tomcat`中对`BIO`模型稍微进行了优化，通过线程池做了限制：
> 在`Tomcat`中，存在一个处理请求的线程池，该线程池声明了核心线程数以及最大线程数，当并发请求数超出配置的最大线程数时，会将客户端的请求加入请求队列中等待，防止并发过高造成创建大量线程，从而引发系统崩溃。

![](https://s3.bmp.ovh/imgs/2023/05/25/543ea085b1bd9ba3.png)

### NIO

**应用程序的线程需要不断地进行IO系统调用，轮询数据是否已经准备好，如果没有准备好，就继续轮询，直到完成IO系统调用为止。**

![](https://s3.bmp.ovh/imgs/2023/05/25/1112abe346eac1c1.png)

**优点：**每次发起的IO系统调用，在内核等待数据过程中可以立即返回。用户线程不会阻塞，实时性较好。