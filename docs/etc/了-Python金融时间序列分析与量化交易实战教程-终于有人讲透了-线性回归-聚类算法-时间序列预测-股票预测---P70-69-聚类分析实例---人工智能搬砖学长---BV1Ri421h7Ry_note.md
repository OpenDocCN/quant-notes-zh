# Python金融时间序列分析与量化交易实战教程：P70：69.聚类分析实例 📊

![](img/87ebc3ada7c9bdb2e319b70c5a8cf76f_1.png)

![](img/87ebc3ada7c9bdb2e319b70c5a8cf76f_3.png)

在本节课中，我们将学习如何在Python中使用聚类算法进行金融数据分析。我们将以K-Means算法为例，演示如何基于股票的历史指标特征进行聚类，并尝试将其应用于简单的交易策略中。

---

![](img/87ebc3ada7c9bdb2e319b70c5a8cf76f_5.png)

## 工具包导入

![](img/87ebc3ada7c9bdb2e319b70c5a8cf76f_7.png)

![](img/87ebc3ada7c9bdb2e319b70c5a8cf76f_9.png)

首先，我们需要导入所有必要的Python工具包。这些工具包与之前课程中使用的保持一致。

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
from sklearn.cluster import KMeans
```

![](img/87ebc3ada7c9bdb2e319b70c5a8cf76f_11.png)

---

![](img/87ebc3ada7c9bdb2e319b70c5a8cf76f_13.png)

## 数据准备与特征提取

上一节我们导入了工具包，本节中我们来看看如何准备数据。我们将从数据集中提取两个关键特征：股票前一天的指标和前两天的指标。这两个特征将作为我们聚类模型的输入。

![](img/87ebc3ada7c9bdb2e319b70c5a8cf76f_15.png)

以下是数据准备的步骤：

![](img/87ebc3ada7c9bdb2e319b70c5a8cf76f_17.png)

1.  加载数据集。
2.  选择“前一天指标”和“前两天指标”这两列作为特征数据。

![](img/87ebc3ada7c9bdb2e319b70c5a8cf76f_19.png)

```python
# 假设 `data` 是已经加载的Pandas DataFrame
features = data[['prev_day_indicator', 'prev_two_day_indicator']]
```

![](img/87ebc3ada7c9bdb2e319b70c5a8cf76f_21.png)

![](img/87ebc3ada7c9bdb2e319b70c5a8cf76f_23.png)

---

![](img/87ebc3ada7c9bdb2e319b70c5a8cf76f_25.png)

## 建立K-Means聚类模型

有了特征数据之后，我们就可以开始建模了。这次的任务与之前的回归分析不同，我们将使用聚类方法。在`sklearn`库中，`KMeans`是一个常用的聚类算法。

首先，我们导入`KMeans`模块并实例化一个模型。在实例化时，我们需要指定一个关键参数：**簇的数量（K值）**。在本例中，我们希望将数据点分为两类（例如，代表“涨”和“跌”的趋势），因此设置`n_clusters=2`。

```python
# 实例化K-Means模型，设定簇的数量为2
model = KMeans(n_clusters=2)
```

![](img/87ebc3ada7c9bdb2e319b70c5a8cf76f_27.png)

接着，我们使用`.fit()`方法，将准备好的特征数据传入模型进行训练。

![](img/87ebc3ada7c9bdb2e319b70c5a8cf76f_29.png)

```python
# 使用特征数据训练模型
model.fit(features)
```

模型训练完成后会显示一些参数，例如初始化中心点的方法、最大迭代次数等。这些参数的默认值通常能满足基本需求，如需深入了解可以查阅`sklearn`官方文档。

![](img/87ebc3ada7c9bdb2e319b70c5a8cf76f_31.png)

---

![](img/87ebc3ada7c9bdb2e319b70c5a8cf76f_33.png)

## 预测与结果处理

模型训练好后，我们可以用它来预测每个数据点属于哪个簇。预测结果会返回每个点对应的簇标签（0或1）。

以下是预测与结果处理的步骤：

1.  使用`.predict()`方法获得预测标签。
2.  为了更直观地理解，我们将标签0和1映射为-1和1（例如，-1代表“跌”，1代表“涨”）。

![](img/87ebc3ada7c9bdb2e319b70c5a8cf76f_35.png)

```python
# 预测每个数据点所属的簇
cluster_labels = model.predict(features)

![](img/87ebc3ada7c9bdb2e319b70c5a8cf76f_37.png)

