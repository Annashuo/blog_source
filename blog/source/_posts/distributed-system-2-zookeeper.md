---
title: Distributed System - Zookeeper
date: 2020-02-01 12:00:15
tags: distributed system
categories: STUDY
---

Introduction of Zookeeper

<!--more-->
### Zookeeper Threading Model
- Application's start code in the main method is executed on the main thread
- When Zookeeper object is created, two additional threads(IO thread and event thread) are created.

#### IO thread
- Handles all the network communication with Zookeeper servers
- Handles Zookeeper requests and responses
- Responds to pings
- Session Management
- Session Timeout
- ...

#### Event Thread
- Manages Zookeeper events
	- Connection 
	- Disconnection
- Custom znode Watchers and Triggers we subscribe to
- Event are executed in Event Thread in order

### Watchers and Triggers
- We cna register a watcher when we call the methods (eg. *getChildren()*, *getData()*, *exists()*). These are one-time trigger
- The watcher allows us to get a notification when a change happens
	- *getChildren(..., watcher)* - Get notified when the list of a znode's children changes
	- *exists(znodePath, whatcher)* - Get notified if a znode gets deleted or created
	- *getData(znodePath, whatcher)* - Get notified if a znode's data gets modified
- If we want to get future notifications, we need to register the watcher again

### The Herd Effect
- A large number of nodes waiting for an event
- When the event happens all nodes get notified and they all wake up
- Only one node can "succeed"
- Indicates bad design, can negatively impact the performance and can completely freeze the cluster

#### Bad Design
![](https://raw.githubusercontent.com/Annashuo/hello-world/master/zookeeper_bad.png)

1. All nodes watch leader, a leader dies
2. Zookeeper send request to all nodes: the zookeeper cluster will have to notify all znodes a *NodeDeleteEvent*
3. All nodes send request to Zookeeper: all nodes call *getChildren* method to get a new view of the Znode hierarchy from the zookeeper cluster
4. All nodes send request to Zookeeper: After the new leader is reelected, all the nodes are going to start watching the Znode of the leader

Since all nodes will send a lot of requests to zookeeper simultaneously, with a large number of nodes in a cluster, this can really overwhelmed Zookeeper.

![](https://raw.githubusercontent.com/Annashuo/hello-world/master/hert_effect.png)

#### Good Design
![](https://raw.githubusercontent.com/Annashuo/hello-world/master/zookeeper_good.png)

1. Each node is going to watch only Znode that comes right before it in the sequence of candidate Znodes
2. Zookeeper send request to one node: When leader dies, the only node that is going to be notified is its immediate successor
3. A node send request to zookeeper: this node call *getChildren* to make sure that it owns the Znode with the smallest sequence number and in that case it knows that it's the new leader

### Important Properties
- **Fault Tolerance** - Your business can run 24*7 with no interruptions
- **Horizontal Scalability** - We can dynamically grow our business on demand 

Having those two "badges of honor" is not a trivial achievement we will try to repeat for every distributed system.

### Reference
Github - https://github.com/Annashuo/Distributed-System
Udemy Course - https://www.udemy.com/course/distributed-systems-cloud-computing-with-java/
 
