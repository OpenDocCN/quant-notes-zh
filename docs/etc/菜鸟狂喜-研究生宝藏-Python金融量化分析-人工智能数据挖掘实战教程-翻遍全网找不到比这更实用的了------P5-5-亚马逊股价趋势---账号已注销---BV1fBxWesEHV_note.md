# Python金融量化分析：P5：亚马逊股价趋势分析

## 概述
在本节课中，我们将继续学习如何使用Prophet模型进行股价预测。我们将以亚马逊（AMZN）的股票数据为例，构建一个基础的预测模型，并学习如何评估模型的性能。通过本节课，你将了解模型评估的基本指标，并认识到当前基础模型的局限性，为后续的模型调优打下基础。

![](img/b1826727e9993eff60ef32e71a45fb69_1.png)

## 导入必要库
上一节我们介绍了Prophet模型的基本使用。本节中我们来看看如何应用它分析另一只股票。首先，我们需要导入分析所需的Python库。

![](img/b1826727e9993eff60ef32e71a45fb69_3.png)

![](img/b1826727e9993eff60ef32e71a45fb69_5.png)

![](img/b1826727e9993eff60ef32e71a45fb69_7.png)

```python
import pandas as pd
from prophet import Prophet
import matplotlib.pyplot as plt
```

![](img/b1826727e9993eff60ef32e71a45fb69_9.png)

![](img/b1826727e9993eff60ef32e71a45fb69_11.png)

## 加载亚马逊股票数据
接下来，我们加载亚马逊（股票代码：AMZN）的历史股价数据进行分析。

```python
# 假设已从数据源（如yfinance库）获取了AMZN的历史数据并存储在DataFrame `df` 中
# df = yf.download('AMZN', start='1997-05-15', end='2023-01-23')
# 此处使用示例代码结构
df = pd.read_csv('amzn_stock.csv')
df['ds'] = pd.to_datetime(df['Date'])
df['y'] = df['Close']
df = df[['ds', 'y']]
```

![](img/b1826727e9993eff60ef32e71a45fb69_13.png)

## 探索性数据分析
在建模之前，我们先对数据有一个直观的了解。以下是亚马逊股价的整体趋势图。



![](img/b1826727e9993eff60ef32e71a45fb69_15.png)

![](img/b1826727e9993eff60ef32e71a45fb69_17.png)

![](img/b1826727e9993eff60ef32e71a45fb69_1.png)



股价呈现出长期的上升趋势，但过程中存在明显的波动。以下是股价的日变动幅度图。



![](img/b1826727e9993eff60ef32e71a45fb69_3.png)



![](img/b1826727e9993eff60ef32e71a45fb69_19.png)

从变动幅度可以看出，股价在某些时期上升和下降的波动很大。



![](img/b1826727e9993eff60ef32e71a45fb69_21.png)

![](img/b1826727e9993eff60ef32e71a45fb69_23.png)

![](img/b1826727e9993eff60ef32e71a45fb69_25.png)

![](img/b1826727e9993eff60ef32e71a45fb69_5.png)



![](img/b1826727e9993eff60ef32e71a45fb69_27.png)

![](img/b1826727e9993eff60ef32e71a45fb69_29.png)

尽管如此，从长期来看，整体趋势仍然是上升的。



![](img/b1826727e9993eff60ef32e71a45fb69_31.png)

![](img/b1826727e9993eff60ef32e71a45fb69_7.png)



## 构建基础预测模型
基于历史数据，我们构建一个基础的Prophet模型来预测未来股价。

```python
model = Prophet()
model.fit(df)
future = model.make_future_dataframe(periods=90) # 预测未来90天
forecast = model.predict(future)
```

![](img/b1826727e9993eff60ef32e71a45fb69_33.png)

![](img/b1826727e9993eff60ef32e71a45fb69_35.png)

模型预测结果如下图所示，其中黑色点代表历史真实股价，绿色线代表模型的预测值。



![](img/b1826727e9993eff60ef32e71a45fb69_37.png)

![](img/b1826727e9993eff60ef32e71a45fb69_9.png)



