---
title: File类
date: 2021-08-05 20:11:22
categories: java
tags: java
---

# File

> java.io.File[C] - File包含文件或者文件夹[目录directory]
>
> 作用:它是用来操作File本身的**元**信息的[比如支持File的创建,删除,获取File的名称,获取File的路径等信息]
>
> 它并不支持文件里面的内容的读写操作[文件内中的内容的读写操作是交给IO流去实现]
>
> 支持本地File以及远程File的操作的.
>
> File对象 - Java程序将磁盘上的文件加载内存中然后映射到File对象上 - File是内存中的磁盘上的映射.

## 构造方法

> File(String path);//利用一个路径来构建一个File实例.这个路径可以是文件的路径,也可以是文件夹的路径
>
> 路径分成相对路径和绝对路径.
>
> 绝对路径 - windows操作系统是以盘符号开头的路径,Linux/mac以/开头的路径
>
> 相对路径 - 不是以盘符或者/开头的路径

## 常用方法

> 1. boolean createNewFile();//创建一个文件,该方法是要抓取异常的.
> 2. String getAbsolutePath();//获取该File的绝对路径
> 3. String getName();//获取File的名称
> 4. String getParent();//获取该File的父目录的绝对路径
> 5. boolean exists();//判断File是否存在
> 6. boolean delete();//删除文件或者文件夹
>
> 6-1. 文件是否可以直接被删除的
>
> 6-2. 如果是空目录 - 直接删除的
>
> 6-2. **如果是非空目录 - 将会删除失败 - 递归算法来实现的.**
>
> 7. boolean mkdir();//创建单层次的目录,一次只能创建一个目录
> 8. boolean mkdirs();//既可以创建单层次的目录,又可以同时创建多个不存在的目录.
> 9. boolean isFile();//判断File实例是否为文件的实例
> 10. boolean isDirectory();//判断file实例是否为文件夹的实例
> 11. String[] list();//返回的是File的名称,返回的是传入的路径下的第一层的内容.
> 12. **File[] listFiles();//返回的第一层的File实例**
> 13. File[] listFiles(FilenameFilter filter);//找到指定后缀/前缀/满足条件的这么一个File实例



## 练习

> 1. 传入一个目录,输出这个目录下的所有的内容.
>
> 如果是文件 - 直接输出名称
>
> 如果是文件夹 - 输出绝对路径

## 作业

> 删除一个非空目录