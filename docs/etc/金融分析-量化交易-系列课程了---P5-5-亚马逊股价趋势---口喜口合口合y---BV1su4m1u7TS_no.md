# Python金融分析+量化交易：P5：亚马逊股价趋势分析 📈

在本节课中，我们将学习如何对亚马逊（AMZN）的股价数据应用一个基础的预测模型，并对模型的预测效果进行评估。我们将重点关注模型的构建、评估指标的解读，并发现当前模型的不足之处，为后续的模型优化打下基础。

![](img/5d449a72cc2cc1f01e0ef2b075f68594_1.png)

上一节我们构建了一个基础的股价预测模型。本节中，我们来看看如何将这个模型应用到亚马逊的股票数据上，并评估其表现。

![](img/5d449a72cc2cc1f01e0ef2b075f68594_3.png)

![](img/5d449a72cc2cc1f01e0ef2b075f68594_5.png)

![](img/5d449a72cc2cc1f01e0ef2b075f68594_7.png)

## 数据准备与模型构建

![](img/5d449a72cc2cc1f01e0ef2b075f68594_9.png)

![](img/5d449a72cc2cc1f01e0ef2b075f68594_11.png)

首先，我们导入必要的库并加载亚马逊的股票数据。股票代码为 `AMZN`。

```python
# 导入所需库
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
from fbprophet import Prophet

![](img/5d449a72cc2cc1f01e0ef2b075f68594_13.png)

# 加载亚马逊股票数据
# 假设数据已加载到变量 `df` 中，包含‘ds’（日期）和‘y’（收盘价）列
```

![](img/5d449a72cc2cc1f01e0ef2b075f68594_15.png)

![](img/5d449a72cc2cc1f01e0ef2b075f68594_17.png)

亚马逊的股价从1997年5月22日的最低点，到我们获取数据时（示例中为1月23日），收盘价达到了1362.54。其股价整体呈现上升趋势，但过程中存在显著的波动。

我们使用与之前相同的基础 Prophet 模型进行拟合。

```python
# 初始化模型
model = Prophet()
# 拟合数据
model.fit(df)
# 构建未来日期数据框，预测未来90天
future = model.make_future_dataframe(periods=90)
# 进行预测
forecast = model.predict(future)
```

![](img/5d449a72cc2cc1f01e0ef2b075f68594_19.png)

模型预测结果显示，未来90天的股价呈现平稳上升的趋势。例如，预测在4月24日的股价约为1366。然而，对比历史数据的波动性，这个预测模型显得较为“保守”和平滑，它没有捕捉到股价剧烈的上下波动。

![](img/5d449a72cc2cc1f01e0ef2b075f68594_21.png)

![](img/5d449a72cc2cc1f01e0ef2b075f68594_23.png)

## 模型评估方法

![](img/5d449a72cc2cc1f01e0ef2b075f68594_25.png)

![](img/5d449a72cc2cc1f01e0ef2b075f68594_27.png)

![](img/5d449a72cc2cc1f01e0ef2b075f68594_29.png)

为了量化模型的表现，我们需要一套评估方法。以下是两种核心的评估思路：

![](img/5d449a72cc2cc1f01e0ef2b075f68594_31.png)

1.  **误差评估**：计算预测值与真实值之间的差异。通常将数据分为训练集和测试集，分别计算误差。
2.  **趋势评估**：判断模型是否能正确预测股价的上升或下降趋势。

以下是具体的评估步骤：

首先，划分训练集和测试集。例如，使用2014年至2017年的数据作为训练集，2017年至2018年的数据作为测试集。

![](img/5d449a72cc2cc1f01e0ef2b075f68594_33.png)

![](img/5d449a72cc2cc1f01e0ef2b075f68594_35.png)

```python
# 划分训练集和测试集
train = df[df[‘ds‘] < ‘2017-01-24‘]
test = df[df[‘ds‘] >= ‘2017-01-24‘]
```

![](img/5d449a72cc2cc1f01e0ef2b075f68594_37.png)

接着，在训练集上拟合模型，并预测测试集时间段的数据。

