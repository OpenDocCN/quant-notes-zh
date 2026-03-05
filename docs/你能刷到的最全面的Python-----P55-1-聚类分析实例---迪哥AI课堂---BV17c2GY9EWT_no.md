# Python金融分析量化交易：P55：聚类分析实例 📊

![](img/b8d78aecdbaea137ecc4ab7a6d294609_1.png)

![](img/b8d78aecdbaea137ecc4ab7a6d294609_3.png)

在本节课中，我们将学习如何在Python中使用聚类分析进行金融量化交易策略的探索。我们将通过一个实例，使用K-Means算法对股票数据进行聚类，并尝试根据聚类结果构建简单的交易策略。

![](img/b8d78aecdbaea137ecc4ab7a6d294609_5.png)

## 工具包与数据导入 🔧

上一节我们介绍了量化分析的基本流程，本节中我们来看看如何为聚类分析准备环境与数据。

![](img/b8d78aecdbaea137ecc4ab7a6d294609_7.png)

![](img/b8d78aecdbaea137ecc4ab7a6d294609_9.png)

首先，导入所有必要的工具包。这些工具包与之前课程中使用的一致。

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
from sklearn.cluster import KMeans
```

![](img/b8d78aecdbaea137ecc4ab7a6d294609_11.png)

接下来，导入我们将要使用的数据。上节课提到，我们需要从数据中提取用于建模的特征。

![](img/b8d78aecdbaea137ecc4ab7a6d294609_13.png)

```python
# 假设数据已加载到变量 `data` 中
# 这里我们选择两个特征：前一天的指标和前两天的指标
features = data[['feature_day_before', 'feature_two_days_before']]
```

## 构建K-Means聚类模型 🤖

![](img/b8d78aecdbaea137ecc4ab7a6d294609_15.png)

有了特征数据后，我们就可以开始建模了。这次的任务与之前的回归方法不同，我们将使用聚类方法。

![](img/b8d78aecdbaea137ecc4ab7a6d294609_17.png)

在scikit-learn库中，有一个非常实用的聚类算法叫做K-Means。我们首先需要导入这个模块。

![](img/b8d78aecdbaea137ecc4ab7a6d294609_19.png)

![](img/b8d78aecdbaea137ecc4ab7a6d294609_21.png)

```python
from sklearn.cluster import KMeans
```

![](img/b8d78aecdbaea137ecc4ab7a6d294609_23.png)

导入模块后，实例化一个K-Means模型。这里需要我们指定一个关键参数：`n_clusters`，即我们希望将数据分成多少个簇（K值）。

![](img/b8d78aecdbaea137ecc4ab7a6d294609_25.png)

在本任务中，我们希望判断股票是上涨还是下跌，因此只需要两个结果（涨或跌）。所以我们将K值设为2。

```python
# 实例化K-Means模型，指定簇的数量为2
model = KMeans(n_clusters=2)
```

然后，使用我们的特征数据来拟合（训练）这个模型。

```python
# 使用选定的特征数据拟合模型
model.fit(features)
```

![](img/b8d78aecdbaea137ecc4ab7a6d294609_27.png)

模型拟合完成后，会显示一些初始化参数，例如中心点初始化方法、最大迭代次数、簇的数量以及模型重复运行的次数。K-Means的结果具有一定随机性，因此模型默认会运行多次（例如10次），并返回整体最优的聚类结果。若想了解详细参数，可以查阅scikit-learn的官方文档。

![](img/b8d78aecdbaea137ecc4ab7a6d294609_29.png)

## 预测与结果处理 📈

模型建立后，我们可以用它来预测当前所有数据点所属的簇。

![](img/b8d78aecdbaea137ecc4ab7a6d294609_31.png)

以下是预测当前数据点所属簇的步骤。

![](img/b8d78aecdbaea137ecc4ab7a6d294609_33.png)

```python
# 预测每个数据点属于哪个簇（0或1）
cluster_labels = model.predict(features)

# 将预测结果添加到原始数据中
data['cluster'] = cluster_labels
```

预测结果通常是0和1。但为了更直观（例如用1代表涨，-1代表跌），我们可以对标签进行转换。

```python
# 将聚类标签0和1映射为-1和1
data['cluster'] = data['cluster'].apply(lambda x: 1 if x == 1 else -1)
```

![](img/b8d78aecdbaea137ecc4ab7a6d294609_35.png)

现在，数据中新增了一列 `cluster`，其值为1或-1，分别代表我们模型认为的两种状态。

![](img/b8d78aecdbaea137ecc4ab7a6d294609_37.png)

## 可视化聚类结果 📊

![](img/b8d78aecdbaea137ecc4ab7a6d294609_39.png)

![](img/b8d78aecdbaea137ecc4ab7a6d294609_41.png)

为了直观地查看聚类效果，我们可以将数据点用散点图画出来，并用不同颜色区分不同的簇。

![](img/b8d78aecdbaea137ecc4ab7a6d294609_43.png)

以下是绘制聚类结果散点图的步骤。

![](img/b8d78aecdbaea137ecc4ab7a6d294609_45.png)

![](img/b8d78aecdbaea137ecc4ab7a6d294609_47.png)

```python
# 绘制散点图
plt.scatter(features.iloc[:, 0],  # X轴：第一个特征（前一天指标）
            features.iloc[:, 1],  # Y轴：第二个特征（前两天指标）
            c=data['cluster'],    # 颜色由簇标签决定
            cmap='coolwarm')      # 使用从蓝到红的颜色映射
