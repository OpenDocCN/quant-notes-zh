# Python金融量化+股票交易：P55：聚类分析实例 📊

![](img/5f4d4f711b0460d8e97f660b9ecdbf38_1.png)

![](img/5f4d4f711b0460d8e97f660b9ecdbf38_3.png)

在本节课中，我们将学习如何在Python中使用聚类分析方法进行股票数据分析。我们将通过一个实例，演示如何利用K-Means算法对股票特征进行聚类，并评估其效果。

![](img/5f4d4f711b0460d8e97f660b9ecdbf38_5.png)

---

![](img/5f4d4f711b0460d8e97f660b9ecdbf38_7.png)

## 导入工具包

![](img/5f4d4f711b0460d8e97f660b9ecdbf38_9.png)

首先，我们需要导入必要的Python工具包。这些工具包与之前课程中使用的保持一致。

![](img/5f4d4f711b0460d8e97f660b9ecdbf38_11.png)

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
from sklearn.cluster import KMeans
```

![](img/5f4d4f711b0460d8e97f660b9ecdbf38_13.png)

---

## 数据准备

![](img/5f4d4f711b0460d8e97f660b9ecdbf38_15.png)

![](img/5f4d4f711b0460d8e97f660b9ecdbf38_17.png)

上一节我们导入了工具包，本节中我们来看看如何准备数据。我们将导入数据集，并从中提取用于建模的特征。

![](img/5f4d4f711b0460d8e97f660b9ecdbf38_19.png)

以下是数据准备步骤：
1.  导入包含股票指标的数据集。
2.  选择两个具体的特征列作为建模指标，例如“前一天指标”和“前两天指标”。

![](img/5f4d4f711b0460d8e97f660b9ecdbf38_21.png)

![](img/5f4d4f711b0460d8e97f660b9ecdbf38_23.png)

---

![](img/5f4d4f711b0460d8e97f660b9ecdbf38_25.png)

## 建立聚类模型

在准备好数据后，我们就可以开始建模了。本次任务与之前不同，我们将使用聚类方法而非回归方法。

在`sklearn`库中，`KMeans`是一个常用的聚类算法。首先，我们需要导入该模块并实例化模型。

以下是建立模型的步骤：
1.  从`sklearn.cluster`导入`KMeans`。
2.  实例化`KMeans`模型，并指定参数`n_clusters=2`，因为我们希望将数据点分为两类（例如，代表上涨和下跌）。

![](img/5f4d4f711b0460d8e97f660b9ecdbf38_27.png)

![](img/5f4d4f711b0460d8e97f660b9ecdbf38_29.png)

```python
# 实例化K-Means模型，设定簇的数量为2
model = KMeans(n_clusters=2)
```

---

![](img/5f4d4f711b0460d8e97f660b9ecdbf38_31.png)

## 模型训练与预测

![](img/5f4d4f711b0460d8e97f660b9ecdbf38_33.png)

模型实例化后，我们需要用数据来训练它，然后对数据进行预测，得到每个数据点所属的簇。

以下是训练与预测的步骤：
1.  使用`.fit()`方法，将选定的特征数据传入模型进行训练。
2.  使用`.predict()`方法，对相同数据进行预测，得到每个样本的簇标签（0或1）。
3.  为了更直观地表示涨跌，我们可以将预测的标签0和1映射为-1和1。

```python
# 训练模型
model.fit(data[['feature1', 'feature2']])

![](img/5f4d4f711b0460d8e97f660b9ecdbf38_35.png)

# 进行预测
predictions = model.predict(data[['feature1', 'feature2']])

