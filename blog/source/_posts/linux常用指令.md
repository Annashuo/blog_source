---
title: linux常用指令
date: 2017-08-24 13:45:31
tags: linux
categories: STUDY
---
查看端口号占用   `lsof -i:5432`

查看io   `iostat -x -k 1`

修改用户usr1的默认目录  `usermod -u uid -d newdirectory -m usr1`

拷贝文件夹  `cp -R 旧 新`

软连接 `ln -s 有内容的  没有内容的`

去除 `rm -r -i 没有内容的`

kill掉某个进程  `kill -s 9 pid`

查看用户uid  `id username`

对比不同 `diff -Unarq  文件夹1  文件夹2 >diff.log`

查找文件夹下含有字符串的文件 `grep -rn "字符串" *`