# 将标签0和1映射为-1和1
data['cluster'] = np.where(cluster_labels == 1, 1, -1)
```

![](img/87ebc3ada7c9bdb2e319b70c5a8cf76f_39.png)

现在，数据集中新增了一列`cluster`，其值为1或-1，代表模型对每个样本点的分类结果。

![](img/87ebc3ada7c9bdb2e319b70c5a8cf76f_41.png)

![](img/87ebc3ada7c9bdb2e319b70c5a8cf76f_43.png)

---

![](img/87ebc3ada7c9bdb2e319b70c5a8cf76f_45.png)

## 结果可视化

![](img/87ebc3ada7c9bdb2e319b70c5a8cf76f_47.png)

![](img/87ebc3ada7c9bdb2e319b70c5a8cf76f_49.png)

为了直观地观察聚类效果，我们可以将数据点在二维平面上用散点图画出来，并用不同颜色区分不同的簇。

以下是绘制散点图的代码：

![](img/87ebc3ada7c9bdb2e319b70c5a8cf76f_51.png)

![](img/87ebc3ada7c9bdb2e319b70c5a8cf76f_53.png)

```python
# 绘制散点图，X轴为“前一天指标”，Y轴为“前两天指标”
plt.scatter(features.iloc[:, 0], features.iloc[:, 1], c=data['cluster'], cmap='bwr')
plt.xlabel('Previous Day Indicator')
plt.ylabel('Previous Two Days Indicator')
plt.title('K-Means Clustering Results')
plt.show()
```

![](img/87ebc3ada7c9bdb2e319b70c5a8cf76f_55.png)

在生成的图中，红色和蓝色的点分别代表两个不同的簇。通过观察可以发现，当两个指标均为负值时（即过去两天都下跌），数据点大多被分到了一个簇中，这可能暗示着某种趋势。

---

![](img/87ebc3ada7c9bdb2e319b70c5a8cf76f_57.png)

## 策略回测与效果评估

仅仅得到分类结果还不够，我们需要评估这个基于聚类的简单策略是否有效。一个基本的思路是：当模型预测为“涨”（标签为1）时，我们假设做多；预测为“跌”（标签为-1）时，我们假设做空或空仓。

![](img/87ebc3ada7c9bdb2e319b70c5a8cf76f_59.png)

![](img/87ebc3ada7c9bdb2e319b70c5a8cf76f_61.png)

以下是计算策略收益并与基准（原始收益）进行比较的步骤：

![](img/87ebc3ada7c9bdb2e319b70c5a8cf76f_63.png)

1.  计算根据聚类信号买卖所产生的收益序列。
2.  将收益序列从对数收益还原为简单收益。
3.  计算策略的累计收益，并与买入持有的基准收益进行对比。

![](img/87ebc3ada7c9bdb2e319b70c5a8cf76f_65.png)

![](img/87ebc3ada7c9bdb2e319b70c5a8cf76f_67.png)

```python
# 计算策略收益：聚类标签 * 日收益率
data['strategy_returns'] = data['cluster'].shift(1) * data['log_returns']

![](img/87ebc3ada7c9bdb2e319b70c5a8cf76f_69.png)

# 将对数收益还原为简单收益，并计算累计收益
cumulative_strategy_returns = np.exp(data['strategy_returns'].dropna()).cumprod()
cumulative_baseline_returns = np.exp(data['log_returns'].dropna()).cumprod()

![](img/87ebc3ada7c9bdb2e319b70c5a8cf76f_71.png)

# 输出最终收益对比
print(f"策略最终收益: {cumulative_strategy_returns.iloc[-1]:.4f}")
print(f"基准最终收益: {cumulative_baseline_returns.iloc[-1]:.4f}")
```

![](img/87ebc3ada7c9bdb2e319b70c5a8cf76f_73.png)

**注意**：由于K-Means算法初始中心点的随机性，每次运行的结果可能略有不同。有时策略可能表现优于基准，有时则可能更差。这正体现了无监督学习在金融预测中的一个挑战：**缺乏明确的标签使得模型评估和结果稳定性变得困难**。

![](img/87ebc3ada7c9bdb2e319b70c5a8cf76f_75.png)

---

![](img/87ebc3ada7c9bdb2e319b70c5a8cf76f_77.png)

![](img/87ebc3ada7c9bdb2e319b70c5a8cf76f_79.png)

## 总结

![](img/87ebc3ada7c9bdb2e319b70c5a8cf76f_81.png)

本节课中我们一起学习了如何在量化交易中使用聚类分析。

![](img/87ebc3ada7c9bdb2e319b70c5a8cf76f_83.png)

![](img/87ebc3ada7c9bdb2e319b70c5a8cf76f_85.png)

1.  **流程回顾**：我们完成了从数据准备、特征提取，到建立K-Means模型、进行预测和结果可视化的完整流程。
2.  **核心应用**：我们尝试将聚类得到的类别标签（1和-1）转化为简单的交易信号，并进行了初步的回测。
3.  **算法特点**：K-Means作为一种无监督学习算法，其优势在于能发现数据内在的结构。但在金融预测领域，由于其**结果的随机性**和**难以评估**的特性，通常不作为首选的预测工具。
4.  **实用建议**：聚类分析更适合用于探索性数据分析，例如识别不同的市场状态或投资者行为模式。在构建严谨的交易策略时，通常需要结合更稳健的监督学习方法或更复杂的模型。

![](img/87ebc3ada7c9bdb2e319b70c5a8cf76f_87.png)

通过本实例，你应当掌握了使用`sklearn`进行聚类分析的基本操作，并理解了其在金融数据分析中的潜在作用与局限性。