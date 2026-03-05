# 量化交易实战：P55：聚类分析实例 📊

![](img/354dab02b72bce95395d6667f5a44e4d_1.png)

在本节课中，我们将学习如何在Python中使用聚类分析（K-Means算法）进行量化交易策略的初步探索。我们将从导入数据开始，逐步完成特征选择、模型建立、结果预测和可视化分析。

![](img/354dab02b72bce95395d6667f5a44e4d_3.png)

---

![](img/354dab02b72bce95395d6667f5a44e4d_5.png)

## 第一步：导入工具包

首先，我们需要导入所有必要的Python工具包。这些工具包与之前课程中使用的保持一致。

![](img/354dab02b72bce95395d6667f5a44e4d_7.png)

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
from sklearn.cluster import KMeans
```

![](img/354dab02b72bce95395d6667f5a44e4d_9.png)

---

## 第二步：导入并准备数据

![](img/354dab02b72bce95395d6667f5a44e4d_11.png)

![](img/354dab02b72bce95395d6667f5a44e4d_13.png)

上一节我们介绍了工具包的导入，本节中我们来看看如何准备数据。

我们导入数据集，并从中提取用于建模的两个特征。这两个特征分别是股票前一天的指标和前两天的指标。

```python
# 假设数据已加载到变量 `data` 中
# 选择特征列
features = data[['feature_day_before', 'feature_two_days_before']]
```

![](img/354dab02b72bce95395d6667f5a44e4d_15.png)

---

![](img/354dab02b72bce95395d6667f5a44e4d_17.png)

## 第三步：建立K-Means聚类模型

![](img/354dab02b72bce95395d6667f5a44e4d_19.png)

有了特征数据后，我们就可以开始建模了。这次的任务与之前的回归分析不同，我们将使用聚类方法。

![](img/354dab02b72bce95395d6667f5a44e4d_21.png)

在`sklearn`库中，我们选择`KMeans`模块。K-Means算法需要指定一个关键参数：簇的数量（K值）。

![](img/354dab02b72bce95395d6667f5a44e4d_23.png)

在我们的任务中，目标是将数据点分为两类，以判断市场是“上涨”还是“下跌”。因此，我们将K值设为2。

![](img/354dab02b72bce95395d6667f5a44e4d_25.png)

以下是建立模型的步骤：

1.  导入`KMeans`模块。
2.  实例化模型，并设置`n_clusters=2`。
3.  使用`.fit()`方法，将特征数据传入模型进行训练。

```python
# 实例化K-Means模型，设定簇数为2
model = KMeans(n_clusters=2)

# 使用特征数据训练模型
model.fit(features)
```

模型训练完成后，会显示一些默认参数，例如初始化中心点的方法、最大迭代次数和模型重复运行的次数。这些参数的详细信息可以在`sklearn`官方文档中查阅。

![](img/354dab02b72bce95395d6667f5a44e4d_27.png)

---

![](img/354dab02b72bce95395d6667f5a44e4d_29.png)

## 第四步：预测并调整结果

模型训练好后，我们可以用它来预测现有数据点所属的簇。

![](img/354dab02b72bce95395d6667f5a44e4d_31.png)

我们将预测结果添加到原始数据中。初始预测结果是0和1，但为了更直观地表示“上涨”（1）和“下跌”（-1），我们需要对结果进行映射调整。

![](img/354dab02b72bce95395d6667f5a44e4d_33.png)

以下是具体操作：

1.  使用`.predict()`方法获得预测标签。
2.  将标签为1的簇映射为1（代表上涨），将标签为0的簇映射为-1（代表下跌）。

```python
# 预测数据点所属的簇
cluster_labels = model.predict(features)

# 将聚类标签（0,1）映射为交易信号（-1,1）
data['cluster_signal'] = np.where(cluster_labels == 1, 1, -1)
```

执行上述代码后，数据集中新增的`cluster_signal`列就包含了我们需要的交易信号。

![](img/354dab02b72bce95395d6667f5a44e4d_35.png)

---

## 第五步：可视化聚类结果

![](img/354dab02b72bce95395d6667f5a44e4d_37.png)

为了更直观地观察聚类效果，我们可以将结果用散点图画出来。

![](img/354dab02b72bce95395d6667f5a44e4d_39.png)

我们将使用两个特征作为X轴和Y轴，并用不同颜色区分两个簇。

![](img/354dab02b72bce95395d6667f5a44e4d_41.png)

![](img/354dab02b72bce95395d6667f5a44e4d_43.png)

```python
# 绘制散点图
plt.scatter(features.iloc[:, 0],  # 第一个特征作为X轴
            features.iloc[:, 1],  # 第二个特征作为Y轴
            c=data['cluster_signal'],  # 按交易信号着色
            cmap='bwr')  # 使用蓝-白-红色系，蓝色为-1，红色为1
