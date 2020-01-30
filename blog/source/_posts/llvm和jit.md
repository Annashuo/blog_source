---
title: llvm和jit
date: 2017-08-24 17:09:16
tags: llvm 
categories: STUDY
---
此文介绍编译器，llvm和jit

<!--more-->
### 传统三段式编译器

![传统编译器](https://github.com/Annashuo/hello-world/blob/master/%E4%BC%A0%E7%BB%9F%E7%BC%96%E8%AF%91%E5%99%A8.png?raw=true)

详细版：

![详细版传统编译器](https://github.com/Annashuo/hello-world/blob/master/%E8%AF%A6%E7%BB%86%E7%89%88%E4%BC%A0%E7%BB%9F%E7%BC%96%E8%AF%91%E5%99%A8.png?raw=true)

- 前端解析源代码，检查错误，并构建语言特定的抽象语法树(AST)
- 优化器负责进行各种转换，尝试改进代码的运行时间，例如消除冗余计算
- 后端将代码映射到目标指令集上，包括指令选择，寄存器分配和指令调度

### LLVM三阶段设计

![](https://github.com/Annashuo/hello-world/blob/master/LLVM三阶段.png?raw=true)

任何一种前端，只要生成LLVM IR文件，都可以复用LLVM的优化器和后端。默认的LLVM的c语言前端是clang。
### LLVM几种格式的文件转换

![LLVM文件转换](https://github.com/Annashuo/hello-world/blob/master/LLVM文件转换.png?raw=true)

- a.c：c语言源码
- a.bc：llvm字节码二进制表示
- a.ll：llvm字节码文本形式
- a.s：机器汇编码
- a.out：可执行的二进制文件

**.bc 和 .ll文件就是LLVM IR文件，两者是同一内容的不同表示方式。**
#### c源码，IR的比较

add.c

	unsigned add(unsigned a, unsigned b){
		return a + b;
	}

clang编译不优化

	编译命令：clang -S -emit-llvm add.c -o add.ll
LLVM IR表示
```
	define i32 @add(i32 %a, i32 %b) #0 {
	entry:
  		%a.addr = alloca i32, align 4
  		%b.addr = alloca i32, align 4
  		store i32 %a, i32* %a.addr, align 4
  		store i32 %b, i32* %b.addr, align 4
  		%0 = load i32, i32* %a.addr, align 4
  		%1 = load i32, i32* %b.addr, align 4
  		%add = add i32 %0, %1
  		ret i32 %add
	}
```
clang编译O3优化

	编译命令：clang -O3 -S -emit-llvm add.c -o add.ll
LLVM IR表示：
```
	define i32 @add(i32 %a, i32 %b) #0 {
	entry:
  		%add = add i32 %b, %a
  		ret i32 %add
	}
```
除此之外，llvm支持O4优化，即多个.bc文件进行link的时候进行优化。而且llvm支持在优化器加入自定义的pass来优化字节码，从而根据具体场景生成更优的机器码。这些都是gcc不具备的功能。

### 动态编译与即时编译器

#### 静态编译和动态编译

- 静态编译

  ![](https://github.com/Annashuo/hello-world/blob/master/静态编译.png?raw=true)

  逐行编译执行。
- 动态编译

  ![](https://github.com/Annashuo/hello-world/blob/master/动态编译.png?raw=true)

  预编译至字节码格式，即时编译器根据执行的过程的信息一次性编译成最优的二进制机器码执行。
  
#### 即时编译器JIT

JIT最早出现于java中，现以JIT 编译器在 JVM 中的运作原理为主介绍JIT，在llvm中过程类似。

![](https://github.com/Annashuo/hello-world/blob/master/jit.png?raw=true)

字节码的执行模式一般有三种：

- 完全采用解释器模式执行
- 完全采用即时编译器模式执行
- 采用解释器+即时编译器混合模式执行

完全采用即时编译器并不是一种高效的做法。即时编译器的开销大，如果编译的代码仅仅只执行一次，采用解释器逐行进行解释执行的效率会比采用即时编译器一次全部编译后执行更高。但如果一段代码被多次调用，即时编译器会做类型特化。比如重复调用add(x,y)函数，解释器解释执行时需要判断x和y的类型，即使实际代码中x和y始终是int型，解释器也不能忽略这些操作。但是即时编译器可以根据调用时的情况，将函数编译成add_int(x:int, y:int)，减少了类型判断的操作。

解释器+即时编译器混合模式本质是一种静态编译，但是解释器在逐行执行时，若发现某段代码是要求即时编译的，则启动即时编译器编译这段代码，然后嵌入到内存中，在逐行执行的过程中，会根据地址到内存中取出即时编译器编译的结果，当成一条指令运行。

LLVM中采用解释器+即时编译器混合模式。LLVM3.6以上执行引擎库称为MC库，其中MCJIT基于核心MC库。LLVM默认使用MC库解释执行字节码，但开发人员可以通过c或c++的接口函数编写和修改LLVM的IR代码，定义某一部分代码的执行引擎为MCJIT，从而调用JIT执行该部分代码。

### LLVM IR

LLVM支持c++和C api编写和修改LLVM IR文件。通过这两种api编写的LLVM代码，不需要再经过编译器前端的解析就可以直接生成LLVM IR文件。因为LLVM IR接近于汇编语言的代码逻辑，所以写起来会比较繁琐。

#### LLVM IR框架

![](https://github.com/Annashuo/hello-world/blob/master/api.png?raw=true)

module是多个function的集合，它对应一个执行引擎engine，engine可以是纯MC类型的也可以是MCJIT类型的，这决定了这个module采用何种执行方式。Target指明了这个engine是执行在什么目标机器上。

每个function内部由多个basic block构成。譬如一个条件语句包含三个basic block：判断，iftrue，iffalse。判断块负责执行判断指令，iftrue块执行条件成立时的代码，iffalse块执行条件不成立时的代码。

每一个basic block包含多条指令，这里的指令主要是load，store，add，minus，leal等类似于汇编逻辑的指令。

LLVM C API随着LLVM版本一直在变，目前还没有稳定下来，目前已知LLVM3.6版本和3.7版本API不可通用，3.7和4.0以上版本不可通用。
