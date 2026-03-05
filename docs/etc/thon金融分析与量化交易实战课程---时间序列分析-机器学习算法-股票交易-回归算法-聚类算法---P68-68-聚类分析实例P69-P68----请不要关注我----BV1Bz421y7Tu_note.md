# 人生苦短，我学量化！：P68：聚类分析实例

![](img/8d8a36da49bc4b03fa606e98ea24adf9_0.png)

![](img/8d8a36da49bc4b03fa606e98ea24adf9_2.png)

## 概述
在本节课中，我们将学习如何在Python中使用聚类算法进行金融数据分析。我们将以K-Means算法为例，演示如何对股票数据进行聚类，并根据聚类结果构建一个简单的交易策略。

![](img/8d8a36da49bc4b03fa606e98ea24adf9_4.png)

---

![](img/8d8a36da49bc4b03fa606e98ea24adf9_6.png)

![](img/8d8a36da49bc4b03fa606e98ea24adf9_8.png)

## 数据与工具包准备
上一节我们介绍了聚类的基本概念，本节中我们来看看如何用代码实现。

![](img/8d8a36da49bc4b03fa606e98ea24adf9_10.png)

首先，我们需要导入必要的Python工具包。

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
from sklearn.cluster import KMeans
```

![](img/8d8a36da49bc4b03fa606e98ea24adf9_12.png)

![](img/8d8a36da49bc4b03fa606e98ea24adf9_14.png)

工具包导入完成后，下一步是加载数据。我们使用与之前课程相同的数据集。

```python
# 假设数据已加载到变量 `data` 中
# 这里我们选择两个特征：前一天的指标和前两天的指标
features = data[['feature_1', 'feature_2']]
```

![](img/8d8a36da49bc4b03fa606e98ea24adf9_16.png)

---

![](img/8d8a36da49bc4b03fa606e98ea24adf9_18.png)

## 构建K-Means聚类模型
有了特征数据后，我们就可以开始建模了。这次的任务与之前的回归不同，我们将使用聚类方法。

![](img/8d8a36da49bc4b03fa606e98ea24adf9_20.png)

![](img/8d8a36da49bc4b03fa606e98ea24adf9_22.png)

在`sklearn`的聚类模块中，我们选择`KMeans`算法。首先需要实例化模型。

![](img/8d8a36da49bc4b03fa606e98ea24adf9_24.png)

![](img/8d8a36da49bc4b03fa606e98ea24adf9_26.png)

以下是实例化K-Means模型的代码，我们需要指定聚类的数量（K值）。

```python
# 实例化K-Means模型，设定聚类数量为2
model = KMeans(n_clusters=2)
```

在这个任务中，我们希望将数据点分为两类，分别对应“上涨”和“下跌”两种状态，因此设置`n_clusters=2`。

接下来，使用我们的特征数据来训练（拟合）这个模型。

![](img/8d8a36da49bc4b03fa606e98ea24adf9_28.png)

```python
# 使用特征数据拟合模型
model.fit(features)
```

![](img/8d8a36da49bc4b03fa606e98ea24adf9_30.png)

模型拟合完成后，会显示一些默认参数，例如初始化中心点的方法、最大迭代次数等。这些参数可以在`sklearn`的官方文档中查阅详细信息。

![](img/8d8a36da49bc4b03fa606e98ea24adf9_32.png)

---

![](img/8d8a36da49bc4b03fa606e98ea24adf9_34.png)

## 预测与结果处理
模型训练好后，我们可以用它来预测每个数据点属于哪个簇。

以下是预测并将结果添加到原始数据中的代码。

```python
# 预测每个数据点所属的簇
cluster_labels = model.predict(features)
# 将预测结果添加到数据中
data['cluster'] = cluster_labels
```

查看数据，预测结果可能是0和1。为了更直观，我们可以将其映射为1和-1，分别代表“看涨”和“看跌”。

![](img/8d8a36da49bc4b03fa606e98ea24adf9_36.png)

```python
# 将聚类标签0和1映射为1和-1
data['cluster'] = data['cluster'].apply(lambda x: 1 if x == 1 else -1)
```

![](img/8d8a36da49bc4b03fa606e98ea24adf9_38.png)

现在，数据中新增了一列`cluster`，其值为1或-1。

![](img/8d8a36da49bc4b03fa606e98ea24adf9_40.png)

![](img/8d8a36da49bc4b03fa606e98ea24adf9_42.png)

---

![](img/8d8a36da49bc4b03fa606e98ea24adf9_44.png)

![](img/8d8a36da49bc4b03fa606e98ea24adf9_46.png)

## 可视化聚类结果
为了直观地看到聚类效果，我们可以将数据点用散点图画出来，并用不同颜色区分不同的簇。

![](img/8d8a36da49bc4b03fa606e98ea24adf9_48.png)

![](img/8d8a36da49bc4b03fa606e98ea24adf9_50.png)

以下是绘制散点图的代码。

![](img/8d8a36da49bc4b03fa606e98ea24adf9_52.png)

```python
plt.scatter(features.iloc[:, 0],  # X轴：第一个特征
            features.iloc[:, 1],  # Y轴：第二个特征
            c=data['cluster'],    # 颜色由簇标签决定
            cmap='coolwarm')      # 使用红蓝渐变色系
