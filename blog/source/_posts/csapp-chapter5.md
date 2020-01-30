---
title: csapp-chapter5
date: 2017-04-08 11:47:23
tags: csapp
categories: STUDY
---
chapter 5 REVIEW

### 程序优化
- 代码移动（code motion）

```
	//before
	for(i=0;i<s.length();i++)
	//after
	int size=s.length();
	for(i=0;i<size;i++)
```

- 减少过程调用
- 消除不必要的存储器引用
- 循环展开（loop unrolling）
- 多路并行

### 概念
- 指令级并行：同时对多条指令求值
- 延迟界限（latency bound）：一系列操作必须严格顺序执行
- 超标量（superscalar）：每个时钟周期执行多个操作，并且是乱序的（out-of-order）
- 分支预测（branch prediction）：处理器会猜测是否会选择分支，同时预测分支的目标地址
- 寄存器重名（register renaming）