plt.xlabel('Feature Day Before')
plt.ylabel('Feature Two Days Before')
plt.title('K-Means Clustering Results')
plt.show()
```

在生成的图中，蓝色点代表一个簇，红色点代表另一个簇。我们可以观察数据的分布模式，例如，当两个特征值都为负（即昨天和前天都下跌）时，数据点可能更多地集中在代表“跌”的簇中。

![](img/b8d78aecdbaea137ecc4ab7a6d294609_49.png)

![](img/b8d78aecdbaea137ecc4ab7a6d294609_51.png)

## 评估聚类策略效果 💹

现在，一个关键问题是：仅凭聚类得到的标签（1或-1）来制定交易策略，是否可靠？

实际上，在量化交易中，单纯使用聚类策略的情况并不多见，因为其效果和可解释性往往不如有监督学习方法。本节的主要目的是让大家熟悉聚类在Python中的实现流程。

![](img/b8d78aecdbaea137ecc4ab7a6d294609_53.png)

尽管如此，我们仍可以简单计算一下，如果按照聚类标签进行交易（例如，标签为1时买入，为-1时卖出或做空），最终的收益情况如何。

![](img/b8d78aecdbaea137ecc4ab7a6d294609_55.png)

![](img/b8d78aecdbaea137ecc4ab7a6d294609_57.png)

首先，我们需要将之前对收益率取对数的操作还原。

![](img/b8d78aecdbaea137ecc4ab7a6d294609_59.png)

```python
# 假设原始收益率列名为 ‘returns’，我们对其取过对数，现在需还原
data['returns_exp'] = np.exp(data['returns'])
```

![](img/b8d78aecdbaea137ecc4ab7a6d294609_61.png)

然后，计算按照聚类信号交易的累积收益，并与基准（如买入持有）进行比较。

![](img/b8d78aecdbaea137ecc4ab7a6d294609_63.png)

```python
# 计算聚类策略的收益：当cluster为1时，获得当日收益；为-1时，获得当日收益的负值（模拟做空）
data['strategy_returns'] = data['cluster'].shift(1) * data['returns_exp']

![](img/b8d78aecdbaea137ecc4ab7a6d294609_65.png)

![](img/b8d78aecdbaea137ecc4ab7a6d294609_67.png)

# 计算策略和基准的累积收益
cumulative_strategy = (1 + data['strategy_returns']).cumprod()
cumulative_baseline = (1 + data['returns_exp']).cumprod()

![](img/b8d78aecdbaea137ecc4ab7a6d294609_69.png)

# 绘制收益曲线对比
plt.plot(cumulative_strategy, label='Clustering Strategy')
plt.plot(cumulative_baseline, label='Buy & Hold Baseline')
plt.legend()
plt.title('Cumulative Returns: Strategy vs Baseline')
plt.show()
```

![](img/b8d78aecdbaea137ecc4ab7a6d294609_71.png)

需要注意的是，由于K-Means算法本身的随机性（如初始中心点的选择），每次运行的结果可能不同，从而导致策略收益波动。因此，在实际应用中，聚类分析通常不作为核心策略工具，更多是用于数据探索或特征工程阶段。

![](img/b8d78aecdbaea137ecc4ab7a6d294609_73.png)

## 总结 📝

![](img/b8d78aecdbaea137ecc4ab7a6d294609_75.png)

![](img/b8d78aecdbaea137ecc4ab7a6d294609_77.png)

本节课中我们一起学习了如何在Python金融分析中应用K-Means聚类算法。

![](img/b8d78aecdbaea137ecc4ab7a6d294609_79.png)

![](img/b8d78aecdbaea137ecc4ab7a6d294609_81.png)

1.  **准备阶段**：我们导入了必要的库并选择了用于聚类的特征数据。
2.  **模型构建**：使用scikit-learn的`KMeans`模块，指定簇数（K=2）来建立模型，并用数据拟合模型。
3.  **预测与处理**：利用拟合好的模型预测数据点所属簇，并对结果标签进行了直观的映射处理。
4.  **结果可视化**：通过散点图将聚类结果可视化，直观地观察了数据的分类情况。
5.  **策略初探与评估**：我们尝试基于聚类标签构建了一个简单的多空交易策略，并计算了其收益。同时，我们也认识到聚类方法在量化交易中的局限性，如结果随机性强、缺乏可解释性和稳定的评估标准。

![](img/b8d78aecdbaea137ecc4ab7a6d294609_83.png)

聚类分析是一种强大的无监督学习工具，但在金融量化领域，它通常用于辅助理解数据结构和生成特征，而非直接作为交易信号源。当传统有监督方法遇到瓶颈时，聚类可以作为一种补充性的探索手段。