plt.xlabel('Feature 1 (t-1)')
plt.ylabel('Feature 2 (t-2)')
plt.title('K-Means Clustering Results')
plt.show()
```

![](img/8d8a36da49bc4b03fa606e98ea24adf9_54.png)

在生成的图中，红色和蓝色的点分别代表被归为不同簇的数据。我们可以观察数据的分布模式，例如，当两个特征值都为负时（即过去两天都下跌），数据点可能更多地集中在某个簇中。

---

![](img/8d8a36da49bc4b03fa606e98ea24adf9_56.png)

## 评估聚类策略效果
现在，一个关键问题是：仅凭这个聚类结果构建的交易策略可靠吗？

![](img/8d8a36da49bc4b03fa606e98ea24adf9_58.png)

为了评估，我们可以计算一下，如果按照聚类标签进行交易（1代表买入，-1代表卖出），最终的收益情况如何。首先需要将收益率数据还原。

![](img/8d8a36da49bc4b03fa606e98ea24adf9_60.png)

![](img/8d8a36da49bc4b03fa606e98ea24adf9_62.png)

以下是计算策略收益的代码。

![](img/8d8a36da49bc4b03fa606e98ea24adf9_64.png)

```python
# 假设原始收益率列名为 ‘returns‘
# 计算聚类策略的收益：信号 * 收益率
data['strategy_returns'] = data['cluster'] * data['returns']
# 计算累计收益（假设之前对收益率取了对数，这里需还原）
cumulative_returns = (1 + data['strategy_returns']).cumprod()
cumulative_baseline = (1 + data['returns']).cumprod()
```

![](img/8d8a36da49bc4b03fa606e98ea24adf9_66.png)

![](img/8d8a36da49bc4b03fa606e98ea24adf9_68.png)

我们可以对比策略累计收益和基准（什么都不做）累计收益。

![](img/8d8a36da49bc4b03fa606e98ea24adf9_70.png)

```python
print(“基准累计收益：“, cumulative_baseline.iloc[-1])
print(“聚类策略累计收益：“, cumulative_returns.iloc[-1])
```

![](img/8d8a36da49bc4b03fa606e98ea24adf9_72.png)

![](img/8d8a36da49bc4b03fa606e98ea24adf9_74.png)

**注意**：由于K-Means算法初始中心点的随机性，每次运行的结果可能略有不同。有时策略收益可能优于基准，有时可能更差。这正体现了聚类分析在金融应用中的一个挑战：**结果不稳定且难以解释**。

![](img/8d8a36da49bc4b03fa606e98ea24adf9_76.png)

---

![](img/8d8a36da49bc4b03fa606e98ea24adf9_78.png)

## 总结
本节课中我们一起学习了如何在Python中使用K-Means算法进行聚类分析。

![](img/8d8a36da49bc4b03fa606e98ea24adf9_80.png)

![](img/8d8a36da49bc4b03fa606e98ea24adf9_82.png)

我们完成了从数据准备、模型构建、预测到结果可视化的全过程，并尝试用聚类结果构建了一个简单的交易策略进行效果评估。

![](img/8d8a36da49bc4b03fa606e98ea24adf9_84.png)

![](img/8d8a36da49bc4b03fa606e98ea24adf9_86.png)

需要强调的是，在量化交易实践中，**聚类分析通常不作为首选的策略开发方法**。主要原因是其结果具有随机性，且由于没有真实标签，很难对聚类结果进行严谨的经济学解释和效果评估。本节课的目的在于让大家熟悉聚类在`sklearn`中的操作流程，理解其基本应用。