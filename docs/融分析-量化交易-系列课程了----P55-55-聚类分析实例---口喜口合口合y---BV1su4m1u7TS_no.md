# Python金融分析+量化交易：P55：聚类分析实例 📊

![](img/1746e67709d93666dd32cef4b6574451_1.png)

在本节课中，我们将学习如何在Python中使用聚类分析，特别是K-Means算法，来对金融数据进行分组，并尝试将其应用于一个简单的量化交易策略中。我们将从数据导入、特征选择、模型构建到结果可视化，完整地走一遍流程。

![](img/1746e67709d93666dd32cef4b6574451_3.png)

---

![](img/1746e67709d93666dd32cef4b6574451_5.png)

## 第一步：导入工具包 🧰

![](img/1746e67709d93666dd32cef4b6574451_7.png)

首先，我们需要导入进行数据分析和建模所需的Python工具包。这些包与之前课程中使用的一致。

![](img/1746e67709d93666dd32cef4b6574451_9.png)

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
from sklearn.cluster import KMeans
```

---

![](img/1746e67709d93666dd32cef4b6574451_11.png)

![](img/1746e67709d93666dd32cef4b6574451_13.png)

## 第二步：导入与准备数据 📈

上一节我们导入了工具包，本节中我们来看看如何准备数据。我们将导入一份金融数据集，并从中提取用于建模的特征。

我们选择了两个特征：`feature_1`（代表前一天的某个指标）和 `feature_2`（代表前两天的某个指标）。这两个指标将作为我们聚类模型的输入。

![](img/1746e67709d93666dd32cef4b6574451_15.png)

![](img/1746e67709d93666dd32cef4b6574451_17.png)

```python
# 假设data是一个包含金融时间序列的DataFrame
# 这里我们创建两个特征列作为示例
data['feature_1'] = data['returns'].shift(1)  # 前一天的收益率
data['feature_2'] = data['returns'].shift(2)  # 前两天的收益率
# 删除包含NaN值的行
data = data.dropna()
```

---

![](img/1746e67709d93666dd32cef4b6574451_19.png)

![](img/1746e67709d93666dd32cef4b6574451_21.png)

## 第三步：构建K-Means聚类模型 🤖

![](img/1746e67709d93666dd32cef4b6574451_23.png)

有了特征数据之后，我们就可以开始建模了。这次的任务与之前的回归分析不同，我们将使用无监督学习中的聚类方法。

![](img/1746e67709d93666dd32cef4b6574451_25.png)

在scikit-learn库中，有一个非常实用的聚类算法叫做K-Means。以下是使用它的步骤：

首先，从`sklearn.cluster`模块中导入`KMeans`类。

```python
from sklearn.cluster import KMeans
```

接着，实例化一个K-Means模型。在这个任务中，我们希望将数据点分成两类（例如，代表“上涨”和“下跌”），因此需要指定聚类数量 `n_clusters=2`。

![](img/1746e67709d93666dd32cef4b6574451_27.png)

```python
model = KMeans(n_clusters=2)
```

![](img/1746e67709d93666dd32cef4b6574451_29.png)

然后，使用我们的特征数据来训练（拟合）这个模型。

```python
# 假设X是我们准备好的特征数据，例如 data[['feature_1', 'feature_2']]
X = data[['feature_1', 'feature_2']].values
model.fit(X)
```

![](img/1746e67709d93666dd32cef4b6574451_31.png)

模型拟合完成后，会显示一些初始化参数，例如中心点初始化方法、最大迭代次数等。K-Means算法为了得到更稳定的结果，默认会运行多次（`n_init`参数控制）并选择最佳的一次。

![](img/1746e67709d93666dd32cef4b6574451_33.png)

---

## 第四步：预测与结果调整 🔮

模型训练好后，我们可以用它来预测每个数据点属于哪个簇（类别）。

```python
# 预测每个样本的簇标签
labels = model.predict(X)
# 将预测结果添加到原数据中
data['cluster'] = labels
```

![](img/1746e67709d93666dd32cef4b6574451_35.png)

初始预测结果是0和1。为了更直观地理解（例如，用1代表“涨”，-1代表“跌”），我们可以对标签进行映射调整。

```python
# 将簇标签0映射为-1，标签1映射为1（或其他你认为合理的映射）
data['cluster_adjusted'] = data['cluster'].apply(lambda x: 1 if x == 1 else -1)
```

![](img/1746e67709d93666dd32cef4b6574451_37.png)

![](img/1746e67709d93666dd32cef4b6574451_39.png)

---

![](img/1746e67709d93666dd32cef4b6574451_41.png)

## 第五步：结果可视化 📊

![](img/1746e67709d93666dd32cef4b6574451_43.png)

![](img/1746e67709d93666dd32cef4b6574451_45.png)

为了直观地查看聚类效果，我们可以将数据点在二维平面上用散点图画出来，并用不同颜色区分不同的簇。

![](img/1746e67709d93666dd32cef4b6574451_47.png)

以下是绘制散点图的代码：

```python
plt.figure(figsize=(10, 6))
# 绘制散点图，x轴为feature_1，y轴为feature_2，颜色根据调整后的簇标签区分
scatter = plt.scatter(data['feature_1'], data['feature_2'], c=data['cluster_adjusted'], cmap='coolwarm', alpha=0.6)
plt.xlabel('Feature 1 (t-1)')
plt.ylabel('Feature 2 (t-2)')
plt.title('K-Means Clustering Results')
plt.colorbar(scatter, label='Cluster (-1 or 1)')
plt.grid(True, linestyle='--', alpha=0.5)
plt.show()
```

![](img/1746e67709d93666dd32cef4b6574451_49.png)

![](img/1746e67709d93666dd32cef4b6574451_51.png)

在生成的图中，蓝色和红色点分别代表被归为不同簇的数据。我们可以观察是否存在某种模式，例如当两个特征都为负值时，数据点是否更多地被分到代表“下跌”的簇中。

---

## 第六步：策略回测与评估 💹

![](img/1746e67709d93666dd32cef4b6574451_53.png)

仅仅完成聚类还不够，我们需要评估这个基于聚类的简单策略是否有效。一个基本的想法是：如果数据点被分到“上涨”簇，我们就假设第二天会上涨并做多；如果被分到“下跌”簇，我们就假设第二天会下跌并做空（或不做多）。

![](img/1746e67709d93666dd32cef4b6574451_55.png)

以下是计算策略收益的步骤：

![](img/1746e67709d93666dd32cef4b6574451_57.png)

首先，根据调整后的簇标签生成交易信号。假设我们简单地认为簇标签就是第二天的涨跌方向信号。

![](img/1746e67709d93666dd32cef4b6574451_59.png)

![](img/1746e67709d93666dd32cef4b6574451_61.png)

```python
# 假设‘returns’是原始的未来一期收益率
# 我们的策略收益是：信号 * 未来收益率
data['strategy_returns'] = data['cluster_adjusted'] * data['returns'].shift(-1)
# 注意：这里使用了未来数据，在实际回测中需要非常小心，避免使用未来函数。
# 本例为简化演示，实际应用中需严格按时间顺序计算。
data = data.dropna()
```

![](img/1746e67709d93666dd32cef4b6574451_63.png)

然后，计算策略的累计收益，并与“买入并持有”基准（即原始收益率）进行对比。

![](img/1746e67709d93666dd32cef4b6574451_65.png)

```python
# 计算累计收益
data['cumulative_returns'] = (1 + data['returns']).cumprod()
data['cumulative_strategy_returns'] = (1 + data['strategy_returns']).cumprod()

