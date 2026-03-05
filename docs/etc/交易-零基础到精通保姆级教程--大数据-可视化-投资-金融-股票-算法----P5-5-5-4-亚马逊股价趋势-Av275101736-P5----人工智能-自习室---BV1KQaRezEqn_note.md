# Python金融量化交易：5.5.4：亚马逊股价趋势 📈

在本节课中，我们将学习如何评估和改进一个基础的股票价格预测模型。我们将以亚马逊（AMZN）的股价数据为例，分析模型的预测效果，并探讨如何通过调整参数来提升模型的准确性。

---

![](img/763bafa332377e49255a8769638c9259_1.png)

![](img/763bafa332377e49255a8769638c9259_3.png)

## 模型构建与初步分析

![](img/763bafa332377e49255a8769638c9259_5.png)

![](img/763bafa332377e49255a8769638c9259_7.png)

上一节我们构建了一个基础的预测模型。本节中，我们来看看如何将这个模型应用到亚马逊的股票数据上，并进行初步的评估。

![](img/763bafa332377e49255a8769638c9259_9.png)

![](img/763bafa332377e49255a8769638c9259_11.png)

首先，导入必要的库并获取亚马逊的股票数据。

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
from fbprophet import Prophet

![](img/763bafa332377e49255a8769638c9259_13.png)

# 获取亚马逊股票数据，股票代码为 AMZN
stock_data = ... # 此处为获取数据的代码
```

![](img/763bafa332377e49255a8769638c9259_15.png)

![](img/763bafa332377e49255a8769638c9259_17.png)

亚马逊的股票从1997年开始交易，我们选取其历史价格进行分析。其股价整体呈现上升趋势，但过程中存在明显的波动。

以下是亚马逊股价的整体走势图（示意图）：
![](img/763bafa332377e49255a8769638c9259_1.png)

我们也可以观察其价格的浮动情况：
![](img/763bafa332377e49255a8769638c9259_3.png)
![](img/763bafa332377e49255a8769638c9259_5.png)
![](img/763bafa332377e49255a8769638c9259_7.png)

## 基础模型预测结果

![](img/763bafa332377e49255a8769638c9259_19.png)

使用基础模型（不调整参数）对亚马逊股价进行拟合和预测。模型预测未来90天的股价走势。

![](img/763bafa332377e49255a8769638c9259_21.png)

![](img/763bafa332377e49255a8769638c9259_23.png)

![](img/763bafa332377e49255a8769638c9259_25.png)

```python
# 初始化Prophet模型，设置预测周期为90天
model = Prophet()
model.fit(train_df)
future = model.make_future_dataframe(periods=90)
forecast = model.predict(future)
```

![](img/763bafa332377e49255a8769638c9259_27.png)

![](img/763bafa332377e49255a8769638c9259_29.png)

在预测结果中，黑色点代表真实股价，绿色点代表模型预测值。
![](img/763bafa332377e49255a8769638c9259_9.png)

![](img/763bafa332377e49255a8769638c9259_31.png)

观察发现，真实股价的突变点较多，而模型的预测曲线则更为平缓。这在一定程度上避免了过拟合，使模型更具一般性。模型预测90天后的股价约为1366美元。
![](img/763bafa332377e49255a8769638c9259_13.png)
![](img/763bafa332377e49255a8769638c9259_15.png)

## 模型评估方法

初步预测完成后，我们需要系统地评估模型性能。评估主要从两个方面进行：预测误差和趋势判断准确性。

![](img/763bafa332377e49255a8769638c9259_33.png)

以下是评估模型时需要计算的核心指标：

![](img/763bafa332377e49255a8769638c9259_35.png)

![](img/763bafa332377e49255a8769638c9259_37.png)

1.  **预测误差**：计算预测值与真实值之间的绝对误差。通常分别计算训练集和测试集的平均误差。
    *公式*：`error = |y_true - y_pred|`

2.  **趋势判断准确率**：判断模型是否能正确预测股价的上升或下降趋势。
    *逻辑*：比较相邻两天的价格差（`diff = price_today - price_yesterday`）。`diff > 0` 表示上升，`diff < 0` 表示下降。统计模型预测的趋势与真实趋势一致的百分比。

## 代码实现与详细分析

现在，我们通过代码来实现上述评估过程。我们将数据分为训练集（2014-2017年）和测试集（2017-2018年），进行模型训练和评估。

```python
# 划分训练集和测试集
train = data[‘2014-01-01‘:‘2017-01-23‘]
test = data[‘2017-01-24‘:‘2018-01-23‘]

# 拟合模型
model = Prophet()
model.fit(train.reset_index().rename(columns={‘Date‘:‘ds‘, ‘Close‘:‘y‘}))

# 预测测试集
future = model.make_future_dataframe(periods=365)
forecast = model.predict(future)
test_forecast = forecast.set_index(‘ds‘)[‘yhat‘].loc[‘2017-01-24‘:‘2018-01-23‘]

![](img/763bafa332377e49255a8769638c9259_39.png)

![](img/763bafa332377e49255a8769638c9259_41.png)

![](img/763bafa332377e49255a8769638c9259_43.png)

# 计算趋势
def get_trend(series):
    diff = np.diff(series)
    return np.sign(diff)  # 大于0返回1，小于0返回-1，等于0返回0

pred_trend = get_trend(test_forecast.values)
true_trend = get_trend(test[‘Close‘].values)

![](img/763bafa332377e49255a8769638c9259_45.png)

# 计算趋势判断准确率
up_accuracy = np.mean((pred_trend > 0) == (true_trend > 0))
down_accuracy = np.mean((pred_trend < 0) == (true_trend < 0))

# 计算预测误差
train_error = np.mean(np.abs(model.history[‘y‘] - model.history[‘yhat‘]))
test_error = np.mean(np.abs(test[‘Close‘] - test_forecast))
```

运行评估代码后，我们得到以下关键结果（示意图）：
![](img/763bafa332377e49255a8769638c9259_33.png)
![](img/763bafa332377e49255a8769638c9259_35.png)

以下是具体的评估指标：

![](img/763bafa332377e49255a8769638c9259_47.png)

*   **训练集平均误差**：18.XX
*   **测试集平均误差**：160.XX
*   **上升趋势预测准确率**：56.XX%
*   **下降趋势预测准确率**：43.XX%

![](img/763bafa332377e49255a8769638c9259_49.png)

最终的可视化结果对比如下：
![](img/763bafa332377e49255a8769638c9259_51.png)

![](img/763bafa332377e49255a8769638c9259_51.png)

图中，深蓝色线为模型预测值，黑色线为真实股价。可以看出，模型预测的曲线过于平缓，未能有效捕捉真实股价的波动和上升趋势，导致测试集误差较大。

![](img/763bafa332377e49255a8769638c9259_53.png)

## 总结与下节预告

本节课中我们一起学习了如何评估一个时间序列预测模型。我们以亚马逊股价为例，计算了模型的预测误差和趋势判断准确率，发现基础模型的预测效果并不理想，其预测曲线过于平滑，无法准确反映股价的真实波动。

![](img/763bafa332377e49255a8769638c9259_55.png)

![](img/763bafa332377e49255a8769638c9259_57.png)

当前模型存在明显的改进空间。那么，如何调整模型参数，使其能更好地捕捉数据中的变化趋势和波动呢？下一节，我们将重点探讨**模型调参**的方法，目标是提升模型的预测精度和趋势捕捉能力。
![](img/763bafa332377e49255a8769638c9259_55.png)
![](img/763bafa332377e49255a8769638c9259_57.png)