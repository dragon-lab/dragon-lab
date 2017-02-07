---
title: Linux系统开发学习
date: 2017-02-03 11:51:48
tags:
    - Linux
---

## 工具学习

### 环境搭建

centos 7 下安装开发环境，运行下面的命令

```bash
$ yum groupinstall -y "Development Tools"
```

### GDB

编译程序加上`-g`选项，这样编译出的可执行程序中包含调试信息，否则之后GDB无法载入可执行文件中对应的代码信息。

```bash
$ gcc -g test.c -o test
```

启动gdb进行调试

```bash
$ gdb test
GNU gdb (GDB) Red Hat Enterprise Linux 7.6.1-94.el7
Copyright (C) 2013 Free Software Foundation, Inc.
License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.  Type "show copying"
and "show warranty" for details.
This GDB was configured as "x86_64-redhat-linux-gnu".
For bug reporting instructions, please see:
<http://www.gnu.org/software/gdb/bugs/>...
Reading symbols from /root/chapter03/test...done.
(gdb)
```

查看文件

```bash
(Gdb) l
```

设置断点

```bash
(gdb) b 6
Breakpoint 1 at 0x400585: file test.c, line 6.
```
需要注意的时，代码并没有运行第6行，而是在运行第6行之前就停止了。

查看断点情况

```bash
(gdb) info b
Num     Type           Disp Enb Address            What
1       breakpoint     keep y   0x0000000000400585 in main at test.c:6
```

运行代码

```bash
(gdb) r
Starting program: /root/chapter03/test

Breakpoint 1, main () at test.c:6
6    int i, n = 0;
Missing separate debuginfos , use: debuginfo-install glibc-2.17-157.el7_3.1.x86_64
```

查看变量值

```bash
(gdb) p n
$1 = 0
```

单步运行

```bash
(gdb) n
8    ret = sum(50); 
```

恢复程序运行

```bash
(gdb) c
Continuing.
The sum of 1-m is 50
The sum of 1-50 is 50
[Inferior 1 (process 21754) exited normally]
```

程序在运行完后退出，之后程序处于“停止状态”

### Makefile

## 链接库

在创建函数库之前，先准备下示例代码

```c hello.h
void hello(const char *name);
```

```c hello.c
#include <stdio.h>

void hello(const char *name) {
    printf("Hello %s!\n", name);
}
```

```c main.c
#include "hello.h"

int main() {
    hello("everyone");
    return 0;
}
```

将`hello.c`编译成`hello.o`

```bash
$ gcc -c hello.c

$ ls
hello.c  hello.h  hello.o  main.c
```

### 静态库

由`hello.o`创建静态库

静态库的命名规范是以lib为前缀，紧接着跟静态库名，扩展名为`.a`。例如，将创建的静态库名为`myhello`，则静态库文件名就是`libmyhello.a`。

使用`ar`命令创建静态库

```bash
$ ar crv libmyhello.a hello.o

$ ls
hello.c  hello.h  hello.o  libmyhello.a  main.c
```

在程序中使用静态库时，需要引入源程序中包含的公共函数。例如在`main.c`中`#include "hello.h"`。

注意：gcc会在静态库名前加上前缀lib，然后用追加扩展名.a，得到的静态库文件名来查找静态文件。

```bash
$ gcc -o hello main.c -L. -lmyhello

$ ls
hello  hello.c  hello.h  hello.o  libmyhello.a  main.c

$ ./hello
Hello everyone

$ gcc main.c libmyhello.a -o main
hello  hello.c  hello.h  hello.o  libmyhello.a  main  main.c

$ ./main
Hello everyone
```

删除静态库文件测试公共函数hello是否真的连接到目标文件hello中了

```bash
$ rm libmyhello.a

$ ls
hello  hello.c  hello.h  hello.o  main  main.c

$ ./hello
Hello everyone
```

程序正常运行，可见静态库中使用的函数已经连接到目标文件中了。

### 动态库

在创建动态链接库之前把前面生成的文件先删除掉，避免混淆。

```bash
$ ls
hello.c  hello.h  main.c
```

动态库文件名命名规范和静态库文件名命名规范累死，也是在动态库名增加前缀`lib`，但其文件扩展名为`.so`。例如将创建的动态库名为`myhello`，则动态库文件名就是`libmyhello.so`。

使用gcc创建动态库，输入一下命令

```bash
$ gcc hello.c -shared -fPIC -o libmyhello.so

$ ls
hello.c  hello.h  hello.o  libmyhello.so  main.c
```

在程序中使用动态库，使用下面的命令进行编译

```bash
$ gcc -o hello main.c -L. -lmyhello

$ ./hello
./hello: error while loading shared libraries: libmyhello.so: cannot open shared object file: No such file or directory
```

程序运行出错，提示找不到`libmyhello.so`。程序运行时，会在 `/usr/lib` 和 `/lib` 等目录中查找需要的动态库文件。 若找到则载入动态库，否则提示类似上述错误而终止程序运行。 将文件 `libmyhello.so` 复制到目录 `/usr/lib` 中再试试。

```bash
$ cp libmyhello.so /usr/lib

# 如果是x64系统则复制到 /usr/lib64 下
$ cp libmyhello.so /usr/lib64

$ ./hello
Hello everyone
```

程序运行成功，说明了动态库在程序运行时是需要的。

当静态库和动态库同时存在时，程序优先使用动态库。

## 内存管理

### malloc

From system

### ptmalloc

From glibc

### tcmalloc

From Google

### jemalloc

From Google

## 文件I/O

## 线程

## 进程

## 管道

## 信号

## 网络
