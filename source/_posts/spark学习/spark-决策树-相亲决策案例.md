---
layout: posts
title: spark_决策树-相亲决策案例
date: 2022-10-29 22:44:07
description: "这是文章开头，显示在主页面，详情请点击此处。"
categories: 
- "spark"
tags:
- "决策树"
---

简介 <!--more-->


决策树算法：

​		选择当前信息增益最大的特征作为树的节点，依次选择相应的特征作为树的节点，直到形成一颗决策树。

```python
# 相亲数据
label,age,handsome,salary,programmer
不见面,32,1,2,0
不见面,25,1,1,0
见面,29,1,1,1
见面,24,1,2,0
不见面,31,1,2,0
见面,35,1,1,1
不见面,30,0,2,0
不见面,31,1,2,0
见面,30,1,1,1
见面,21,1,2,0
不见面,21,1,1,0
见面,21,1,1,1
不见面,29,0,1,1
不见面,29,1,0,1
不见面,29,0,1,1
见面,30,1,2,0
不见面,32,1,1,0
见面,27,1,2,1
见面,29,1,2,0
见面,25,1,1,1
```

​		流程  

```python
# 构建环境

# 加载数据

# 标签数值化

# 特征向量化

# 构建决策树模型

# 规范预测标签数值还原

# 数据集划分

# 构建 pipeline

# 使用训练集训练

# 使用测试集合测试/预测

# 输出决策过程

# 评估模型好坏
```



​		提示：

​		`[trainSet, testSet] = df.randomSplit(weights=[0.8,0.2])`	

​		这段代码的意思，并不是严格按照4:1的比例划分数据集为训练集和测试集，而是 It only guarantees that each object has this probability (0.8,0.2) to be assigned to a specific subset.它只保证每个对象被分配到特定子集的概率为0.8和0.2。

​		If number of records is low you'll see fluctuations like here. This is normal behavior.如果记录的数量很低，你会看到像这里这样的波动。这是正常行为。

```python
# coding:utf8
from pyspark.ml import Pipeline
from pyspark.ml.classification import DecisionTreeClassifier
from pyspark.ml.evaluation import MulticlassClassificationEvaluator
from pyspark.ml.feature import StringIndexer, VectorAssembler, IndexToString
from pyspark.sql import SparkSession

if __name__=="__main__":
    # "decision tree demo example for find husband"

    # create environment 构建环境
    spark = SparkSession.builder.appName("Decision_tree").master("local[*]")\
        .getOrCreate()

    # load data 加载数据
    df = spark.read.format("csv") \
        .option("sep",",") \
        .option("header","true") \
        .option("inferSchema","true") \
        .load("file:///opt/data/spark-structure-streaming/data/appointment.data")
    df.printSchema # check data
    df.show()
    print("________________________\n")
    # 标签数值化
    stringIndexer = StringIndexer().setInputCol("label").setOutputCol("label_index").fit(df)

    # 特征向量化         (后续使用pipeline做统一的训练和转换，所以这里不需要再次fit)
    vectorAssembler = VectorAssembler().setInputCols(["age","handsome","salary","programmer"])\
        .setOutputCol("features")

    # 构建决策树模型       (后续使用pipeline做统一的训练和转换，所以这里不需要再次fit)
    decisionTreeClassifier = DecisionTreeClassifier()\
        .setFeaturesCol("features")\
        .setLabelCol("label_index")\
        .setPredictionCol("predict_index")\
        .setMaxDepth(4)

    # 规范预测标签数值还原
    indexToString = IndexToString().setInputCol("predict_index").setOutputCol("predict_string")\
        .setLabels(stringIndexer.labels)    #（设置 数值和字符串的对应关系在哪里）

    # 数据集划分
    (trainSet, testSet) = df.randomSplit(weights=[0.8,0.2])
    print("trainSet is :")
    trainSet.show()
    print("testSet is :")
    testSet.show()

    # 构建 pipeline
    pipeline = Pipeline().setStages([stringIndexer
                                    ,vectorAssembler
                                    ,decisionTreeClassifier
                                    ,indexToString ])

    # 使用训练集训练
    model = pipeline.fit(trainSet)

    # 使用测试集合测试/预测
    testResult = model.transform(testSet)
    testResult.show()

    # 输出决策过程
    print("模型决策过程为 :\n %s" %(model.stages[2].toDebugString))

    # 评估模型好坏
    accuracy = MulticlassClassificationEvaluator(predictionCol="predict_index",labelCol="label_index",metricName="accuracy")\
        .evaluate(testResult)
    print("测试集 错误率为: %f "%(1-accuracy))

    path = "file:///opt/data/spark-structure-streaming/model_decision_tree/model_1"
    model.save(path)
    # model2 = DecisionTreeClassificationModel.load(path)  # 下次加载这个训练好的模型，去跑数据，可以直接用。

    spark.stop()
```

