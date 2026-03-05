# Python金融分析与量化交易实战：P55：聚类分析实例 📊

![](img/fecc6a14a798c308a82df8f5b3726dcc_1.png)

![](img/fecc6a14a798c308a82df8f5b3726dcc_3.png)

在本节课中，我们将学习如何在Python中使用聚类分析（特别是K-Means算法）来构建一个简单的量化交易策略。我们将从数据导入开始，逐步完成特征选择、模型构建、结果预测和可视化，并最终评估策略的收益表现。

![](img/fecc6a14a798c308a82df8f5b3726dcc_5.png)

---

![](img/fecc6a14a798c308a82df8f5b3726dcc_7.png)

![](img/fecc6a14a798c308a82df8f5b3726dcc_9.png)

## 第一步：导入工具包 🔧

首先，我们需要导入所有必要的Python工具包。这些工具包与之前课程中使用的保持一致。

![](img/fecc6a14a798c308a82df8f5b3726dcc_11.png)

![](img/fecc6a14a798c308a82df8f5b3726dcc_13.png)

```python
import pandas as pd
import numpy as np
from sklearn.cluster import KMeans
import matplotlib.pyplot as plt
```

---

![](img/fecc6a14a798c308a82df8f5b3726dcc_15.png)

![](img/fecc6a14a798c308a82df8f5b3726dcc_17.png)

## 第二步：导入并准备数据 📈

![](img/fecc6a14a798c308a82df8f5b3726dcc_19.png)

上一节我们导入了工具包，本节中我们来看看如何准备数据。我们使用一份金融数据，并从中提取两个特征用于建模。

![](img/fecc6a14a798c308a82df8f5b3726dcc_21.png)

![](img/fecc6a14a798c308a82df8f5b3726dcc_23.png)

以下是数据准备步骤：
1.  导入数据集。
2.  选择两个关键特征：`feature_1`（代表前一天的指标）和 `feature_2`（代表前两天的指标）。

![](img/fecc6a14a798c308a82df8f5b3726dcc_25.png)

```python
# 假设数据已加载到变量 `data` 中
# 这里我们选择两个特征列
features = data[['feature_1', 'feature_2']]
```

---

## 第三步：构建K-Means聚类模型 🤖

![](img/fecc6a14a798c308a82df8f5b3726dcc_27.png)

有了特征数据后，我们就可以开始建模了。这次的任务与之前的回归分析不同，我们将使用聚类方法。

![](img/fecc6a14a798c308a82df8f5b3726dcc_29.png)

在scikit-learn库中，`KMeans` 是一个常用的聚类算法。以下是建模步骤：
1.  从 `sklearn.cluster` 导入 `KMeans` 模块。
2.  实例化模型，并指定要聚类的簇数（K值）。在本例中，我们希望将数据点分为“上涨”和“下跌”两类，因此设置 `n_clusters=2`。
3.  使用 `.fit()` 方法在特征数据上训练模型。

![](img/fecc6a14a798c308a82df8f5b3726dcc_31.png)

```python
# 实例化K-Means模型，设定簇数为2
model = KMeans(n_clusters=2, random_state=42)
# 在特征数据上训练模型
model.fit(features)
```

![](img/fecc6a14a798c308a82df8f5b3726dcc_33.png)

---

## 第四步：预测并处理聚类结果 🏷️

模型训练完成后，我们可以用它来预测每个数据点属于哪个簇。

![](img/fecc6a14a798c308a82df8f5b3726dcc_35.png)

以下是预测与结果处理的步骤：
1.  使用 `.predict()` 方法获取每个样本的预测标签（0或1）。
2.  将预测结果添加到原始数据框中。
3.  为了方便理解，我们将标签0映射为“-1”（代表下跌），标签1映射为“1”（代表上涨）。

![](img/fecc6a14a798c308a82df8f5b3726dcc_37.png)

```python
# 预测每个数据点所属的簇
predictions = model.predict(features)
# 将预测结果添加到数据中
data['cluster_raw'] = predictions
# 将标签0映射为-1，标签1映射为1
data['cluster'] = data['cluster_raw'].apply(lambda x: 1 if x == 1 else -1)
```

![](img/fecc6a14a798c308a82df8f5b3726dcc_39.png)

![](img/fecc6a14a798c308a82df8f5b3726dcc_41.png)

---

![](img/fecc6a14a798c308a82df8f5b3726dcc_43.png)

![](img/fecc6a14a798c308a82df8f5b3726dcc_45.png)

## 第五步：可视化聚类结果 👁️

![](img/fecc6a14a798c308a82df8f5b3726dcc_47.png)

