# 量化交易教程：5.5：亚马逊股价趋势分析

在本节课中，我们将学习如何对亚马逊（AMZN）的股价数据应用时间序列预测模型，并对模型的预测效果进行初步评估。我们将重点关注模型的构建、评估指标的解读，并识别当前模型的不足之处，为后续的参数优化做准备。

## 模型构建与数据准备

上一节我们构建了一个基础的预测模型。本节中，我们来看看如何将其应用于亚马逊的股票数据。

![](img/8502e4d1ae8dc797c24d2580c3835cd2_1.png)

![](img/8502e4d1ae8dc797c24d2580c3835cd2_3.png)

首先，导入必要的库并获取亚马逊的股票数据。股票代码为 `AMZN`。

![](img/8502e4d1ae8dc797c24d2580c3835cd2_5.png)

![](img/8502e4d1ae8dc797c24d2580c3835cd2_7.png)

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
from prophet import Prophet
# 假设使用yfinance库获取数据
import yfinance as yf

![](img/8502e4d1ae8dc797c24d2580c3835cd2_9.png)

![](img/8502e4d1ae8dc797c24d2580c3835cd2_11.png)

# 获取亚马逊股票数据
stock_data = yf.download('AMZN', start='1997-01-01', end='2023-01-23')
```

亚马逊股价历史最低点出现在1997年5月22日。在我们执行代码的日期（示例中为1月23日），其收盘价为1362.54美元。下图展示了其股价的整体趋势，总体呈上升态势。

![](img/8502e4d1ae8dc797c24d2580c3835cd2_1.png)

![](img/8502e4d1ae8dc797c24d2580c3835cd2_13.png)

同时，股价的日波动情况如下图所示，虽然存在频繁的上下波动，但长期趋势是上升的。

![](img/8502e4d1ae8dc797c24d2580c3835cd2_15.png)

![](img/8502e4d1ae8dc797c24d2580c3835cd2_3.png)
![](img/8502e4d1ae8dc797c24d2580c3835cd2_5.png)
![](img/8502e4d1ae8dc797c24d2580c3835cd2_7.png)

![](img/8502e4d1ae8dc797c24d2580c3835cd2_17.png)

## 基础模型预测

接下来，我们使用Prophet模型进行基础预测。以下是构建和训练模型的代码。

```python
# 准备Prophet要求的数据格式
df = stock_data.reset_index()[['Date', 'Close']]
df.columns = ['ds', 'y']

# 创建并拟合模型
model = Prophet()
model.fit(df)

![](img/8502e4d1ae8dc797c24d2580c3835cd2_19.png)

# 构建未来90天的预测数据框
future = model.make_future_dataframe(periods=90)
forecast = model.predict(future)

![](img/8502e4d1ae8dc797c24d2580c3835cd2_21.png)

# 绘制预测结果
fig = model.plot(forecast)
plt.show()
```

![](img/8502e4d1ae8dc797c24d2580c3835cd2_23.png)

![](img/8502e4d1ae8dc797c24d2580c3835cd2_25.png)

在预测结果图中，黑色点代表历史真实股价，绿色线代表模型的预测值。

![](img/8502e4d1ae8dc797c24d2580c3835cd2_27.png)

![](img/8502e4d1ae8dc797c24d2580c3835cd2_29.png)

![](img/8502e4d1ae8dc797c24d2580c3835cd2_9.png)
![](img/8502e4d1ae8dc797c24d2580c3835cd2_11.png)

![](img/8502e4d1ae8dc797c24d2580c3835cd2_31.png)

观察发现，真实股价序列包含更多突变点，而模型的预测曲线则更为平缓。这在一定程度上是合理的，因为一个过于贴合历史数据中每一个波动的模型可能意味着过拟合，其泛化能力可能较差。我们更希望模型能捕捉主要趋势，保持一定的平滑性。

使用基础模型（未调参）预测未来90天，结果显示股价将平缓上升。例如，预测在4月24日的股价约为1366美元。

![](img/8502e4d1ae8dc797c24d2580c3835cd2_13.png)
![](img/8502e4d1ae8dc797c24d2580c3835cd2_15.png)

然而，与实际可能更强劲的上升趋势相比，该模型的预测显得相对保守。这种保守性有助于降低离群点和突变点对预测结果的过度影响。

![](img/8502e4d1ae8dc797c24d2580c3835cd2_33.png)

## 模型评估方法

![](img/8502e4d1ae8dc797c24d2580c3835cd2_35.png)

为了量化模型的性能，我们需要对其进行评估。评估主要关注两个方面：预测误差和趋势判断准确性。

![](img/8502e4d1ae8dc797c24d2580c3835cd2_37.png)

我们将数据划分为训练集和测试集。例如，使用2014年至2017年的数据作为训练集，2017年至2018年的数据作为测试集。

以下是评估步骤的概要：

1.  **计算误差**：通过比较预测值与真实值之间的差异来计算误差，常用指标如平均绝对误差（MAE）。
    **公式**：`误差 = |真实值 - 预测值|`
2.  **评估趋势判断**：检查模型是否正确预测了股价的上升或下降趋势。即，在真实值上升的日子里，预测值是否也上升；在真实值下降的日子里，预测值是否也下降。

## 评估代码实现与解析

以下代码实现了上述评估流程：

```python
# 划分训练集和测试集
train = df[df['ds'] < '2017-01-24']
test = df[(df['ds'] >= '2017-01-24') & (df['ds'] < '2018-01-24')]