```python
# output
+------+---+--------+------+----------+
| label|age|handsome|salary|programmer|
+------+---+--------+------+----------+
|不见面| 32|       1|     2|         0|
|不见面| 25|       1|     1|         0|
|  见面| 29|       1|     1|         1|
|  见面| 24|       1|     2|         0|
|不见面| 31|       1|     2|         0|
|  见面| 35|       1|     1|         1|
|不见面| 30|       0|     2|         0|
|不见面| 31|       1|     2|         0|
|  见面| 30|       1|     1|         1|
|  见面| 21|       1|     2|         0|
|不见面| 21|       1|     1|         0|
|  见面| 21|       1|     1|         1|
|不见面| 29|       0|     1|         1|
|不见面| 29|       1|     0|         1|
|不见面| 29|       0|     1|         1|
|  见面| 30|       1|     2|         0|
|不见面| 32|       1|     1|         0|
|  见面| 27|       1|     2|         1|
|  见面| 29|       1|     2|         0|
|  见面| 25|       1|     1|         1|
+------+---+--------+------+----------+

________________________

trainSet is :
+------+---+--------+------+----------+
| label|age|handsome|salary|programmer|
+------+---+--------+------+----------+
|不见面| 25|       1|     1|         0|
|不见面| 29|       0|     1|         1|
|不见面| 29|       1|     0|         1|
|不见面| 30|       0|     2|         0|
|不见面| 31|       1|     2|         0|
|不见面| 31|       1|     2|         0|
|不见面| 32|       1|     1|         0|
|不见面| 32|       1|     2|         0|
|  见面| 21|       1|     1|         1|
|  见面| 21|       1|     2|         0|
|  见面| 24|       1|     2|         0|
|  见面| 25|       1|     1|         1|
|  见面| 29|       1|     1|         1|
|  见面| 29|       1|     2|         0|
|  见面| 30|       1|     1|         1|
|  见面| 30|       1|     2|         0|
|  见面| 35|       1|     1|         1|
+------+---+--------+------+----------+

testSet is :
+------+---+--------+------+----------+
| label|age|handsome|salary|programmer|
+------+---+--------+------+----------+
|不见面| 21|       1|     1|         0|
|不见面| 29|       0|     1|         1|
|  见面| 27|       1|     2|         1|
+------+---+--------+------+----------+

2022-07-19 00:27:13,842 WARN impl.DecisionTreeMetadata: DecisionTree reducing maxBins from 32 to 17 (= number of training instances)
+------+---+--------+------+----------+-----------+------------------+-------------+-----------+-------------+--------------+
| label|age|handsome|salary|programmer|label_index|          features|rawPrediction|probability|predict_index|predict_string|
+------+---+--------+------+----------+-----------+------------------+-------------+-----------+-------------+--------------+
|不见面| 21|       1|     1|         0|        0.0|[21.0,1.0,1.0,0.0]|    [0.0,3.0]|  [0.0,1.0]|          1.0|          见面|
|不见面| 29|       0|     1|         1|        0.0|[29.0,0.0,1.0,1.0]|    [1.0,0.0]|  [1.0,0.0]|          0.0|        不见面|
|  见面| 27|       1|     2|         1|        1.0|[27.0,1.0,2.0,1.0]|    [0.0,4.0]|  [0.0,1.0]|          1.0|          见面|
+------+---+--------+------+----------+-----------+------------------+-------------+-----------+-------------+--------------+

模型决策过程为 :
 DecisionTreeClassificationModel: uid=DecisionTreeClassifier_50d5fdd7f108, depth=4, numNodes=13, numClasses=2, numFeatures=4
  If (feature 0 <= 24.5)
   Predict: 1.0
  Else (feature 0 > 24.5)
   If (feature 3 <= 0.5)
    If (feature 0 <= 30.5)
     If (feature 0 <= 27.0)
      Predict: 0.0
     Else (feature 0 > 27.0)
      Predict: 1.0
    Else (feature 0 > 30.5)
     Predict: 0.0
   Else (feature 3 > 0.5)
    If (feature 1 <= 0.5)
     Predict: 0.0
    Else (feature 1 > 0.5)
     If (feature 2 <= 0.5)
      Predict: 0.0
     Else (feature 2 > 0.5)
      Predict: 1.0

测试集 错误率为: 0.333333 

Process finished with exit code 0

```

![截屏2022-07-19 15.29.08](%E5%86%B3%E7%AD%96%E6%A0%91-%E7%9B%B8%E4%BA%B2%E5%86%B3%E7%AD%96%E6%A1%88%E4%BE%8B.assets/%E6%88%AA%E5%B1%8F2022-07-19%2015.29.08-7050379.jpg)

