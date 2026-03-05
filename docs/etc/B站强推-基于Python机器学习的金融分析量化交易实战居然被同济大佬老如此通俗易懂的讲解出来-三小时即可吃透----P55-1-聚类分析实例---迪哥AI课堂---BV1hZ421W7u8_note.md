# 金融量化分析：P55：聚类分析实例 📊

![](img/1712e3c2576c0b993720ce0e539b0ffb_1.png)

![](img/1712e3c2576c0b993720ce0e539b0ffb_3.png)

在本节课中，我们将学习如何在Python中使用聚类分析方法进行金融量化分析。我们将通过一个实例，演示如何利用K-Means算法对股票数据进行聚类，并尝试基于聚类结果构建简单的交易策略。

![](img/1712e3c2576c0b993720ce0e539b0ffb_5.png)

---

![](img/1712e3c2576c0b993720ce0e539b0ffb_7.png)

## 概述

![](img/1712e3c2576c0b993720ce0e539b0ffb_9.png)

上一节我们介绍了回归分析在量化交易中的应用。本节中，我们来看看另一种无监督学习方法——聚类分析。我们将使用K-Means算法，根据股票前两日的指标特征，将数据点划分为不同的簇，并尝试解读其与市场涨跌的潜在关联。

![](img/1712e3c2576c0b993720ce0e539b0ffb_11.png)

---

![](img/1712e3c2576c0b993720ce0e539b0ffb_13.png)

## 步骤一：导入工具包

首先，我们需要导入必要的Python工具包。这些包与之前课程中使用的保持一致。

![](img/1712e3c2576c0b993720ce0e539b0ffb_15.png)

![](img/1712e3c2576c0b993720ce0e539b0ffb_17.png)

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
from sklearn.cluster import KMeans
```

![](img/1712e3c2576c0b993720ce0e539b0ffb_19.png)

---

![](img/1712e3c2576c0b993720ce0e539b0ffb_21.png)

## 步骤二：加载与准备数据

![](img/1712e3c2576c0b993720ce0e539b0ffb_23.png)

![](img/1712e3c2576c0b993720ce0e539b0ffb_25.png)

接下来，我们导入要分析的数据集。根据上节课的讨论，我们需要从数据中提取用于建模的特征。

以下是数据准备步骤：
*   我们选择了两个特征：股票前一天的指标和前两天的指标。
*   这两个指标将作为K-Means聚类模型的输入。

---

## 步骤三：建立K-Means聚类模型

![](img/1712e3c2576c0b993720ce0e539b0ffb_27.png)

与之前使用的回归方法不同，本次任务我们采用聚类方法。我们将使用`sklearn`库中的`KMeans`模块。

![](img/1712e3c2576c0b993720ce0e539b0ffb_29.png)

以下是建立模型的步骤：
1.  导入`KMeans`模块。
2.  实例化一个K-Means模型。在实例化时，需要指定一个关键参数`n_clusters`，即我们希望将数据聚成多少个簇（K值）。
3.  在本例中，我们的目标相对简单：试图区分上涨和下跌两种情况。因此，我们将`n_clusters`参数设置为2。

![](img/1712e3c2576c0b993720ce0e539b0ffb_31.png)

```python
# 实例化K-Means模型，设定簇的数量为2
model = KMeans(n_clusters=2)
```

![](img/1712e3c2576c0b993720ce0e539b0ffb_33.png)

---

## 步骤四：训练模型与生成预测

模型实例化后，我们需要用数据来训练（拟合）它，然后得到每个数据点的聚类标签。

以下是具体操作：
1.  使用`fit`方法，将准备好的特征数据传入模型进行训练。
2.  训练完成后，使用`predict`方法获取每个数据点所属的簇（标签为0或1）。
3.  为了更直观地表示涨跌，我们将聚类标签进行映射：将其中一个簇的标签改为1（代表上涨），另一个改为-1（代表下跌）。

![](img/1712e3c2576c0b993720ce0e539b0ffb_35.png)

```python
# 使用特征数据训练模型
model.fit(data[['feature1', 'feature2']])

![](img/1712e3c2576c0b993720ce0e539b0ffb_37.png)

# 预测每个数据点所属的簇
data['cluster'] = model.predict(data[['feature1', 'feature2']])

![](img/1712e3c2576c0b993720ce0e539b0ffb_39.png)

![](img/1712e3c2576c0b993720ce0e539b0ffb_41.png)

