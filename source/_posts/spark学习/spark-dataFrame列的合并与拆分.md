---
layout: posts
title: spark_dataFrame列的合并与拆分
date: 2022-10-29 22:40:29
description: "这是文章开头，显示在主页面，详情请点击此处。"
categories: 
- "spark"
tags:
- "dataFrame"
---

简介 <!--more-->

# 注意点：

一、

​		foreach 的传入参数只能是序列中的一个元素，

`Seq((1,a), (2,b), (3,c)).foreach( x => println(x) )`

​		如果是想把传入的参数拆解开，需要用到偏函数，

`Seq((1,a), (2,b), (3,c)).foreach({case(x,y) => println(x,y)})`

​		最外层的括号省略，成为如下形式：

`Seq((1,a), (2,b), (3,c)).foreach {case(x,y) => println(x,y) }`

​		其实不仅仅是在foreach中，在map等其他的方法中，这些方法的参数要求是一个函数，这个函数的参数仅仅是序列中的一个元素，这样的情况，要拆解元素，都可以用到  `{case ... => ...}`    偏函数这样的形式。 		

​		一句话就是 ----> **用偏函数 的形式来 拆开元素使用**。

二、

​		zipWithIndex或者zip方法来自动地创建一个计数器，假设你有一个有序集合days，那么你可以使用zipWithIndex和counter来打印带有计数器的集合元素：	

```scala
scala> val days = Array("Sunday", "Monday", "Tuesday", "Wednesday","Thursday", "Friday", "Saturday")
days: Array[String] = Array(Sunday, Monday, Tuesday, Wednesday, Thursday, Friday, Saturday)
 
scala> days.zipWithIndex.foreach{case(day,count) => println(s"$count is $day")}
0 is Sunday
1 is Monday
2 is Tuesday
3 is Wednesday
4 is Thursday
5 is Friday
6 is Saturday
```

三、	

​		tabulate()()

第一个参数接收一个或多个值，代表一维或多维列表

第二个参数相当于一个或多个for循环，（）内的参数就是这些for循环最中间对每个for循环的值的计算公式

```scala
List.tabulate(2,3)()  // 代表生成一个二维的列表，两行三列

// 假设第二个参数是b
List.tabulate(2,3)(b)
// 这个b是什么呢
for (x1 <- 0 until 2){
	for (x2 <- 0 until 3){
		b;  // x1与x2的计算公式
	}
}

// 在第二个列表直接输入b的计算公式, 每个x用“_”代表
List.tabulate(2,3)(_*_)  // 代表x1 * x2

// 上面得到的列表
List[List[Int]] = List(List(0, 0, 0), List(0, 1, 2))

// 写成详细的计算过程
List[List[Int]] = List(List(0 * 0, 0 * 1, 0 * 2), 
					   List(1 * 0, 1 * 1, 1 * 2))
```



----------------------------------------------------------------------------------------------

# 制作数据

merging_split_testdata

```tex
name,age,phone
Ming,20,13170340012
zhansan,22,15953576688
lisi,28,18545784857
```

# 代码实现

spark 的dataframe 合并 、 切分代码，concat_split_test

```scala
package Spark_DataFrame_column_merging_and_splitting

import org.apache.spark.sql.expressions.UserDefinedFunction
import org.apache.spark.sql.types.StringType
import org.apache.spark.sql.{DataFrame, Row, SparkSession}

import scala.reflect.internal.util.TriState.False

object concat_split_test {
  def main(args: Array[String]):Unit={
    val spark: SparkSession = SparkSession
      .builder()
      .master("local[*]")
      .appName("merging_and_splitting_tets")
      .getOrCreate()
    spark.sparkContext.setLogLevel("WARN")
    import spark.implicits._

    val path = "file:///opt/data/maven_scala_test/data/merging_split_testdata"
    val df: DataFrame = spark.read.option("header","true").csv(path)
    df.cache()
    df.show()

    val separator = ","

    // merging 
    import org.apache.spark.sql.functions._
    val df_merging_1: DataFrame = df.select(concat_ws(separator, $"name", $"age", $"phone").cast(StringType).alias("value"))
    println("df_merging_1 is :")
    df_merging_1.show(truncate=false)

    val df_merging_2 = df.map(_.toSeq.foldLeft("")(_+separator+_).substring(1))
        //一定要有substring() 来截取出子串，不然为空。因为我们人为加了一个“” 在最左边，所以从1号位置取。
    println("df_merging_2 is :")
    df_merging_2.show(truncate=false)

    def mergeCols(row:Row):String={
      row.toSeq.foldLeft("")(_+separator+_).substring(1)
    }
    val mergeColsUDF: UserDefinedFunction = udf(mergeCols _)  // 注册udf。
    val df_merging_3: DataFrame = df.select(mergeColsUDF(struct($"name", $"age", $"phone")).as("value"))
    println("df_merging_3 is :")
    df_merging_3.show(truncate=false)


    // split
    val first_row: Row = df_merging_3.first()
    val numAttrs: Int = first_row.toString().split(separator).length
    val attrs: Array[String] = Array.tabulate(numAttrs)(num => {
      "col_" + num
    })
    var newDF: DataFrame = df_merging_3.withColumn("splitCols", split($"value", separator))
    attrs.zipWithIndex.foreach( {case(col,index)=>println(col,index)} )  //用偏函数 的形式来 拆开元素使用,这里只是验证一下.
    attrs.zipWithIndex.foreach{ case(col,index)=>{
      newDF = newDF.withColumn(col,$"splitCols".getItem(index))
    }}
    newDF.show()

    df.unpersist()
  }
}
```

​		输出如下：

```scala
+-------+---+-----------+
|   name|age|      phone|
+-------+---+-----------+
|   Ming| 20|13170340012|
|zhansan| 22|15953576688|
|   lisi| 28|18545784857|
+-------+---+-----------+

df_merging_1 is :
+----------------------+
|value                 |
+----------------------+
|Ming,20,13170340012   |
|zhansan,22,15953576688|
|lisi,28,18545784857   |
+----------------------+

df_merging_2 is :
+----------------------+
|value                 |
+----------------------+
|Ming,20,13170340012   |
|zhansan,22,15953576688|
|lisi,28,18545784857   |
+----------------------+

df_merging_3 is :
+----------------------+
|value                 |
+----------------------+
|Ming,20,13170340012   |
|zhansan,22,15953576688|
|lisi,28,18545784857   |
+----------------------+

(col_0,0)
(col_1,1)
(col_2,2)
+--------------------+--------------------+-------+-----+-----------+
|               value|           splitCols|  col_0|col_1|      col_2|
+--------------------+--------------------+-------+-----+-----------+
| Ming,20,13170340012|[Ming, 20, 131703...|   Ming|   20|13170340012|
|zhansan,22,159535...|[zhansan, 22, 159...|zhansan|   22|15953576688|
| lisi,28,18545784857|[lisi, 28, 185457...|   lisi|   28|18545784857|
+--------------------+--------------------+-------+-----+-----------+

Process finished with exit code 0
```

