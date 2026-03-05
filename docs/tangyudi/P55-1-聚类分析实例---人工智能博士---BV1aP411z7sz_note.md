# 量化交易：P55：聚类分析实例 📊

![](img/5115212720ca3cf384d48b43aea7062e_1.png)

在本节课中，我们将学习如何在量化交易中使用聚类分析。我们将通过一个具体的实例，演示如何利用K-Means算法对股票数据进行聚类，并尝试基于聚类结果构建一个简单的交易策略。

![](img/5115212720ca3cf384d48b43aea7062e_3.png)

---

![](img/5115212720ca3cf384d48b43aea7062e_5.png)

## 第一步：导入工具包

![](img/5115212720ca3cf384d48b43aea7062e_7.png)

首先，我们需要导入所有必要的Python工具包。这些工具包与之前课程中使用的保持一致。

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
from sklearn.cluster import KMeans
```

![](img/5115212720ca3cf384d48b43aea7062e_9.png)

![](img/5115212720ca3cf384d48b43aea7062e_11.png)

---

## 第二步：加载与准备数据

上一节我们导入了工具包，本节中我们来看看如何准备数据。我们使用一份股票数据，并从中提取两个特征用于建模。

![](img/5115212720ca3cf384d48b43aea7062e_13.png)

![](img/5115212720ca3cf384d48b43aea7062e_15.png)

我们选择的两个特征是：
1.  股票前一天的收益率。
2.  股票前两天的收益率。

这两个指标将作为我们聚类模型的输入特征。

![](img/5115212720ca3cf384d48b43aea7062e_17.png)

![](img/5115212720ca3cf384d48b43aea7062e_19.png)

```python
# 假设 `df` 是已经加载的包含股票历史数据的DataFrame
# 这里我们创建两个特征列
data = df[['returns_lag1', 'returns_lag2']].dropna()
```

![](img/5115212720ca3cf384d48b43aea7062e_21.png)

---

![](img/5115212720ca3cf384d48b43aea7062e_23.png)

## 第三步：建立K-Means聚类模型

有了特征数据后，我们就可以开始建模了。这次的任务与之前的回归分析不同，我们将使用聚类方法。

在Scikit-learn库中，有一个非常实用的聚类算法叫做K-Means。以下是建立模型的步骤：

1.  **导入模块**：从`sklearn.cluster`中导入`KMeans`。
2.  **实例化模型**：创建一个K-Means模型实例。我们需要指定一个关键参数`n_clusters`，即我们希望将数据分成多少个簇（K值）。
3.  **拟合模型**：使用我们的特征数据来训练模型。

![](img/5115212720ca3cf384d48b43aea7062e_25.png)

在我们的任务中，目标相对简单：我们希望判断股票是上涨还是下跌。因此，我们将簇的数量设定为2。

![](img/5115212720ca3cf384d48b43aea7062e_27.png)

```python
# 1. 实例化K-Means模型，设定簇的数量为2
model = KMeans(n_clusters=2, random_state=42)

# 2. 使用特征数据拟合模型
model.fit(data)
```

![](img/5115212720ca3cf384d48b43aea7062e_29.png)

模型拟合完成后，会显示一些默认参数，例如初始化中心点的方法、最大迭代次数等。K-Means算法具有随机性，因此Scikit-learn默认会运行多次（`n_init`参数控制）并选择最佳结果。

![](img/5115212720ca3cf384d48b43aea7062e_31.png)

---

## 第四步：预测与结果处理

模型建立后，我们可以用它来预测现有数据点所属的簇。

以下是预测和结果处理的步骤：
1.  使用训练好的模型对数据进行预测，得到每个样本的簇标签（0或1）。
2.  为了方便理解，我们可以将簇标签进行映射。例如，将原本的`0`映射为`-1`（代表下跌），将`1`映射为`1`（代表上涨）。

![](img/5115212720ca3cf384d48b43aea7062e_33.png)

```python
# 对数据进行预测，得到簇标签
cluster_labels = model.predict(data)