观察预测结果可以发现，真实股价序列中存在更多的突变点，而模型的预测曲线则更为平缓均衡。这在一定程度上是合理的，因为一个稳健的模型不应过度拟合历史数据中的每一个噪声和突变点，否则其泛化能力会变差。我们的目标是捕捉主要趋势，而非每一个细节。

## 模型评估方法
构建模型后，我们需要评估其性能。以下是评估时间序列预测模型的两个核心角度。

1.  **误差评估**：计算预测值与真实值之间的差异。通常，我们将数据划分为训练集和测试集。用训练集拟合模型，然后在测试集上计算预测误差。误差可以用平均绝对误差（MAE）等公式表示：
    **`误差 = | 预测值 - 真实值 |`**
2.  **趋势方向评估**：除了具体数值，预测股价的涨跌方向同样重要。即判断模型是否能正确预测出第二天股价相对于前一天是上涨还是下跌。

## 实施模型评估
现在，我们按照上述方法对亚马逊股价预测模型进行评估。我们将2014年至2017年的数据作为训练集，2017年至2018年的数据作为测试集。

![](img/b1826727e9993eff60ef32e71a45fb69_39.png)

以下是评估过程的核心代码步骤：

![](img/b1826727e9993eff60ef32e71a45fb69_41.png)

![](img/b1826727e9993eff60ef32e71a45fb69_43.png)

```python
# 划分训练集和测试集
train = df[df['ds'] < '2017-01-24']
test = df[df['ds'] >= '2017-01-24']

![](img/b1826727e9993eff60ef32e71a45fb69_45.png)

# 在训练集上拟合模型
model = Prophet()
model.fit(train)

# 创建包含测试集日期的未来数据框，并进行预测
future = model.make_future_dataframe(periods=365) # 测试集长度为365天
forecast = model.predict(future)

# 提取测试集对应的预测结果
test_forecast = forecast[forecast['ds'].isin(test['ds'])]

# 计算测试集上的平均绝对误差 (MAE)
from sklearn.metrics import mean_absolute_error
test_mae = mean_absolute_error(test['y'], test_forecast['yhat'])
print(f"测试集平均绝对误差: {test_mae}")

![](img/b1826727e9993eff60ef32e71a45fb69_47.png)

![](img/b1826727e9993eff60ef32e71a45fb69_49.png)

# 计算趋势方向预测准确率
# 1. 计算实际趋势方向（今日收盘价 vs 昨日收盘价）
actual_trend = test['y'].diff().apply(lambda x: 1 if x > 0 else 0).iloc[1:]
# 2. 计算预测趋势方向
predicted_trend = test_forecast['yhat'].diff().apply(lambda x: 1 if x > 0 else 0).iloc[1:]
# 3. 计算方向预测准确率
direction_accuracy = (actual_trend == predicted_trend).mean()
print(f"趋势方向预测准确率: {direction_accuracy * 100:.2f}%")
```

![](img/b1826727e9993eff60ef32e71a45fb69_51.png)

运行评估代码后，我们得到类似下图的预测与真实值对比。



![](img/b1826727e9993eff60ef32e71a45fb69_53.png)

![](img/b1826727e9993eff60ef32e71a45fb69_51.png)



评估结果显示，模型在测试集上的平均误差较大，且趋势方向预测准确率仅在50%左右，接近随机猜测的水平。图中蓝色预测线与黑色真实线也存在显著差距，模型预测过于平缓，未能有效捕捉股价的实际波动和上升势头。

![](img/b1826727e9993eff60ef32e71a45fb69_55.png)

![](img/b1826727e9993eff60ef32e71a45fb69_57.png)

## 总结
本节课中我们一起学习了如何对亚马逊股价构建Prophet预测模型，并引入了两种关键的模型评估方法：基于数值的误差评估和基于方向的趋势评估。通过评估我们发现，使用默认参数的基础模型表现不佳，预测过于保守，未能很好地拟合股价的动态变化。这明确指出了对模型进行参数调优的必要性。在下一节课中，我们将探讨如何调整Prophet模型的参数以提升其预测性能。