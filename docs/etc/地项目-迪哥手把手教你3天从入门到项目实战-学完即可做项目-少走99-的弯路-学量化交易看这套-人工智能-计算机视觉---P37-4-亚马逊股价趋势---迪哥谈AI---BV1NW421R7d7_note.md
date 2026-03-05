# 金融量化分析：P37：4-亚马逊股价趋势 📈

在本节课中，我们将学习如何应用时间序列预测模型来分析亚马逊（AMZN）的股价数据。我们将构建一个基础模型，对其进行评估，并分析其预测效果，为后续的模型优化打下基础。

## 模型构建与数据准备

上一节我们构建了一个基础的股价预测模型。本节中，我们来看看如何将这个模型应用到亚马逊的股票数据上，并观察其表现。

![](img/d9d6e23aa1d8d723fb368ab5729e5918_1.png)

首先，导入必要的库。

![](img/d9d6e23aa1d8d723fb368ab5729e5918_3.png)

![](img/d9d6e23aa1d8d723fb368ab5729e5918_4.png)

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
from fbprophet import Prophet
```

![](img/d9d6e23aa1d8d723fb368ab5729e5918_5.png)

接着，我们加载亚马逊（股票代码：AMZN）的历史股价数据。该数据集记录了从1997年至今的股价信息。

```python
# 假设 df 是包含‘ds’（日期）和‘y’（收盘价）列的DataFrame
df = pd.read_csv('AMZN_stock.csv')
df['ds'] = pd.to_datetime(df['ds'])
```

![](img/d9d6e23aa1d8d723fb368ab5729e5918_7.png)

数据显示，亚马逊股价最低点出现在1997年5月22日。在我们执行分析时（示例中为1月23日），其最高收盘价达到了 **1362.54**。总体来看，股价呈现出长期的上升趋势。

![](img/d9d6e23aa1d8d723fb368ab5729e5918_9.png)

![](img/d9d6e23aa1d8d723fb368ab5729e5918_1.png)

股价的日常波动幅度较大，但整体趋势向上。

![](img/d9d6e23aa1d8d723fb368ab5729e5918_11.png)

![](img/d9d6e23aa1d8d723fb368ab5729e5918_3.png)
![](img/d9d6e23aa1d8d723fb368ab5729e5918_4.png)
![](img/d9d6e23aa1d8d723fb368ab5729e5918_5.png)

## 基础模型预测

我们使用Prophet模型进行初步拟合和预测。模型公式可以简化为：

**y(t) = g(t) + s(t) + h(t) + ε_t**

其中，`g(t)` 是趋势项，`s(t)` 是季节性项，`h(t)` 是节假日效应，`ε_t` 是误差项。

```python
model = Prophet()
model.fit(df)
future = model.make_future_dataframe(periods=90)
forecast = model.predict(future)
```

![](img/d9d6e23aa1d8d723fb368ab5729e5918_13.png)

下图展示了预测结果（绿点）与真实股价（黑点）的对比。可以看到，预测模型产生的曲线更为平滑，而真实数据则包含更多突变点。这是我们所期望的，因为一个过于拟合噪声（突变点）的模型可能泛化能力较差。

![](img/d9d6e23aa1d8d723fb368ab5729e5918_15.png)

![](img/d9d6e23aa1d8d723fb368ab5729e5918_16.png)

![](img/d9d6e23aa1d8d723fb368ab5729e5918_7.png)

![](img/d9d6e23aa1d8d723fb368ab5729e5918_17.png)

![](img/d9d6e23aa1d8d723fb368ab5729e5918_19.png)

我们利用此模型预测未来90天的股价。预测结果显示，股价呈现平稳上升趋势。例如，预测到4月24日的股价约为 **1366**。

![](img/d9d6e23aa1d8d723fb368ab5729e5918_21.png)

![](img/d9d6e23aa1d8d723fb368ab5729e5918_9.png)
![](img/d9d6e23aa1d8d723fb368ab5729e5918_11.png)

观察发现，模型的预测相对保守，未能完全捕捉到可能存在的强劲上升势头。但它有效地降低了离群点和突变点对整体趋势判断的干扰。

## 模型评估方法

为了量化模型性能，我们需要一套评估标准。本节我们将介绍两种核心的评估思路。

首先，我们需要划分训练集和测试集。例如，使用2014年至2017年的数据作为训练集，2017年至2018年的数据作为测试集。

![](img/d9d6e23aa1d8d723fb368ab5729e5918_23.png)

```python
train = df[df['ds'] < '2017-01-24']
test = df[df['ds'] >= '2017-01-24']
```

![](img/d9d6e23aa1d8d723fb368ab5729e5918_25.png)

以下是两个关键的评估维度：

![](img/d9d6e23aa1d8d723fb368ab5729e5918_27.png)

1.  **误差评估**：计算预测值与真实值之间的差异。常用指标如平均绝对误差（MAE）。
    **公式**：`MAE = (1/n) * Σ|y_i - ŷ_i|`
    其中 `y_i` 是真实值，`ŷ_i` 是预测值。

2.  **趋势方向评估**：判断模型是否准确预测了股价的上升或下降趋势。即，在真实上涨的日子里，模型是否也预测为上涨；在真实下跌的日子里，模型是否也预测为下跌。

![](img/d9d6e23aa1d8d723fb368ab5729e5918_13.png)

## 评估过程详解

现在，我们通过代码调试（Debug）的方式，一步步理解评估过程的具体实现。

首先，在训练集上拟合模型，并在测试集时段内进行预测。

```python
model_eval = Prophet()
model_eval.fit(train)
future_eval = model_eval.make_future_dataframe(periods=365)
forecast_eval = model_eval.predict(future_eval)
```

关键步骤在于趋势方向的计算。我们通过比较连续两天的预测值（或真实值）来判断趋势：

```python
# 计算预测值的每日差值
predicted_diff = np.diff(forecast_eval['yhat'].values)
# 使用np.sign将差值转换为趋势方向：1为上涨，-1为下跌，0为不变
predicted_trend = np.sign(predicted_diff)
```

![](img/d9d6e23aa1d8d723fb368ab5729e5918_29.png)

`np.sign` 函数的作用是：
*   输入大于0，输出 **1**
*   输入小于0，输出 **-1**
*   输入等于0，输出 **0**

![](img/d9d6e23aa1d8d723fb368ab5729e5918_30.png)

![](img/d9d6e23aa1d8d723fb368ab5729e5918_31.png)

![](img/d9d6e23aa1d8d723fb368ab5729e5918_29.png)
![](img/d9d6e23aa1d8d723fb368ab5729e5918_30.png)
![](img/d9d6e23aa1d8d723fb368ab5729e5918_31.png)

然后，我们将预测的趋势与真实的趋势进行比较：

![](img/d9d6e23aa1d8d723fb368ab5729e5918_33.png)

```python
# 计算真实趋势
actual_diff = np.diff(test['y'].values)
actual_trend = np.sign(actual_diff)
# 计算趋势预测准确率
rise_accuracy = np.mean(predicted_trend[actual_trend == 1] == 1)
fall_accuracy = np.mean(predicted_trend[actual_trend == -1] == -1)
```

同时，计算训练集和测试集的平均绝对误差：

```python
train_mae = np.mean(np.abs(model_eval.predict(train)['yhat'] - train['y']))
test_mae = np.mean(np.abs(forecast_eval.loc[forecast_eval['ds'].isin(test['ds']), 'yhat'] - test['y']))
```

## 评估结果分析

运行评估代码后，我们得到以下关键指标：

*   **训练集平均误差**：约 **18**（货币单位）
*   **测试集平均误差**：约 **160**（货币单位）
*   **上升趋势预测准确率**：**56%**
*   **下降趋势预测准确率**：**43.81%**

![](img/d9d6e23aa1d8d723fb368ab5729e5918_35.png)

![](img/d9d6e23aa1d8d723fb368ab5729e5918_35.png)

可视化结果如下图所示。图中，**深蓝色线**代表模型预测值，**黑色线**代表真实股价。红色竖线标识了预测开始的时点（2017年1月）。

![](img/d9d6e23aa1d8d723fb368ab5729e5918_37.png)

![](img/d9d6e23aa1d8d723fb368ab5729e5918_37.png)
![](img/d9d6e23aa1d8d723fb368ab5729e5918_38.png)

![](img/d9d6e23aa1d8d723fb368ab5729e5918_38.png)

分析图表和指标可以得出结论：当前基础模型的预测性能有待提升。测试集误差远大于训练集误差，且趋势预测准确率接近随机猜测（50%）。模型的预测线过于平缓，未能有效捕捉真实股价的波动和上升动能。

![](img/d9d6e23aa1d8d723fb368ab5729e5918_40.png)
![](img/d9d6e23aa1d8d723fb368ab5729e5918_41.png)

## 总结

![](img/d9d6e23aa1d8d723fb368ab5729e5918_40.png)

![](img/d9d6e23aa1d8d723fb368ab5729e5918_41.png)

本节课中我们一起学习了如何将Prophet时间序列模型应用于亚马逊股价预测。我们完成了从数据加载、基础模型构建到模型评估的完整流程。评估结果显示，当前模型在误差控制和趋势捕捉方面表现不佳，预测过于保守和平滑。这明确指出了模型有优化的空间。在接下来的课程中，我们将重点探讨如何通过调整模型参数来提升其预测精度和捕捉市场动态的能力。