# 将簇标签映射为更直观的涨跌信号（例如：0 -> -1， 1 -> 1）
# 注意：这里需要根据实际聚类中心的意义来判断映射关系，可能需要调整
data['cluster_signal'] = np.where(cluster_labels == 1, 1, -1)
```

![](img/5115212720ca3cf384d48b43aea7062e_35.png)

现在，数据框中新增了一列`cluster_signal`，其值为1或-1，代表模型对该数据点所属类别的判断。

![](img/5115212720ca3cf384d48b43aea7062e_37.png)

---

![](img/5115212720ca3cf384d48b43aea7062e_39.png)

![](img/5115212720ca3cf384d48b43aea7062e_41.png)

## 第五步：可视化聚类结果

![](img/5115212720ca3cf384d48b43aea7062e_43.png)

为了直观地观察聚类效果，我们可以将数据点绘制在散点图上，并用不同颜色区分不同的簇。

以下是绘制散点图的代码：

![](img/5115212720ca3cf384d48b43aea7062e_45.png)

![](img/5115212720ca3cf384d48b43aea7062e_47.png)

```python
plt.figure(figsize=(10, 6))
# 绘制散点图，x轴为‘returns_lag1’， y轴为‘returns_lag2’
# 颜色‘c’根据簇标签‘cluster_signal’区分
plt.scatter(data['returns_lag1'], data['returns_lag2'], c=data['cluster_signal'], cmap='bwr', alpha=0.6)
plt.xlabel('Returns (t-1)')
plt.ylabel('Returns (t-2)')
plt.title('Stock Returns Clustering with K-Means')
plt.colorbar(label='Cluster Signal (1: Up, -1: Down)')
plt.show()
```

在生成的图中，蓝色和红色的点分别代表两个不同的簇。我们可以观察数据点的分布，例如，当两个滞后收益率都为负值时（左下角区域），数据点可能更多地被划分到代表“下跌”的簇中。

---

![](img/5115212720ca3cf384d48b43aea7062e_49.png)

## 第六步：评估策略效果

![](img/5115212720ca3cf384d48b43aea7062e_51.png)

仅仅完成聚类还不够，我们需要评估基于此聚类结果的简单策略是否有效。

![](img/5115212720ca3cf384d48b43aea7062e_53.png)

我们将进行一个简单的回测：
1.  假设我们根据`cluster_signal`进行交易：信号为1时买入（或做多），信号为-1时卖出（或做空）。
2.  计算在此策略下的累计收益，并与基准（如买入并持有）进行比较。

![](img/5115212720ca3cf384d48b43aea7062e_55.png)

**注意**：由于K-Means的随机性，每次运行的结果可能略有不同，因此评估时需要谨慎，可能需要多次运行或使用更稳健的评估方法。

![](img/5115212720ca3cf384d48b43aea7062e_57.png)

![](img/5115212720ca3cf384d48b43aea7062e_59.png)

```python
# 计算策略收益：假设信号直接作为每日收益率的方向
data['strategy_returns'] = data['cluster_signal'] * data['actual_returns']  # 需要实际的‘actual_returns’列

![](img/5115212720ca3cf384d48b43aea7062e_61.png)

# 计算累计收益
cumulative_strategy_returns = (1 + data['strategy_returns']).cumprod()
cumulative_baseline_returns = (1 + data['actual_returns']).cumprod()

![](img/5115212720ca3cf384d48b43aea7062e_63.png)

# 绘制收益曲线对比
plt.figure(figsize=(12, 6))
plt.plot(cumulative_strategy_returns, label='Clustering Strategy')
plt.plot(cumulative_baseline_returns, label='Buy & Hold', linestyle='--')
plt.xlabel('Time')
plt.ylabel('Cumulative Returns')
plt.title('Strategy Backtest')
plt.legend()
plt.grid(True)
plt.show()
```

![](img/5115212720ca3cf384d48b43aea7062e_65.png)

在实际运行中，聚类策略的效果可能时好时坏。这正说明了聚类分析在量化交易中的局限性：它是一种无监督学习方法，缺乏明确的标签（涨/跌）来指导优化，因此结果难以评估和稳定复现。

![](img/5115212720ca3cf384d48b43aea7062e_67.png)

---

![](img/5115212720ca3cf384d48b43aea7062e_69.png)

## 总结

![](img/5115212720ca3cf384d48b43aea7062e_71.png)

本节课中我们一起学习了如何在量化交易中应用K-Means聚类分析。我们完成了从数据准备、模型构建、结果预测到可视化和简单回测的全过程。

![](img/5115212720ca3cf384d48b43aea7062e_73.png)

![](img/5115212720ca3cf384d48b43aea7062e_75.png)

核心要点总结：
*   **方法**：使用`KMeans(n_clusters=2)`对股票滞后收益率特征进行聚类。
*   **过程**：包括数据导入、特征工程、模型拟合（`model.fit`）、预测（`model.predict`）和结果映射。
*   **可视化**：通过散点图直观展示聚类效果。
*   **评估**：基于聚类信号构建简单交易策略并进行回测，但结果受随机性影响较大。

![](img/5115212720ca3cf384d48b43aea7062e_77.png)

需要强调的是，聚类分析作为一种无监督学习，在缺乏先验标签的金融预测中通常不是首选方法，因为它难以进行严格的性能评估和逻辑解释。本实例主要用于演示技术流程，在实际应用中需要结合更多有监督学习方法和严谨的风险控制。