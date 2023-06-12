---
title: Java发展史和基本数据类型
date: 2021-07-31 08:44:50
categories: java
tags: java
---

# Java能做什么

> 1. 传统的项目 - 简单的,公司内部使用的,不会存在高并发的问题.
>
> 教务管理系统,OA自动化办公系统,CRM客户关系管理系统,ERP企业资源计划系统
>
> 2. B2C - 典型的电子商务模式,涉及到高并发的业务
>
> 企业2客户 - 企业卖自己的服务或者产品
>
> 3. C2B2C - 新型的模式 - 淘宝
>
> 平台 - 对双方进行监管
>
> 卖家
>
> 买家
>
> 4. 大数据 - 一定是对业务非常熟悉[数据分析,数据非常敏感]

# Java的历史

> 1. 原先是属于SUN公司[Stanford University Network 斯坦福网络大学]
>
> Sun Microsystem - 太阳微电子公司
>
> 2. 后来在2010年10月份被Oracle公司[出名的产品-oracle数据库 - NO.1]
>
> 74亿美金收购 - www.oracle.com
>
> 3. 前身Oak语言[橡树],印度尼西亚的**爪哇**岛[咖啡豆 - javabean]
> 4. 诞生于1995年5月23日,96年正式推出jdk1.0版本.
> 5. 商用的jdk版本 - **jdk8.x** , jdk11.x ,jdk16.x
> 6. java之父 - james gosling 詹姆斯 高斯林



# Java支持的三个平台

> 原先的名称j2se , j2ee, j2me
>
> 1. JavaSE - Java Standard Editional - Java标准版
>
> 适合做**桌面应用程序[维护比较麻烦,服务器端如果更新,客户端需要更新]**,包含了java里面的基础的语法,核心
>
> 2. JavaEE - 2018年 - JakartaEE - 雅佳达EE - Java Enterprise Editional - Java企业版
>
> 是一套技术的规范的体系.不是一门具体的技术.里面包含了13种核心技术
>
> 比如jdbc,servlet等遵守javaee的规范.现在javaee里面的核心技术已经使用不多了
>
> 现在普遍都在使用spring全家桶中的技术.
>
> 适合做web应用程序[web网站]
>
> 3. JavaME - Java Micro Editional - Java微型版,适合做手持设备 - "不用"



# 检测一下jdk的环境变量是否配置成功

> 打开终端,输入cmd - 输入java -version
>
> ```java
> admindeMacBook-Pro:Desktop admin$ java -version
> java version "1.8.0_66"
> Java(TM) SE Runtime Environment (build 1.8.0_66-b17)
> Java HotSpot(TM) 64-Bit Server VM (build 25.66-b17, mixed mode)
> ```



## 环境变量的配置

> Java.zip - 解压缩 - 比如放在D盘

> 所有的软件不要放在中文目录下,不要放在带有特殊符号的目录下[programe files(x86)]
>
> 尽量不要放在C盘[权限的问题,拒绝你的访问的]

> 桌面 - 右击计算机 - 属性 - 高级系统设置 - 环境变量(N) - 定位到系统变量
>
> 1. 新建
>
> 变量名:JAVA_HOME
>
> 变量值:jdk1.8.0_66的绝对路径
>
> 确定
>
> 2. 找到内置的变量名Path - 编辑 - 新建
>
> %JAVA_HOME%\bin
>
> 3. 关闭刚所有打开的设置窗口,重新打开终端[黑窗口]
>
> win+r - cmd ->输入  java -version



# Java语言特点

> 1. 简单
> 2. 可移植性
> 3. 面向对象 - 核心
> 4. 与平台无关性[跨平台性,跨操作系统平台]
> 5. 安全性 - 本身是属于强类型的语言.在编译期间就要确定数据的类型.

# JDK和JRE和JVM