![](img/1746e67709d93666dd32cef4b6574451_67.png)

# 绘制收益曲线对比
plt.figure(figsize=(12, 6))
plt.plot(data['cumulative_returns'], label='Buy & Hold', linewidth=2)
plt.plot(data['cumulative_strategy_returns'], label='Clustering Strategy', linewidth=2)
plt.xlabel('Time')
plt.ylabel('Cumulative Returns')
plt.title('Strategy Backtest: Clustering vs Buy & Hold')
plt.legend()
plt.grid(True, linestyle='--', alpha=0.5)
plt.show()
```

![](img/1746e67709d93666dd32cef4b6574451_69.png)

![](img/1746e67709d93666dd32cef4b6574451_71.png)

**注意**：由于K-Means聚类本身具有随机性（初始中心点随机选择），每次运行的结果可能不同，导致策略收益不稳定。因此，在实际应用中，需要对聚类结果进行多次运行和评估，并谨慎使用。

![](img/1746e67709d93666dd32cef4b6574451_73.png)

---

![](img/1746e67709d93666dd32cef4b6574451_75.png)

## 总结 📝

![](img/1746e67709d93666dd32cef4b6574451_77.png)

本节课中我们一起学习了如何在Python中应用K-Means聚类分析于金融数据。我们完成了从数据准备、模型构建、聚类预测到结果可视化和简单策略回测的全过程。

![](img/1746e67709d93666dd32cef4b6574451_79.png)

![](img/1746e67709d93666dd32cef4b6574451_81.png)

核心要点包括：
1.  **K-Means算法**：一种无监督聚类算法，通过`sklearn.cluster.KMeans`实现，核心是迭代寻找簇中心点。公式上，它最小化所有点到其所属簇中心的距离平方和。
2.  **应用流程**：导入数据 -> 选择特征 -> 实例化模型 (`KMeans(n_clusters=2)`) -> 拟合模型 (`model.fit(X)`) -> 预测标签 (`model.predict(X)`)。
3.  **策略思路**：将聚类结果（两类）映射为交易信号（如1和-1），与未来收益率结合计算策略收益。
4.  **注意事项**：聚类分析在量化交易中应用有限，主要是因为其缺乏明确的标签，结果难以评估和解释，且具有随机性。它通常不作为首选的预测模型，而是在探索性数据分析或特定场景下使用。

![](img/1746e67709d93666dd32cef4b6574451_83.png)

通过这个实例，你应当掌握了聚类分析的基本操作，并理解了将其应用于金融数据分析时的潜在价值和局限性。