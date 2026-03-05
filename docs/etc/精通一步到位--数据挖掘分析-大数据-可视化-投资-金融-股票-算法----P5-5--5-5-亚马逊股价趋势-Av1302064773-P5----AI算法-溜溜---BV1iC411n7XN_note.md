# 量化交易与Python金融分析实战：5.5：亚马逊股价趋势分析与模型评估

在本节课中，我们将学习如何评估一个基础的股票价格预测模型。我们将以亚马逊（AMZN）的股价数据为例，构建模型，并通过计算误差和分析趋势预测准确率来评估其表现。通过这个过程，你将理解模型评估的基本方法，并发现当前模型的不足之处，为后续的模型优化打下基础。

## 构建亚马逊股价预测模型

![](img/9ce59ba3036e842cc0df66a56fa0978e_1.png)

![](img/9ce59ba3036e842cc0df66a56fa0978e_3.png)

![](img/9ce59ba3036e842cc0df66a56fa0978e_5.png)

上一节我们介绍了基础模型的构建。本节中，我们来看看如何将同样的方法应用于另一只股票——亚马逊（AMZN），并观察其效果。

![](img/9ce59ba3036e842cc0df66a56fa0978e_7.png)

![](img/9ce59ba3036e842cc0df66a56fa0978e_9.png)

首先，我们导入必要的库并获取亚马逊的股票数据。

![](img/9ce59ba3036e842cc0df66a56fa0978e_11.png)

```python
import pandas as pd
import numpy as np
import yfinance as yf
from fbprophet import Prophet
import matplotlib.pyplot as plt

![](img/9ce59ba3036e842cc0df66a56fa0978e_13.png)

# 获取亚马逊股票数据
stock = yf.download('AMZN', start='1997-05-15')
print(stock.tail())
```

![](img/9ce59ba3036e842cc0df66a56fa0978e_15.png)

亚马逊股票从1997年5月22日的最低点，到我们获取数据时的最高点（例如1362.54），整体呈现上升趋势，但过程中存在显著的波动。

![](img/9ce59ba3036e842cc0df66a56fa0978e_17.png)

## 模型初步结果与观察

使用Prophet模型对股价进行拟合和预测，我们得到了基础模型的结果。

以下是构建和训练模型的代码：

![](img/9ce59ba3036e842cc0df66a56fa0978e_19.png)

```python
# 准备Prophet所需的数据格式
df = stock.reset_index()[['Date', 'Close']]
df.columns = ['ds', 'y']

![](img/9ce59ba3036e842cc0df66a56fa0978e_21.png)

![](img/9ce59ba3036e842cc0df66a56fa0978e_23.png)

![](img/9ce59ba3036e842cc0df66a56fa0978e_25.png)

# 初始化并拟合模型
model = Prophet()
model.fit(df)

![](img/9ce59ba3036e842cc0df66a56fa0978e_27.png)

![](img/9ce59ba3036e842cc0df66a56fa0978e_29.png)

# 创建未来90天的预测数据框
future = model.make_future_dataframe(periods=90)
forecast = model.predict(future)

![](img/9ce59ba3036e842cc0df66a56fa0978e_31.png)

# 绘制预测结果
fig = model.plot(forecast)
plt.show()
```

模型预测结果显示，未来90天的股价呈现平缓上升的趋势。然而，对比历史数据中频繁的突变点，模型预测的曲线显得过于平滑。这提示我们，模型可能为了避免过拟合而过于保守，未能充分捕捉股价的剧烈波动。

## 如何评估模型性能

![](img/9ce59ba3036e842cc0df66a56fa0978e_33.png)

![](img/9ce59ba3036e842cc0df66a56fa0978e_35.png)

仅仅观察预测曲线是不够的。我们需要量化的指标来评估模型的优劣。模型评估主要关注两个方面：**预测值的误差**和**趋势方向的准确性**。

![](img/9ce59ba3036e842cc0df66a56fa0978e_37.png)