> **三者之间的区别** -  简单题 - 笔试
>
> 1. JDK - Java Development Kit - Java开发工具包,针对于开发者的.包含了很多开发工具
>
> 比如java.exe , javac.exe, javadoc.exe , jar.exe等
>
> **JDK中是包含JRE的**
>
> 2. JRE - Java Runtime Enviroment - Java运行时环境,包含了很多核心的内库.
>
> 针对于使用java语言开发出来软件的用户.如果一个用户仅仅想运行一个java程序的话
>
> 那么计算机中只需要安装jre即可.
>
> **JRE包含JVM**
>
> 3. JVM - Java Virtual Machine - Java虚拟机 - 后期重点详细介绍jvm[笔试的大头 - 内存模型]
>
> jvm本身就是用C语言编写的 - jvm的源码通读一遍.
>
> **java程序并不是直接在计算机中进行跑的.而是在虚拟机上执行的.虚拟机是安装在操作系统上的.**
>
> **注意的是java语言是跨平台的[使用java语言编写的程序可以运行在不同的os上],但是jvm不是跨平台的**
>
> **不同的操作系统需要安装不同的jvm**



Javac.exe：编译java程序（编译器）
java.exe:执行编译好的.class文件（解释器）
Javadoc.exe:用于生产Java文档（Java文档工具）
Jdb.exe(全程：jdk debug)：调试Java程序（调试器）
Javaprof.exe:用于剖析程序（剖析工具）

**JVM：JVM 是 Java Virtual Machine（Java 虚拟机）的缩写，它是整个 java**

**实现跨平台的最核心的部分，所有的 java 程序会首先被编译为.class 的类文**

**件，这种类文件可以在虚拟机上执行，也就是说 class 并不直接与机器的操**

**作系统相对应，而是经过虚拟机间接与操作系统交互，由虚拟机将程序解释**

**给本地系统执行。JVM 是 Java 平台的基础，和实际的机器一样，它也有自**

**己的指令集，并且在运行时操作不同的内存区域。 JVM 通过抽象操作系统**

**和 CPU 结构，提供了一种与平台无关的代码执行方法，即与特殊的实现方**

**法、主机硬件、主机操作系统无关。JVM 的主要工作是解释自己的指令集（即**

**字节码）到 CPU 的指令集或对应的系统调用，保护用户免被恶意程序骚**

**扰。 JVM 对上层的 Java 源文件是不关心的**

