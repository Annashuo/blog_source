---
title: Reading Paper
date: 2016-09-13 10:52:39
categories: paper
---
# Sparse and Redundant Representation-Based Smart Meter Data Compression and Pattern Extraction

<!-- more -->
### Abstract
Two main issues:
1.communication and storage of big data.(电网数据存储，有损压缩)
2.effective extraction of useful information.(信息提取)
本文用K_SVD稀疏表示技术（包含字典学习和稀疏编码）将负载曲线数据分解为几个partial usage patterns(PUPs)的线性组合，然后用线性支持向量机(SVM)将数据分为两类（居民消费者和中小型企业）。同时用k-means聚类、主成分分析(PCA)、小波变换(DWT)、分段聚合近似(PAA)作为对比。

#### 介绍
数据压缩可分为两类：无损压缩(lossless compression)和有损压缩(lossy compression)。
无损压缩包括LAP2,FLAC,TTAEnc,ZIP,JPEG-LS,SPIHT-lossless。
有损压缩包括小波变换(discrete wavelet, DWT),傅里叶变换(dicrete Fourier transform, DFT),主成分分析(principal component analysis, PCA),单值分解(singular value decomposition,SVD)。
相对于无损压缩，有损压缩可以讲话数据位数，同时不损失数据信息。

聚类和稀疏编码都可以进行负载曲线数据的压缩，但是聚类是降幅在数据看成一个整体，而稀疏编码则是将每日的负载数据分解为不同的PUPs。这样不同的负载数据可以被看做PUPs的线性组合。

本文采用的数据压缩算法和提取特征的算法是非负的K-SVD算法，分类算法是线性的支持向量机。

#### 基于非负K-SVD稀疏编码(NON-NEGATIVE K-SVD BASED SPARSE CODING)
 - 稀疏编码
X=DA
X是输入变量，是一个N*M的矩阵，其中M对应负载曲线的个数，即数据集中消费者数目；N对应的是属性个数，又称为维度。
A是M*K冗余字典，其实就是所提取的变量的系数矩阵，其中不为0的元素个数为s。
D是K维的特征向量。

稀疏编码目标是找到一个稀疏的且有冗余的字典集来表示原有的数据。稀疏性表示只有较小的一部分A内的元素非零，有冗余意思是K>N。
在本文中，经过稀疏编码，发现只有第1，3，5，K个PUP的系数不为零，所以原48维的数据经过稀疏编码后变成四个系数。

 - 非负K-SVD
 稀疏编码是有损的，即X>DA。所以优化问题变为 min|X-DA|^2 .同时还需要用最小平均标准差RMSE来评估算法的效果。完整K-SVD算法如下图。
  ![k-svd](https://raw.githubusercontent.com/Annashuo/hello-world/master/k-svd.png)

#### 线性支持向量机((linear SVM)
线性支持向量机的原理不打算写了，各种书都说烂了...matlab，R，python都有现成的函数。写写这篇文章提到的用线性支持向量机四个原因：
-  线性SVM不需要计算核函数值，速度快于基于核函数的SVM；
-  线性SVM只有一个参数需要被设定，找到这一个参数的最优值比较容易；
-  只要数据量够大，线性SVM交叉检验效果好；
-  特征的比重直接代表了特征的重要性，物理含义好解释。