```python
# 在训练集上拟合模型
model_train = Prophet()
model_train.fit(train)
# 构建测试集未来的日期框架（这里‘periods‘应为测试集的天数）
future_test = model_train.make_future_dataframe(periods=len(test))
# 进行预测
forecast_test = model_train.predict(future_test)
# 提取测试集对应的预测结果
predicted_test = forecast_test[forecast_test[‘ds‘].isin(test[‘ds‘])][‘yhat‘]
```

然后，计算评估指标。

**1. 计算平均绝对误差（MAE）**
误差计算公式为：
`误差 = |真实值 - 预测值|`
分别计算训练集和测试集的平均误差。

```python
# 计算训练集误差
train_mae = np.mean(np.abs(train[‘y‘] - forecast_train[‘yhat‘].iloc[:len(train)]))
# 计算测试集误差
test_mae = np.mean(np.abs(test[‘y‘] - predicted_test))
```

![](img/5d449a72cc2cc1f01e0ef2b075f68594_39.png)

**2. 计算趋势预测准确率**
趋势通过比较相邻两天的价格差来判断。如果今日价格高于昨日，则为上升趋势（记为1），反之为下降趋势（记为0）。

![](img/5d449a72cc2cc1f01e0ef2b075f68594_41.png)

![](img/5d449a72cc2cc1f01e0ef2b075f68594_43.png)

以下是计算趋势准确率的代码逻辑：

![](img/5d449a72cc2cc1f01e0ef2b075f68594_45.png)

```python
# 计算预测值的趋势
predicted_diff = np.diff(predicted_test.values)
predicted_trend = np.sign(predicted_diff)  # 大于0为1，小于0为-1，等于0为0
predicted_trend[predicted_trend >= 0] = 1  # 将0和正数视为上升
predicted_trend[predicted_trend < 0] = 0   # 将负数视为下降

# 计算真实值的趋势
actual_diff = np.diff(test[‘y‘].values)
actual_trend = np.sign(actual_diff)
actual_trend[actual_trend >= 0] = 1
actual_trend[actual_trend < 0] = 0

# 计算趋势预测准确率
trend_accuracy = np.mean(predicted_trend == actual_trend)
# 分别计算上升和下降趋势的准确率
up_accuracy = np.mean(predicted_trend[actual_trend == 1] == 1)
down_accuracy = np.mean(predicted_trend[actual_trend == 0] == 0)
```

## 评估结果分析

![](img/5d449a72cc2cc1f01e0ef2b075f68594_47.png)

![](img/5d449a72cc2cc1f01e0ef2b075f68594_49.png)

将上述评估方法应用于我们的亚马逊股价预测模型，我们得到了以下结果：

![](img/5d449a72cc2cc1f01e0ef2b075f68594_51.png)

*   **训练集平均误差**：约18美元
*   **测试集平均误差**：约160美元
*   **上升趋势预测准确率**：约56%
*   **下降趋势预测准确率**：约43%

![](img/5d449a72cc2cc1f01e0ef2b075f68594_53.png)

从可视化结果来看，模型的预测线（蓝色）相对平缓，而真实的股价走势（黑色）波动剧烈，两者存在较大差距。测试集的误差远大于训练集误差，且趋势预测准确率接近随机猜测（50%），这表明我们的模型存在**欠拟合**问题。它过于保守，未能有效学习到数据中复杂的波动模式。

本节课中我们一起学习了如何将Prophet模型应用于具体的股票（亚马逊），并系统地对模型进行了评估。我们发现，基础模型在波动剧烈的股价数据上表现不佳，预测过于平滑，误差较大，且无法有效捕捉价格变化趋势。这明确指出了当前模型的局限性。

![](img/5d449a72cc2cc1f01e0ef2b075f68594_55.png)

![](img/5d449a72cc2cc1f01e0ef2b075f68594_57.png)

在下一节中，我们的目标将是如何调整模型参数，例如增加季节性、变化点先验尺度等，以提升模型捕捉复杂波动和趋势的能力，从而做出更准确的预测。