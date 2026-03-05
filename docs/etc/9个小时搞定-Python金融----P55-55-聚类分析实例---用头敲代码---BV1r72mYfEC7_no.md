# Python金融量化分析：P55：聚类分析实例 📊

![](img/ab3c42b38fd083a229135073a47a5c0d_1.png)

在本节课中，我们将学习如何在Python中使用聚类分析进行金融量化策略的构建。我们将通过一个实例，演示如何利用K-Means算法对股票数据进行聚类，并根据聚类结果进行简单的策略回测。

![](img/ab3c42b38fd083a229135073a47a5c0d_3.png)

---

![](img/ab3c42b38fd083a229135073a47a5c0d_5.png)

## 工具包导入 📦

![](img/ab3c42b38fd083a229135073a47a5c0d_7.png)

首先，我们需要导入所有必要的Python工具包。这些包与之前课程中使用的保持一致。

![](img/ab3c42b38fd083a229135073a47a5c0d_9.png)

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
from sklearn.cluster import KMeans
```

---

![](img/ab3c42b38fd083a229135073a47a5c0d_11.png)

## 数据准备 📈

![](img/ab3c42b38fd083a229135073a47a5c0d_13.png)

上一节我们介绍了工具包的导入，本节中我们来看看如何准备用于聚类的数据。

我们导入数据，并从中提取两个特征。这两个特征分别是股票前一天的指标和前两天的指标，它们将作为我们聚类模型的输入。

```python
# 假设data是已经加载好的Pandas DataFrame
features = data[['feature_1', 'feature_2']]  # 替换为实际的列名
```

![](img/ab3c42b38fd083a229135073a47a5c0d_15.png)

![](img/ab3c42b38fd083a229135073a47a5c0d_17.png)

---

## 模型构建与训练 🤖

![](img/ab3c42b38fd083a229135073a47a5c0d_19.png)

![](img/ab3c42b38fd083a229135073a47a5c0d_21.png)

有了特征数据之后，我们就可以开始构建模型了。这次的任务与之前的回归分析不同，我们将使用聚类方法。

![](img/ab3c42b38fd083a229135073a47a5c0d_23.png)

在scikit-learn库中，K-Means是一个常用的聚类算法。以下是实例化并训练K-Means模型的步骤。

![](img/ab3c42b38fd083a229135073a47a5c0d_25.png)

首先，导入KMeans模块并创建一个模型实例。我们需要指定一个关键参数：`n_clusters`，即我们希望将数据分成多少个簇（K值）。在本例中，我们希望判断股票是涨还是跌，因此设定为2个簇。

```python
# 创建K-Means模型实例，指定簇数为2
model = KMeans(n_clusters=2, random_state=42)
```

接下来，使用我们的特征数据来训练（拟合）这个模型。

```python
# 使用特征数据拟合模型
model.fit(features)
```

模型训练完成后，会显示一些初始化参数，例如中心点初始化方法、最大迭代次数和模型重复运行的次数。K-Means的结果具有一定随机性，因此模型默认会运行多次（例如10次）并返回最佳结果。

![](img/ab3c42b38fd083a229135073a47a5c0d_27.png)

---

![](img/ab3c42b38fd083a229135073a47a5c0d_29.png)

## 结果预测与处理 🔮

模型训练好后，我们可以用它来预测现有数据点所属的簇。

![](img/ab3c42b38fd083a229135073a47a5c0d_31.png)

以下是预测并将结果添加到原始数据中的代码。初始预测结果是0和1，但为了更直观（例如代表“涨”和“跌”），我们将其映射为1和-1。

![](img/ab3c42b38fd083a229135073a47a5c0d_33.png)

```python
# 预测数据点所属的簇
cluster_predictions = model.predict(features)

