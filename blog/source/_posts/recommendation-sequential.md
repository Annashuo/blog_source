---
title: recommendation-sequential
date: 2017-05-02 14:38:29
tags: recommendation
categories: STUDY
---

本文主要介绍“集体智慧编程”第二章的内容，python环境为2.7,所有代码都在[这里](https://github.com/Annashuo/hadoop_project/blob/master/recommendations.py)

<!--more-->

### 数据介绍
#### 简单自建critics数据集

格式为 '人名':{'电影一':评分一,'电影二':评分二,...}

```
critics={'Lisa Rose': {'Lady in the Water': 2.5, 'Snakes on a Plane': 3.5,
 'Just My Luck': 3.0, 'Superman Returns': 3.5, 'You, Me and Dupree': 2.5, 
 'The Night Listener': 3.0},
'Gene Seymour': {'Lady in the Water': 3.0, 'Snakes on a Plane': 3.5, 
 'Just My Luck': 1.5, 'Superman Returns': 5.0, 'The Night Listener': 3.0, 
 'You, Me and Dupree': 3.5}, 
'Michael Phillips': {'Lady in the Water': 2.5, 'Snakes on a Plane': 3.0,
 'Superman Returns': 3.5, 'The Night Listener': 4.0},
'Claudia Puig': {'Snakes on a Plane': 3.5, 'Just My Luck': 3.0,
 'The Night Listener': 4.5, 'Superman Returns': 4.0, 
 'You, Me and Dupree': 2.5},
'Mick LaSalle': {'Lady in the Water': 3.0, 'Snakes on a Plane': 4.0, 
 'Just My Luck': 2.0, 'Superman Returns': 3.0, 'The Night Listener': 3.0,
 'You, Me and Dupree': 2.0}, 
'Jack Matthews': {'Lady in the Water': 3.0, 'Snakes on a Plane': 4.0,
 'The Night Listener': 3.0, 'Superman Returns': 5.0, 'You, Me and Dupree': 3.5},
'Toby': {'Snakes on a Plane':4.5,'You, Me and Dupree':1.0,'Superman Returns':4.0}}

```

#### MoviesLens数据集

[官方下载链接](https://grouplens.org/datasets/movielens/
)
下载下来的文件解压后主要有两个文件比较有用，一个是u.data，内容大概如下

![u.data](https://github.com/Annashuo/hello-world/blob/master/u_data_head10.png?raw=true)

四列分别为usrID, movieID, rating, timestamp，每一行分别对应一个用户对一个影片的评分

第二个文件是u.item，大概内容如下

![u.item](https://github.com/Annashuo/hello-world/blob/master/u_item_head10.png?raw=true)

新建loadMovieLens方法用于加载数据

```
#注意将数据文件放在当前文件夹里的data文件夹下的movielens文件夹里
def loadMovieLens(path='./data/movielens'):
  # Get movie titles
  movies={}
  for line in open(path+'/u.item'):
    (id,title)=line.split('|')[0:2]
    movies[id]=title
  
  # Load data
  prefs={}
  for line in open(path+'/u.data'):
    (user,movieid,rating,ts)=line.split('\t')
    prefs.setdefault(user,{})
    prefs[user][movies[movieid]]=float(rating)
  return prefs
```

### 基于用户的协作型过滤（user-based collabarative filtering）

#### 相似度评价体系
##### 欧几里得距离评价
用属性构造偏好空间，两者在偏好空间中距离越近，说明两者的相似度越高。

比如对于以下的数据，计算Lisa Rose和Gene Seymour的欧几里得距离为
$Eu = sqrt((2.5-3.0)^2+(3.5-3.5)^2+(3.0-1.5)^2+(3.5-5.0)^2+(2.5-3.5)^2)$

欧几里得距离越小，说明两者相似度越高。

```
'Lisa Rose': {'Lady in the Water': 2.5, 'Snakes on a Plane': 3.5,
 'Just My Luck': 3.0, 'Superman Returns': 3.5, 'You, Me and Dupree': 2.5, 
 'The Night Listener': 3.0},
'Gene Seymour': {'Lady in the Water': 3.0, 'Snakes on a Plane': 3.5, 
 'Just My Luck': 1.5, 'Superman Returns': 5.0, 'The Night Listener': 3.0, 
 'You, Me and Dupree': 3.5}, 
```
现在我们人为构建一个公式，$1/(1+sqrt(Eu))$，这一函数返回值介于0到1，返回1说明两者有完全一样的偏好

欧几里得距离评价函数sim_distance(prefs, person1, person2)如下

```
from math import sqrt

# Returns a distance-based similarity score for person1 and person2
def sim_distance(prefs,person1,person2):
  # Get the list of shared_items
  si={}
  for item in prefs[person1]: 
    if item in prefs[person2]: 
      si[item]=1

  # if they have no ratings in common, return 0
  if len(si)==0: return 0

  # Add up the squares of all the differences
  sum_of_squares=sum([pow(prefs[person1][item]-prefs[person2][item],2) 
                      for item in prefs[person1] if item in prefs[person2]])

  return 1/(1+sum_of_squares)
```

##### 皮尔逊相关度评价
对于随机变量X和Y，皮尔森相关系数的就是：(x和y的协方差) / (x的标准差∗y的标准差)，用于判断两组数的线性关系程度。相对于欧几里得距离评价法，皮尔逊相关度评价有一个明显的优点是，假如两个用户对相同的电影的评价是正相关的，但是始终有一个用户的评价比另一个偏高很多，运用欧氏距离，两个用户在偏好空间里可能距离是很远的，求得得相似度并不高，但是运用皮尔逊相关度评价，就可以得到两者相关性很高的结果，即相似度很高的结论。

皮尔逊相关度函数sim_pearson(prefs, p1, p2)如下

```
# Returns the Pearson correlation coefficient for p1 and p2
def sim_pearson(prefs,p1,p2):
  # Get the list of mutually rated items
  si={}
  for item in prefs[p1]: 
    if item in prefs[p2]: si[item]=1

  # if they are no ratings in common, return 0
  if len(si)==0: return 0

  # Sum calculations
  n=len(si)
  
  # Sums of all the preferences
  sum1=sum([prefs[p1][it] for it in si])
  sum2=sum([prefs[p2][it] for it in si])
  
  # Sums of the squares
  sum1Sq=sum([pow(prefs[p1][it],2) for it in si])
  sum2Sq=sum([pow(prefs[p2][it],2) for it in si])	
  
  # Sum of the products
  pSum=sum([prefs[p1][it]*prefs[p2][it] for it in si])
  
  # Calculate r (Pearson score)
  num=pSum-(sum1*sum2/n)
  den=sqrt((sum1Sq-pow(sum1,2)/n)*(sum2Sq-pow(sum2,2)/n))
  if den==0: return 0

  r=num/den

  return r
```
#### 求相似用户
topMatches(prefs, person, n=5, similarity=sim_pearson)函数求得person和其他用户的similarity，规则是pearson相关度或欧几里得距离评价，返回前n个最相似的用户。

#### 推荐电影
基于用户的协作型过滤推荐电影的基本思路如下：

假如我们现在要给一个特定用户p1推荐电影，首先运用欧几里得距离规则或者皮尔逊相关度评价求p1和其他所有用户的相似度，用每一个相似度来对每一个用户对其看过的电影的评分进行加权，然后对于用户p1没看过的每一部电影，将评分加权值求和，对所有电影并进行排序得到电影的推荐排名。

该函数为getRecommendations(prefs, person, similarity=sim_pearson)

这里有一个可以降低准确度来提高运行速度的方法，就是得到p1和其他所有用户的相似度后，可以只选取相似度高的部分用户进行后面的加权求和操作。

### 基于物品的协作型过滤（item-based collaborative filtering）

在上面基于用户的协作型过滤过程中，用topMatches函数可以求出每两个用户之间的相似度，那么相同的道理我们也可以求出每两部电影之间的相似度。只需要把原用户电影数据矩阵进行转置，就可以调用相同的topMatches函数。

求出所有电影之间两两相似度矩阵的函数为calculateSimilarItems(prefs,n)

基于物品的协作型过滤推荐电影的基本思路如下：

假如我们现在要给用户p1推荐电影，首先求出所有电影之间两两的相似度，对于用户p1他自己看过的每一部电影，用p1对该电影的评分和该电影与p1没看过的其他所有电影的相似度一一相乘，然后对于p1没看过的其他所有电影的加权评分值进行求和，并排序得到推荐电影排名。

该函数为getRecommendedItems(prefs,itemMatch,user)

这里同样有一个可以提高速度的地方，即只求出用户p1看过的电影和其他电影之间的相似度，不需要求所有电影两两之间的相似度。