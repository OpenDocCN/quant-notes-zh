# Python金融量化交易：P55：聚类分析实例 📊

![](img/9bcae3d091d7b583dbe2c48b33655a28_1.png)

![](img/9bcae3d091d7b583dbe2c48b33655a28_3.png)

在本节课中，我们将学习如何在Python中使用聚类分析（K-Means算法）进行量化交易策略的初步探索。我们将从数据导入、特征选择开始，逐步完成模型构建、结果预测与可视化，并最终评估该策略的简单收益表现。

![](img/9bcae3d091d7b583dbe2c48b33655a28_5.png)

---

![](img/9bcae3d091d7b583dbe2c48b33655a28_7.png)

## 第一步：导入工具包

![](img/9bcae3d091d7b583dbe2c48b33655a28_9.png)

首先，我们需要导入所有必要的Python工具包。这与之前课程中的步骤一致。

![](img/9bcae3d091d7b583dbe2c48b33655a28_11.png)

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
from sklearn.cluster import KMeans
```

![](img/9bcae3d091d7b583dbe2c48b33655a28_13.png)

---

![](img/9bcae3d091d7b583dbe2c48b33655a28_15.png)

## 第二步：导入与准备数据

![](img/9bcae3d091d7b583dbe2c48b33655a28_17.png)

上一节我们导入了工具包，本节中我们来看看如何准备数据。我们将导入数据集，并从中提取用于建模的两个特征。

![](img/9bcae3d091d7b583dbe2c48b33655a28_19.png)

![](img/9bcae3d091d7b583dbe2c48b33655a28_21.png)

以下是数据准备步骤：
*   导入数据集。
*   选择两个特征列：`‘前一天指标’` 和 `‘前两天指标’`。这两个指标将作为聚类模型的输入。

![](img/9bcae3d091d7b583dbe2c48b33655a28_23.png)

![](img/9bcae3d091d7b583dbe2c48b33655a28_25.png)

---

## 第三步：构建K-Means聚类模型

有了准备好的特征数据，接下来我们进行建模。与之前使用的回归方法不同，本次任务我们采用无监督学习中的聚类方法。

我们将使用`sklearn`库中的`KMeans`算法。首先需要实例化模型，并指定一个关键参数：簇的数量（K值）。在本例中，我们希望将数据点分为两类（例如，代表“上涨”和“下跌”的趋势），因此设置`n_clusters=2`。

![](img/9bcae3d091d7b583dbe2c48b33655a28_27.png)

```python
# 实例化K-Means模型，设定簇的数量为2
model = KMeans(n_clusters=2)
```

![](img/9bcae3d091d7b583dbe2c48b33655a28_29.png)

---

![](img/9bcae3d091d7b583dbe2c48b33655a28_31.png)

## 第四步：训练模型与预测

![](img/9bcae3d091d7b583dbe2c48b33655a28_33.png)

模型实例化后，我们用数据对其进行训练（拟合），然后预测每个数据点所属的簇。

以下是具体操作：
1.  使用`fit`方法，将选定的特征数据传入模型进行训练。
2.  使用`predict`方法，获取每个数据点的预测类别（0或1）。
3.  为了更直观地理解结果，我们将预测的标签0和1映射为-1和1（例如，-1代表看跌，1代表看涨）。

```python
# 使用特征数据训练模型
model.fit(data[['前一天指标', '前两天指标']])

![](img/9bcae3d091d7b583dbe2c48b33655a28_35.png)

# 预测数据点所属的簇
data['cluster'] = model.predict(data[['前一天指标', '前两天指标']])

![](img/9bcae3d091d7b583dbe2c48b33655a28_37.png)

