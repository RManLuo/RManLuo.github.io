---
title: neo4j导入和创建多数据库
date: 2020-05-07 12:49:08
tags:
	- neo4j
categories:
	- 数据库
cover: https://neo4j.com/wp-content/themes/neo4jweb/assets/images/neo4j-logo-textreverse.png
mathjax: true
---

## 前言

最近在玩neo4j，用来存储实验的图数据库，以前都是只用做一个数据库就好了。但是由于一个数据集的paper被review怼的惨不忍睹，只能多搞几个数据集来实验，于是就开始倒腾建立多个数据集的方法。

## Neo4j-admin import

使用Neio4j-admin import导入csv

`neo4j-admin --database=xxx.db --nodes=users.csv --relationships=relations.csv`

--database：数据库名字，由于创建了多个数据库所以必须改名，如果出现数据库名字重复或者不为空可以执行`sudo rm -rf /var/lib/neo4j/data/databases/xx.db`清空

--nodes：节点csv，`[name:ID]`，ID字段是必须的，是唯一索引，name是feature的名字，`:LABEL`是节点的标签，貌似可以用[name:ID(label)]替代:LABEL ，但是此时在import 的时候要在`--nodes:User=users.csv`明确label

```
movies.csv. 

movieId:ID,title,year:int,:LABEL
tt0133093,"The Matrix",1999,Movie
tt0234215,"The Matrix Reloaded",2003,Movie;Sequel
tt0242653,"The Matrix Revolutions",2003,Movie;Sequel
```

--relations：关系csv，必须包含`START_ID`, `END_ID` 和 `:TYPE`，分别是开始节点，结束节点，和边的种类

```
roles.csv. 

:START_ID,role,:END_ID,:TYPE
keanu,"Neo",tt0133093,ACTED_IN
keanu,"Neo",tt0234215,ACTED_IN
keanu,"Neo",tt0242653,ACTED_IN
laurence,"Morpheus",tt0133093,ACTED_IN
laurence,"Morpheus",tt0234215,ACTED_IN
laurence,"Morpheus",tt0242653,ACTED_IN
carrieanne,"Trinity",tt0133093,ACTED_IN
carrieanne,"Trinity",tt0234215,ACTED_IN
carrieanne,"Trinity",tt0242653,ACTED_IN
```

## 多个数据库并存

neo4j默认是导入`graph.db`，在有多个数据库的时候，可以通过创建符号链接的方式切换数据库

```
mv graph.db graph1.db
ln -s graph1.db graph.db
ln -s graph2.db graph.db
```

![image-20200507130527694](http://image.rman.top/20200507130538.png)