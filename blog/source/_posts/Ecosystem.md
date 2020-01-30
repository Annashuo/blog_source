title: Hadoop Ecosystem
date: 2017-04-27 17:12:18
tags: Hadoop
categories: Hadoop Project
---

![Hadoop_Ecosystem](https://github.com/Annashuo/hadoop_project/blob/master/hadoop_ecosystem.png?raw=true)

<!--more-->

#### HDFS: Hadoop Distributed File System

It allows to distribute the storage to big data across all clusters of computer. It also maintain the redundant copy of that data. If one of computer breaks down, it can recover from that backup.

#### YARN: Yet Another Resource Negotiator

It is the data storage part of hadoop and also the data manage part of hadoop. It is a system that manage the storage of the data on your computing clusters

#### MapReduce
A programming model that allows to process your data across the data clusters. Mapper has been builted to transfer your data in parallel across different computing cluster and map; Reducer is to aggregate data together.

#### Pig

A high level API allows you to write scripts like SQL to request complex result without writing java or python.

#### Hive

It is more directly to use SQL, making your storage system more look like a SQL database. 

#### Apache Ambari

It is a tool for you to have a view on have data moved through clusters or how application functioning on the data.

#### MESOS

It is also a resources negotiator like YARN.

#### Spark

Run query on your data. You need to write Spark script using either python or java. Spark is really fast technology. It runs efficiently and reliabily on your clusters.

#### TEZ

It often use with hive to accelerate the programing running. 

#### HBASE

A No-SQL database. It exposes your data to transfer from an platform. It is a very fast database technology to transfer data from different clusters or platforms.

#### STORM 

A way for processing streaming data quickly.

#### OOZIE

A way as scheduling jobs on your clusters. There may be different steps and using different system in hadoop. OOZIE is a way to schedule all these things as jobs.

#### ZooKeeper

It is basically a technology to coordinate all these technologies as nodes. It can track which node is up and which node is down.

#### Data Ingestion

transform your own database or web server data into a HDFS.