为了直观地查看聚类效果，我们可以将数据点用散点图绘制出来，并用不同颜色区分不同的簇。

![](img/fecc6a14a798c308a82df8f5b3726dcc_49.png)

![](img/fecc6a14a798c308a82df8f5b3726dcc_51.png)

以下是绘制散点图的代码：
```python
plt.figure(figsize=(10, 6))
# 绘制散点图，x轴和y轴分别为两个特征，颜色根据聚类结果区分
plt.scatter(data['feature_1'], data['feature_2'], c=data['cluster'], cmap='bwr', alpha=0.6)
plt.xlabel('Feature 1 (Previous Day)')
plt.ylabel('Feature 2 (Day Before)')
plt.title('K-Means Clustering Results')
plt.colorbar(label='Cluster Label (1=Up, -1=Down)')
plt.show()
```
在生成的图中，蓝色点可能代表一个簇（例如“下跌”），红色点代表另一个簇（例如“上涨”）。我们可以观察特征与聚类结果之间的关系。

---

![](img/fecc6a14a798c308a82df8f5b3726dcc_53.png)

## 第六步：评估策略收益 💹

![](img/fecc6a14a798c308a82df8f5b3726dcc_55.png)

现在，我们来评估基于聚类结果的简单交易策略的收益表现。基本思路是：当预测标签为1（上涨）时，我们假设做多；当预测标签为-1（下跌）时，我们假设做空或空仓。

![](img/fecc6a14a798c308a82df8f5b3726dcc_57.png)

![](img/fecc6a14a798c308a82df8f5b3726dcc_59.png)

以下是计算策略收益的步骤：
1.  计算每日的真实收益率 `returns`。
2.  将聚类预测标签与每日收益率相乘，得到策略的日收益率序列。公式为：`策略日收益率 = 预测标签 * 真实日收益率`。
3.  计算策略的累计收益，并与基准（如买入持有）进行比较。

![](img/fecc6a14a798c308a82df8f5b3726dcc_61.png)

![](img/fecc6a14a798c308a82df8f5b3726dcc_63.png)

```python
# 计算策略的日收益率：如果预测为上涨(cluster=1)，则收益为正向；预测为下跌(cluster=-1)，则收益为反向。
data['strategy_returns'] = data['cluster'] * data['returns']
# 计算策略的累计收益
data['cumulative_strategy'] = (1 + data['strategy_returns']).cumprod()
# 计算基准（买入持有）的累计收益
data['cumulative_baseline'] = (1 + data['returns']).cumprod()

![](img/fecc6a14a798c308a82df8f5b3726dcc_65.png)

# 输出最终累计收益
print(f"策略最终累计收益: {data['cumulative_strategy'].iloc[-1]:.4f}")
print(f"基准最终累计收益: {data['cumulative_baseline'].iloc[-1]:.4f}")
```
**注意**：由于K-Means聚类具有随机性（尤其是中心点初始化），每次运行的结果可能不同，导致收益评估不稳定。在实际应用中，需要多次运行并分析结果的稳定性。

![](img/fecc6a14a798c308a82df8f5b3726dcc_67.png)

![](img/fecc6a14a798c308a82df8f5b3726dcc_69.png)

---

![](img/fecc6a14a798c308a82df8f5b3726dcc_71.png)

![](img/fecc6a14a798c308a82df8f5b3726dcc_73.png)

## 总结 📝

![](img/fecc6a14a798c308a82df8f5b3726dcc_75.png)

本节课中我们一起学习了如何在量化交易中使用聚类分析。
1.  **流程回顾**：我们从数据准备开始，选择了两个特征，使用 `KMeans` 算法将数据点聚为两类，并将结果映射为交易信号（1和-1）。
2.  **结果可视化**：通过散点图直观展示了聚类效果。
3.  **策略评估**：构建了一个简单的基于聚类信号的交易策略，并计算了其收益。我们发现，由于聚类算法的随机性和缺乏明确的标签进行模型评估，其在实际交易中的直接应用效果可能不稳定且难以精确分析。

![](img/fecc6a14a798c308a82df8f5b3726dcc_77.png)

![](img/fecc6a14a798c308a82df8f5b3726dcc_79.png)

因此，聚类分析通常不作为量化策略的首选核心方法，而是在探索性数据分析或无监督学习场景中更为常用。理解其原理和实现方式，有助于我们在合适的场景下运用这一工具。

![](img/fecc6a14a798c308a82df8f5b3726dcc_81.png)

![](img/fecc6a14a798c308a82df8f5b3726dcc_83.png)

---