plt.xlabel('Feature Day Before')
plt.ylabel('Feature Two Days Before')
plt.title('K-Means Clustering Result')
plt.show()
```

![](img/354dab02b72bce95395d6667f5a44e4d_45.png)

从图中可以观察到，当两个特征值都为负（即连续下跌）时，数据点多被归为蓝色簇（下跌信号），这符合我们的直观认知。

![](img/354dab02b72bce95395d6667f5a44e4d_47.png)

---

![](img/354dab02b72bce95395d6667f5a44e4d_49.png)

## 第六步：评估策略收益

![](img/354dab02b72bce95395d6667f5a44e4d_51.png)

现在，我们有了基于聚类的交易信号。接下来，我们需要评估这个策略的收益表现。

我们将计算并比较两种情况的累计收益：
1.  原始收益。
2.  按照聚类信号进行交易后的收益。

以下是计算步骤：

![](img/354dab02b72bce95395d6667f5a44e4d_53.png)

1.  首先，确保收益率数据已进行对数化处理。
2.  将聚类信号与下一期的收益率相乘，得到策略收益序列。
3.  对策略收益序列进行累加和指数化还原，得到最终策略净值。

```python
# 假设原始对数收益率为 `log_returns`
# 根据聚类信号计算策略收益（信号作用于下一期）
strategy_returns = data['cluster_signal'].shift(1) * log_returns

![](img/354dab02b72bce95395d6667f5a44e4d_55.png)

![](img/354dab02b72bce95395d6667f5a44e4d_57.png)

# 计算累计对数收益
cumulative_log_returns = strategy_returns.cumsum()

![](img/354dab02b72bce95395d6667f5a44e4d_59.png)

# 将累计对数收益还原为净值
strategy_net_value = np.exp(cumulative_log_returns)

![](img/354dab02b72bce95395d6667f5a44e4d_61.png)

# 同样计算买入持有的净值
buy_hold_net_value = np.exp(log_returns.cumsum())

![](img/354dab02b72bce95395d6667f5a44e4d_63.png)

# 输出最终净值对比
print(f"策略最终净值: {strategy_net_value.iloc[-1]:.4f}")
print(f"买入持有最终净值: {buy_hold_net_value.iloc[-1]:.4f}")
```

![](img/354dab02b72bce95395d6667f5a44e4d_65.png)

**注意**：由于K-Means聚类中心初始化具有随机性，每次运行的结果可能略有不同。有时策略收益可能优于买入持有，有时则可能更差。这正体现了聚类分析在金融预测中的不确定性和局限性。

![](img/354dab02b72bce95395d6667f5a44e4d_67.png)

---

![](img/354dab02b72bce95395d6667f5a44e4d_69.png)

## 总结与注意事项 📝

![](img/354dab02b72bce95395d6667f5a44e4d_71.png)

本节课中我们一起学习了如何将K-Means聚类分析应用于量化交易场景。

![](img/354dab02b72bce95395d6667f5a44e4d_73.png)

我们完成了从数据准备、模型建立、结果预测到收益评估的完整流程。核心步骤是使用`sklearn`的`KMeans`模型，指定`n_clusters=2`，将数据分为两类以生成交易信号。

![](img/354dab02b72bce95395d6667f5a44e4d_75.png)

然而，需要重点强调的是，聚类分析是一种**无监督学习**方法，在量化交易中通常不作为首选策略，原因如下：
*   **缺乏明确标签**：聚类结果没有真实标签进行对照，难以进行严谨的经济学解释和效果评估。
*   **结果不稳定**：算法随机性可能导致每次运行的结果不一致。
*   **逻辑支撑弱**：相较于基于经济理论的回归模型，聚类得出的信号逻辑性较弱。

![](img/354dab02b72bce95395d6667f5a44e4d_77.png)

![](img/354dab02b72bce95395d6667f5a44e4d_79.png)

因此，聚类分析更适用于探索性数据分析或在传统方法无效时进行尝试。在实际量化策略开发中，应优先考虑逻辑清晰、可解释性强的监督学习方法。

![](img/354dab02b72bce95395d6667f5a44e4d_81.png)

---

![](img/354dab02b72bce95395d6667f5a44e4d_83.png)

**本节课中我们一起学习了**：使用Python和K-Means算法进行聚类分析的基本流程，并将其应用于生成简单的交易信号。我们了解了从建模到评估的全过程，同时也认识到聚类方法在金融预测中的优点与局限性。