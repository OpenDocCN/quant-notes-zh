# Python金融分析与量化交易实战：P67：聚类分析实例

![](img/bc665d63a2ba9ede6eb0e3bd44c86c88_1.png)

![](img/bc665d63a2ba9ede6eb0e3bd44c86c88_3.png)

在本节课中，我们将学习如何在Python中使用聚类方法进行金融数据分析。我们将通过一个实例，演示如何利用K-Means算法对股票数据进行聚类，并尝试基于聚类结果构建简单的交易策略。

![](img/bc665d63a2ba9ede6eb0e3bd44c86c88_5.png)

---

## 概述

![](img/bc665d63a2ba9ede6eb0e3bd44c86c88_7.png)

![](img/bc665d63a2ba9ede6eb0e3bd44c86c88_9.png)

上一节我们介绍了回归分析在量化交易中的应用。本节中，我们来看看另一种无监督学习方法——聚类分析。我们将使用K-Means算法，根据股票的前期表现特征将其分为不同的类别，并探讨这种分类方法在预测市场走势上的潜在应用。

---

![](img/bc665d63a2ba9ede6eb0e3bd44c86c88_11.png)

## 第一步：导入工具包

![](img/bc665d63a2ba9ede6eb0e3bd44c86c88_13.png)

首先，我们需要导入必要的Python工具包。这些包与之前课程中使用的保持一致。

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
from sklearn.cluster import KMeans
```

![](img/bc665d63a2ba9ede6eb0e3bd44c86c88_15.png)

---

![](img/bc665d63a2ba9ede6eb0e3bd44c86c88_17.png)

## 第二步：准备数据

![](img/bc665d63a2ba9ede6eb0e3bd44c86c88_19.png)

接下来，导入我们要分析的数据集。在上节课中，我们提到需要从数据中提取特征。在本例中，我们选择了两个特征：股票前一天的收益率指标和前两天的收益率指标。这两个指标将作为我们建模的输入。

![](img/bc665d63a2ba9ede6eb0e3bd44c86c88_21.png)

![](img/bc665d63a2ba9ede6eb0e3bd44c86c88_23.png)

```python
# 假设 `data` 是包含股票数据的DataFrame
# 我们选取 ‘feature_1’ (前一天指标) 和 ‘feature_2’ (前两天指标) 作为特征
features = data[['feature_1', 'feature_2']]
```

![](img/bc665d63a2ba9ede6eb0e3bd44c86c88_25.png)

---

## 第三步：建立K-Means聚类模型

与之前使用的回归方法不同，本次任务我们将使用聚类方法。在scikit-learn库中，有一个非常实用的算法叫做K-Means。

![](img/bc665d63a2ba9ede6eb0e3bd44c86c88_27.png)

以下是建立模型的第一步，导入KMeans模块并实例化模型。

```python
# 从sklearn导入KMeans
from sklearn.cluster import KMeans

![](img/bc665d63a2ba9ede6eb0e3bd44c86c88_29.png)

# 实例化KMeans模型，指定要聚成2类（K=2）
model = KMeans(n_clusters=2)
```

![](img/bc665d63a2ba9ede6eb0e3bd44c86c88_31.png)

在实例化模型时，我们需要指定一个关键参数 `n_clusters`，即K值，它代表我们希望将数据分成多少个簇。在本任务中，我们的目标相对简单：判断股票是上涨还是下跌。因此，我们指定K=2，将数据聚成两个簇。

---

![](img/bc665d63a2ba9ede6eb0e3bd44c86c88_33.png)

## 第四步：训练模型与预测

![](img/bc665d63a2ba9ede6eb0e3bd44c86c88_35.png)

模型实例化后，我们可以用数据来训练（拟合）模型，然后预测每个数据点所属的簇。

以下是训练模型并获取预测结果的步骤。

```python
# 使用特征数据拟合模型
model.fit(features)

# 预测每个数据点属于哪个簇（0或1）
cluster_labels = model.predict(features)

![](img/bc665d63a2ba9ede6eb0e3bd44c86c88_37.png)

# 将预测结果添加到原始数据中
data['cluster'] = cluster_labels
```

![](img/bc665d63a2ba9ede6eb0e3bd44c86c88_39.png)

执行上述代码后，数据中会新增一列 `cluster`，其值为0或1，代表每个样本点被分配到的簇。

![](img/bc665d63a2ba9ede6eb0e3bd44c86c88_41.png)

---

![](img/bc665d63a2ba9ede6eb0e3bd44c86c88_43.png)

![](img/bc665d63a2ba9ede6eb0e3bd44c86c88_45.png)

## 第五步：调整与可视化结果

![](img/bc665d63a2ba9ede6eb0e3bd44c86c88_47.png)

初始的聚类标签是0和1。为了更直观地理解，我们可以将其映射为-1和1，分别代表“看跌”和“看涨”的信号。

![](img/bc665d63a2ba9ede6eb0e3bd44c86c88_49.png)

![](img/bc665d63a2ba9ede6eb0e3bd44c86c88_51.png)

然后，我们可以通过散点图将聚类结果可视化，观察两个簇在特征空间中的分布。

```python
# 将聚类标签0映射为-1，标签1映射为1，代表交易信号
data['signal'] = data['cluster'].apply(lambda x: -1 if x == 0 else 1)

