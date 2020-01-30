---
title: llvm安装
date: 2017-08-24 13:48:58
tags: llvm
categories: STUDY
---
mac下安装xcode自带llvm

以下为在linux系统下的安装

以下操作目录均为/data/home/llvm

<!--more-->
### 安装autoconf和automake
```
下载autoconf-2.65.tar.bz2和automake-1.14.1.tar.gz
tar -jxvf autoconf-2.65.tar.bz2
tar -zxvf automake-1.14.1.tar.gz

cd autoconf-2.65
./configure; make; make install
cd ../automake-1.14.1
./configure; make; make install
	
```
### 安装gcc4.8以上版本
一般电脑都安装了gcc，具体安装过程不再此详述。
### 安装python2.7.5
安装python可以直接用yum或者下载安装包进行安装，过程再此也不再详述。
### 安装cmake
	下载cmake-3.8.2.tar.gz
	tar -zxvf cmake-3.8.2.tar.gz  
	cd cmake-3.8.2  
	./configure --prefix=/data/home/llvm/cmake  
	make  
	make install  
	添加环境变量export PATH=/data/home/llvm/cmake/bin:$PATH

#### 安装完成后
![cmake--version](https://github.com/Annashuo/hello-world/blob/master/cmake.png?raw=true)
#### 可能出现的一些坑：

1.  报错/lib64/libstdc++.so.6: version `GLIBCXX_3.4.21' not found：

解决方法: http://blog.csdn.net/rznice/article/details/51090966

2. 找不到sys/acl.h等文件

解决方法： sudo yum list libacl* 把列出来的所有包都安装上

3. 找不到xml/...等文件

解决方法： sudo yum list libxml* 把列出来的所有包都安装上

### 安装llvm和clang3.7.1

	下载：
	llvm-3.7.1.src.tar.xz
	cfe-3.7.1.src.tar.xz
	compiler-rt-3.7.1.src.tar.xz
	clang-tools-extra-3.7.1.src.tar.xz 
	libcxx-3.7.1.src.tar.xz
	
	tar -xvf llvm-3.7.1.src.tar.xz  
	tar -xvf cfe-3.7.1.src.tar.xz  
	tar -xvf compiler-rt-3.7.1.src.tar.xz  
	tar -xvf clang-tools-extra-3.7.1.src.tar.xz  
	tar -xvf libcxx-3.7.1.src.tar.xz 

	mv cfe-3.7.1.src clang  
	mv clang/ llvm-3.7.1.src/tools/

	mv clang-tools-extra-3.7.1.src extra  
	mv extra/ llvm-3.7.1.src/tools/clang/

	mv compiler-rt-3.7.1.src compiler-rt  
	mv compiler-rt llvm-3.7.1.src/projects/

	mkdir build-3.7.1
	cd build-3.7.1

	cmake -DCMAKE_BUILD_TYPE=Debug /data/home/llvm/llvm-3.7.1.src
	make -j32
	cmake -DCMAKE_INSTALL_PREFIX=/data/home/llvm/llvm37 -P cmake_install.cmake
	添加环境变量：
	export PATH=/data/home/llvm/llvm37/bin:$PATH
	export LD_LIBRARY_PATH=/data/home/llvm/llvm37/lib:$LD_LIBRARY_PATH
	
#### 安装完成后

![](https://github.com/Annashuo/hello-world/blob/master/lli.png?raw=true)

### 安装一些依赖库（根据gcc版本可选）

	cd libcxx-3.7.1.src/lib
	./buildit
	sudo cp ./libc++.so.1.0 /usr/lib
	cd /usr/lib
	sudo ln -sf libc++.so.1.0 usr/lib/libc++.so
	sudo ln -sf libc++.so.1.0 usr/lib/libc++.so.1
	cd ~
#### 安装完成后

![](https://github.com/Annashuo/hello-world/blob/master/libcpp.png?raw=true)

### 测试clang和llvm和gdb是否可用

#### test.c文件

    #include <stdio.h>  
    int main(){
    	printf("hello world\n");
    return 0;
    }
编译命令：

- c文件直接产生可执行文件(a.out)

	`clang test.c`
- c文件产生IR文件(.ll)

	`clang -S -emit-llvm -c test.c -o test.ll`
- c文件产生字节码文件(.bc)

	`clang -emit-llvm -c test.c -o test.bc`
- c文件生成汇编文件(.s)

	`clang -S test.c -o test.s`
- c文件生成目标文件(.o)

	`clang -c test.c -o test.o`
- 目标文件生成可执行文件

	`clang test.o -o test`
- 字节码文件生成cpp文件

	`llc -march=cpp test.bc`
- 字节码文件生成汇编文件(.s)

	`llc test.bc`

#### new_feature.cpp文件
	#include <iostream>
	#include <string>
	class myClass
	{
	public:
    	std::string s="Hello CPP\n";
	};
	int main(){
    	std::cout<<myClass().s;
	}
- 生成可执行a.out文件

	`clang++ --std=c++0x  new_feature.cpp`
#### gdb的使用
showdebug.c文件


	1   int compute_factorial(int n)
	2   {
	3       if (n <= 1)
	4           return 1;
	5
	6       int f = n;
	7       while (--n > 1)
	8           f \*= n;
	9       return f;
	10  }
	11
	12
	13  int main(int argc, char** argv)
	14  {
	15      if (argc < 2)
	16          return -1;
	17      char firstletter = argv[1][0];
	18      int result = compute_factorial(firstletter - '0');
	19
	20      // Returned result is clipped at 255...
	21      return result;
	22  }

gdb调试过程：

	$ clang -cc1 -O0 -g -emit-llvm showdebug.c
	$ gdb --quiet --args lli showdebug.ll 5
	Reading symbols from lli...done.
	(gdb) b showdebug.c:6
	No source file named showdebug.c.
	Make breakpoint pending on future shared library load? (y or [n]) y
	Breakpoint 1 (showdebug.c:6) pending.
	(gdb) r
	Starting program: lli -use-mcjit showdebug.ll 5
	[Thread debugging using libthread_db enabled]

	Breakpoint 1, compute_factorial (n=5) at showdebug.c:6
	6        int f = n;
	(gdb) p n
	$1 = 5
	(gdb) p f
	$2 = 0
	(gdb) n
	7        while (--n > 1)
	(gdb) p f
	$3 = 5
	(gdb) b showdebug.c:9
	Breakpoint 2 at 0x7ffff7ed404c: file showdebug.c, line 9.
	(gdb) c
	Continuing.

	Breakpoint 2, compute_factorial (n=1) at showdebug.c:9
	9        return f;
	(gdb) p f
	$4 = 120
	(gdb) bt
	#0  compute_factorial (n=1) at showdebug.c:9
	#1  0x00007ffff7ed40a9 in main (argc=2, argv=0x16677e0) at showdebug.c:18
	#2  0x3500000001652748 in ?? ()
	#3  0x00000000016677e0 in ?? ()
	#4  0x0000000000000002 in ?? ()
	#5  0x0000000000d953b3 in llvm::MCJIT::runFunction (this=0x16151f0, F=0x1603020, ArgValues=...) at /home/ebenders_test/llvm_svn_rw/lib/ExecutionEngine/MCJIT/MCJIT.cpp:161
	#6  0x0000000000dc8872 in llvm::ExecutionEngine::runFunctionAsMain (this=0x16151f0, Fn=0x1603020, argv=..., envp=0x7fffffffe040)
    at /home/ebenders_test/llvm_svn_rw/lib/ExecutionEngine/ExecutionEngine.cpp:397
	#7  0x000000000059c583 in main (argc=4, argv=0x7fffffffe018, envp=0x7fffffffe040) at /home/ebenders_test/llvm_svn_rw/tools/lli/lli.cpp:324
	(gdb) finish
	Run till exit from #0  compute_factorial (n=1) at showdebug.c:9
	0x00007ffff7ed40a9 in main (argc=2, argv=0x16677e0) at showdebug.c:18
	18       int result = compute_factorial(firstletter - '0');
	Value returned is $5 = 120
	(gdb) p result
	$6 = 23406408
	(gdb) n
	21       return result;
	(gdb) p result
	$7 = 120
	(gdb) c
	Continuing.

	Program exited with code 0170.
	(gdb)
可能出现的坑：

1. Reading symbols from lli...(no debugging symbols found)...done

	解决方法：

```
	退出gdb
	lli --version
```
![](https://github.com/Annashuo/hello-world/blob/master/lli.png?raw=true)

	如果里面没有一句DEBUG build with assertions.

	重装llvm，注意重装时要先make clean

2. Missing separate debuginfos, use: debuginfo-install ncurses-libs-5.9-13.20130511.el7.x86_64

	不影响gdb使用，忽略
3. [Inferior 1 (process 15958) exited with code 0170]

	不影响gdb使用，忽略
