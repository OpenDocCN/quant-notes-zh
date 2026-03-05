# 量化交易教程：P55：聚类分析实例 📊

![](img/a7888b4d61716b4204d13a3502fff1a2_1.png)

在本节课中，我们将学习如何在Python中使用聚类分析（特别是K-Means算法）来构建一个简单的量化交易策略。我们将从数据导入开始，逐步完成特征选择、模型构建、结果预测和可视化，并最终评估策略的收益表现。

![](img/a7888b4d61716b4204d13a3502fff1a2_3.png)

---

![](img/a7888b4d61716b4204d13a3502fff1a2_5.png)

## 工具包与数据导入

![](img/a7888b4d61716b4204d13a3502fff1a2_7.png)

首先，我们需要导入所有必要的Python工具包。这些工具包与之前课程中使用的一致，用于数据处理、建模和可视化。

![](img/a7888b4d61716b4204d13a3502fff1a2_9.png)

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
from sklearn.cluster import KMeans
```

接下来，我们导入本课要使用的数据。上节课提到，我们需要从数据中提取一些特征用于建模。

![](img/a7888b4d61716b4204d13a3502fff1a2_11.png)

```python
# 假设数据已加载到变量 `data` 中
# 这里我们选择两个特征：前一天的指标和前两天的指标
features = data[['feature_prev_day', 'feature_two_days_ago']]
```

![](img/a7888b4d61716b4204d13a3502fff1a2_13.png)

---

## 构建K-Means聚类模型

![](img/a7888b4d61716b4204d13a3502fff1a2_15.png)

上一节我们导入了数据和特征，本节中我们来看看如何使用K-Means算法进行聚类建模。与之前使用的回归方法不同，聚类是一种无监督学习方法。

![](img/a7888b4d61716b4204d13a3502fff1a2_17.png)

以下是构建K-Means模型的步骤：

![](img/a7888b4d61716b4204d13a3502fff1a2_19.png)

1.  **导入KMeans模块**：我们从`sklearn.cluster`中导入`KMeans`类。
2.  **实例化模型**：创建一个KMeans模型实例。关键参数`n_clusters`需要指定，它代表我们希望将数据分成多少个簇（K值）。
3.  **拟合模型**：使用`.fit()`方法，将我们选定的特征数据传入模型进行训练。

![](img/a7888b4d61716b4204d13a3502fff1a2_21.png)

![](img/a7888b4d61716b4204d13a3502fff1a2_23.png)

在我们的任务中，目标是将数据点分为两类（例如，代表“上涨”和“下跌”），因此设置`n_clusters=2`。

![](img/a7888b4d61716b4204d13a3502fff1a2_25.png)

```python
# 实例化K-Means模型，设定簇的数量为2
model = KMeans(n_clusters=2, random_state=42)

# 使用选定的特征数据拟合模型
model.fit(features)
```

模型拟合后，会显示一些默认参数，例如初始化中心点的方法、最大迭代次数和模型重复运行的次数。这些参数的详细信息可以在scikit-learn官方文档中查阅。

---

## 预测与结果处理

![](img/a7888b4d61716b4204d13a3502fff1a2_27.png)

![](img/a7888b4d61716b4204d13a3502fff1a2_29.png)

模型训练完成后，我们可以用它来预测每个数据点属于哪个簇。

```python
# 对数据进行预测，得到每个样本的簇标签（0或1）
cluster_labels = model.predict(features)

![](img/a7888b4d61716b4204d13a3502fff1a2_31.png)

# 将预测结果添加到原始数据中
data['cluster'] = cluster_labels
```

![](img/a7888b4d61716b4204d13a3502fff1a2_33.png)

默认情况下，K-Means的预测结果是0和1。为了更直观地对应“上涨”（1）和“下跌”（-1），我们可以对标签进行映射转换。

```python
# 将簇标签0映射为-1（代表下跌），标签1保持不变（代表上涨）
data['cluster'] = data['cluster'].apply(lambda x: -1 if x == 0 else 1)
```

处理完成后，数据中新增的`cluster`列就包含了我们需要的分类信号。

---

## 结果可视化

![](img/a7888b4d61716b4204d13a3502fff1a2_35.png)

为了直观地查看聚类效果，我们可以将数据点在二维平面上用散点图画出来，并用不同颜色区分不同的簇。

![](img/a7888b4d61716b4204d13a3502fff1a2_37.png)

以下是绘制散点图的代码：

![](img/a7888b4d61716b4204d13a3502fff1a2_39.png)

```python
# 绘制散点图
plt.figure(figsize=(8, 6))
# 根据‘cluster’列的值分配颜色
plt.scatter(data['feature_prev_day'], data['feature_two_days_ago'], c=data['cluster'], cmap='coolwarm', alpha=0.6)
plt.xlabel('Feature (Previous Day)')
plt.ylabel('Feature (Two Days Ago)')
plt.title('K-Means Clustering Results')
plt.colorbar(label='Cluster (-1: Down, 1: Up)')
plt.grid(True, linestyle='--', alpha=0.5)
plt.show()
```

![](img/a7888b4d61716b4204d13a3502fff1a2_41.png)

在生成的图中，红色和蓝色的点分别代表两个不同的簇。通过观察可以发现，当两个特征值都为负（即过去两天都下跌）时，数据点更可能被分到代表“下跌”的簇中，这暗示了某种趋势。

![](img/a7888b4d61716b4204d13a3502fff1a2_43.png)

![](img/a7888b4d61716b4204d13a3502fff1a2_45.png)

---

![](img/a7888b4d61716b4204d13a3502fff1a2_47.png)

## 策略回测与评估

![](img/a7888b4d61716b4204d13a3502fff1a2_49.png)

现在，我们有了基于聚类的交易信号（`cluster`列），接下来需要评估这个策略的收益表现。我们将计算并比较单纯持有原始资产与遵循聚类信号进行交易的累计收益。

![](img/a7888b4d61716b4204d13a3502fff1a2_51.png)

首先，我们需要确保收益率数据是正确的（如果之前进行过对数处理，需要先还原）。

```python
# 假设原始收益率列名为 ‘returns’， 并已进行对数处理
# 将其还原为普通收益率
data['returns_exp'] = np.exp(data['returns']) - 1

