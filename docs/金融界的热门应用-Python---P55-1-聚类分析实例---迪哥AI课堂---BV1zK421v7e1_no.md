# Python金融分析量化交易：P55：聚类分析实例 📊

![](img/23ab931409172cf6a83abd8c7af6a4eb_1.png)

![](img/23ab931409172cf6a83abd8c7af6a4eb_3.png)

在本节课中，我们将学习如何使用聚类分析（K-Means算法）对股票数据进行分组，并尝试基于此构建一个简单的交易策略。我们将从数据准备、模型构建、结果可视化到策略收益评估，完整地走一遍流程。

![](img/23ab931409172cf6a83abd8c7af6a4eb_5.png)

---

![](img/23ab931409172cf6a83abd8c7af6a4eb_7.png)

## 工具包与数据导入

![](img/23ab931409172cf6a83abd8c7af6a4eb_9.png)

首先，我们需要导入所有必要的Python工具包。这些包与之前课程中使用的一致。

```python
import pandas as pd
import numpy as np
from sklearn.cluster import KMeans
import matplotlib.pyplot as plt
```

![](img/23ab931409172cf6a83abd8c7af6a4eb_11.png)

![](img/23ab931409172cf6a83abd8c7af6a4eb_13.png)

工具包导入完毕后，下一步是加载我们要分析的数据集。这份数据在上节课中已经介绍过，我们需要从中提取用于建模的特征。

```python
# 假设数据已加载到变量 `data` 中
# 这里我们选择‘前一天指标’和‘前两天指标’作为特征
features = data[['feature_1', 'feature_2']]
```

![](img/23ab931409172cf6a83abd8c7af6a4eb_15.png)

---

![](img/23ab931409172cf6a83abd8c7af6a4eb_17.png)

## 构建K-Means聚类模型

![](img/23ab931409172cf6a83abd8c7af6a4eb_19.png)

上一节我们准备好了数据特征，本节中我们来看看如何使用K-Means算法进行聚类建模。与之前使用的回归方法不同，聚类是一种无监督学习方法，用于将数据点分组。

![](img/23ab931409172cf6a83abd8c7af6a4eb_21.png)

![](img/23ab931409172cf6a83abd8c7af6a4eb_23.png)

在`sklearn`库中，我们可以方便地使用`KMeans`模块。首先，我们需要实例化一个K-Means模型。

![](img/23ab931409172cf6a83abd8c7af6a4eb_25.png)

```python
# 实例化K-Means模型，指定要分成2个簇（对应涨和跌）
model = KMeans(n_clusters=2, random_state=42)
```

接下来，我们使用提取的特征数据来训练（拟合）这个模型。

```python
# 使用特征数据拟合模型
model.fit(features)
```

模型训练完成后，我们可以用它来预测每个数据点属于哪个簇（类别）。

![](img/23ab931409172cf6a83abd8c7af6a4eb_27.png)

![](img/23ab931409172cf6a83abd8c7af6a4eb_29.png)

```python
# 预测每个数据点的簇标签
cluster_labels = model.predict(features)
```

初始的预测结果是0和1。为了更直观地对应“涨”和“跌”，我们可以将标签进行映射转换。

![](img/23ab931409172cf6a83abd8c7af6a4eb_31.png)

```python
# 将簇标签0和1映射为-1和1（或其他便于理解的标识）
data['cluster'] = np.where(cluster_labels == 1, 1, -1)
```

![](img/23ab931409172cf6a83abd8c7af6a4eb_33.png)

---

## 可视化聚类结果

模型已经给出了分类结果，现在我们可以通过散点图直观地查看数据是如何被分成两类的。

以下是绘制聚类结果散点图的代码：

![](img/23ab931409172cf6a83abd8c7af6a4eb_35.png)

```python
plt.figure(figsize=(10, 6))
# 绘制散点图，根据簇标签着色
scatter = plt.scatter(features.iloc[:, 0],  # X轴：第一个特征
                      features.iloc[:, 1],  # Y轴：第二个特征
                      c=data['cluster'],    # 颜色由簇标签决定
                      cmap='coolwarm',      # 使用红蓝渐变色系
                      alpha=0.7)
plt.xlabel('Feature 1 (前一天指标)')
plt.ylabel('Feature 2 (前两天指标)')
plt.title('K-Means聚类结果可视化')
plt.colorbar(scatter, label='Cluster Label (1: 涨, -1: 跌)')
plt.grid(True, linestyle='--', alpha=0.5)
plt.show()
```

![](img/23ab931409172cf6a83abd8c7af6a4eb_37.png)

在生成的图中，红色和蓝色的点分别代表被模型归为不同簇的数据点。我们可以观察到一个趋势：当两个特征值均为负（代表过去两天下跌）时，数据点更可能被归为“跌”的类别。

![](img/23ab931409172cf6a83abd8c7af6a4eb_39.png)

---

![](img/23ab931409172cf6a83abd8c7af6a4eb_41.png)