# 将预测结果（0,1）映射为（1, -1），并添加到数据中
data['cluster'] = np.where(cluster_predictions == 1, 1, -1)
```

现在，数据中新增了一列 `cluster`，其值为1或-1，分别代表模型划分的两个类别。

---

## 结果可视化 📊

![](img/ab3c42b38fd083a229135073a47a5c0d_35.png)

为了直观地查看聚类效果，我们可以将数据用散点图画出来，并用不同颜色区分不同的簇。

以下是绘制散点图的代码：

![](img/ab3c42b38fd083a229135073a47a5c0d_37.png)

```python
plt.figure(figsize=(10, 6))
# 绘制散点图，x轴和y轴分别对应两个特征
plt.scatter(features.iloc[:, 0], features.iloc[:, 1], c=data['cluster'], cmap='coolwarm')
plt.xlabel('Feature 1 (e.g., Previous Day Indicator)')
plt.ylabel('Feature 2 (e.g., Day Before Yesterday Indicator)')
plt.title('K-Means Clustering Results')
plt.colorbar(label='Cluster (1: Rise, -1: Fall)')
plt.show()
```

![](img/ab3c42b38fd083a229135073a47a5c0d_39.png)

在生成的图中，红色和蓝色的点分别代表两个不同的簇。通过观察可以发现，当两个特征值都为负（即前两天都下跌）时，数据点大多被分到代表“跌”的簇中，这符合一定的逻辑趋势。

![](img/ab3c42b38fd083a229135073a47a5c0d_41.png)

![](img/ab3c42b38fd083a229135073a47a5c0d_43.png)

---

![](img/ab3c42b38fd083a229135073a47a5c0d_45.png)

## 策略回测与评估 💹

![](img/ab3c42b38fd083a229135073a47a5c0d_47.png)

仅仅得到聚类分组还不够，我们需要评估基于这个聚类结果的交易策略是否有效。

以下是进行简单策略回测的步骤。我们计算按照聚类信号（1买入，-1卖出或做空）操作后的累计收益，并与原始收益进行对比。

![](img/ab3c42b38fd083a229135073a47a5c0d_49.png)

![](img/ab3c42b38fd083a229135073a47a5c0d_51.png)

首先，需要确保收益率数据已经过对数处理，这里我们将其还原为简单收益率。

```python
# 假设‘returns’是原始的对数收益率列
# 将其还原为简单收益率
simple_returns = np.exp(data['returns']) - 1

# 计算聚类策略的收益率：信号 * 简单收益率
strategy_returns = data['cluster'] * simple_returns

![](img/ab3c42b38fd083a229135073a47a5c0d_53.png)

# 将策略收益率添加到数据中
data['strategy_returns'] = strategy_returns
```

然后，我们可以计算并比较基准收益和策略收益。

![](img/ab3c42b38fd083a229135073a47a5c0d_55.png)

![](img/ab3c42b38fd083a229135073a47a5c0d_57.png)

```python
# 计算累计收益
cumulative_baseline = (1 + simple_returns).cumprod()
cumulative_strategy = (1 + strategy_returns).cumprod()

![](img/ab3c42b38fd083a229135073a47a5c0d_59.png)

# 打印最终收益对比
print(f"基准最终收益: {cumulative_baseline.iloc[-1]:.4f}")
print(f"聚类策略最终收益: {cumulative_strategy.iloc[-1]:.4f}")
```

![](img/ab3c42b38fd083a229135073a47a5c0d_61.png)

**注意**：由于K-Means聚类具有随机性，每次运行的结果可能不同，导致策略收益波动。有时策略可能跑赢基准，有时则可能不如。这正体现了聚类分析在金融预测中的不确定性和挑战。

![](img/ab3c42b38fd083a229135073a47a5c0d_63.png)

---

![](img/ab3c42b38fd083a229135073a47a5c0d_65.png)

## 关于聚类分析的讨论 💭

![](img/ab3c42b38fd083a229135073a47a5c0d_67.png)

在实际的金融量化分析中，聚类分析的使用需要谨慎。

![](img/ab3c42b38fd083a229135073a47a5c0d_69.png)

聚类是一种无监督学习方法，它没有明确的标签（即我们不知道真正的“涨”或“跌”），因此其结果难以像有监督模型那样进行严格的评估和归因分析。我们无法确切知道模型划分簇的依据是否稳定可靠。

![](img/ab3c42b38fd083a229135073a47a5c0d_71.png)

本节课介绍聚类，主要是为了让大家在缺乏明确标签或探索数据结构时，多一种分析工具。但在构建核心交易策略时，通常更依赖于逻辑清晰、可解释性强的有监督学习方法（如回归、分类）。

![](img/ab3c42b38fd083a229135073a47a5c0d_73.png)

---

![](img/ab3c42b38fd083a229135073a47a5c0d_75.png)

## 总结 ✨

![](img/ab3c42b38fd083a229135073a47a5c0d_77.png)

![](img/ab3c42b38fd083a229135073a47a5c0d_79.png)

本节课中我们一起学习了如何在Python金融量化分析中应用K-Means聚类算法。

![](img/ab3c42b38fd083a229135073a47a5c0d_81.png)

1.  **准备数据**：我们从股票数据中提取了关键特征。
2.  **构建模型**：使用scikit-learn的`KMeans`将数据点划分为两个簇。
3.  **结果处理**：将模型输出映射为直观的交易信号（1和-1）。
4.  **结果可视化**：通过散点图直观展示了聚类效果。
5.  **策略回测**：基于聚类信号进行了简单的收益回测，并讨论了其有效性和随机性。

![](img/ab3c42b38fd083a229135073a47a5c0d_83.png)

记住，聚类是一个强大的数据探索工具，但在用于预测性金融建模时，其稳定性和可解释性存在局限。它更适合作为辅助分析手段，或在传统方法无效时进行尝试。