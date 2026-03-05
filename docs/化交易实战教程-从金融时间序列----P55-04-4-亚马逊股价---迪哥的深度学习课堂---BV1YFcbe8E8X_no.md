# Python金融分析与量化交易实战教程：P55：04-4-亚马逊股价趋势 📈

## 概述
在本节课中，我们将学习如何评估和改进一个基础的股票价格预测模型。我们将以亚马逊（AMZN）的股价数据为例，分析模型预测结果与真实值之间的差异，并探讨如何通过评估指标来理解模型的性能，为后续的模型调优打下基础。

![](img/095d49d15f5b7e9dcda2a79faf220e71_1.png)

![](img/095d49d15f5b7e9dcda2a79faf220e71_3.png)

上一节我们介绍了如何构建一个基础的股价预测模型。本节中，我们来看看如何评估这个模型的性能，并分析其预测结果。

![](img/095d49d15f5b7e9dcda2a79faf220e71_5.png)

![](img/095d49d15f5b7e9dcda2a79faf220e71_7.png)

## 数据准备与模型构建
首先，我们导入必要的库并加载亚马逊（AMZN）的股票数据。亚马逊的股票从1997年5月22日开始交易，我们获取其历史价格数据。在本次分析中，我们关注其收盘价。

以下是加载数据和构建基础模型的代码：

![](img/095d49d15f5b7e9dcda2a79faf220e71_9.png)

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
from fbprophet import Prophet

# 加载亚马逊股票数据
df = pd.read_csv('AMZN.csv')
df['y'] = df['Close']
df['ds'] = pd.to_datetime(df['Date'])

# 构建Prophet模型
model = Prophet()
model.fit(df[['ds', 'y']])
```

模型构建完成后，我们用它来预测未来90天的股价趋势。预测结果显示，模型给出了一个相对平稳的上升趋势。

![](img/095d49d15f5b7e9dcda2a79faf220e71_11.png)

![](img/095d49d15f5b7e9dcda2a79faf220e71_13.png)

![](img/095d49d15f5b7e9dcda2a79faf220e71_15.png)

![](img/095d49d15f5b7e9dcda2a79faf220e71_9.png)

![](img/095d49d15f5b7e9dcda2a79faf220e71_17.png)

![](img/095d49d15f5b7e9dcda2a79faf220e71_19.png)

![](img/095d49d15f5b7e9dcda2a79faf220e71_21.png)

## 模型评估方法
基础模型预测完成后，我们需要评估其性能。评估主要关注两个方面：预测误差和趋势判断的准确性。

以下是评估模型时需要考虑的两个核心点：

1.  **预测误差**：计算预测值与真实值之间的绝对差异。我们可以将数据划分为训练集和测试集，分别计算两者的平均误差。
    *   **公式**：`error = |y_true - y_pred|`
2.  **趋势判断**：分析模型是否能正确预测股价的上升或下降趋势。即，当实际趋势为上升时，模型预测是否也为上升；下降时同理。

![](img/095d49d15f5b7e9dcda2a79faf220e71_23.png)

![](img/095d49d15f5b7e9dcda2a79faf220e71_25.png)

## 实施模型评估
接下来，我们具体实施评估流程。我们将数据划分为训练集（2014年至2017年）和测试集（2017年至2018年），并使用训练好的模型在测试集上进行预测和评估。

![](img/095d49d15f5b7e9dcda2a79faf220e71_27.png)

以下是评估过程的核心代码步骤：

```python
# 划分训练集和测试集
train = df[(df['ds'] >= '2014-01-01') & (df['ds'] < '2017-01-01')]
test = df[(df['ds'] >= '2017-01-01') & (df['ds'] < '2018-01-01')]

# 在训练集上拟合模型
model = Prophet()
model.fit(train[['ds', 'y']])

# 创建未来日期框架并进行预测
future = model.make_future_dataframe(periods=365)
forecast = model.predict(future)

# 提取测试集对应的预测结果
test_forecast = forecast[forecast['ds'].isin(test['ds'])]

![](img/095d49d15f5b7e9dcda2a79faf220e71_29.png)

![](img/095d49d15f5b7e9dcda2a79faf220e71_31.png)

![](img/095d49d15f5b7e9dcda2a79faf220e71_33.png)

# 计算预测误差
train_error = np.mean(np.abs(model.predict(train[['ds']])['yhat'] - train['y']))
test_error = np.mean(np.abs(test_forecast['yhat'] - test['y']))

![](img/095d49d15f5b7e9dcda2a79faf220e71_35.png)

# 计算趋势判断准确率
def calculate_trend_accuracy(true_series, pred_series):
    true_trend = np.sign(np.diff(true_series))  # 实际趋势：1为升，-1为降
    pred_trend = np.sign(np.diff(pred_series))  # 预测趋势
    accuracy = np.mean(true_trend == pred_trend)  # 趋势一致的比例
    return accuracy

up_trend_accuracy = calculate_trend_accuracy(test['y'].values, test_forecast['yhat'].values)
```

运行评估代码后，我们得到了模型的性能指标。结果显示，模型在测试集上的平均预测误差较大，且趋势判断的准确率仅在50%左右，说明当前模型的表现并不理想，其预测相对保守，未能很好地捕捉股价的实际波动。

![](img/095d49d15f5b7e9dcda2a79faf220e71_39.png)

![](img/095d49d15f5b7e9dcda2a79faf220e71_37.png)

## 评估结果分析与总结
本节课中我们一起学习了如何评估一个时间序列预测模型。我们以亚马逊股价预测为例，计算了模型的预测误差和趋势判断准确率。

![](img/095d49d15f5b7e9dcda2a79faf220e71_39.png)

![](img/095d49d15f5b7e9dcda2a79faf220e71_41.png)

通过分析发现，当前的基础模型存在明显不足：
*   预测值过于平滑，未能有效捕捉股价的实际波动和突变点。
*   在测试集上，预测值与真实值差异较大。
*   模型对于股价上升或下降趋势的判断准确率接近随机猜测的水平。

这些评估结果清晰地表明，当前的模型参数和配置有待优化。在下一节中，我们的目标将是探索如何调整模型参数，例如引入季节性、变化点先验等，以使模型能够更好地拟合历史数据中的波动，并提升对未来趋势预测的准确性。

![](img/095d49d15f5b7e9dcda2a79faf220e71_43.png)

![](img/095d49d15f5b7e9dcda2a79faf220e71_45.png)

---
**总结**：本节课我们掌握了模型评估的两大核心——误差衡量与趋势判断，并通过实践发现了基础预测模型的局限性，这为我们后续的模型调优指明了方向。