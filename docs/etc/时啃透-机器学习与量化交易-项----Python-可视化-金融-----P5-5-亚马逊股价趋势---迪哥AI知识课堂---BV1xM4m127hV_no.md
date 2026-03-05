# 机器学习与量化交易：P5：亚马逊股价趋势 📈

在本节课中，我们将学习如何评估和优化一个基础的股价预测模型。我们将以亚马逊（AMZN）的股票数据为例，分析模型预测结果与真实值之间的差异，并探讨如何通过调整模型来提升预测的准确性。

![](img/41cece2f801db004ad489832bb9e21eb_1.png)

![](img/41cece2f801db004ad489832bb9e21eb_3.png)

![](img/41cece2f801db004ad489832bb9e21eb_5.png)

## 模型构建与数据准备

![](img/41cece2f801db004ad489832bb9e21eb_7.png)

![](img/41cece2f801db004ad489832bb9e21eb_9.png)

![](img/41cece2f801db004ad489832bb9e21eb_11.png)

上一节我们介绍了基础模型的构建，本节中我们来看看如何将其应用于新的股票数据。

首先，我们导入必要的库并加载亚马逊的股票数据。股票代码为 `AMZN`。我们获取其历史价格数据，并观察其整体趋势和价格波动情况。

![](img/41cece2f801db004ad489832bb9e21eb_13.png)

![](img/41cece2f801db004ad489832bb9e21eb_15.png)

以下是加载数据并观察趋势的代码示例：
```python
import yfinance as yf
import matplotlib.pyplot as plt

![](img/41cece2f801db004ad489832bb9e21eb_17.png)

# 下载亚马逊股票数据
stock = yf.Ticker(“AMZN”)
df = stock.history(period=“max”)

# 绘制收盘价趋势图
plt.figure(figsize=(12, 6))
plt.plot(df.index, df[‘Close’], label=‘AMZN Close Price’)
plt.title(‘Amazon Stock Price Trend’)
plt.xlabel(‘Date’)
plt.ylabel(‘Price (USD)’)
plt.legend()
plt.show()
```
数据显示，亚马逊股价从1997年的低点持续上涨至近期高点，整体呈上升趋势，但过程中存在显著的波动。

![](img/41cece2f801db004ad489832bb9e21eb_19.png)

## 基础模型预测结果

![](img/41cece2f801db004ad489832bb9e21eb_21.png)

![](img/41cece2f801db004ad489832bb9e21eb_23.png)

![](img/41cece2f801db004ad489832bb9e21eb_25.png)

我们使用与之前相同的基础模型对亚马逊股价进行预测。模型使用默认参数，并预测未来90天的股价走势。

![](img/41cece2f801db004ad489832bb9e21eb_27.png)

![](img/41cece2f801db004ad489832bb9e21eb_29.png)

![](img/41cece2f801db004ad489832bb9e21eb_31.png)

以下是进行预测的代码核心部分：
```python
from prophet import Prophet

# 准备Prophet所需的数据格式
df_prophet = df.reset_index()[[‘Date’, ‘Close’]]
df_prophet.columns = [‘ds’, ‘y’]

# 初始化并拟合模型
model = Prophet()
model.fit(df_prophet)

![](img/41cece2f801db004ad489832bb9e21eb_33.png)

![](img/41cece2f801db004ad489832bb9e21eb_35.png)

# 创建未来90天的数据框并进行预测
future = model.make_future_dataframe(periods=90)
forecast = model.predict(future)
```
预测结果显示，模型给出的未来趋势是一条相对平缓的上升曲线。与真实股价的剧烈波动相比，模型的预测显得更为保守和平稳。这在一定程度上避免了过拟合噪声数据，但可能无法捕捉到重要的趋势突变点。

![](img/41cece2f801db004ad489832bb9e21eb_37.png)

## 模型评估方法

为了量化模型的性能，我们需要一套评估标准。评估主要关注两个方面：预测数值的误差以及预测趋势方向的准确性。

以下是评估模型的核心步骤：