# 在训练集上拟合模型
model_eval = Prophet()
model_eval.fit(train)

# 创建包含未来365天（测试集）的预测框架
future_eval = model_eval.make_future_dataframe(periods=365)
forecast_eval = model_eval.predict(future_eval)

![](img/8502e4d1ae8dc797c24d2580c3835cd2_39.png)

![](img/8502e4d1ae8dc797c24d2580c3835cd2_41.png)

# 提取测试集对应的预测结果
test_forecast = forecast_eval[forecast_eval['ds'].isin(test['ds'])]

![](img/8502e4d1ae8dc797c24d2580c3835cd2_43.png)

# 计算测试集预测值与真实值
y_true = test['y'].values
y_pred = test_forecast['yhat'].values

# 1. 计算误差
train_error = np.mean(np.abs(model_eval.history['y'] - model_eval.history['yhat']))
test_error = np.mean(np.abs(y_true - y_pred))

![](img/8502e4d1ae8dc797c24d2580c3835cd2_45.png)

# 2. 计算趋势判断准确率
# 计算每日变化的方向（1为上涨，0为下跌）
true_trend = np.sign(np.diff(y_true)).clip(min=0) # 上涨为1，下跌为0
pred_trend = np.sign(np.diff(y_pred)).clip(min=0)

# 计算上涨和下跌趋势的预测准确率
rise_accuracy = np.mean((true_trend == 1) & (pred_trend == 1)) / np.mean(true_trend == 1)
fall_accuracy = np.mean((true_trend == 0) & (pred_trend == 0)) / np.mean(true_trend == 0)

print(f"训练集平均误差: {train_error:.2f}")
print(f"测试集平均误差: {test_error:.2f}")
print(f"上涨趋势预测准确率: {rise_accuracy:.2%}")
print(f"下跌趋势预测准确率: {fall_accuracy:.2%}")
```

代码关键点解析：
*   `np.diff()`: 计算相邻元素的差值，用于判断每日涨跌。
*   `np.sign().clip(min=0)`: `np.sign()`返回数值的正负号（1, 0, -1），`.clip(min=0)`将-1变为0，从而得到上涨（1）和下跌（0）的标签。
*   趋势准确率计算：分别统计在真实上涨/下跌的日子里，模型也预测为上涨/下跌的比例。

运行评估代码后，我们得到了模型的性能指标。同时，可以绘制测试集期间的预测与真实值对比图。

![](img/8502e4d1ae8dc797c24d2580c3835cd2_47.png)

![](img/8502e4d1ae8dc797c24d2580c3835cd2_49.png)

![](img/8502e4d1ae8dc797c24d2580c3835cd2_51.png)

评估结果可能显示：
*   训练集平均误差较小（例如18美元），但测试集平均误差很大（例如160美元），说明模型泛化能力不足。
*   上涨趋势预测准确率约为56%，下跌趋势预测准确率约为44%。这接近随机猜测的水平，表明当前模型在捕捉股价变化方向上效果不佳。

![](img/8502e4d1ae8dc797c24d2580c3835cd2_51.png)

在对比图中，深蓝色线代表模型在测试集期间的预测值，黑色线代表真实股价。

![](img/8502e4d1ae8dc797c24d2580c3835cd2_53.png)

![](img/8502e4d1ae8dc797c24d2580c3835cd2_55.png)
![](img/8502e4d1ae8dc797c24d2580c3835cd2_57.png)

可以观察到，模型的预测轨迹过于平缓，未能有效捕捉真实股价的波动和上升势头，导致预测值与真实值之间存在巨大差距。

## 总结

![](img/8502e4d1ae8dc797c24d2580c3835cd2_55.png)

![](img/8502e4d1ae8dc797c24d2580c3835cd2_57.png)

本节课中我们一起学习了如何对亚马逊股价应用Prophet模型进行预测和评估。我们完成了从数据获取、基础模型拟合到模型评估的完整流程。关键发现是：当前的基础模型预测结果过于平滑和保守，在测试集上误差较大，且对股价涨跌趋势的判断准确率接近随机水平。这明确指出了模型存在改进空间。在下一节中，我们将探讨如何通过调整模型参数来提升其预测性能，使其能更好地捕捉股价的波动趋势。