# 将聚类标签0和1映射为-1和1
data['cluster'] = data['cluster'].where(data['cluster'] == 1, -1)
```

![](img/9bcae3d091d7b583dbe2c48b33655a28_39.png)

---

![](img/9bcae3d091d7b583dbe2c48b33655a28_41.png)

![](img/9bcae3d091d7b583dbe2c48b33655a28_43.png)

## 第五步：结果可视化

![](img/9bcae3d091d7b583dbe2c48b33655a28_45.png)

![](img/9bcae3d091d7b583dbe2c48b33655a28_47.png)

现在，我们已经得到了每个数据点的类别归属。为了更直观地观察聚类效果，我们可以将结果用散点图可视化。

![](img/9bcae3d091d7b583dbe2c48b33655a28_49.png)

在散点图中，X轴和Y轴分别代表两个特征，点的颜色则根据其所属的簇（-1或1）进行区分。

![](img/9bcae3d091d7b583dbe2c48b33655a28_51.png)

```python
plt.scatter(data['前一天指标'], data['前两天指标'], c=data['cluster'], cmap='bwr')
plt.xlabel('前一天指标')
plt.ylabel('前两天指标')
plt.title('K-Means聚类结果')
plt.show()
```

从图中可以观察两个簇的分布情况。例如，当两个特征值均为负（代表连续下跌）的数据点可能被聚为一类，这或许暗示着某种持续趋势。

![](img/9bcae3d091d7b583dbe2c48b33655a28_53.png)

---

![](img/9bcae3d091d7b583dbe2c48b33655a28_55.png)

![](img/9bcae3d091d7b583dbe2c48b33655a28_57.png)

## 第六步：策略效果简单评估

![](img/9bcae3d091d7b583dbe2c48b33655a28_59.png)

最后，我们来简单评估一下基于此聚类结果的交易策略效果。我们假设：当预测标签为1时买入/持有，为-1时卖出/做空（或空仓），然后计算其累积收益，并与基准（如简单持有）进行比较。

![](img/9bcae3d091d7b583dbe2c48b33655a28_61.png)

![](img/9bcae3d091d7b583dbe2c48b33655a28_63.png)

需要注意的是，聚类分析是一种无监督方法，缺乏明确的标签进行效果验证，且结果可能因初始中心点随机选择而波动。因此，在实际量化交易中需谨慎使用。

![](img/9bcae3d091d7b583dbe2c48b33655a28_65.png)

![](img/9bcae3d091d7b583dbe2c48b33655a28_67.png)

```python
# 计算聚类策略的收益（此处为简化示例，假设收益与标签直接相关）
# 注意：实际计算需结合价格数据，此处仅为逻辑演示
strategy_return = (data['returns'] * data['cluster']).cumsum()

![](img/9bcae3d091d7b583dbe2c48b33655a28_69.png)

![](img/9bcae3d091d7b583dbe2c48b33655a28_71.png)

# 计算基准收益（如简单买入持有）
baseline_return = data['returns'].cumsum()

![](img/9bcae3d091d7b583dbe2c48b33655a28_73.png)

# 绘制收益曲线对比
plt.plot(strategy_return, label='聚类策略')
plt.plot(baseline_return, label='基准策略')
plt.legend()
plt.show()
```

![](img/9bcae3d091d7b583dbe2c48b33655a28_75.png)

通过多次运行可能会发现，聚类策略的收益表现不稳定，有时优于基准，有时更差。这正体现了聚类方法在金融预测中的不确定性和局限性。

![](img/9bcae3d091d7b583dbe2c48b33655a28_77.png)

![](img/9bcae3d091d7b583dbe2c48b33655a28_79.png)

---

![](img/9bcae3d091d7b583dbe2c48b33655a28_81.png)

![](img/9bcae3d091d7b583dbe2c48b33655a28_83.png)

本节课中我们一起学习了如何应用K-Means聚类算法进行量化交易的初步分析。我们完成了从数据准备、模型构建、结果可视化到简单回测的完整流程。需要强调的是，聚类作为一种无监督学习方法，在缺乏先验标签的金融场景中解释性较弱，结果波动较大，通常不作为主流量化策略的核心方法，但可以作为特征探索或复杂模型的一部分进行尝试。