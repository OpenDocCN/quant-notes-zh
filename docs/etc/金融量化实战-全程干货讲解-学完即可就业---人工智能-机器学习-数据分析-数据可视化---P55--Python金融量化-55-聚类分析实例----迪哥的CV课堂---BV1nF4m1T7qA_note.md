# Python金融量化：P55：聚类分析实例 📊

![](img/f84608097431785e9c4b2b6a11cd51e5_1.png)

![](img/f84608097431785e9c4b2b6a11cd51e5_3.png)

在本节课中，我们将学习如何在Python中使用聚类分析进行金融量化策略的构建。我们将从导入数据开始，使用K-Means算法对股票数据进行聚类，并尝试基于聚类结果制定简单的交易策略，最后评估其效果。

![](img/f84608097431785e9c4b2b6a11cd51e5_5.png)

---

![](img/f84608097431785e9c4b2b6a11cd51e5_7.png)

## 1. 导入工具包

![](img/f84608097431785e9c4b2b6a11cd51e5_9.png)

首先，我们需要导入必要的Python工具包。这些包与之前课程中使用的保持一致。

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
from sklearn.cluster import KMeans
```

![](img/f84608097431785e9c4b2b6a11cd51e5_11.png)

![](img/f84608097431785e9c4b2b6a11cd51e5_13.png)

---

## 2. 加载与准备数据

![](img/f84608097431785e9c4b2b6a11cd51e5_15.png)

上一节我们导入了工具包，本节中我们来看看如何加载和处理数据。我们将使用一份股票数据，并从中提取两个特征：前一天的指标和前两天的指标。这两个指标将作为我们聚类模型的输入。

![](img/f84608097431785e9c4b2b6a11cd51e5_17.png)

以下是数据准备步骤：
1.  加载数据文件。
2.  选择“前一天指标”和“前两天指标”这两列作为特征。

![](img/f84608097431785e9c4b2b6a11cd51e5_19.png)

![](img/f84608097431785e9c4b2b6a11cd51e5_21.png)

```python
# 假设数据已加载到变量 `data` 中
features = data[['prev_day_indicator', 'prev_two_days_indicator']]
```

![](img/f84608097431785e9c4b2b6a11cd51e5_23.png)

![](img/f84608097431785e9c4b2b6a11cd51e5_25.png)

---

## 3. 构建K-Means聚类模型

有了特征数据之后，我们就可以开始建模了。这次的任务与之前的回归分析不同，我们将使用聚类方法。在`sklearn`库中，`KMeans`是一个常用的聚类算法。

以下是建模步骤：
1.  从`sklearn.cluster`导入`KMeans`模块。
2.  实例化一个`KMeans`模型，并指定聚类数量（`n_clusters`）。由于我们的目标是区分“上涨”和“下跌”两种状态，因此将`n_clusters`设置为2。

![](img/f84608097431785e9c4b2b6a11cd51e5_27.png)

```python
# 实例化K-Means模型，设定聚类数为2
model = KMeans(n_clusters=2)
```

![](img/f84608097431785e9c4b2b6a11cd51e5_29.png)

---

![](img/f84608097431785e9c4b2b6a11cd51e5_31.png)

## 4. 训练模型与预测

![](img/f84608097431785e9c4b2b6a11cd51e5_33.png)

模型实例化后，我们需要用数据来训练它，并预测每个数据点属于哪个簇（类别）。

以下是具体操作：
1.  使用`.fit()`方法在特征数据上训练模型。
2.  使用`.predict()`方法获取每个数据点的预测类别标签（0或1）。

```python
# 在特征数据上训练模型
model.fit(features)

