# Python金融量化分析：P54：聚类分析实例 📊

![](img/aa9e741bfaa0332f1a114c9d19348686_1.png)

![](img/aa9e741bfaa0332f1a114c9d19348686_3.png)

在本节课中，我们将学习如何使用Python进行聚类分析，并将其应用于金融量化策略中。我们将从导入工具包开始，逐步完成数据准备、模型建立、结果预测和可视化分析，最后评估聚类策略的收益效果。

![](img/aa9e741bfaa0332f1a114c9d19348686_5.png)

---

![](img/aa9e741bfaa0332f1a114c9d19348686_7.png)

## 第一步：导入工具包 🧰

![](img/aa9e741bfaa0332f1a114c9d19348686_9.png)

与之前课程一样，我们首先需要导入必要的Python工具包。

![](img/aa9e741bfaa0332f1a114c9d19348686_11.png)

```python
import pandas as pd
import numpy as np
from sklearn.cluster import KMeans
import matplotlib.pyplot as plt
```

![](img/aa9e741bfaa0332f1a114c9d19348686_13.png)

---

## 第二步：导入与准备数据 📈

![](img/aa9e741bfaa0332f1a114c9d19348686_15.png)

上一节我们导入了工具包，本节中我们来看看如何准备用于聚类的数据。我们使用一份金融数据，并从中提取两个特征：前一天的指标和前两天的指标。

![](img/aa9e741bfaa0332f1a114c9d19348686_17.png)

```python
# 假设 `data` 是已加载的Pandas DataFrame
features = data[['feature_1', 'feature_2']]  # 替换为实际的列名
```

![](img/aa9e741bfaa0332f1a114c9d19348686_19.png)

![](img/aa9e741bfaa0332f1a114c9d19348686_21.png)

---

![](img/aa9e741bfaa0332f1a114c9d19348686_23.png)

![](img/aa9e741bfaa0332f1a114c9d19348686_25.png)

## 第三步：建立K-Means聚类模型 🤖

有了特征数据后，我们就可以开始建模了。这次的任务与之前的回归分析不同，我们将使用聚类方法。在`sklearn`库中，`KMeans`是一个常用的聚类算法。

以下是建立模型的步骤：

1.  从`sklearn.cluster`导入`KMeans`模块。
2.  实例化`KMeans`模型，并指定要聚类的簇数（K值）。在本例中，我们希望将数据分为“涨”和“跌”两类，因此K值设为2。

![](img/aa9e741bfaa0332f1a114c9d19348686_27.png)

```python
# 实例化K-Means模型，设定簇数为2
model = KMeans(n_clusters=2, random_state=42)
# 使用特征数据拟合模型
model.fit(features)
```

![](img/aa9e741bfaa0332f1a114c9d19348686_29.png)

模型参数说明：
*   `n_clusters=2`：指定聚类的簇数。
*   `random_state=42`：设定随机种子，确保结果可复现。
*   其他参数如`max_iter`（最大迭代次数）、`n_init`（初始化次数）均使用默认值。

---

![](img/aa9e741bfaa0332f1a114c9d19348686_31.png)

![](img/aa9e741bfaa0332f1a114c9d19348686_33.png)

## 第四步：预测与结果处理 🔮

模型建立完成后，我们可以用它来预测每个数据点所属的簇。

以下是预测和结果处理的步骤：

1.  使用`model.predict()`方法对特征数据进行预测，得到每个样本的簇标签（0或1）。
2.  为了方便理解，我们将标签0映射为-1（代表“跌”），标签1映射为1（代表“涨”）。

![](img/aa9e741bfaa0332f1a114c9d19348686_35.png)

```python
# 预测数据点所属的簇
cluster_labels = model.predict(features)
# 将簇标签0映射为-1，1映射为1
data['cluster'] = np.where(cluster_labels == 1, 1, -1)
```

![](img/aa9e741bfaa0332f1a114c9d19348686_37.png)

---

![](img/aa9e741bfaa0332f1a114c9d19348686_39.png)

![](img/aa9e741bfaa0332f1a114c9d19348686_41.png)

## 第五步：结果可视化 📊

![](img/aa9e741bfaa0332f1a114c9d19348686_43.png)

为了直观地观察聚类效果，我们可以将数据点在二维平面上用散点图展示出来，并用不同颜色区分不同的簇。

![](img/aa9e741bfaa0332f1a114c9d19348686_45.png)

![](img/aa9e741bfaa0332f1a114c9d19348686_47.png)

以下是绘制散点图的代码：

![](img/aa9e741bfaa0332f1a114c9d19348686_49.png)