以下是评估模型的核心步骤：

1.  **划分数据集**：将历史数据分为训练集和测试集。例如，用2014年至2017年的数据训练，用2017年至2018年的数据测试。
2.  **计算预测误差**：在测试集上，计算模型预测值与真实值之间的差异。常用**平均绝对误差（MAE）**来衡量。
    **公式**：`MAE = mean(|y_true - y_pred|)`
3.  **评估趋势预测准确率**：判断模型是否准确预测了股价的上涨或下跌方向。通过比较预测序列和真实序列的每日涨跌符号来计算正确率。

## 实施模型评估

现在，让我们将上述评估方法付诸实践。我们以2017年为界，划分训练集和测试集，并进行评估。

以下是评估过程的关键代码片段：

```python
# 划分训练集和测试集
train = df[df['ds'] < '2017-01-01']
test = df[df['ds'] >= '2017-01-01']

![](img/9ce59ba3036e842cc0df66a56fa0978e_39.png)

![](img/9ce59ba3036e842cc0df66a56fa0978e_41.png)

![](img/9ce59ba3036e842cc0df66a56fa0978e_43.png)

# 在训练集上拟合模型
model = Prophet()
model.fit(train)

# 预测测试集时间段
future = model.make_future_dataframe(periods=len(test))
forecast = model.predict(future)
test_forecast = forecast[forecast['ds'].isin(test['ds'])]

![](img/9ce59ba3036e842cc0df66a56fa0978e_45.png)

# 计算测试集平均绝对误差 (MAE)
from sklearn.metrics import mean_absolute_error
test_mae = mean_absolute_error(test['y'], test_forecast['yhat'])
print(f"测试集平均绝对误差 (MAE): {test_mae:.2f}")

# 评估趋势预测准确率
def trend_accuracy(actual, predicted):
    # 计算每日涨跌方向：1为涨，0为跌
    actual_trend = np.sign(np.diff(actual)).clip(min=0)
    predicted_trend = np.sign(np.diff(predicted)).clip(min=0)
    # 计算方向预测正确的比例
    accuracy = np.mean(actual_trend == predicted_trend)
    return accuracy

trend_acc = trend_accuracy(test['y'].values, test_forecast['yhat'].values)
print(f"趋势方向预测准确率: {trend_acc:.2%}")
```

![](img/9ce59ba3036e842cc0df66a56fa0978e_47.png)

运行评估代码后，我们可能得到类似以下的结果：
*   测试集平均绝对误差（MAE）可能高达160美元以上，这意味着平均每次预测偏差很大。
*   趋势方向预测准确率可能在50%左右徘徊，这接近随机猜测的水平。

![](img/9ce59ba3036e842cc0df66a56fa0978e_49.png)

这些量化指标明确地告诉我们：当前这个未经调优的基础模型表现不佳。它既不能精确预测股价的具体数值，也难以可靠地判断股价明天的涨跌方向。

![](img/9ce59ba3036e842cc0df66a56fa0978e_51.png)

## 结果分析与后续目标

![](img/9ce59ba3036e842cc0df66a56fa0978e_53.png)

评估结果图表会清晰显示，模型的预测线（蓝色）与股价的真实走势线（黑色）存在巨大差距，预测值过于平缓，无法跟上真实的波动。

本节课中我们一起学习了如何系统地评估一个时间序列预测模型。我们以亚马逊股价为例，实施了从数据划分、模型训练到计算**预测误差（MAE）**和**趋势准确率**的全过程。结果表明，我们的初始模型表现不理想。

![](img/9ce59ba3036e842cc0df66a56fa0978e_55.png)

![](img/9ce59ba3036e842cc0df66a56fa0978e_57.png)

这恰恰引出了我们下一阶段的核心任务：**模型优化**。既然发现了问题，接下来我们就需要学习如何通过调整模型参数、引入新的特征或尝试不同的算法，来提升模型的预测精度和趋势捕捉能力，使其更能反映金融市场的真实动态。