![](img/23ab931409172cf6a83abd8c7af6a4eb_43.png)

## 基于聚类的简单策略与收益评估

![](img/23ab931409172cf6a83abd8c7af6a4eb_45.png)

![](img/23ab931409172cf6a83abd8c7af6a4eb_47.png)

有了聚类标签后，我们可以构建一个非常简单的策略：当预测标签为“涨”（1）时买入持有，为“跌”（-1）时卖出或空仓。接下来，我们评估这个策略的收益表现。

![](img/23ab931409172cf6a83abd8c7af6a4eb_49.png)

首先，我们需要计算策略的每日收益。假设原始数据中有一列 `returns` 代表股票的实际日收益率。

![](img/23ab931409172cf6a83abd8c7af6a4eb_51.png)

```python
# 计算策略收益：当cluster为1时，获取当日收益；为-1时，获取当日负收益（即做空或空仓的近似）
data['strategy_returns'] = data['cluster'] * data['returns']
```

由于我们之前可能对收益率数据做过对数处理，现在需要将其还原为普通收益率进行计算。

![](img/23ab931409172cf6a83abd8c7af6a4eb_53.png)

```python
# 如果returns是对数收益，则需还原
# 假设原始‘returns’列是普通收益，则无需此步
# data['cumulative_returns'] = (1 + data['strategy_returns']).cumprod()
# 更通用的方式是计算累计收益
cumulative_strategy = (data['strategy_returns']).cumsum().apply(np.exp)
cumulative_baseline = (data['returns']).cumsum().apply(np.exp)
```

最后，我们可以对比策略的累计收益与简单买入持有（Baseline）的累计收益。

![](img/23ab931409172cf6a83abd8c7af6a4eb_55.png)

![](img/23ab931409172cf6a83abd8c7af6a4eb_57.png)

```python
# 绘制收益曲线对比
plt.figure(figsize=(12, 6))
plt.plot(cumulative_strategy, label='聚类策略累计收益', linewidth=2)
plt.plot(cumulative_baseline, label='买入持有累计收益', linewidth=2, linestyle='--')
plt.xlabel('时间')
plt.ylabel('累计收益')
plt.title('聚类策略 vs. 买入持有策略收益对比')
plt.legend()
plt.grid(True)
plt.show()

![](img/23ab931409172cf6a83abd8c7af6a4eb_59.png)

# 输出最终收益
final_return_strategy = cumulative_strategy.iloc[-1]
final_return_baseline = cumulative_baseline.iloc[-1]
print(f"聚类策略最终收益: {final_return_strategy:.4f}")
print(f"买入持有最终收益: {final_return_baseline:.4f}")
```

![](img/23ab931409172cf6a83abd8c7af6a4eb_61.png)

![](img/23ab931409172cf6a83abd8c7af6a4eb_63.png)

**注意**：K-Means聚类具有随机性，每次运行结果可能不同，导致策略收益波动。在实际应用中，需要多次运行并评估其稳定性。

![](img/23ab931409172cf6a83abd8c7af6a4eb_65.png)

---

![](img/23ab931409172cf6a83abd8c7af6a4eb_67.png)

![](img/23ab931409172cf6a83abd8c7af6a4eb_69.png)

## 关于聚类在金融分析中的实用性讨论

![](img/23ab931409172cf6a83abd8c7af6a4eb_71.png)

虽然本例中聚类策略可能显示出一定的收益，但在实际量化交易中，单纯使用聚类分析存在明显局限：

![](img/23ab931409172cf6a83abd8c7af6a4eb_73.png)

1.  **无监督性**：聚类没有标签，难以像分类或回归问题一样进行严格的模型评估和调优。
2.  **可解释性差**：很难解释为什么某些点被归为一类，以及其与市场行为的直接因果关系。
3.  **结果不稳定**：K-Means对初始中心点敏感，多次运行结果可能不一致。
4.  **逻辑关联弱**：将数据点分组与预测未来涨跌之间的逻辑链条较为间接和脆弱。

![](img/23ab931409172cf6a83abd8c7af6a4eb_75.png)

![](img/23ab931409172cf6a83abd8c7af6a4eb_77.png)

因此，聚类分析在量化策略中通常不作为核心方法，而是在数据探索、特征构造或作为复杂模型的一部分时使用。当缺乏明确标签或需要从数据中发现隐藏结构时，它可以作为一个辅助工具。

![](img/23ab931409172cf6a83abd8c7af6a4eb_79.png)

---

![](img/23ab931409172cf6a83abd8c7af6a4eb_81.png)

![](img/23ab931409172cf6a83abd8c7af6a4eb_83.png)

本节课中我们一起学习了如何使用Python的`sklearn`库实现K-Means聚类分析，并将其应用于金融数据，构建并可视化了一个简单的分类策略，最后对策略收益进行了基础评估。大家需要掌握聚类的基本流程，同时理解其在实战中的优势与局限性。