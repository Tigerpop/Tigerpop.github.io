---
layout: posts
title: PCA主成分分析
date: 2022-10-28 21:07:36
description: "这是文章开头，显示在主页面，详情请点击此处。"
categories: 
- "机器学习"
tags:
- "pca（Principal Component Analysis"
- "无监督学习(unsupervised learning)"
---


#%% md <!--more-->

# PCA在癌症基因组图谱数据中的应用
## 一、实验简介
### （一）问题描述
据统计，全球每年新增癌症患者达700万人，死于癌症的病人达500万人，60％的患者确诊后只能存活5年。目前已知的癌症有200多种，但是，无论什么癌症，在肿瘤的特殊类别（分型）或发展的不同分期方面都发现有基因组的特异变化，而正是基因组的改变（突变）导致了细胞分化、发育和生长通路的不正常，从而引发细胞不正常地失控增殖、生长。

通过应用基因组分析技术，特别是采用大规模的基因组测序，将人类全部癌症的基因组变异图谱绘制出来，并进行系统分析，旨在找到所有致癌和抑癌基因的微小变异，了解癌细胞发生、发展的机制，在此基础上取得新的诊断和治疗方法。

现如今，机器学习在数据分析领域已经慢慢走向成熟。如果可以用机器学习开发一套算法程序对癌症基因组图谱数据进行分析，辅助医生快速得出正确诊断结果，这将大大提高现代医学对癌症的诊断效率。

本实验借助了TCGA中的癌症基因组图谱数据，利用Sklearn机器学习库中的PCA算法对该数据集特征进行降维，然后利用SVM算法分别对降维前后的数据进行学习，最终使用这两个SVM算法模型对同一个新数据集的癌症种类进行对比分析。

#%% md

### （二）问题需求
本次癌症基因组图谱数据集记录了801个样本数据，其特征数据是由癌症患者的RPPA【评估外显子组序列】 、DNA methylation【DNA甲基化】、Copy number【拷贝数变化(通过SNP阵列测量)】、Mutation【转录本剪接变化】、microRNA【microRNA表达】指标的数据构成，其中每个指标有多个数据，每个样本总共有20532个属性，样本标签是癌症的类型，数据集中总共记录了5种癌症类型，分别用数字1，2，3，4，5表示。

数据文件所在位置："pca\data.csv" 。   
本实验首先对该数据集按8：2的比例划分出了训练集和测试集，由于癌症基因组图谱数据集特征比较多，这对算法的训练和预测都造成了巨大困难，所以对训练集和测试集的数据特征进行了PCA降维，之后对降维前后的数据集分别训练同一个分类算法模型，让训练好的算法模型去预测测试集数据，最后对测试集数据结果进行准确率评估。

#%% md

## 二、实验任务与要求
本实验调用Sklearn库中的PCA算法模块对癌症基因图谱泛癌症数据特征进行降维，用降维前后的数据分别来训练SVM算法模型，之后是用两个SVM模型对新的数据集进行预测分类，并对分类结果进行准确率评估，最后对PCA算法的降维效果进行分析。具体的实验要求如下：

1.观察数据：为了能初步了解数据特征的实际情况，可以观察前五条样本数据、每列数据的详细信息，以及数据中是否有空缺值。

2.数据处理：对观察数据阶段所提出的问题进行解决，之后将数据集按8:2划分为训练集和测试集，对训练集和测试集数据调用PCA算法进行降维并对该算法进行简单介绍。

3.PCA算法降维效果测试：使用降维前后的两个训练集分别对SVM算法模型进行训练，得到两个训练好的SVM算法模型对测试集数据进行预测，并对预测结果进行准确率评分，最后记录两个模型的训练、预测整个过程的时间。


#%% md

## 三、实验步骤
本实验使用癌症基因组图谱数据，经过PCA算法模型对数据进行降维，之后供SVM算法模型进行学习与分类，比较PCA降维的效果，主要由以下几个步骤构成：

1.加载相关的包。

2.加载数据。

3.数据处理。

4.PCA算法降维效果测试。

#%% md

### (一）加载相关的包
（1）加载numpy、pandas等包做数据处理。

（2）从sklearn.datasets和sklearn.model_selection中加载数据集和划分数据集。

（3）从sklearn.decomposition加载PCA算法模块对数据进行降维。

（4）从sklearn加载svm算法模块进行分类。