# 将标签0和1映射为-1和1，以便于理解
data['cluster'] = np.where(predictions == 1, 1, -1)
```

![](img/5f4d4f711b0460d8e97f660b9ecdbf38_37.png)

![](img/5f4d4f711b0460d8e97f660b9ecdbf38_39.png)

---

![](img/5f4d4f711b0460d8e97f660b9ecdbf38_41.png)

![](img/5f4d4f711b0460d8e97f660b9ecdbf38_43.png)

## 结果可视化

![](img/5f4d4f711b0460d8e97f660b9ecdbf38_45.png)

![](img/5f4d4f711b0460d8e97f660b9ecdbf38_47.png)

为了直观地观察聚类效果，我们可以将数据点用散点图表示出来，并用不同颜色区分不同的簇。

以下是可视化步骤：
1.  以两个特征为X轴和Y轴绘制散点图。
2.  使用`c`参数，根据`cluster`列的值（-1或1）为点着色。

![](img/5f4d4f711b0460d8e97f660b9ecdbf38_49.png)

![](img/5f4d4f711b0460d8e97f660b9ecdbf38_51.png)

```python
plt.scatter(data['feature1'], data['feature2'], c=data['cluster'], cmap='bwr')
plt.xlabel('Feature 1 (前一天指标)')
plt.ylabel('Feature 2 (前两天指标)')
plt.title('K-Means聚类结果')
plt.show()
```

从图中可以看到，数据被分成了红色和蓝色两个簇。例如，当两个特征值都为负（代表连续下跌）时，数据点可能更多地集中在代表“下跌”的簇中。

![](img/5f4d4f711b0460d8e97f660b9ecdbf38_53.png)

---

![](img/5f4d4f711b0460d8e97f660b9ecdbf38_55.png)

## 策略效果评估

![](img/5f4d4f711b0460d8e97f660b9ecdbf38_57.png)

![](img/5f4d4f711b0460d8e97f660b9ecdbf38_59.png)

现在，我们有了基于聚类的分类标签（-1和1），接下来需要评估这个简单的聚类策略是否有效。我们将计算如果按照这个标签进行交易（例如，标签为1时买入，为-1时卖出或做空），最终的收益情况。

![](img/5f4d4f711b0460d8e97f660b9ecdbf38_61.png)

以下是评估步骤：
1.  计算原始收益率序列。
2.  将聚类预测的标签（-1, 1）作为交易信号，计算策略收益率。
3.  对比策略收益率与原始（或基准）收益率的累计收益。

![](img/5f4d4f711b0460d8e97f660b9ecdbf38_63.png)

```python
# 计算策略收益率：信号 * 原始收益率
data['strategy_returns'] = data['cluster'] * data['returns']

![](img/5f4d4f711b0460d8e97f660b9ecdbf38_65.png)

![](img/5f4d4f711b0460d8e97f660b9ecdbf38_67.png)

# 计算累计收益（假设‘returns’是日收益率）
cumulative_returns = (1 + data['returns']).cumprod()
cumulative_strategy_returns = (1 + data['strategy_returns']).cumprod()

![](img/5f4d4f711b0460d8e97f660b9ecdbf38_69.png)

![](img/5f4d4f711b0460d8e97f660b9ecdbf38_71.png)

# 绘制对比图
plt.plot(cumulative_returns, label='基准收益')
plt.plot(cumulative_strategy_returns, label='聚类策略收益')
plt.legend()
plt.show()
```

![](img/5f4d4f711b0460d8e97f660b9ecdbf38_73.png)

需要注意的是，K-Means聚类具有随机性，每次运行结果可能略有不同，导致策略收益不稳定。在实际应用中，聚类分析由于缺乏明确的标签和难以进行严谨的经济学解释，通常不作为首选的量化策略开发方法，更多是在探索性数据分析或无监督场景下使用。

![](img/5f4d4f711b0460d8e97f660b9ecdbf38_75.png)

---

![](img/5f4d4f711b0460d8e97f660b9ecdbf38_77.png)

![](img/5f4d4f711b0460d8e97f660b9ecdbf38_79.png)

## 总结

![](img/5f4d4f711b0460d8e97f660b9ecdbf38_81.png)

![](img/5f4d4f711b0460d8e97f660b9ecdbf38_83.png)

本节课中我们一起学习了如何在金融数据分析中应用K-Means聚类算法。我们从数据准备开始，逐步完成了模型建立、训练预测、结果可视化以及简单的策略效果评估。需要记住的是，聚类是一种无监督学习方法，在量化交易中直接应用存在解释性弱和结果不稳定的局限，但它仍然是探索数据内在结构的有力工具。