# 预测每个数据点所属的簇
data['cluster'] = model.predict(features)
```

![](img/f84608097431785e9c4b2b6a11cd51e5_35.png)

为了更直观地对应“涨”（1）和“跌”（-1），我们对预测标签进行映射转换。

![](img/f84608097431785e9c4b2b6a11cd51e5_37.png)

```python
# 将聚类标签0和1映射为-1和1
data['cluster'] = data['cluster'].apply(lambda x: 1 if x == 1 else -1)
```

![](img/f84608097431785e9c4b2b6a11cd51e5_39.png)

![](img/f84608097431785e9c4b2b6a11cd51e5_41.png)

---

![](img/f84608097431785e9c4b2b6a11cd51e5_43.png)

![](img/f84608097431785e9c4b2b6a11cd51e5_45.png)

## 5. 可视化聚类结果

![](img/f84608097431785e9c4b2b6a11cd51e5_47.png)

现在，我们已经得到了聚类结果。为了更直观地观察两个簇的分布情况，我们可以将数据点绘制成散点图。

![](img/f84608097431785e9c4b2b6a11cd51e5_49.png)

以下是绘图步骤：
1.  以“前一天指标”为X轴，“前两天指标”为Y轴。
2.  根据`cluster`列的值（-1或1）为数据点着色，以区分不同的簇。

![](img/f84608097431785e9c4b2b6a11cd51e5_51.png)

```python
plt.scatter(data['prev_day_indicator'], data['prev_two_days_indicator'], c=data['cluster'], cmap='bwr')
plt.xlabel('前一天指标')
plt.ylabel('前两天指标')
plt.title('K-Means聚类结果')
plt.show()
```

从图中可以观察到，当两个指标均为负值（即昨天和前天都下跌）时，数据点大多被分到同一个簇中，这可能暗示着“今日下跌”的趋势。

![](img/f84608097431785e9c4b2b6a11cd51e5_53.png)

---

![](img/f84608097431785e9c4b2b6a11cd51e5_55.png)

## 6. 基于聚类的简单策略与回测

![](img/f84608097431785e9c4b2b6a11cd51e5_57.png)

![](img/f84608097431785e9c4b2b6a11cd51e5_59.png)

仅仅得到聚类分组还不够，我们需要评估这个分组是否能带来盈利。接下来，我们尝试构建一个简单的策略：当预测标签为1时买入（或持有），为-1时卖出（或空仓），并计算其累积收益。

![](img/f84608097431785e9c4b2b6a11cd51e5_61.png)

![](img/f84608097431785e9c4b2b6a11cd51e5_63.png)

以下是策略回测步骤：
1.  计算策略的日收益率：`策略收益 = 预测标签 * 实际日收益率`。
2.  将策略的累积收益率与买入持有策略（即始终持有）的收益率进行对比。

![](img/f84608097431785e9c4b2b6a11cd51e5_65.png)

```python
# 计算策略收益：假设‘returns’是实际的日收益率列
data['strategy_returns'] = data['cluster'] * data['returns']

![](img/f84608097431785e9c4b2b6a11cd51e5_67.png)

# 计算累积收益（需先将对数收益还原为普通收益）
data['cumulative_returns'] = (1 + data['returns']).cumprod()
data['cumulative_strategy_returns'] = (1 + data['strategy_returns']).cumprod()

![](img/f84608097431785e9c4b2b6a11cd51e5_69.png)

![](img/f84608097431785e9c4b2b6a11cd51e5_71.png)

# 打印最终收益对比
print(f"买入持有最终收益: {data['cumulative_returns'].iloc[-1]:.4f}")
print(f"聚类策略最终收益: {data['cumulative_strategy_returns'].iloc[-1]:.4f}")
```

![](img/f84608097431785e9c4b2b6a11cd51e5_73.png)

**注意**：K-Means聚类具有随机性，每次运行结果可能不同，导致策略收益不稳定。在实际应用中，聚类分析在金融量化中的使用相对有限，主要因为其缺乏明确的标签，难以进行严谨的评估和归因分析。

![](img/f84608097431785e9c4b2b6a11cd51e5_75.png)

![](img/f84608097431785e9c4b2b6a11cd51e5_77.png)

---

![](img/f84608097431785e9c4b2b6a11cd51e5_79.png)

## 总结

![](img/f84608097431785e9c4b2b6a11cd51e5_81.png)

![](img/f84608097431785e9c4b2b6a11cd51e5_83.png)

本节课中我们一起学习了如何在Python中应用K-Means聚类进行金融数据分析。我们完成了从数据准备、模型构建、结果可视化到简单策略回测的全过程。需要强调的是，聚类作为一种无监督学习方法，在量化交易中通常不作为核心策略工具，因为其结果难以评估和稳定复现。本实例主要用于演示聚类分析的基本流程和潜在应用场景。