# 计算聚类策略的收益率：当信号为1时做多，为-1时做空（或视为反向信号）
data['strategy_returns'] = data['cluster'].shift(1) * data['returns_exp']  # 使用前一天的信号交易今天的收益
```

![](img/a7888b4d61716b4204d13a3502fff1a2_53.png)

然后，我们计算并对比两种收益的累计总和。

![](img/a7888b4d61716b4204d13a3502fff1a2_55.png)

```python
# 计算累计收益
cumulative_returns_buy_hold = data['returns_exp'].cumsum()
cumulative_returns_strategy = data['strategy_returns'].cumsum()

![](img/a7888b4d61716b4204d13a3502fff1a2_57.png)

![](img/a7888b4d61716b4204d13a3502fff1a2_59.png)

# 打印最终累计收益
print(f"买入持有策略累计收益: {cumulative_returns_buy_hold.iloc[-1]:.4f}")
print(f"聚类交易策略累计收益: {cumulative_returns_strategy.iloc[-1]:.4f}")
```

![](img/a7888b4d61716b4204d13a3502fff1a2_61.png)

**请注意**：由于K-Means聚类中心初始化的随机性，每次运行代码得到的结果和收益可能会有所不同。有时策略收益可能优于买入持有，有时则可能更差。这正体现了聚类方法在金融预测中的不确定性和挑战。

![](img/a7888b4d61716b4204d13a3502fff1a2_63.png)

---

![](img/a7888b4d61716b4204d13a3502fff1a2_65.png)

## 关于聚类分析的讨论

![](img/a7888b4d61716b4204d13a3502fff1a2_67.png)

![](img/a7888b4d61716b4204d13a3502fff1a2_69.png)

从上述实验可以看出，聚类分析得到的结果具有一定的随机性，且难以像有监督学习那样进行严格的评估和归因分析。在量化交易的实际应用中，聚类分析通常不是首选的策略构建方法。

![](img/a7888b4d61716b4204d13a3502fff1a2_71.png)

它主要适用于以下情况：
*   探索性数据分析，发现数据中潜在的结构或模式。
*   在缺乏明确标签或先验知识时，作为一种尝试性方法。
*   作为更复杂策略中的一个特征生成或预处理步骤。

![](img/a7888b4d61716b4204d13a3502fff1a2_73.png)

因此，本节课学习聚类分析的目的，主要是让大家掌握在Python中实现聚类的基本流程，并理解其作为量化工具的优点与局限性。

![](img/a7888b4d61716b4204d13a3502fff1a2_75.png)

---

![](img/a7888b4d61716b4204d13a3502fff1a2_77.png)

## 总结

![](img/a7888b4d61716b4204d13a3502fff1a2_79.png)

![](img/a7888b4d61716b4204d13a3502fff1a2_81.png)

本节课中我们一起学习了以下内容：
1.  **导入工具与数据**：准备了进行聚类分析所需的Python环境和特征数据。
2.  **构建K-Means模型**：使用`sklearn`的`KMeans`类，通过指定簇数来建立聚类模型。
3.  **进行预测与后处理**：用模型预测数据点归属，并将结果映射为有业务含义的交易信号（如1和-1）。
4.  **可视化聚类结果**：通过散点图直观展示数据如何被分为不同的簇。
5.  **策略回测与评估**：基于聚类信号构建简单交易策略，并计算其收益以评估效果。
6.  **认识方法的局限性**：理解了聚类分析在量化交易中因随机性和难以评估而存在的应用限制。

![](img/a7888b4d61716b4204d13a3502fff1a2_83.png)

通过这个实例，你不仅学会了如何用代码实现聚类分析，更重要的是理解了如何批判性地看待和评估一个量化模型的实际效果。