# 机器学习与量化交易：P55：聚类分析实例 📊

![](img/0f06618aecfe30b33c08705de6c97fb7_1.png)

![](img/0f06618aecfe30b33c08705de6c97fb7_3.png)

在本节课中，我们将学习如何在Python中使用聚类方法进行量化交易策略分析。我们将通过一个实例，使用K-Means算法对股票数据进行聚类，并观察其效果。

![](img/0f06618aecfe30b33c08705de6c97fb7_5.png)

---

![](img/0f06618aecfe30b33c08705de6c97fb7_7.png)

![](img/0f06618aecfe30b33c08705de6c97fb7_9.png)

## 第一步：导入工具包

首先，我们需要导入所有必要的Python工具包。这些工具包与之前课程中使用的保持一致。

![](img/0f06618aecfe30b33c08705de6c97fb7_11.png)

![](img/0f06618aecfe30b33c08705de6c97fb7_13.png)

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
from sklearn.cluster import KMeans
```

---

![](img/0f06618aecfe30b33c08705de6c97fb7_15.png)

## 第二步：导入并准备数据

![](img/0f06618aecfe30b33c08705de6c97fb7_17.png)

![](img/0f06618aecfe30b33c08705de6c97fb7_19.png)

上一节我们导入了工具包，本节中我们来看看如何准备数据。我们将导入数据，并提取用于建模的两个特征：前一天的指标和前两天的指标。

![](img/0f06618aecfe30b33c08705de6c97fb7_21.png)

以下是数据准备步骤：
1.  读取数据文件。
2.  选择 `‘feature_1’` 和 `‘feature_2’` 这两列作为特征。

![](img/0f06618aecfe30b33c08705de6c97fb7_23.png)

![](img/0f06618aecfe30b33c08705de6c97fb7_25.png)

```python
data = pd.read_csv(‘your_data.csv’)
features = data[[‘feature_1‘, ’feature_2‘]]
```

---

## 第三步：建立K-Means聚类模型

![](img/0f06618aecfe30b33c08705de6c97fb7_27.png)

有了准备好的特征数据后，接下来我们进行建模。这次的任务与之前的回归方法不同，我们将使用聚类方法。

![](img/0f06618aecfe30b33c08705de6c97fb7_29.png)

在scikit-learn库中，我们选择 `KMeans` 模块。K-Means算法的核心是将数据点划分为K个簇，使得每个点都属于离它最近的簇中心。

以下是建立模型的步骤：
1.  从 `sklearn.cluster` 导入 `KMeans`。
2.  实例化模型，并指定簇的数量 `n_clusters=2`，因为我们希望将数据分为“涨”和“跌”两类。
3.  使用 `.fit()` 方法在特征数据上训练模型。

![](img/0f06618aecfe30b33c08705de6c97fb7_31.png)

![](img/0f06618aecfe30b33c08705de6c97fb7_33.png)

```python
# 实例化K-Means模型，设定簇数为2
model = KMeans(n_clusters=2, random_state=42)
# 在特征数据上训练模型
model.fit(features)
```

模型参数包括初始化中心点的方法、最大迭代次数和模型重复运行的次数，以确保结果的稳定性。

---

## 第四步：预测并调整结果

![](img/0f06618aecfe30b33c08705de6c97fb7_35.png)

模型训练完成后，我们可以用它来预测每个数据点属于哪个簇。预测结果通常是0或1，但为了更直观地表示“涨”和“跌”，我们需要对其进行调整。

![](img/0f06618aecfe30b33c08705de6c97fb7_37.png)

![](img/0f06618aecfe30b33c08705de6c97fb7_39.png)

以下是预测与调整步骤：
1.  使用 `.predict()` 方法获得每个数据点的预测标签（0或1）。
2.  创建一个新的列 `‘cluster’` 来存储预测结果。
3.  使用 `np.where` 函数将标签0映射为-1（代表“跌”），标签1保持不变（代表“涨”）。

![](img/0f06618aecfe30b33c08705de6c97fb7_41.png)

![](img/0f06618aecfe30b33c08705de6c97fb7_43.png)

```python
# 预测数据点的簇归属
predictions = model.predict(features)
# 将预测结果添加到数据中，并调整标签含义
data[‘cluster’] = np.where(predictions == 1, 1, -1)
```

![](img/0f06618aecfe30b33c08705de6c97fb7_45.png)

![](img/0f06618aecfe30b33c08705de6c97fb7_47.png)

---

![](img/0f06618aecfe30b33c08705de6c97fb7_49.png)

## 第五步：可视化聚类结果

![](img/0f06618aecfe30b33c08705de6c97fb7_51.png)

为了直观地查看聚类效果，我们可以将结果用散点图画出来。不同的颜色代表不同的簇。

以下是可视化步骤：
1.  以 `feature_1` 为X轴，`feature_2` 为Y轴绘制散点图。
2.  根据 `‘cluster’` 列的值（1或-1）为数据点着色。

![](img/0f06618aecfe30b33c08705de6c97fb7_53.png)

```python
plt.scatter(features.iloc[:, 0], features.iloc[:, 1], c=data[‘cluster’], cmap=‘coolwarm’)
plt.xlabel(‘Feature 1 (Previous Day)’)
plt.ylabel(‘Feature 2 (Day Before)’)
plt.title(‘K-Means Clustering Results’)
plt.show()
```

![](img/0f06618aecfe30b33c08705de6c97fb7_55.png)

![](img/0f06618aecfe30b33c08705de6c97fb7_57.png)

在图中，蓝色点可能代表一个簇（例如“跌”），红色点代表另一个簇（例如“涨”）。可以观察到，当两个特征值都为负时，数据点更可能被归为“跌”的类别。

![](img/0f06618aecfe30b33c08705de6c97fb7_59.png)

---

![](img/0f06618aecfe30b33c08705de6c97fb7_61.png)

![](img/0f06618aecfe30b33c08705de6c97fb7_63.png)

## 第六步：评估策略效果

![](img/0f06618aecfe30b33c08705de6c97fb7_65.png)

![](img/0f06618aecfe30b33c08705de6c97fb7_67.png)

现在，我们有了基于聚类的“涨/跌”预测。接下来，我们需要评估这个简单的策略是否能带来收益。

![](img/0f06618aecfe30b33c08705de6c97fb7_69.png)

以下是评估步骤：
1.  计算如果按照聚类标签进行交易（1代表买入，-1代表卖出或做空）的累计收益。
2.  将计算出的收益与原始收益进行对比。

![](img/0f06618aecfe30b33c08705de6c97fb7_71.png)

![](img/0f06618aecfe30b33c08705de6c97fb7_73.png)

```python
# 计算聚类策略的收益（假设‘returns’是原始日收益率列）
data[‘strategy_returns’] = data[‘cluster’].shift(1) * data[‘returns’]
# 计算累计收益
cumulative_returns = (1 + data[[‘returns‘, ’strategy_returns‘]]).cumprod()
# 绘制收益曲线对比
cumulative_returns.plot()
plt.show()
```

![](img/0f06618aecfe30b33c08705de6c97fb7_75.png)

需要注意的是，由于K-Means聚类本身具有随机性（如中心点初始化），每次运行的结果和收益可能不同。在实际应用中，聚类方法缺乏明确的标签进行评估，因此其效果和稳定性往往不如有监督学习方法。

![](img/0f06618aecfe30b33c08705de6c97fb7_77.png)

![](img/0f06618aecfe30b33c08705de6c97fb7_79.png)

---

![](img/0f06618aecfe30b33c08705de6c97fb7_81.png)

![](img/0f06618aecfe30b33c08705de6c97fb7_83.png)

本节课中我们一起学习了如何在量化交易中使用K-Means聚类。我们完成了从数据准备、模型建立、结果预测到可视化评估的完整流程。虽然聚类在某些探索性场景下可以提供洞察，但由于其无监督的特性及结果的随机性，在严谨的策略开发中需谨慎使用。核心在于理解聚类作为一种工具，在特定情况下的应用方法与局限性。