---
title: Distributed System - Introduction
date: 2020-02-01 12:00:15
tags: distributed system
categories: STUDY
---
Introduction to Distributed System

A Distributed System is a system of several <u> processes </u>, running on <u>different computers</u>, communicating with each other through the <u>network</u>, and are sharing a <u>state</u> or are working together to achieve a <u>common goal</u>.

<!--more-->

### Problems of Centralized System
- Performance and Storage - limited vertical scalability
- Single Point of failure - loss of money and trust
- High latency - poor user experience
- Security and privacy

### Terminology
- **node**: a process running on a dedicated machine
- **cluster**: collection of computers/nodes connected to each other; the nodes in a cluster are working on the same task and typically are running the same code.

### Apache Zookeeper
High Performance Distributed System Coordination Service.

-  Is a distributed system itself that provides us high availability and reliability
-  Typically runs in a cluster of an odd numver of nodes, higher than 3
-  Uses redundancy to allow failures and stay functional

### Znodes' Properties
Zookeeper provides us with a very familiar and easy software abstraction and data model that looks a lot like a <u>tree</u> and is very similar to a <u>file system</u>. Each element in that tree or virtual file system is called a <font color=red>Znode</font>.

![](https://raw.githubusercontent.com/Annashuo/hello-world/master/znode.png)
#### Znode - Hybrid between a <u>file</u> and a <u>directory</u>
- Znodes can store any data inside (like a file)
- Anodes can have children znodes (like a directory)

#### Znode Types
- Persistent - persists between sessions
- Ephemeral - is deleted when the session ends

### Simply Use Zookeeper 

#### Install and Configure
```
download from https://mirrors.tuna.tsinghua.edu.cn/apache/zookeeper/
tar -zxvf apache-zookeeper-3.5.6-bin.tar.gz
cd apache-zookeeper-3.5.6-bin/
scp conf/zoo_sample.cfg conf/zoo.cfg  # .cgf is configure file
mkdir logs
cd bin
./zkServer.sh start		# ./zkServer.sh finds more commands
```

#### Simply Use
```
./zkCli.sh
help	# find more commands
ls /
create /parent "some parent data"
ls /
ls /parent
create /parent/child "some child data"
ls /
get /parent
get /parent/child
stat /parent
stat /parent/child
deleteall /parent	# delete parent and child 
```

