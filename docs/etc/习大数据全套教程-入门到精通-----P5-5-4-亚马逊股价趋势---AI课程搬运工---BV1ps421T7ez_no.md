# Python量化交易：5.4：亚马逊股价趋势分析 📈

在本节课中，我们将学习如何应用时间序列模型分析亚马逊（AMZN）的股价数据，并对模型进行初步评估。我们将构建一个基础预测模型，并探讨如何衡量其预测效果。

## 概述

![](img/c6d68fe7ae7048b94b9c9f967466ade9_1.png)

上一节我们构建了一个基础的股价预测模型。本节中，我们将更换分析对象，使用亚马逊的股票数据，并学习如何评估模型的预测性能。我们将重点关注预测误差和趋势判断的准确性。

![](img/c6d68fe7ae7048b94b9c9f967466ade9_3.png)

![](img/c6d68fe7ae7048b94b9c9f967466ade9_4.png)

## 数据准备与初步观察

![](img/c6d68fe7ae7048b94b9c9f967466ade9_5.png)

首先，我们导入必要的库并加载亚马逊的股票数据。

![](img/c6d68fe7ae7048b94b9c9f967466ade9_7.png)

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
from prophet import Prophet
```

![](img/c6d68fe7ae7048b94b9c9f967466ade9_8.png)

我们获取亚马逊（股票代码：AMZN）的历史股价数据。该数据时间跨度较长，从1997年5月22日的最低点，到我们获取数据时的近期高点。例如，在某个时间点，其收盘价达到了 **1362.54**。

![](img/c6d68fe7ae7048b94b9c9f967466ade9_10.png)

以下是该股票价格的整体趋势图，可以看出其长期呈上升趋势，但过程中存在显著波动。

![](img/c6d68fe7ae7048b94b9c9f967466ade9_1.png)

![](img/c6d68fe7ae7048b94b9c9f967466ade9_12.png)

![](img/c6d68fe7ae7048b94b9c9f967466ade9_13.png)

股价的每日波动情况如下图所示，虽然存在频繁的上升和下降，但总体幅度维持着上升的势头。

![](img/c6d68fe7ae7048b94b9c9f967466ade9_3.png)
![](img/c6d68fe7ae7048b94b9c9f967466ade9_4.png)
![](img/c6d68fe7ae7048b94b9c9f967466ade9_5.png)

## 构建基础预测模型

接下来，我们使用 Prophet 库构建一个基础的预测模型。模型结构如下：

![](img/c6d68fe7ae7048b94b9c9f967466ade9_15.png)

```python
model = Prophet()
model.fit(df_train)
future = model.make_future_dataframe(periods=90)
forecast = model.predict(future)
```

![](img/c6d68fe7ae7048b94b9c9f967466ade9_16.png)

模型拟合后，我们得到预测结果。下图展示了真实值（黑点）与模型预测值（绿线）的对比。

![](img/c6d68fe7ae7048b94b9c9f967466ade9_18.png)

![](img/c6d68fe7ae7048b94b9c9f967466ade9_19.png)

![](img/c6d68fe7ae7048b94b9c9f967466ade9_20.png)

![](img/c6d68fe7ae7048b94b9c9f967466ade9_22.png)

![](img/c6d68fe7ae7048b94b9c9f967466ade9_7.png)
![](img/c6d68fe7ae7048b94b9c9f967466ade9_8.png)

![](img/c6d68fe7ae7048b94b9c9f967466ade9_24.png)

观察发现，真实股价的突变点较多，而模型的预测曲线则更为平缓均衡。这是我们期望的初步效果，因为一个过于拟合历史数据中所有突变点的模型可能泛化能力较差。我们希望模型能捕捉主要趋势，同时保持一定的平滑性。

## 进行未来预测

我们使用构建好的模型预测未来90天的股价。设置预测天数 `periods=90`。

```python
future = model.make_future_dataframe(periods=90)
forecast = model.predict(future)
```

![](img/c6d68fe7ae7048b94b9c9f967466ade9_26.png)

预测结果显示，模型预测股价将平稳上升。例如，它预测在4月24日的股价约为 **1366**。绿色曲线代表了模型的预测路径。

![](img/c6d68fe7ae7048b94b9c9f967466ade9_28.png)

![](img/c6d68fe7ae7048b94b9c9f967466ade9_12.png)
![](img/c6d68fe7ae7048b94b9c9f967466ade9_13.png)

![](img/c6d68fe7ae7048b94b9c9f967466ade9_30.png)

评估这个基础模型，我们发现它的预测相对保守。虽然实际股价可能上涨得更剧烈，但保守的预测有助于降低异常波动点对整体预测的影响。

## 模型评估方法

为了量化模型的性能，我们需要一套评估标准。以下是两个核心的评估维度：

1.  **预测误差**：计算预测值与真实值之间的差异。我们可以将数据划分为训练集和测试集（例如，用2017年之前的数据训练，2017年之后的数据测试），分别计算平均绝对误差等指标。误差公式可以表示为：
    `误差 = |真实值 - 预测值|`

2.  **趋势判断准确性**：评估模型是否能正确判断股价的上升或下降趋势。即，当实际趋势为上升时，模型预测是否也为上升；当实际趋势下降时，模型预测是否也为下降。

## 代码实现与评估分析

以下是实现模型评估的关键代码步骤和结果分析。

首先，划分训练集和测试集。我们使用2014年至2017年共三年的数据作为训练集，2017年至2018年共一年的数据作为测试集。

![](img/c6d68fe7ae7048b94b9c9f967466ade9_32.png)

![](img/c6d68fe7ae7048b94b9c9f967466ade9_33.png)

![](img/c6d68fe7ae7048b94b9c9f967466ade9_34.png)

```python
train = df[‘2014-01-01’:‘2017-01-01’]
test = df[‘2017-01-01’:‘2018-01-01’]
```

接着，训练模型并在测试集上进行预测。

![](img/c6d68fe7ae7048b94b9c9f967466ade9_36.png)

```python
model = Prophet()
model.fit(train)
future = model.make_future_dataframe(periods=365)
forecast = model.predict(future)
```

然后，我们计算趋势判断的准确性。通过计算每日股价与前一日股价的差值（`diff`）来判断趋势方向（上升或下降），并与模型预测的趋势进行比较。

```python
# 计算实际趋势
actual_trend = np.sign(test[‘y’].diff().dropna())
# 计算预测趋势
predicted_trend = np.sign(forecast[‘yhat’].diff().dropna())
# 计算趋势判断正确率
trend_accuracy = (actual_trend == predicted_trend).mean()
```

最后，我们计算训练集和测试集的平均绝对误差（MAE）。

![](img/c6d68fe7ae7048b94b9c9f967466ade9_38.png)

```python
from sklearn.metrics import mean_absolute_error
train_mae = mean_absolute_error(train[‘y’], forecast.loc[:len(train)-1, ‘yhat’])
test_mae = mean_absolute_error(test[‘y’], forecast.loc[len(train):len(train)+len(test)-1, ‘yhat’])
```

![](img/c6d68fe7ae7048b94b9c9f967466ade9_40.png)

运行评估代码后，我们得到以下关键指标和可视化结果：

*   **训练集平均误差**：约 18
*   **测试集平均误差**：约 160
*   **上升趋势预测正确率**：56%
*   **下降趋势预测正确率**：约44%

![](img/c6d68fe7ae7048b94b9c9f967466ade9_42.png)

![](img/c6d68fe7ae7048b94b9c9f967466ade9_43.png)

下图直观展示了预测值（深蓝色线）与真实值（黑色线）在测试期内的对比。可以看出，预测值与真实值之间存在较大差距，模型预测的曲线过于平缓，未能有效捕捉实际股价的波动和上升势头。

![](img/c6d68fe7ae7048b94b9c9f967466ade9_40.png)
![](img/c6d68fe7ae7048b94b9c9f967466ade9_42.png)
![](img/c6d68fe7ae7048b94b9c9f967466ade9_43.png)

## 总结

![](img/c6d68fe7ae7048b94b9c9f967466ade9_45.png)

![](img/c6d68fe7ae7048b94b9c9f967466ade9_46.png)

本节课中，我们一起学习了如何对亚马逊股价进行时间序列分析和预测。我们构建了一个基础的 Prophet 模型，并对其进行了初步评估。评估结果显示，当前模型在测试集上的预测误差较大，且趋势判断准确率接近随机猜测水平，表明模型有较大的优化空间。关键问题在于模型预测过于平缓，未能捕捉股价的实际波动。在接下来的课程中，我们将探讨如何通过调整模型参数来优化预测性能，使其能更准确地反映股价的变化趋势。