1.  **划分数据集**：我们将2014年至2017年的数据作为训练集，将2017年至2018年的数据作为测试集。
2.  **计算预测误差**：通过计算预测值与真实值之间的平均绝对误差（MAE）来衡量数值精度。
    *   公式：**误差 = | 真实值 - 预测值 |**
    *   我们分别计算训练集误差（`train_error`）和测试集误差（`test_error`）。
3.  **评估趋势方向**：我们检查模型是否正确地预测了股价“上涨”或“下跌”的日子。
    *   方法：计算每日股价相对于前一日的变化方向（正数为涨，负数为跌），然后比较预测方向与真实方向是否一致。
    *   最终输出“上涨趋势预测正确率”和“下跌趋势预测正确率”。

![](img/41cece2f801db004ad489832bb9e21eb_39.png)

![](img/41cece2f801db004ad489832bb9e21eb_41.png)

运行评估代码后，我们得到如下关键指标：
*   训练集平均误差：约18美元
*   测试集平均误差：约160美元
*   上涨趋势预测正确率：56%
*   下跌趋势预测正确率：43.81%

![](img/41cece2f801db004ad489832bb9e21eb_43.png)

这些指标表明，当前模型在测试集上的预测误差较大，且趋势预测的准确率接近随机猜测（50%），说明模型有巨大的优化空间。

![](img/41cece2f801db004ad489832bb9e21eb_45.png)

## 结果可视化与分析

我们将预测结果与真实数据进行可视化对比，以便更直观地发现问题。

以下是绘制对比图的代码示意：
```python
# 筛选出测试集时间段的数据进行绘图
test_dates = df_prophet[‘ds’][(df_prophet[‘ds’] >= ‘2017-01-24’) & (df_prophet[‘ds’] <= ‘2018-01-24’)]
test_actual = df_prophet[‘y’][(df_prophet[‘ds’] >= ‘2017-01-24’) & (df_prophet[‘ds’] <= ‘2018-01-24’)]
test_forecast = forecast[‘yhat’][(forecast[‘ds’] >= ‘2017-01-24’) & (forecast[‘ds’] <= ‘2018-01-24’)]

![](img/41cece2f801db004ad489832bb9e21eb_47.png)

![](img/41cece2f801db004ad489832bb9e21eb_49.png)

plt.figure(figsize=(14, 7))
plt.plot(test_dates, test_actual, ‘k-’, label=‘Actual Price’)
plt.plot(test_dates, test_forecast, ‘b-’, label=‘Predicted Price’)
plt.fill_between(test_dates,
                 forecast[‘yhat_lower’][(forecast[‘ds’] >= ‘2017-01-24’) & (forecast[‘ds’] <= ‘2018-01-24’)],
                 forecast[‘yhat_upper’][(forecast[‘ds’] >= ‘2017-01-24’) & (forecast[‘ds’] <= ‘2018-01-24’)],
                 alpha=0.2, color=‘gray’, label=‘Uncertainty Interval’)
plt.title(‘Amazon Stock: Actual vs Predicted (Test Set)’)
plt.xlabel(‘Date’)
plt.ylabel(‘Price (USD)’)
plt.legend()
plt.show()
```
从图像中可以清晰看到，黑色的真实股价线波动剧烈，而蓝色的预测线则非常平缓。两者在测试集后期出现了数百美元的巨大偏差。这明确指出了当前模型的不足：它过度平滑，无法有效捕捉股价的实际波动和上升动能。

![](img/41cece2f801db004ad489832bb9e21eb_51.png)

## 总结与下节预告

![](img/41cece2f801db004ad489832bb9e21eb_53.png)

本节课中我们一起学习了如何评估一个时间序列预测模型。我们以亚马逊股价为例，构建了基础预测模型，并通过误差分析和趋势方向准确率两个维度对其性能进行了量化评估。结果表明，当前的模型预测过于保守和平滑，无法准确反映股价的真实波动。

![](img/41cece2f801db004ad489832bb9e21eb_55.png)

![](img/41cece2f801db004ad489832bb9e21eb_57.png)

基于本次评估发现的问题，我们下一节课的目标就很明确了：**如何调整和优化模型参数**，使其能够更好地捕捉数据中的趋势和季节性变化，从而提升预测的准确性和实用性。我们将探索Prophet模型的关键参数，以期获得更贴合真实市场的预测结果。