# 将聚类标签0和1映射为1和-1，以表示涨跌方向
data['cluster'] = data['cluster'].where(data['cluster'] == 1, -1)
```

![](img/1712e3c2576c0b993720ce0e539b0ffb_43.png)

![](img/1712e3c2576c0b993720ce0e539b0ffb_45.png)

---

![](img/1712e3c2576c0b993720ce0e539b0ffb_47.png)

## 步骤五：可视化聚类结果

![](img/1712e3c2576c0b993720ce0e539b0ffb_49.png)

为了直观地观察聚类效果，我们可以将数据点在二维平面上用散点图表示出来，并用不同颜色区分不同的簇。

![](img/1712e3c2576c0b993720ce0e539b0ffb_51.png)

以下是绘图代码：
```python
plt.scatter(data['feature1'], data['feature2'], c=data['cluster'], cmap='bwr')
plt.xlabel('前一日指标')
plt.ylabel('前两日指标')
plt.title('K-Means聚类结果')
plt.show()
```
在生成的图中，蓝色和红色点分别代表两个不同的簇。我们可以观察特征分布与簇划分之间的关系。例如，当两个特征值均为负（代表连续下跌）时，数据点可能更多地集中在代表“下跌”的簇中。

---

![](img/1712e3c2576c0b993720ce0e539b0ffb_53.png)

## 步骤六：评估聚类策略的收益

![](img/1712e3c2576c0b993720ce0e539b0ffb_55.png)

现在，我们基于聚类结果构建一个简单的策略：当预测标签为1时，我们假设次日上涨；当标签为-1时，假设次日下跌。然后计算这个策略的累积收益，并与基准（如买入持有）进行比较。

![](img/1712e3c2576c0b993720ce0e539b0ffb_57.png)

![](img/1712e3c2576c0b993720ce0e539b0ffb_59.png)

以下是计算收益的步骤：
1.  计算根据聚类信号产生的每日收益。
2.  将收益累加，得到策略的累积收益曲线。
3.  需要注意的是，由于K-Means聚类中心初始化具有随机性，每次运行的结果可能略有不同，因此可能需要多次尝试以观察结果的稳定性。

![](img/1712e3c2576c0b993720ce0e539b0ffb_61.png)

![](img/1712e3c2576c0b993720ce0e539b0ffb_63.png)

```python
# 计算策略收益：聚类标签 * 次日实际收益率
data['strategy_returns'] = data['cluster'].shift(1) * data['actual_returns']

![](img/1712e3c2576c0b993720ce0e539b0ffb_65.png)

# 计算累积收益
cumulative_strategy_returns = (1 + data['strategy_returns']).cumprod()
cumulative_baseline_returns = (1 + data['actual_returns']).cumprod()

![](img/1712e3c2576c0b993720ce0e539b0ffb_67.png)

# 绘制收益曲线对比
plt.plot(cumulative_strategy_returns, label='聚类策略')
plt.plot(cumulative_baseline_returns, label='基准收益')
plt.legend()
plt.show()
```

![](img/1712e3c2576c0b993720ce0e539b0ffb_69.png)

![](img/1712e3c2576c0b993720ce0e539b0ffb_71.png)

**注意**：在实际应用中，聚类分析在量化交易中的直接应用并不广泛。主要原因是聚类属于无监督学习，缺乏明确的标签（即涨跌的真实结果），因此难以像分类或回归模型那样进行严格的性能评估和归因分析。本节课的目的是让大家熟悉聚类在金融数据分析中的基本操作流程和潜在问题。

![](img/1712e3c2576c0b993720ce0e539b0ffb_73.png)

---

![](img/1712e3c2576c0b993720ce0e539b0ffb_75.png)

![](img/1712e3c2576c0b993720ce0e539b0ffb_77.png)

## 总结

![](img/1712e3c2576c0b993720ce0e539b0ffb_79.png)

本节课中我们一起学习了以下内容：
1.  **聚类分析的应用**：了解了如何将K-Means聚类算法应用于金融时间序列数据。
2.  **完整操作流程**：从数据准备、模型建立、训练预测到结果可视化的完整步骤。
3.  **策略构建与评估**：尝试基于聚类结果构建简单的方向性交易策略，并计算其收益。
4.  **认识局限性**：理解了聚类分析在量化交易中面临的挑战，如结果随机性、缺乏明确评估标准等。

![](img/1712e3c2576c0b993720ce0e539b0ffb_81.png)

![](img/1712e3c2576c0b993720ce0e539b0ffb_83.png)

聚类分析通常作为探索性数据分析（EDA）的工具，或在其他方法效果有限时的一种尝试。在构建严谨的交易系统时，通常会更依赖于有监督学习方法。