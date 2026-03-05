# 人工智能数据挖掘实战：5：亚马逊股价趋势分析 📈

在本节课中，我们将学习如何评估和改进一个基础的股价预测模型。我们将以亚马逊（AMZN）的股票数据为例，分析模型预测的准确性，并探讨如何通过调整参数来提升模型性能。

![](img/a35f78d39fe40485f74a4ae2f4b76cbf_1.png)

## 概述

![](img/a35f78d39fe40485f74a4ae2f4b76cbf_3.png)

![](img/a35f78d39fe40485f74a4ae2f4b76cbf_5.png)

![](img/a35f78d39fe40485f74a4ae2f4b76cbf_7.png)

上一节我们构建了一个基础的股价预测模型。本节中，我们将使用亚马逊的股票数据来评估该模型的表现。我们会计算预测误差，分析模型对股价上升和下降趋势的预测能力，并观察模型是否过于保守。通过这些分析，为后续的模型调优打下基础。

![](img/a35f78d39fe40485f74a4ae2f4b76cbf_9.png)

![](img/a35f78d39fe40485f74a4ae2f4b76cbf_11.png)

## 数据准备与模型构建

首先，我们导入必要的库并加载亚马逊（股票代码：AMZN）的历史股价数据。亚马逊是一家全球知名的科技公司，其股价具有分析价值。

![](img/a35f78d39fe40485f74a4ae2f4b76cbf_13.png)

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
from fbprophet import Prophet

![](img/a35f78d39fe40485f74a4ae2f4b76cbf_15.png)

# 加载亚马逊股票数据
df = pd.read_csv(‘AMZN.csv‘)
df[‘Date‘] = pd.to_datetime(df[‘Date‘])
df = df[[‘Date‘, ‘Close‘]].rename(columns={‘Date‘: ‘ds‘, ‘Close‘: ‘y‘})
```

![](img/a35f78d39fe40485f74a4ae2f4b76cbf_17.png)

我们使用Prophet模型进行初步拟合。Prophet是一个由Facebook开发的时间序列预测工具，非常适合具有趋势和季节性的数据。

```python
# 初始化并拟合Prophet模型
model = Prophet()
model.fit(df)

# 创建未来90天的预测数据框
future = model.make_future_dataframe(periods=90)
forecast = model.predict(future)
```

![](img/a35f78d39fe40485f74a4ae2f4b76cbf_19.png)

![](img/a35f78d39fe40485f74a4ae2f4b76cbf_21.png)

下图展示了模型对历史数据的拟合情况（黑色点）以及对未来90天的预测（绿色线）。可以看到，预测趋势相对平缓。

![](img/a35f78d39fe40485f74a4ae2f4b76cbf_23.png)

![](img/a35f78d39fe40485f74a4ae2f4b76cbf_25.png)

![](img/a35f78d39fe40485f74a4ae2f4b76cbf_9.png)

![](img/a35f78d39fe40485f74a4ae2f4b76cbf_27.png)

![](img/a35f78d39fe40485f74a4ae2f4b76cbf_29.png)

![](img/a35f78d39fe40485f74a4ae2f4b76cbf_31.png)

## 模型评估方法

构建基础模型后，我们需要评估其性能。以下是两种核心的评估思路：

1.  **误差评估**：计算预测值与真实值之间的差异。通常将数据分为训练集和测试集，分别计算平均绝对误差（MAE）等指标。
    *   **公式**：`误差 = | 真实值 - 预测值 |`
2.  **趋势预测准确率评估**：判断模型是否能正确预测股价的上升或下降趋势。我们关心的是方向是否正确，而不仅仅是具体数值。

## 实战评估：以一年数据为例

![](img/a35f78d39fe40485f74a4ae2f4b76cbf_33.png)

![](img/a35f78d39fe40485f74a4ae2f4b76cbf_35.png)

接下来，我们具体实施评估。我们选取2014年至2017年的数据作为训练集，2017年至2018年的数据作为测试集。

![](img/a35f78d39fe40485f74a4ae2f4b76cbf_37.png)

以下是评估过程的核心代码步骤：

```python
# 划分训练集和测试集
train = df[df[‘ds‘] < ‘2017-01-24‘]
test = df[df[‘ds‘] >= ‘2017-01-24‘]

# 在训练集上拟合模型
model = Prophet()
model.fit(train)

# 预测测试集时间段
future = model.make_future_dataframe(periods=365)
forecast = model.predict(future)
test_forecast = forecast[forecast[‘ds‘].isin(test[‘ds‘])]

# 计算测试集预测误差
test_error = np.mean(np.abs(test[‘y‘].values - test_forecast[‘yhat‘].values))
```

![](img/a35f78d39fe40485f74a4ae2f4b76cbf_39.png)

除了数值误差，我们还需要计算趋势预测的准确率。思路是：分别计算预测序列和真实序列每日相对于前一日是上涨（记为1）还是下跌（记为0），然后比较两者的一致性。

![](img/a35f78d39fe40485f74a4ae2f4b76cbf_41.png)

![](img/a35f78d39fe40485f74a4ae2f4b76cbf_43.png)

```python
# 计算真实趋势序列
actual_diff = np.sign(np.diff(test[‘y‘].values))
actual_trend = (actual_diff > 0).astype(int)

# 计算预测趋势序列
pred_diff = np.sign(np.diff(test_forecast[‘yhat‘].values))
pred_trend = (pred_diff > 0).astype(int)

![](img/a35f78d39fe40485f74a4ae2f4b76cbf_45.png)

# 计算趋势预测准确率
trend_accuracy = np.mean(actual_trend == pred_trend)
```

运行评估代码后，我们得到如下关键结果：
*   测试集平均预测误差约为160美元。
*   模型预测上涨趋势的准确率约为56%。
*   模型预测下跌趋势的准确率约为44%。

下图直观对比了测试集上模型的预测值（深蓝色线）与真实股价（黑色线）。可以看出，模型预测的曲线较为平缓，未能充分捕捉真实股价的波动和上升势头。

![](img/a35f78d39fe40485f74a4ae2f4b76cbf_47.png)

![](img/a35f78d39fe40485f74a4ae2f4b76cbf_51.png)

![](img/a35f78d39fe40485f74a4ae2f4b76cbf_49.png)

## 评估结果分析与下节展望

![](img/a35f78d39fe40485f74a4ae2f4b76cbf_51.png)

本节课中我们一起学习了如何评估时间序列预测模型。通过对亚马逊股价的分析，我们发现基础Prophet模型的预测存在较大误差，且对趋势方向的预测准确率接近随机猜测（50%左右）。模型表现出的“保守”或“平滑”特性，使其错过了许多关键的波动点。

![](img/a35f78d39fe40485f74a4ae2f4b76cbf_53.png)

核心问题在于：**当前的模型参数可能过于简单，无法捕捉股票数据中的复杂变化模式。**

因此，下一节我们的目标很明确：**调整模型参数，尝试让模型更好地拟合历史数据中的波动和趋势，从而提升其预测未来走势的准确性。** 我们将探索如何通过改变Prophet模型的季节性、趋势变化点等参数来优化模型性能。

![](img/a35f78d39fe40485f74a4ae2f4b76cbf_55.png)

![](img/a35f78d39fe40485f74a4ae2f4b76cbf_57.png)

---
**总结**：本节课我们以亚马逊股价为例，完成了对基础预测模型的评估。我们学会了计算预测误差和分析趋势预测准确率，并发现了模型预测过于平滑、不够精准的问题。这为我们下一步进行模型调优指明了方向。