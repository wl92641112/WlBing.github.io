---
title: Spark之DataFrame操作(python版本)
date: 2020-05-06 00:08:09
tags: [Spark,DataFrame,PySpark]
categories: Spark
---
# 1. DataFrame概述

<!--more-->

# 2. DataFrame保存


# 3. DataFrame创建 

# 4. DataFrame常用操作

##     1.   创建一个上下文对象

```python
conf =  SparkConf().setMaster("local").setAppName("My App")  # scpark 配置 local 单机模式
sc = SparkContext(conf=conf)  # 创建saprkConttext  上下文对象
```

##     2.  读取json文件