```python
plt.figure(figsize=(10, 6))
# 绘制散点图，x轴为第一个特征，y轴为第二个特征，颜色根据簇标签区分
plt.scatter(features.iloc[:, 0], features.iloc[:, 1], c=data['cluster'], cmap='bwr', alpha=0.6)
plt.xlabel('Feature 1 (e.g., Previous Day Indicator)')
plt.ylabel('Feature 2 (e.g., Day Before Yesterday Indicator)')
plt.title('K-Means Clustering Results (2 Clusters)')
plt.colorbar(label='Cluster Label (1=Up, -1=Down)')
plt.grid(True, linestyle='--', alpha=0.5)
plt.show()
```

![](img/aa9e741bfaa0332f1a114c9d19348686_51.png)

![](img/aa9e741bfaa0332f1a114c9d19348686_53.png)

在生成的图中，蓝色点可能代表一个簇（例如“跌”），红色点代表另一个簇（例如“涨”）。通过观察可以发现，当两个特征值都为负时，数据点更可能被归为“跌”的类别。

---

## 第六步：策略回测与评估 💹

![](img/aa9e741bfaa0332f1a114c9d19348686_55.png)

现在，我们来评估基于聚类结果的简单交易策略效果。策略逻辑是：当预测标签为1（涨）时，持有正收益；当预测标签为-1（跌）时，持有负收益（或空仓）。我们将比较使用聚类策略后的累计收益与原始收益。

![](img/aa9e741bfaa0332f1a114c9d19348686_57.png)

![](img/aa9e741bfaa0332f1a114c9d19348686_59.png)

以下是计算收益的步骤：

![](img/aa9e741bfaa0332f1a114c9d19348686_61.png)

1.  假设原始收益率数据在`data[‘returns’]`列中。
2.  计算聚类策略的收益率：`策略收益 = 原始收益 * 簇标签`。标签为1时持有正收益，为-1时持有负收益。
3.  计算累计收益并进行比较。

![](img/aa9e741bfaa0332f1a114c9d19348686_63.png)

![](img/aa9e741bfaa0332f1a114c9d19348686_65.png)

```python
# 计算聚类策略的收益率
data['strategy_returns'] = data['returns'] * data['cluster']
# 计算累计收益
data['cumulative_returns'] = (1 + data['returns']).cumprod()
data['cumulative_strategy_returns'] = (1 + data['strategy_returns']).cumprod()

![](img/aa9e741bfaa0332f1a114c9d19348686_67.png)

# 输出最终累计收益
final_return = data['cumulative_returns'].iloc[-1]
final_strategy_return = data['cumulative_strategy_returns'].iloc[-1]
print(f"原始累计收益: {final_return:.4f}")
print(f"聚类策略累计收益: {final_strategy_return:.4f}")
```

![](img/aa9e741bfaa0332f1a114c9d19348686_69.png)

![](img/aa9e741bfaa0332f1a114c9d19348686_71.png)

**注意**：由于K-Means聚类具有随机性（尤其是中心点初始化），每次运行的结果可能略有不同，导致策略收益波动。在实际应用中，需要多次运行并评估其稳定性。

![](img/aa9e741bfaa0332f1a114c9d19348686_73.png)

---

![](img/aa9e741bfaa0332f1a114c9d19348686_75.png)

## 总结 📝

![](img/aa9e741bfaa0332f1a114c9d19348686_77.png)

![](img/aa9e741bfaa0332f1a114c9d19348686_79.png)

本节课中我们一起学习了如何在Python中使用K-Means算法进行聚类分析，并将其应用于金融场景。

![](img/aa9e741bfaa0332f1a114c9d19348686_81.png)

1.  **流程回顾**：我们完成了从数据准备、模型建立、预测到可视化的完整流程。
2.  **核心方法**：使用`sklearn.cluster.KMeans`建立模型，并通过`.fit()`和`.predict()`方法进行训练与预测。
3.  **结果应用**：将聚类得到的标签（1和-1）作为一个简单的交易信号，构建了基础的量化策略。
4.  **注意事项**：聚类分析属于无监督学习，在金融量化中直接应用时，其信号的经济学含义可能不明确，且结果受随机性影响。因此，它通常不作为首选的策略生成方法，而是在探索性数据分析或特定场景下使用。

![](img/aa9e741bfaa0332f1a114c9d19348686_83.png)

![](img/aa9e741bfaa0332f1a114c9d19348686_85.png)

通过本实例，你应已掌握聚类分析的基本操作，并了解其在量化研究中的潜在用途与局限性。