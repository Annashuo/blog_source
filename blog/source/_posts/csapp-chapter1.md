---
title: csapp-chapter1
date: 2017-03-03 17:21:53
tags: csapp
categories: STUDY
---

Chapter 1 REVIEW

<!--more-->
### 文件和编码
只由ASCII字符构成的文件成为*文本文件*，所有其他文件称为*二进制文件*
### 编译系统
编译系统(compilation system):预处理器cpp、编译器ccl、汇编器as、链接器ld

预处理阶段：源程序（.c）-> 被修改的源程序（.i） 完成读取头文件并直接插入程序文本的工作

编译阶段：被修改的源程序（.i）-> 汇编程序（.s）

汇编阶段：汇编程序（.s）-> 可重定位的目标程序（.o）.o文件是二进制文件

链接阶段：可重定位的目标程序（.o）-> 可执行目标程序（二进制）负责多个.o文件的合并

shell是一个命令行解释器，接受命令行或一个可执行目标程序的名字，加载并运行这个文件
### 系统硬件组成
- 总线：传送字，字长4字节（32位）或8字节（64位）
- I/O设备： 每个I/O设备通过控制器或适配器与总线相连
- 主存DRAM
- 处理器CPU

### 存储层析结构
![memory hierarchy](https://raw.githubusercontent.com/Annashuo/hello-world/master/memory_hierarchy.png)
### 进程和线程
- 一个系统可以同时运行多个进程，进程并发运行，进程的指令交错执行，成为上下文切换
- 一个进程由多个线程组成，线程共享同样的代码和全局数据

### 虚拟存储器
每个进程都有自己的虚拟存储地址空间

![virtual memory address](https://raw.githubusercontent.com/Annashuo/hello-world/master/virtual_memory_address.png)
### 并发和并行
并发(concurrency)：同时具有多个活动的系统
并行(parallelism)：用并发使系统运行得更快

- 线程级并发

超线程(simultaneous nulti-threading):允许CPU执行多个控制流

- 指令级并行

流水线(pipelining)：将一条指令所需要的活动划分成不同的步骤
超标量(superscalar)：处理器达到比一个周期一条指令更快的执行效率

- 单指令、多数据并行

SIMD(simple instruction multiple data)：一条指令可以并行执行的操作

### 抽象
文件时I/O的抽象，虚拟存储器是对程序存储器的抽象，进程对一个正在运行的程序的抽象，虚拟机是对整个计算机的抽象