![](https://img-blog.csdnimg.cn/20201027233843394.PNG?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3RpYW5feWFfamlhbl9rZQ==,size_16,color_FFFFFF,t_70)

**结构和操作系统有关，是运行 Java 程序必不可少的（除非用其他一些编译**

**环境编译成.exe 可执行文件……），JRE 的地位就象一台 PC 机一样，我们写**

**好的 Win32 应用程序需要操作系统帮我们运行，同样的，我们编写的 Java**

**程序也必须要 JRE 才能运行。**

**JDK：JDK 是 java development kit（java 开发工具包）的缩写。每个学 java**

**的人都会先在机器上装一个 JDK，那 让我们看一下 JDK 的安装目录。在目**

**录下面有六个文件夹、一个 src 类库源码压缩包、和其他几个声明文件。其**

**中，真正在运行 java 时起作用的是以下四个文件夹：bin、include、lib、jre。**

**现在我们可以看出这样一个关系，JDK 包含 JRE，而 JRE 包含 JVM。**

**bin : 最主要的是编译器(javac.exe)**

**include : java 和 JVM 交互用的头文件**

**lib：类库**

**jre:java 运行环境**

**（注意：这里的 bin、lib 文件夹和 jre 里的 bin、lib 是不同的）总的来说 JDK**

**是用于 java 程序的开发,而 jre 则是只能运行 class 而没有编译的功能。**

**eclipse、idea 等其他 IDE 有自己的编译器而不是用 JDK bin 目录中自带的，**

**所以在安装时你会发现他们只要求你选 jre 路径就 ok 了。**

**JDK,JRE,JVM 三者关系概括如下：**

**jdk 是 JAVA 程序开发时用的开发工具包，其内部也有 JRE 运行环境 JRE。**

**JRE 是 JAVA 程序运行时需要的运行环境，就是说如果你光是运行 JAVA 程**

**序而不是去搞开发的话，只安装 JRE 就能运行已经存在的 JAVA 程序了。JDk、**



**JRE 内部都包含 JAVA 虚拟机 JVM，JAVA 虚拟机内部包含许多应用程序的**

**类的解释器和类加载器等等。**





# HelloWorld程序

> java的基础单元是类class
>
> **类的命名** - 标识符的命名
>
> 1. 只能由字母,数字,下划线,$符号组成,但是不能以数字开头
> 2. 推荐使用"大驼峰"命名规则,单词首字母必须要大写.其余单词小写
> 3. 不能是jdk中内置的对象类型[String,System]
> 4. 不能是java中的一些关键字,关键字[java语言赋予了这些单词具备一定的语法含义]
>
> byte,short,int,long,float,double,char,boolean,if,for,else,while,break,continue,class,
>
> public,private,protected等
>
> 5. 不能是java中的保留关键字 - goto,const
> 6. 不推荐使用中文,防止出现乱码问题.
> 7. 不推荐使用中文的拼音
> 8. 推荐见名知意
> 9. 不能是以下三个字面量 - true,false,null



## 代码

> 1. 在D盘根目下新建一个文件HelloWorld.java
>
> java的源文件就是以.java为后缀的文件
>
> 2. 记事本打开HelloWorld.java文件
>
> ```java
> //编程语言中,只要出现了符号,一定必须都是英文输入法下的
> //整体的结构
> //java是以类作为基础的单位的 - 定义类
> //关键是class
> //类的名称要和文件的名称高度保持一致
> //{} - block - 块 - 代码块
> public class HelloWorld{//1. 类
>     //规范 - 缩进四个空格
>     //定义一个main方法 - 程序的"大门"
>     //运行该程序的时候,jvm就会自动寻找main方法,然后进入到这个main
>     //方法中去执行
>     //今天先记住语法
>     public static void main(String[] args){//2. 方法
>        //方法体,现阶段代码一定是放在方法体内部的.
>        //jdk中提供了内置的对象[拥有一些功能],提供好了,开发者就可以直接使用
>        //java.lang.System类 - 向控制台输出一句话
>        System.out.println("HelloWorld");//3. 具体的程序
>     }
> }
> ```

> 3. java源代码是不能够直接执行的.因为计算机是不识别java语言程序的.
>
> 需要对.java为后缀的源文件进行一个**编译操作**.如果一旦语法发生了问题,都会导致编译失败.
>
> 假设HelloWorld.java文件是放在D盘根目录下,需要打开终端,然后通过dos命令进入到D盘
>
> ```cmd
> C:/User/admin>d:
> D:/>javac HelloWorld.java
> ```
>
> `效果,javac.exe工具其实就是在调用jdk中的编译器,作用:就是.java源文件编译成.class字节码文件`
>
> 4. 思考如何运行一个java程序呢?
>
> 利用java.exe
>
> ```java
> D:/>java HelloWorld
> ```

## 过程

> 1. 编写.java源代码
> 2. javac.exe - 编译
> 3. java.exe - 执行

## Java程序的执行的原理

![](D:/%E7%AE%A1%E7%88%B8%E7%88%B8%E7%9A%84%E8%B5%84%E6%96%99/cnm/j03s_tech/note/imgs/jvm.png)  

```java
.java源文件 -> 经过jdk中的编译器,成为.class字节码文件 -> 经过jvm中的解释器[进行逐行翻译(解释)]
-> 机器能够识别的符号

java属于解释型的语言,"JVM把它YY成以.class字节码为指令的CPU"

编译型语言 - C语言,将这个语言编写的程序直接编译成计算机能够执行的程序.比如C语言写的代码可以直接编译成可执行文件 xx.exe

C语言执行的性能高于java语言(每次执行都会经过jvm解释器进行解释)
```

# 集成开发环境

> 1. eclipse[日食] - 免费的,IBM公司的产品[收购SUN公司的] 
> 2. idea - 最好的,收费的
>
> d:/aistar/j03s_student

# Java中的包-package

> 1. 包是用来管理类的.包的本质就是一个文件夹.包对于类的作用相当于文件夹对于文件的作用.
> 2. 包的命名规则
>
> 2-1. 推荐全部采用小写字母,如果出现多个单词,请你用.隔开,.隔开的每个部分都是一个目录
>
> 2-2. 包的命名推荐采用公司的域名倒置[唯一性] + 项目代号
>
> ​       com.baidu.car   tech.aistar
>
> 2-3. 不用采用java或者javax开头 - 因为jdk中的内置的类和拓展的类都是在java或者javax包中.
>
> 3. 一旦创建了package,需要在类文件的**首行**采用**package关键字来声明包.**

# java中三种注释

> 1. 单行注释 //   ctrl+/  添加/取消注释
> 2. 多行注释
>
> ```java
> /*
>     * 
>     *  多行注释的内容 - 注释的内容是不会经过jdk的编译器的
>  *
>  */
> ```
>
> 3. 文档注释
>
>    > 可以生成文档树
>    >
>    > ```java
>    > /**
>    > * 放在类上面
>    > 
>    > * 放在方法上
>    > *
>    > */
>    > ```



# main方法细节

```java
public[公开的,公共的] static[静态的] - 都是属于后期学习OO中的修饰符
修饰符在使用的时候,之间是没有顺序的.
public static void main(String[] args){...}    //ok

static public void main(String[] args){...}   //ok

//jdk8中方法的参数支持可变长参数
public static void main(String... args){...}  //ok

//args - 我们传给虚拟机的参数
```



# 输出语句

> 1. System.out.println(); // 换行
>
> ```java
> System.out.print("\n");
> 
> ```
>
> 2. \n和\r
>
> ```java
> package tech.aistar.day01;
>          
> /**
>     * 本类用来演示: \n \r
>  *
>     * @author: success
>     * @date: 2021/7/15 3:18 下午
>  */
> public class NrDemo {
>     public static void main(String[] args) {
>         //相当于是System.out.println("hello");
>         System.out.print("hello\n");//输出之后不换行
>         System.out.println("world");
>          
>         // \n - 当输出完毕之后,光标停在下一行的起始位置 - 换行
>          
>         // \r - 当输出完毕之后,光标停在当前行的起始位置 回车
>                  
>         // \n\r 诞生的场景就是早期的打字机
>          
>         //外面的终端执行的结果是 - veyy    √
>                  
>         //伪终端显示的结果是 - ve
>         System.out.println("Loyy\rve");
>     }
> }
> 
> ```

# 特殊字符

> \ 是代表转义字符

> 1. \n - 当输出完毕之后,光标停在下一行的起始位置 - 换行
> 2. \r - 当输出完毕之后,光标停在当前行的起始位置 回车
> 3. \b - 退格
> 4. \t - 制表符,相当于tab键,默认是空4格
> 5. 双引号
>
> ```java
> \"
> 
> ```
>
> 6. 单引号
>
> ```java
> \'
> 
> ```
>
> 7. 单个\
>
> ```java
> \\
> 
> ```

```java
package tech.aistar.day01;

/**
 * 本类用来演示: 特殊字符
 *
 * @author: success
 * @date: 2021/7/15 3:35 下午
 */
public class SignDemo {
    public static void main(String[] args) {
        //\b - 退格
        System.out.println("xxx\byy");//xxyy

        //\t - 制表符,相当于tab键,默认是空4格
        System.out.println("hello\tworld");

        //输出 james:"success is good boy"

        //双引号
        System.out.println("james:\"success is good boy\"");

        // \ 转义含义
        //单个\
        System.out.println("D:\\temp\\news");

        //输出\\
        System.out.println("\\\\");

    }
}

```



# 变量

> **编程的核心就是从定义变量开始**
>
> java语言是一个**强类型的语言**[在编译期间必须要确定好数据的类型],javascript弱类型的语言
>
> 变量的本质 - "内存中某块区域的名称",编程的任务就是对数据进行crud+数据分析的操作.
>
> 但是前提是数据需要先进行存储[内存,磁盘(文件,db数据库) - JVM内存
>
> "JVM对字节码文件进行解释的前奏工作 - 把这个字节码文件加载到自己的内存中,java只能操作内存中的数据"
>
> java中是如何定义和存储简单的这些数据呢?
>
> **就是通过数据类型以及变量来定义和存储的.**



## 数据类型

> java中的数据类型有两大块
>
> 1. **基本数据类型(8种)** - 编程思想中将void归纳为第9种
>
> 数据类型决定了存储数据的这块空间的大小,并且这个大小一旦确定了,将不能够改变了.
>
> 2. 对象类型(无数种)
>
> 2-1. 内置对象类型 - System,String - Jdk中提供的类
>
> 2-2. 自定义对象类型 - 自己定义的那些类HelloWorld,SignDemo

### 八种基本数据类型

|         | 名称         | 大小           | 范围             | 默认值       |
| ------- | ------------ | -------------- | ---------------- | ------------ |
| byte    | 字节         | 占1个字节8bit  | -128~127         | 0            |
| short   | 短整型       | 占2个字节16bit | -2^15~2^15-1     | 0            |
| int     | 整型         | 占4个字节32bit | -2^31~2^31-1     | 0            |
| long    | 长整型       | 占8个字节64bit | -2^63~2^63-1     | 0            |
| float   | 单精度浮点数 | 占4个字节32bit | +-(3.4*10^38)    | 0.0          |
| double  | 双精度浮点数 | 占8个字节64bit | +-(1.798*10^308) | 0.0          |
| char    | 字符         | 占2个字节16bit | 0~65535          | '\u0000'空格 |
| boolean | 布尔类型     | 占1个字节8bit  | true/false       | false        |

## 变量的定义

> 变量的命名的规范请你参考类的命名规范,除了类的命名采用的"大驼峰",而变量命名采用的是"小驼峰"
>
> "小驼峰" - 首字母小写,其余每个单词的首字母大写.比如**studentAge**

`语法`

```java
数据类型 变量名 [= 变量值];

```

## 剖析变量的背后

```java
package tech.aistar.day01;

/**
 * 本类用来演示: 变量入门
 *
 * @author: success
 * @date: 2021/7/15 3:43 下午
 */
public class VarDemo {
    public static void main(String[] args) {
        //数据类型 变量名 [= 变量值];
        byte b = 10;

        //代码的背后 - 埋了个"种子","发芽"

        //1. 数据肯定是存储在JVM内存中的
        //2. 定义在方法内部的变量 - 局部变量[JVM的栈区]
        //3. 局部变量的生命周期是伴随着方法(main)的调用的开始和结束

        //讲解的本质
        //流程
        //1. JVM加载VarDemo.class到内存
        //2. JVM找到main方法想要去执行里面的程序
        //3. 此处需要申请一块区域来保存数据10
        //   3-1. 区域的大小由前面的数据类型来决定的 - int类型 - 区域4个字节32bit的大小
        //   3-2. 栈里面的区域的大小一旦被确定了,就不能改变 - 肯定有的时候会有内存的浪费
        //   3-3. 虽然空间浪费了,但是读取的效率会提高 - 典型的"以空间换时间"

        //4. 只要是内存中的空间,这个空间必然会有一个地址,所以变量的存在是为了给这个区域取了个名字
        //   变量是为了方便用户来通过它访问到这个空间中存储的具体的那个值的

        //把整数10赋值给一个int类型的变量a
        int a = 10;

        System.out.println(a);

        // == 比较的就是"坑 - 区域"里面存储的数据
        System.out.println(b == a);//true
    }
}

```

# 数据类型的转换

> 常识规定
>
> a. java中看到一个整数,默认就是int类型
>
> b. java中看到一个小数,默认就是double类型
>
> c. 大的数据类型和小的数据类型进行计算的时候,计算得到的结果是偏向类型大的一方.

> 1. 自动转换
>
> 小的数据类型可以自动转换成大的数据类型.
>
> **byte->short->int->long**
>
> **char->int**
>
> **int->double**
>
> 2. 强制类型转换
>
> 大的数据类型转换成小的数据类型的时候,需要进行强制类型转换的操作
>
> ```java
> MaxType 变量1 = 值1;
> 
> MinType 变量2 = (MinType)变量1;
> 
> ```
>
> 3. 隐式转换
>
> ```java
> int->long
> 整数后面添加l/L
>      
> double->float
> 小数后面加上f/F
>      
> 推荐在double后面加上d/D - 提高语义
> 
> ```

