（5）从sklearn.metrics中加载accuracy_score评分的函数对模型分类结果计算准确率。

（6）加载time类记时。

#%%

import pandas as pd   #数据分析、处理包
import numpy as np   #矩阵、数组处理包
from sklearn.model_selection import train_test_split#数据划分函数
from sklearn.decomposition import PCA        #PCA降维算法 
from sklearn import svm   #支持向量机算法
from sklearn.metrics import accuracy_score   #准确率评分函数
import time      #时间戳模块

#%% md

### (二）加载数据

#%%

#读取癌症基因组图谱数据特征
filename_data = 'pca\data.csv'
df_feature = pd.read_csv(filename_data)
#读取癌症基因组图谱数据标签
filename_label = 'pca\labels.csv'
df_label = pd.read_csv(filename_label)

#%% md

### (三）数据处理

#%% md

#### 1.观察数据

#%%

df_feature.head()   #观察数据集中的前5条

#%% md

初步观察可知，数据集有20532列，其中第一列是样本的序号，不是样本特征，需要删除该列。

#%% md

由于算法模型需要的全是数值型数据，在数据预处理前必须要对每列数据类型进行了解，可以利用info()函数来观察数据集中每列的数据类型。

#%%

df_feature.info()

#%% md

从输出可以看到数据集所有列中只有一列是object类型，其余列都是float64类型，从上面所观察到数据集的前五列可知，该object类型是数据集的第一列。

#%% md

由上表结合数据介绍可以了解到，数据特征比较多，数据情况比较复杂，而且都是数值型数据，若想要进一步了解这些数据，可以利用Pandas中的describe()函数对每列数据的总数量、平均值、标准差、最小值、下四分位数、中位数、上四分位数、最大值进行计算并显示出来。

#%%

describe = df_feature.describe()#观察数据的整体信息 ，注意数据的大小、行列规模、数据类型等，便于后续数据处理。  
describe

#%% md

从上表中可以看到数据集中有几列数据中的值为0.0的非常多，甚至列gene_5数据中全是0（无效值），这只是能看到的数据，还有很多列是不能看到的，在机器学习中无效值太多不仅会对算法模型的训练时间、预测时间有影响，而且还会使训练出的算法模型效果比较差，当然，这个问题可以用PCA算法来解决。

#%% md

SVM算法模型对空缺值数据是比较敏感的，需要将空缺值检测出来并填充。由观察数据阶段可知，除了第一列数据类型其余列数据类型全是float64，如果出现空缺值只有np.nan这一种情况，下面利用Numpy库中的isnan()函数对数据类型为float64的列数据进行空缺值搜索，返回是否有np.nan的bool类型值。

#%%

np.any(np.isnan(df_feature.iloc[:,1:]))

#%% md

这里可以看到输出的是False，说明数据集中没有空缺值。

#%% md

#### 2.数据预处理

#%% md

（1）删除非特征列  
根据前面步骤所分析的，第一列数据不是样本特征，为了降低训练难度，需要将这些非特征的列进行删除，可以利用Pandas库中的drop()函数从数据集中删除第一列数据。

#%%

df_feature = df_feature.drop([df_feature.columns[0]],axis=1)

#%% md

（2）数据划分  
在不影响算法模型训练的情况下，使模型的评估更有可信度，可以将数据集按8：2划分为训练集和测试集，此处调用train_test_split(df_feature, df_label['Class_num'],test_size = 0.2)函数对数据进行划分。 

函数train_test_split()参数解释：

    第一个参数：样本特征数据；
    第二个参数：样本标签数据；
    第三个参数：训练集样本数据占总样本数据的比列；
    第四个参数：固定训练集和测试集的随机种子。

#%%

feature_train, feature_test, label_train, label_test = train_test_split(df_feature, df_label['Class_num'],test_size = 0.2,random_state=1)

#%% md

（3）PCA降维   
PCA（主成分分析）是一种常用的无监督学习数据分析算法。该算法通过线性变换将原始数据变换为一组各维度线性无关的表示，可用于提取数据的主要特征分量，以最大化投影后的方差，其次是投影产生的损失最小为原则对数据集进行降维，常用于高维数据的降维。    
由于前面观察数据时发现了数据中有大量的0值，这对算法的训练并没有什么好处，可以利用PCA降维算法进行处理，首先调用Sklearn库中的PCA(n_components)类创建PCA算法类对象（参数n_components是降维后的维度），其次，通过对象调用fit_transform()方法对训练集数据进行拟合，最后将拟合好的模型对数据进行降维。n_components可以设置一个较小的值，然后根据下面检验是否合理再不断增大该值。函数参数解释：

    第一个参数：需要降维的特征数据。
