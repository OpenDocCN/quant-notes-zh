# Python金融分析与量化交易实战：P55：聚类分析实例 📊

![](img/f5808e78f2b3aea2c0335c418d8c4e2b_1.png)

![](img/f5808e78f2b3aea2c0335c418d8c4e2b_3.png)

在本节课中，我们将学习如何在Python中使用聚类分析方法，特别是K-Means算法，来处理金融数据。我们将通过一个实例，演示如何根据股票的前期指标特征进行聚类，并初步评估其效果。

![](img/f5808e78f2b3aea2c0335c418d8c4e2b_5.png)

---

![](img/f5808e78f2b3aea2c0335c418d8c4e2b_7.png)

![](img/f5808e78f2b3aea2c0335c418d8c4e2b_9.png)

## 第一步：导入工具包 🧰

首先，我们需要导入必要的Python工具包。这些包与之前课程中使用的保持一致。

![](img/f5808e78f2b3aea2c0335c418d8c4e2b_11.png)

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
from sklearn.cluster import KMeans
```

![](img/f5808e78f2b3aea2c0335c418d8c4e2b_13.png)

---

![](img/f5808e78f2b3aea2c0335c418d8c4e2b_15.png)

## 第二步：准备数据 📈

![](img/f5808e78f2b3aea2c0335c418d8c4e2b_17.png)

上一节我们导入了工具包，本节中我们来看看如何准备数据。我们将导入数据集，并从中提取用于建模的特征。

![](img/f5808e78f2b3aea2c0335c418d8c4e2b_19.png)

![](img/f5808e78f2b3aea2c0335c418d8c4e2b_21.png)

以下是数据准备步骤：
1.  加载数据集。
2.  选择两个特征列：`‘前一天指标’` 和 `‘前两天指标’`。这两个指标将作为我们聚类模型的输入。

![](img/f5808e78f2b3aea2c0335c418d8c4e2b_23.png)

![](img/f5808e78f2b3aea2c0335c418d8c4e2b_25.png)

---

## 第三步：建立K-Means聚类模型 🤖

有了特征数据后，我们就可以开始建模了。之前我们使用的是回归方法，这次我们将尝试聚类方法。在`sklearn`库中，`KMeans`是实现K-Means聚类算法的模块。

以下是建立模型的步骤：
1.  从`sklearn.cluster`导入`KMeans`模块。
2.  实例化一个`KMeans`模型，并指定参数`n_clusters=2`。这意味着我们希望将数据点分成两个簇（例如，可能对应“上涨”和“下跌”两类情况）。
3.  使用`.fit()`方法，将我们准备好的特征数据传入模型进行训练。

![](img/f5808e78f2b3aea2c0335c418d8c4e2b_27.png)

核心代码：
```python
# 实例化K-Means模型，设定簇的数量为2
model = KMeans(n_clusters=2)
# 使用特征数据拟合模型
model.fit(data[['前一天指标', '前两天指标']])
```

![](img/f5808e78f2b3aea2c0335c418d8c4e2b_29.png)

---

![](img/f5808e78f2b3aea2c0335c418d8c4e2b_31.png)

## 第四步：获取聚类结果并调整 🔄

![](img/f5808e78f2b3aea2c0335c418d8c4e2b_33.png)

模型训练完成后，我们可以用它来预测每个数据点属于哪个簇。

以下是获取并调整结果的步骤：
1.  使用`.predict()`方法得到每个样本的聚类标签（0或1）。
2.  为了更直观地对应“上涨”（1）和“下跌”（-1），我们将预测标签进行映射转换：将标签1映射为1，标签0映射为-1。

```python
# 预测聚类标签
labels = model.predict(data[['前一天指标', '前两天指标']])
# 将标签0和1映射为-1和1
data['cluster_label'] = np.where(labels == 1, 1, -1)
```

![](img/f5808e78f2b3aea2c0335c418d8c4e2b_35.png)

---

![](img/f5808e78f2b3aea2c0335c418d8c4e2b_37.png)

## 第五步：可视化聚类结果 📊

![](img/f5808e78f2b3aea2c0335c418d8c4e2b_39.png)

为了直观地观察聚类效果，我们可以将数据点用散点图绘制出来，并用不同颜色区分不同的簇。

![](img/f5808e78f2b3aea2c0335c418d8c4e2b_41.png)

![](img/f5808e78f2b3aea2c0335c418d8c4e2b_43.png)

以下是可视化步骤：
1.  以`‘前一天指标’`为X轴，`‘前两天指标’`为Y轴绘制散点图。
2.  根据`‘cluster_label’`列的值（1或-1）为数据点着色，以区分两个簇。

![](img/f5808e78f2b3aea2c0335c418d8c4e2b_45.png)

![](img/f5808e78f2b3aea2c0335c418d8c4e2b_47.png)

```python
plt.scatter(data['前一天指标'], data['前两天指标'], c=data['cluster_label'], cmap='bwr')
plt.xlabel(‘前一天指标’)
plt.ylabel(‘前两天指标’)
plt.title(‘K-Means聚类结果’)
plt.show()
```

![](img/f5808e78f2b3aea2c0335c418d8c4e2b_49.png)

从图中可以观察两个簇的分布情况。例如，当两个指标均为负值（代表前期下跌）时，数据点可能更多地集中在某个簇中。

![](img/f5808e78f2b3aea2c0335c418d8c4e2b_51.png)

---

## 第六步：初步评估策略效果 ⚖️

![](img/f5808e78f2b3aea2c0335c418d8c4e2b_53.png)

现在，我们有了基于聚类的“涨跌”分类。接下来，我们简单地评估一下，如果按照这个分类结果进行交易（例如，预测为1时买入，预测为-1时卖出或做空），其收益表现如何。

![](img/f5808e78f2b3aea2c0335c418d8c4e2b_55.png)

![](img/f5808e78f2b3aea2c0335c418d8c4e2b_57.png)

以下是评估步骤：
1.  计算按照聚类标签进行交易的累计收益。
2.  与原始数据的基准收益（如买入并持有）进行对比。

![](img/f5808e78f2b3aea2c0335c418d8c4e2b_59.png)

```python
# 计算聚类策略的收益（假设标签1对应买入持有，标签-1对应卖出或做空带来的收益变化）
# 此处为简化示例，实际计算需结合具体的收益计算逻辑
strategy_returns = data[‘returns’] * data[‘cluster_label’]
cumulative_strategy_return = (1 + strategy_returns).cumprod()

