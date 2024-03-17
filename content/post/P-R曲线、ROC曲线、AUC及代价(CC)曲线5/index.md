---
date: 2024-03-17
tags:
  - Example
  - Tag
title: P-R曲线、ROC曲线、AUC及代价(CC)曲线5
description: 
slug: 23:15
share: true
canonicalURL: ""
keywords:
  - 关键字1
  - 关键字2
series: 系列
filename: index
lastmod: 
lang: cn
cover:
    image: 'https://cos-1-1304819371.cos.ap-beijing.myqcloud.com/pic-go/WeChat735b0c9bb69b5ba653e29a813f6053dc.png?imageSlim'
author: 
dir: post/P-R曲线、ROC曲线、AUC及代价(CC)曲线5
categories:
  - Example Category
weight: 1
---


**

## P-R曲线

**
解释：P为查准率(precision)，表示真正例数占预测正例数的比例。
			R为召回率(recall)，表示真正例数占实际正例数的比例。
			根据分类结果建立混淆矩阵，如下图所示
			![](https://cos-1-1304819371.cos.ap-beijing.myqcloud.com/pic-go/WeChat735b0c9bb69b5ba653e29a813f6053dc.png?imageSlim)
进一步定义，查准率P=TP/(TP+FP)，查全率R=TP/(TP+FN)

由西瓜书关于P-R曲线的描述：根据学习器的预测为正例的概率对样例进行排序，排在前边的样本更有可能为正例，然后按此顺序逐个将样本作为正例进行预测，计算出每次的查全率、查准率，最终作出P-R曲线图。

### Code：

法一，可自行模拟上诉过程

```python
import numpy as np
from matplotlib.font_manager import FontProperties
import matplotlib.pyplot as plt

#0-坏瓜 1-好瓜
Data=np.round(np.random.uniform(0,1,1000)).tolist()
Probability=np.random.uniform(0,1,1000).tolist()
西瓜=list(zip(Data, Probability))
西瓜=sorted(西瓜,key=lambda x:x[1],reverse=True)
P=[]
R=[]
TPR=[]
FPR=[]
FNR=[]
for 好瓜 in range(1,1000):
    TP = 0
    FP=0
    TN=0
    FN = 0
    for idx in range(0,好瓜):
        if 西瓜[idx][0] == 1:
            TP=TP+1
        else:
            FP=FP+1
    for idx in range(好瓜,1000):
        if 西瓜[idx][0]==0:
            TN = TN + 1
        else:
            FN=FN+1
    P.append(TP/(TP+FP) if TP+FP!=0 else TP/(TP+FP+1))
    R.append(TP/(TP+FN) if TP+FN!=0 else TP/(TP+FN+1))
    TPR.append(TP/(TP+FN) if TP+FN!=0 else TP/(TP+FN+1))
    FPR.append(FP/(TN+FP) if TN+FP!=0 else FP/(TN+FP+1))
    FNR.append(1-TPR[-1])
font = FontProperties(fname=r"c:\windows\fonts\simsun.ttc", size=14)
plt.xlabel("查全率", fontproperties=font)
plt.ylabel("查准率", fontproperties=font)
plt.title('P-R曲线', fontproperties=font)
plt.plot(R,P)
f1=np.polyfit(R,P,4) #使用4次多项式拟合P-R曲线
predict_P=np.polyval(f1,R)
plt.plot(R,predict_P)
plt.show()
```
法二，使用scikit库函数直接求得P，R值

```python
import numpy as np
from matplotlib.font_manager import FontProperties
import matplotlib.pyplot as plt
from sklearn.metrics import precision_recall_curve, roc_curve, auc

Data=np.round(np.random.uniform(0,1,1000)).tolist()
Probability=np.random.uniform(0,1,1000).tolist()
P,R, thresholds = precision_recall_curve(Data, Probability)
font = FontProperties(fname=r"c:\windows\fonts\simsun.ttc", size=14)
plt.xlabel("查全率", fontproperties=font)
plt.ylabel("查准率", fontproperties=font)
plt.title('P-R曲线', fontproperties=font)
plt.plot(R,P)
f1=np.polyfit(R,P,4)
predict_P=np.polyval(f1,R)
plt.plot(R,predict_P)
plt.show()
```

**

## ROC曲线与AUC

**
解释：中文全称为受试者工作特征，同P-R曲线相似，衡纵坐标涉及假真例率FPR和真正例率TPR。AUC为ROC曲线下的面积。
两者分别定义为FPR=TP/(TP+FN)，FPR=FP/(TN+FP)。
绘制方法与P-R曲线类似，就此略过。AUC可通过scikit库函数直接求得或者利用以下公式求梯形和得到面积：
$$
AUC=\frac 1 2 \sum_{\mathclap{1\le i\le m-1}} (x_{i+1}-x_{i})·(y_{i+1}+y_{i})
$$

### Code：

法一，可自行模拟上诉过程

```python
import numpy as np
from matplotlib.font_manager import FontProperties
import matplotlib.pyplot as plt

#0-坏瓜 1-好瓜

Data=np.round(np.random.uniform(0,1,1000)).tolist()
Probability=np.random.uniform(0,1,1000).tolist()
西瓜=list(zip(Data, Probability))
西瓜=sorted(西瓜,key=lambda x:x[1],reverse=True)
P=[]
R=[]
TPR=[]
FPR=[]
FNR=[]
for 好瓜 in range(1,1000):
    TP = 0
    FP=0
    TN=0
    FN = 0
    for idx in range(0,好瓜):
        if 西瓜[idx][0] == 1:
            TP=TP+1
        else:
            FP=FP+1
    for idx in range(好瓜,1000):
        if 西瓜[idx][0]==0:
            TN = TN + 1
        else:
            FN=FN+1
    P.append(TP/(TP+FP) if TP+FP!=0 else TP/(TP+FP+1))
    R.append(TP/(TP+FN) if TP+FN!=0 else TP/(TP+FN+1))
    TPR.append(TP/(TP+FN) if TP+FN!=0 else TP/(TP+FN+1))
    FPR.append(FP/(TN+FP) if TN+FP!=0 else FP/(TN+FP+1))
    FNR.append(1-TPR[-1])
font = FontProperties(fname=r"c:\windows\fonts\simsun.ttc", size=14)
plt.xlabel("假正例率", fontproperties=font)
plt.ylabel("真正例率", fontproperties=font)
plt.title('ROC曲线', fontproperties=font)
plt.plot(FPR,TPR)
f2=np.polyfit(FPR,TPR,4)
predict_TPR=np.polyval(f2,FPR)
plt.plot(FPR,predict_TPR)
plt.show()
AUC2=0
for idx in range(1,len(FPR)):
    AUC2=AUC2+(FPR[idx]-FPR[idx-1])*(TPR[idx]+TPR[idx-1])/2
print("公式计算的AUC=",AUC2)
```

法二，使用scikit库函数直接求得TPR，FPR值

```python
import numpy as np
from matplotlib.font_manager import FontProperties
import matplotlib.pyplot as plt
from sklearn.metrics import precision_recall_curve, roc_curve, auc

Data=np.round(np.random.uniform(0,1,1000)).tolist()
Probability=np.random.uniform(0,1,1000).tolist()
P,R, thresholds = precision_recall_curve(Data, Probability)
font = FontProperties(fname=r"c:\windows\fonts\simsun.ttc", size=14)
FPR,TPR, thresholds  =  roc_curve(Data, Probability)
plt.xlabel("假正例率", fontproperties=font)
plt.ylabel("真正例率", fontproperties=font)
plt.title('ROC曲线', fontproperties=font)
plt.plot(FPR,TPR)
f2=np.polyfit(FPR,TPR,4)
predict_TPR=np.polyval(f2,FPR)
plt.plot(FPR,predict_TPR)
plt.show()
AUC = auc(FPR,TPR)
print("scikit库计算的AUC=",AUC)
```

**

## 代价(CC)曲线

**
解释：对于第i类样本预测为第j类样本的代价cost(i,j)，其中图中的横坐标为正例率代价，纵坐标为[0,1]范围内的归一化代价

根据分类结果建立混淆矩阵，如下图所示
![二分类代价矩阵](https://img-blog.csdnimg.cn/20200922085805528.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2ZseWluZ193aGFsZTE=,size_16,color_FFFFFF,t_70#pic_center)

对横纵坐标分别定义得到正例率代价P(+)cost=(p·cost(0,1))/(p·cost(0,1)+(1-p)·cost(1,0))，归一化代价cost(norm)=(FNR·p·cost(0,1)+FPR·(1-p)·cost(1,0))/(p·cost(0,1)+(1-p)·cost(1,0))，其中p为样例为正例的概率

可自行模拟上诉过程绘制代价曲线图，第一种方法是n条经$$(0,FPR_{i})，(1,FNR_{i})$$两点的直线所交得的y值最小曲线，第二种方法是根据定义模拟拟合代价曲线，横轴为取值为[0,1]的正例率概率代价：
$$P(+)cost=\frac {p·cost_{01}} {p·cost_{01}+(1-p)·cost_{10}}$$，
纵坐标为取值为[0,1]的归一化代价：
$$cost_{norm}=FNR·P(+)cost+FPR·(1-P(+)cost)=\frac {FNR·p·cost_{01}+FPR·(1-p)·cost_{10}} {p·cost_{01}+(1-p)·cost_{10}}$$；代价曲线围成的面积同上诉AUC的求法，计算梯形和的面积表示曲线下的面积：
$$
期望总体代价=\frac 1 2 \sum_{\mathclap{1\le i\le m-1}} (x_{i+1}-x_{i})·(y_{i+1}+y_{i})
$$
最终得到如下效果图：
![在这里插入图片描述](https://img-blog.csdnimg.cn/2020092609055550.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2ZseWluZ193aGFsZTE=,size_16,color_FFFFFF,t_70#pic_center)

### Code：

```python
import numpy as np
from matplotlib.font_manager import FontProperties
import matplotlib.pyplot as plt

#0-坏瓜 1-好瓜
Data=np.round(np.random.uniform(0,1,1000)).tolist()
Probability=np.random.uniform(0,1,1000).tolist()
西瓜=list(zip(Data, Probability))
西瓜=sorted(西瓜,key=lambda x:x[1],reverse=True)
P=[]
R=[]
TPR=[]
FPR=[]
FNR=[]
for 好瓜 in range(1,1000):
    TP = 0
    FP=0
    TN=0
    FN = 0
    for idx in range(0,好瓜):
        if 西瓜[idx][0] == 1:
            TP=TP+1
        else:
            FP=FP+1
    for idx in range(好瓜,1000):
        if 西瓜[idx][0]==0:
            TN = TN + 1
        else:
            FN=FN+1
    P.append(TP/(TP+FP) if TP+FP!=0 else TP/(TP+FP+1))
    R.append(TP/(TP+FN) if TP+FN!=0 else TP/(TP+FN+1))
    TPR.append(TP/(TP+FN) if TP+FN!=0 else TP/(TP+FN+1))
    FPR.append(FP/(TN+FP) if TN+FP!=0 else FP/(TN+FP+1))
    FNR.append(1-TPR[-1])
font = FontProperties(fname=r"c:\windows\fonts\simsun.ttc", size=14)
plt.xlabel('正例率代价', fontproperties=font)
plt.ylabel('归一化代价', fontproperties=font)
plt.title('代价曲线与期望总体代价', fontproperties=font)
for idx in range(len(FNR)):
    k=(FNR[idx]-FPR[idx])/(1-0)
    b=FPR[idx]
    x=np.arange(0,1.1,0.1)
    y=k*x+b
    plt.plot(x,y,color='blue')
pCost=[]
costNorm=[]
for x in np.arange(0,1,0.01):
    pCost.append(x)
    ymin=1
    for idx in range(len(FNR)):
        ymin=min(ymin,FNR[idx]*x+FPR[idx]*(1-x))
    costNorm.append(ymin)
plt.plot(pCost,costNorm,color='red')
plt.show()
期望总体代价=0
for idx in range(1,len(pCost)):
    期望总体代价=期望总体代价+(pCost[idx]-pCost[idx-1])*(costNorm[idx]+costNorm[idx-1])/2
print('期望总体代价=',期望总体代价)
```

## 补充

上述过程使用随机算法进行分类，故所绘制的曲线与书本中的情形有些许差距。为检验算法实现的正确性，可设置分类器全部预测正确的情况，即当概率为0~0.5区间时预测为坏瓜否则为好瓜，判断最终的曲线是否为直角线段即可。