![](img/bc665d63a2ba9ede6eb0e3bd44c86c88_53.png)

![](img/bc665d63a2ba9ede6eb0e3bd44c86c88_55.png)

# 绘制散点图，用颜色区分不同的簇
plt.scatter(data['feature_1'], data['feature_2'], c=data['signal'], cmap='bwr')
plt.xlabel('Feature 1 (前一天指标)')
plt.ylabel('Feature 2 (前两天指标)')
plt.title('K-Means聚类结果')
plt.show()
```

在生成的图中，蓝色点代表一个簇（信号为-1），红色点代表另一个簇（信号为1）。通过观察可以发现，当两个特征值均为负（即前期下跌）时，数据点多被分到蓝色簇，这可能预示着继续下跌的趋势。

---

![](img/bc665d63a2ba9ede6eb0e3bd44c86c88_57.png)

## 第六步：评估策略效果

![](img/bc665d63a2ba9ede6eb0e3bd44c86c88_59.png)

现在，我们有了基于聚类的交易信号。接下来，需要评估这个简单策略的收益表现。我们将计算按照聚类信号进行交易的累计收益，并与基准（如买入持有）进行比较。

![](img/bc665d63a2ba9ede6eb0e3bd44c86c88_61.png)

以下是计算收益的步骤。

![](img/bc665d63a2ba9ede6eb0e3bd44c86c88_63.png)

```python
# 假设 ‘returns’ 是股票每日的真实收益率
# 根据聚类信号计算策略收益：信号为1时做多，信号为-1时做空（或空仓）
data['strategy_returns'] = data['signal'].shift(1) * data['returns']

![](img/bc665d63a2ba9ede6eb0e3bd44c86c88_65.png)

![](img/bc665d63a2ba9ede6eb0e3bd44c86c88_67.png)

# 计算累计收益（需将收益率从对数形式还原）
data['cumulative_returns'] = (1 + data['returns']).cumprod()
data['cumulative_strategy'] = (1 + data['strategy_returns']).cumprod()

![](img/bc665d63a2ba9ede6eb0e3bd44c86c88_69.png)

# 输出最终累计收益
print(“基准累计收益:”, data['cumulative_returns'].iloc[-1])
print(“聚类策略累计收益:”, data['cumulative_strategy'].iloc[-1])
```

![](img/bc665d63a2ba9ede6eb0e3bd44c86c88_71.png)

需要注意的是，K-Means聚类具有随机性，每次运行结果可能略有不同，这会导致策略收益不稳定。在实际应用中，可能需要多次运行并评估平均表现。

![](img/bc665d63a2ba9ede6eb0e3bd44c86c88_73.png)

![](img/bc665d63a2ba9ede6eb0e3bd44c86c88_75.png)

---

![](img/bc665d63a2ba9ede6eb0e3bd44c86c88_77.png)

## 总结

![](img/bc665d63a2ba9ede6eb0e3bd44c86c88_79.png)

本节课中我们一起学习了如何在金融数据分析中应用K-Means聚类算法。我们完成了从数据准备、模型建立、结果预测到策略评估的完整流程。核心步骤包括：使用 `KMeans(n_clusters=2)` 建立模型，用 `.fit()` 和 `.predict()` 方法进行训练与预测，并将结果用于生成交易信号。

![](img/bc665d63a2ba9ede6eb0e3bd44c86c88_81.png)

然而，必须指出，聚类分析作为一种无监督学习方法，在量化交易中应用有限。主要挑战在于其结果难以评估（因为没有真实标签），且策略的稳定性和可解释性不如有监督学习方法（如回归、分类）。因此，聚类通常不作为首选的量化策略构建方法，而是在探索性数据分析或特定场景下辅助使用。

![](img/bc665d63a2ba9ede6eb0e3bd44c86c88_83.png)

![](img/bc665d63a2ba9ede6eb0e3bd44c86c88_85.png)

---

![](img/bc665d63a2ba9ede6eb0e3bd44c86c88_87.png)

通过本实例，你应当已经掌握了在Python中执行聚类分析的基本操作，并理解了其在金融建模中的潜在作用与局限性。