![](img/f5808e78f2b3aea2c0335c418d8c4e2b_61.png)

![](img/f5808e78f2b3aea2c0335c418d8c4e2b_63.png)

# 计算基准收益
baseline_return = (1 + data[‘returns’]).cumprod()

![](img/f5808e78f2b3aea2c0335c418d8c4e2b_65.png)

![](img/f5808e78f2b3aea2c0335c418d8c4e2b_67.png)

# 绘制对比曲线
plt.plot(cumulative_strategy_return, label=‘聚类策略’)
plt.plot(baseline_return, label=‘基准收益’)
plt.legend()
plt.show()
```

![](img/f5808e78f2b3aea2c0335c418d8c4e2b_69.png)

![](img/f5808e78f2b3aea2c0335c418d8c4e2b_71.png)

**请注意**：由于K-Means聚类算法的初始中心点是随机选择的，每次运行的结果可能略有不同，这会导致策略收益评估存在一定的随机性。在实际应用中，聚类分析通常作为探索性工具，其直接用于交易决策的效果往往不稳定且难以进行严格的量化评估。

![](img/f5808e78f2b3aea2c0335c418d8c4e2b_73.png)

---

![](img/f5808e78f2b3aea2c0335c418d8c4e2b_75.png)

## 总结 📝

![](img/f5808e78f2b3aea2c0335c418d8c4e2b_77.png)

![](img/f5808e78f2b3aea2c0335c418d8c4e2b_79.png)

本节课中我们一起学习了如何在量化分析中应用K-Means聚类算法。
1.  我们回顾了从导入库、准备数据到建立模型的完整流程。
2.  我们使用`sklearn`的`KMeans`将数据点分为两个簇，并可视化了结果。
3.  我们初步探讨了将聚类结果转化为交易信号并评估其效果的方法，同时也指出了聚类方法在金融预测中存在的局限性（如随机性、缺乏可解释性等）。

![](img/f5808e78f2b3aea2c0335c418d8c4e2b_81.png)

![](img/f5808e78f2b3aea2c0335c418d8c4e2b_83.png)

聚类是一种无监督学习方法，适用于在缺乏明确标签时探索数据的内在结构。但在实际的量化交易策略开发中，它通常不作为核心预测模型，而是用于特征工程、市场状态划分等辅助环节。当传统有监督方法遇到瓶颈时，聚类可以提供一个不同的分析视角。