调用transform()方法对测试集数据进行降维。该函数参数解释：
    
    第一个参数：需要降维的特征数据。
注意transform()和fit_transform()函数返回的是数组，在下面索引行和索引列时需注意语法，数组中没有.iloc。

#%%

n_components=50      #设置降维后的维度
pca = PCA(n_components)    #实例化PCA降维类
feature_train_pca = pca.fit_transform(feature_train)  #对训练集数据进行PCA降维
feature_test_pca = pca.transform(feature_test)  #对测试集数据进行PCA降维

#%% md

检验降维是否合理：   
PCA算法是根据方差来降维的，因此可以利用降维后各成分方差情况来判断降维后的维数是否合理。PCA算法类的属性explained_variance_ratio_是降维后的各成分方差占比，比例越大，保留的信息越多，此处计算降维后各成分的方差占比之和，一般该值在70-95之间，从下面结果可看出样本数据降到50维是可以的。

#%%

sum(pca.explained_variance_ratio_)

#%% md

### (四）PCA算法降维效果测试

支持向量机（SVM）是一种二类分类模型，其基本模型定义为特征空间上的间隔最大的线性分类器，其学习策略是使间隔最大化，最终可转化为一个凸二次规划问题的求解。该算法不仅适用于线性问题，还适用于非线性问题(用核函数)，由于本次数据特征比较多，降维后数据间将不存在线性关系，因此这里选用SVM进行PCA算法来对PCA降维的效果进行测试。   

测试PCA降维效果需要使用降维前后的两个训练集分别对SVM算法模型进行训练，得到两个训练好的SVM算法模型对测试集数据进行预测，并对预测结果进行准确率评分。为了避免代码重复，此处编写一个函数test_model(feature_train, feature_test, label_train, label_test)实现SVM算法模型的调用、训练、预测以及预测结果评估，并对该过程的时间进行了记录，函数参数解释:

    feature_train：训练集特征数据；
    feature_test：测试集特征数据；
    label_train：训练集标签数据；
    label_test：测试集标签数。

#%%

def test_model(feature_train, feature_test, label_train, label_test):
    starttime = time.time()
    model = svm.SVC(kernel='sigmoid')
    model.fit(feature_train, label_train)
    #make prediction of the gene dataset 
    label_prediction = model.predict(feature_test)
    print('预测结果的准确率：',accuracy_score(label_test,label_prediction),'耗时:',time.time()-starttime)

#%% md

使用降维前的数据集对模型进行训练。

#%%

test_model(feature_train,feature_test,label_train,label_test)

#%% md

使用降维后的数据集对模型进行训练。

#%%

test_model(feature_train_pca,feature_test_pca,label_train,label_test)

#%% md

## 四、结果分析

本实验采用癌症基因组图谱数据进行PCA降维，使用降维前后的数据分别训练SVM算法模型，并评估模型对测试集数据分类的准确率。从上面的实验中可以看出：未进行PCA降维的数据训练出的SVM算法模型预测测试集，其预测结果的准确率只有34.2%，整个过程耗时达到17秒多；然而，经过降维之后的数据训练同一个模型对测试集进行预测，其预测结果可达到了90%左右的准确率，并且耗时仅需0.015秒。得到这个结果的可能原因是：首先，说明特征数量对训练和预测的时间会有明显的影响，特征数量越多其耗时越久；其次说明数据集中的无效值多，引起数据分类困难，使训练模型和模型预测的效果都会变差。而PCA算法以最大化投影后的方差，投影产生的损失最小为原则对数据集进行降维，这样不仅降低了数据分类的困难，而且还降低了数据的特征数量，因此进行PCA降维后的数据在模型的训练时间和预测结果上都要比未经降维的数据要优。

#%%

from sklearn.decomposition import PCA
from sklearn.datasets import load_iris


#%%

data = load_iris()

#%%

x = data.data
x

#%%

model = PCA(n_components=3)

#%%

model.fit(X=x)#训练模型

#%%

model.fit_transform(x)#降维后的数据

#%%
