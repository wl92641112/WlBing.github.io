---
title: Spark之DataFrame操作(python版本)
date: 2020-05-06 00:08:09
tags: [Spark,DataFrame,PySpark]
categories: Spark
---
# 0.Spark SQL简介

说到Spark sql 就要说的一个 数据仓库技术hive，hive是为了帮助Hadoop实现sql查询的一种技术，hive本身不存数据，数据是在底层的hdfs里面，hive是sql查询是转化为底层的mapreduce作业进行。如图所示 hive的执行原理



![hive查询原理](https://github.com/wl92641112/wl92641112.github.io/blob/hexo/source/_posts/Spark%E4%B9%8BDataFrame%E6%93%8D%E4%BD%9C(python%E7%89%88%E6%9C%AC)/hive%E6%9F%A5%E8%AF%A2%E5%8E%9F%E7%90%86.png?raw=true)

spark sql则是把根据hive的原理，实现sql转化为spark的查询查询语句。

spark是线程级别的并行，而MapReduce是进程级并行。

spark sql 不受hive限制，只是兼容HIve。

spark sql应用于DataFrame(带有Schema[模式]信息的RDD)，使用户可以在park sql中执行sql语句，数据即可以来着rdd，也可以是Hive,HDFS,Cassandra等外部数据源，也可以是json格式数据。 目前支持java，scala，python。

spark sql就是使用DataFrame帮助融合多个数据源，可以对外部和内部各种数据源执行各种操作，其次，可以支持大量的数据源和数据分析算法，spark sql 可以融合传统关系数据库的结构化数据管理能力和机器学习的数据处理能力。

<!--more-->

# 1. DataFrame概述

DataFrame可以叫做数据框。让spark具备了处理大规模结构化数据的能力，不仅比原来的Rdd转化方式更加简单易用，而且获得了更高的计算性能。

saprk能够轻松的从Mysql到DataFrame转化，并且支持sql查询。

Rdd是分布式的java对象集合，但是对象内部结构对于Rdd而言却是不可知的。

DataFrame是一种Rdd为基础的分布式数据集合，提供了详细的结构信息。

对比 Rdd是一个一个的 person对象，你是不能看到他的具体结构而DataFrame 是一个以rdd为基础的分布式结构化数据集合。通俗的说rdd是一个关闭的抽屉你看不到里面结构，DataFrame是一个打开的抽屉可以直接看到里面的结构。

![df和rdd区](https://github.com/wl92641112/wl92641112.github.io/blob/hexo/source/_posts/Spark%E4%B9%8BDataFrame%E6%93%8D%E4%BD%9C(python%E7%89%88%E6%9C%AC)/df%E5%92%8Crdd%E5%8C%BA%E5%88%AB.png?raw=true)

# 2. DataFrame创建

从spark2.0以后创建spark都用SparkSession接口，SparkSeeion支持不同数据源加载数据，并把数据转化为DataFrame，并支持把DataFrame转换陈SqlContext自身的表，然后时候sql语句来操作数据，SparkSesion提供HIveQL以及其他依赖于Hive的功能支持。

```python
"""
注意在启动进入pyspark交互式环境以后，pyspark就默认提供了一个SparkContext对象(名称为sc)和一个sparkSession对象名为spark
不需要在创建直接可以拿来用
"""

from pyspark import SparkContext,SparkConf
from pyspark.sql import SparkSession  # SparkSession编写spark sql 的指挥官
spark = SparkSession.builder.config(conf=SparkConf()).getOrCreate()
# pyspark 下 spark 对象可以直接用

# 创建DataFrame  
# 注意 读本地文件需要 file:///文件名 例如 file:///usr/local/spark/people.text
# 分布文件 需要写 hdfs:/localhost:9000/路径全名
spark.read.text("people.text")  # text文件
spark.read.josn("people.json") # json 文件
spark.read.parquat("people.parquet") # parquet文件

# 或者
spark.read.format("text").load("people.text")
spark.read.format("json").load("people.json")
spark.read.format("parquet").load("people.parquet")

# 在安装完spark 的时候是有两个样例文件的
# 路径 /use/local/spark/examples/src/main/respurces/people.json
# 路径 /use/local/spark/examples/src/main/respurces/people.text

# 在交互式 的python环境 spark 直接可以用
>>>df=spark.read.json("file:///user-data/home/wangliang/people.json") # 生成df
>>>df.show() # 显示 df                                                       
+---+----+                                                                  
|age|neme|                                                                  
+---+----+                                                                  
| 30|  张三|                                                                
| 17|  李四|                                                                
| 28|  王五|                                                                
+---+----+      
```






# 3. DataFrame保存 

```python
# spark.write 保存DataFrame
  
# 注意 保存本地文件需要 file:///文件名 例如 file:///usr/local/spark/people.text
# 分布文件 需要写 hdfs:/localhost:9000/路径全名
# 这里给的简写
df.write.text("people.text")  # text文件
df.write.josn("people.json") # json 文件
df.write.parquat("people.parquet") # parquet文件

# 或者
df.write.format("text").save("people.text")
df.write.format("json").save("people.json")
df.write.format("parquet").save("people.parquet")

>>>df=spark.read.json("file:///user-data/home/wangliang/people.json")                     >>>df.show()                                                         
+---+----+                                                                  
|age|neme|                                                                  
+---+----+                                                                 
| 30|  张三|                                                                
| 17|  李四|                                                               
| 28|  王五|                                                                
+---+----+                                                                  
>>>newdf = df.select(df['age'])  # 生成一个新的df                                           >>>newdf.show()                                                     
+---+                                                                       
|age|                                                                       
+---+                                                                       
| 30|                                                                       
| 17|                                                                      
| 28|                                                                       
+---+    
>>>newdf.write.format("json").save("file:///user-data/home/wangliang/people2.json")
# 注意这里生成的是一个名录名称，不是一个json文件，.json不是扩展名
>>>cd people2.json/                                                  │
/user-data/home/wangliang/people2.json

# 读取
>>>df=spark.read.json("file:///user-data/home/wangliang/people2.json")  
>>>df.show() # 默认显示20行
+---+                                                 
|age|                                                 
+---+                                                 
| 30|                                                 
| 17|                                                 
| 28|                                                 
+---+
```



# 4. DataFrame常用操作

``` python
>>>df=spark.read.json("file:///user-data/home/wangliang/people.json")                     >>>df.show()                                                         
+---+----+                                                                  
|age|neme|                                                                  
+---+----+                                                                 
| 30|  张三|                                                                
| 17|  李四|                                                               
| 28|  王五|                                                                
+---+----+

# printSchema()  显示df数据结构 nullable 可以为空
>>>df.printSchema() 
root                                                  
 |-- age: long (nullable = true)                      
 |-- neme: string (nullable = true)
    
# select() 挑选显示的列    
>>>df.select(df["neme"],df["age"],df["age"]+1,df["age"]*3).show()
+----+---+---------+---------+                        
|neme|age|(age + 1)|(age * 3)|                        
+----+---+---------+---------+                        
|  张三| 30|       31|       90|                      
|  李四| 17|       18|       51|                      
|  王五| 28|       29|       84|                      
+----+---+---------+---------+  

# filter() # 完成过滤操作的
>>>df.filter(df["age"]>20).show()
+---+----+                                            
|age|neme|                                            
+---+----+                                            
| 30|  张三|                                          
| 28|  王五|                                          
+---+----+  
>>>df.filter(df["age"]>20).filter(df["age"]<30).show() # 大于 小于操作
+---+----+                                                                               |age|neme|                                                                               +---+----+                                                                               | 28|  王五|             
+---+----+     
>>>df.filter("age>20 and age<30").show()  # 大于 小于操作
+---+----+                                                                               |age|neme|                                                                               +---+----+                                                                               | 28|  王五|             
+---+----+ 
>>>df.filter("neme='王五'").show() # 等于 字符串 要加双引号
+---+----+                                                                               |age|neme|                                                                               +---+----+                                                                               | 28|  王五|             
+---+----+ 
>>>df.filter("neme!='王五'").show() # 不等于 字符串 要加双引号
+---+----+                                                                               |age|neme|                                                                               +---+----+                                                                               | 30|  张三|                                                                               | 17|  李四|                                                                               +---+----+    

# groupBy()  分组
>>>df.groupBy("age").count().show() # 根据年龄分组 
+---+-----+                                                                               |age|count|                                                                               +---+-----+                                                                               | 17|    1|                                                                               | 28|    1|                                                                               | 30|    1|                                                                               +---+-----+ 

from pyspark.sql import functions as F 
# 使用 函数 进行分组求和
>>>df.groupBy(df.neme).agg(F.count(df.neme),F.sum(df.age)).show() # 计算每个名字都有对少人，然后对这些人的年龄进行求和 
+----+-----------+--------+                                                               |neme|count(neme)|sum(age)|                                                               +----+-----------+--------+                                                               |  王五|          1|      28|                                                             |  李四|          1|      17|                                                             |  张三|          1|      30|                                                             +----+-----------+--------+   
>>>df.groupBy().agg(F.sum(df.age)).show()  # 如果groupBy传空则可以对所有的age求和
+--------+                                                                               |sum(age)|                                                                               +--------+                                                                               |      75|                                                                               +--------+ 

# sort()  排序
>>>df.sort(df["age"].desc()).show() # 根据年龄降序排序
+---+----+                                                                               |age|neme|                                                                               +---+----+                                                                               | 30|  张三|                                                                               | 28|  王五|                                                                               | 17|  李四|                                                                               +---+----+
>>>df.sort(df["age"].desc(),df["neme"].asc()).show() # 根据年龄降序排序 按照名字升序
+---+----+                                                                               |age|neme|                                                                               +---+----+                                                                               | 30|  张三|                                                                               | 28|  王五|                                                                               | 17|  李四|                                                                               +---+----+  

# collect() 把df转化为列表 可以使用for变量，即可以用索引也可以用键值对取
>>>df.collect()
[Row(age=30, neme='张三'), Row(age=17, neme='李四'), Row(age=28, neme='王五')]
>>>df.collect()[1][1] 
'李四'
>>>df.collect()[1]["neme"]
'李四'

# 创建一个新列
>>>df.withColumn("is—lisi",F.when(df.neme == '李四',1).otherwise(0)) # 创建一个列是不是李四如果是为 1 不是为0
+---+----+-------+                                                                       |age|neme|is—lisi|                                                                       +---+----+-------+                                                                       | 30|  张三|      0|                                                                       | 17|  李四|      1|                                                                       | 28|  王五|      0|                                                                       +---+----+-------+ 

# cache() 缓存
```

# 5. Rdd模式转化DataFrame

先在准备一个text 文件 ,text是字符串 无法直接读取文件生成df

![](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20200509001847983.png)

### 5.1 利用反射机制去推断RDD模式

第一个map 分割每一个字符串为一个列表，第二个map转化每一个列表为一个row对象。

![image-20200508233655407](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20200508233655407.png)

```python
from pyspark import SparkContext,SparkConf
from pyspark.sql import SparkSession  # SparkSession编写spark sql 的指挥官
spark = SparkSession.builder.config(conf=SparkConf()).getOrCreate()

# 如果是在pyspark 的交互环境内 直接就又 spark 对象，可以不用设置，第二种创建方法
from pyspark.sql import SparkSession
spark_host = "local[*]"
app_name = "abc"
spark =SparkSession.builder.master(spark_host).appName(app_name).getOrCreate()

# 生成rdd，然后去推断df
people=spark.sparkContext.textFile("file:///user-data/home/wangliang/people.text").\
map(lambda x:x.split(",")).\
map(lambda p:Row(name=p[0],age=int(p[1])))

# 用rdd生成df 这里可以是 toDF()方法 效果一样
scheaPeople = spark.createDataFrame(people)
# 注册临时表,只有创建临时表才可以用sql
scheaPeople.createOrReplaceTempView("people")
# 使用sql
results = spark.sql("select name,age from people where age>20")
# 展示 结果
results.show()
+--------+---+
|    name|age|
+--------+---+
|zhangsan| 29|
|    lisi| 30|
+--------+---+
```



### 5.2 用编程方式去定义RDD模式



用编程的模式去定义rdd需要先经过三个步骤

1.第一步：制作表头

2.第二步：制作“表中数据”

3.第三步：把表头和数据拼装起来

![image-20200508231505141](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20200508231505141.png)

```python
from pyspark.sql.types import StringType,StructField,StructType 
from pyspark.sql import Row
# 第一步生成表头
schemString = "name age"  # 表头字符串 name age 用空格分开
# StructField 表示列格式，第一个参数为列名，第二个是类型，第三个是 是否可以为空
fields = [StructField(field_name,StringType(),True) for field_name in schemString.split(" ")]
#  fields  输出 [StructField(name,StringType,true), StructField(age,StringType,true)]
# StructType 是用来描述包装表头的，帮你生成整个df 格式
schema = StructType(fields)

# 生成表中记录
from pyspark import SparkContext,SparkConf
from pyspark.sql import SparkSession  # SparkSession编写spark sql 的指挥官
spark = SparkSession.builder.config(conf=SparkConf()).getOrCreate()
people=spark.sparkContext.textFile("file:///user-data/home/wangliang/people.text")

# 第一个map 分割每一个字符串为一个列表。此时为rdd
parts = people.map(lambda x:x.split(",")) 
# 第二个map转化每一个列表为一个row对象。此时为rdd
peole = parts.map(lambda p:Row(p[0],p[1].strip()))
# 拼接表头和表中记录 此时为df
scheaPeople = spark.createDataFrame(peole,schema)

# df可以用show
scheaPeople.show()
+--------+---+
|    name|age|
+--------+---+
|zhangsan| 29|
|  wangwu| 10|
|    lisi| 30|
+--------+---+

# 注册临时表,只有创建临时表才可以用sql
scheaPeople.createOrReplaceTempView("people")
# 使用sql
results = spark.sql("SELECT name,age FROM people")
# 展示 结果
results.show()
+--------+---+
|    name|age|
+--------+---+
|zhangsan| 29|
|  wangwu| 10|
|    lisi| 30|
+--------+---+
```

