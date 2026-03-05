# Python量化交易：4.4.3：fbprophet时间序列预测实例 📈

![](img/d7db4f3578fbc7d93e99329bcd43e327_1.png)

在本节课中，我们将学习如何使用Facebook开发的`fbprophet`库进行时间序列预测。我们将通过一个实例，从数据建模、可视化到未来预测，完整地走一遍流程，并理解其中的核心概念和参数。

![](img/d7db4f3578fbc7d93e99329bcd43e327_3.png)

## 概述

![](img/d7db4f3578fbc7d93e99329bcd43e327_5.png)

![](img/d7db4f3578fbc7d93e99329bcd43e327_7.png)

`fbprophet`是一个强大的时间序列预测工具，它能够自动处理数据中的趋势、季节性和节假日效应。本节我们将使用它来对股票价格数据进行建模和预测，并学习如何解读模型输出的各种图表。

![](img/d7db4f3578fbc7d93e99329bcd43e327_9.png)

---

![](img/d7db4f3578fbc7d93e99329bcd43e327_11.png)

## 模型构建与初步可视化

![](img/d7db4f3578fbc7d93e99329bcd43e327_13.png)

![](img/d7db4f3578fbc7d93e99329bcd43e327_15.png)

首先，我们来看一下构建模型并绘制基本预测图的过程。

![](img/d7db4f3578fbc7d93e99329bcd43e327_17.png)

执行创建模型的操作后，`fbprophet`会帮助我们完成建模并进行预测，最终生成一张图表。

![](img/d7db4f3578fbc7d93e99329bcd43e327_1.png)

![](img/d7db4f3578fbc7d93e99329bcd43e327_19.png)

在这张图中：
*   黑色点代表我们观测到的历史真实值。
*   绿色点代表模型拟合（预测）出的值。
*   浅绿色区域代表预测的置信区间。

![](img/d7db4f3578fbc7d93e99329bcd43e327_21.png)

这样，一个基本的时间序列预测图就构造出来了。

![](img/d7db4f3578fbc7d93e99329bcd43e327_23.png)

构造出模型之后，我们可以利用`fbprophet`框架自带的函数来深入分析模型发现的规律。

![](img/d7db4f3578fbc7d93e99329bcd43e327_5.png)

![](img/d7db4f3578fbc7d93e99329bcd43e327_25.png)

![](img/d7db4f3578fbc7d93e99329bcd43e327_27.png)

该函数不是我们自己写的，而是框架内置的。它可以直接展示模型训练后发现的多种趋势，例如：
*   按年份的整体趋势。
*   按月份（一个月内）的周期性趋势。
*   按一年中不同月份的年度季节性趋势。

![](img/d7db4f3578fbc7d93e99329bcd43e327_29.png)

![](img/d7db4f3578fbc7d93e99329bcd43e327_13.png)

这些趋势都可以被可视化出来。接下来，我们深入看一下建模的具体步骤。

---

## 深入代码：模型初始化

![](img/d7db4f3578fbc7d93e99329bcd43e327_31.png)

我们回到代码中，查看`create_model`函数的具体实现。

第一步是重载画图工具，然后实例化`fbprophet`模型。

```python
model = cf.create_model()
```

跳进`create_model`函数，首先是对模型进行实例化。实例化时会指定一系列参数：

```python
def create_model(self):
    # 实例化 Prophet 模型
    model = Prophet(
        yearly_seasonality=True,   # 是否拟合年度季节性
        weekly_seasonality=False,  # 是否拟合周度季节性（初始设为False）
        daily_seasonality=False,   # 是否拟合每日季节性
        changepoint_prior_scale=0.05 # 关键参数：趋势变化点的灵活性
    )
    # 添加自定义的月度周期（30.5天）
    model.add_seasonality(name='monthly', period=30.5, fourier_order=5)
    return model
```

![](img/d7db4f3578fbc7d93e99329bcd43e327_33.png)

以下是关键参数说明：
*   `yearly_seasonality`, `weekly_seasonality`, `daily_seasonality`: 控制是否绘制相应的季节性变化图。
*   `changepoint_prior_scale`: 这是一个关键参数，默认值为`0.05`。它控制了模型趋势变化的灵活度，值越大模型对趋势变化越敏感，我们稍后会详细讨论。
*   我们还可以通过`.add_seasonality()`方法添加自定义的季节性周期，这里添加了一个以30.5天为周期的“月度”趋势。

模型实例化后返回，我们就得到了一个基本的时间序列模型对象。

---

![](img/d7db4f3578fbc7d93e99329bcd43e327_35.png)

## 模型训练与历史拟合

![](img/d7db4f3578fbc7d93e99329bcd43e327_37.png)

拿到模型后，我们需要用历史数据对其进行训练。

![](img/d7db4f3578fbc7d93e99329bcd43e327_39.png)

![](img/d7db4f3578fbc7d93e99329bcd43e327_41.png)

```python
# 使用最近3年的数据进行训练
history = self.data.iloc[-self.training_days*365:]
model.fit(history)
```

![](img/d7db4f3578fbc7d93e99329bcd43e327_43.png)

这里的`self.training_days`指定了使用多少年的数据进行训练，例如设为3则表示只用最近3年的数据。

训练完成后，我们使用`model.make_future_dataframe`来生成用于预测的时间框架。

```python
# 生成未来预测框架，periods=0表示不预测未来新日期
future = model.make_future_dataframe(periods=0)
forecast = model.predict(future)
```

![](img/d7db4f3578fbc7d93e99329bcd43e327_45.png)

当`periods`参数设为0时，表示我们**不预测未来的新时间点**，只对历史数据的时间点进行拟合和预测，这常用于检验模型在历史数据上的表现。

执行预测后，`forecast`对象中会包含多个列，主要有：
*   `yhat`: 预测值。
*   `yhat_lower`: 预测置信区间的下限。
*   `yhat_upper`: 预测置信区间的上限。

![](img/d7db4f3578fbc7d93e99329bcd43e327_47.png)

![](img/d7db4f3578fbc7d93e99329bcd43e327_49.png)

我们可以用`yhat`作为核心预测指标，用`yhat_lower`和`yhat_upper`来描绘置信区间。

![](img/d7db4f3578fbc7d93e99329bcd43e327_51.png)

---

![](img/d7db4f3578fbc7d93e99329bcd43e327_53.png)

![](img/d7db4f3578fbc7d93e99329bcd43e327_55.png)

## 绘制历史拟合图

![](img/d7db4f3578fbc7d93e99329bcd43e327_57.png)

接下来，我们将预测结果可视化。

![](img/d7db4f3578fbc7d93e99329bcd43e327_59.png)

```python
# 绘制观测值（历史真实数据）
plt.plot(history[‘ds‘], history[‘y‘], ‘k.‘, label=‘Observation‘)
# 绘制预测值
plt.plot(forecast[‘ds‘], forecast[‘yhat‘], ‘g-‘, label=‘Prediction‘)
# 绘制置信区间
plt.fill_between(forecast[‘ds‘], forecast[‘yhat_lower‘], forecast[‘yhat_upper‘], color=‘green‘, alpha=0.2)
plt.legend()
plt.show()
```

![](img/d7db4f3578fbc7d93e99329bcd43e327_61.png)

![](img/d7db4f3578fbc7d93e99329bcd43e327_63.png)

![](img/d7db4f3578fbc7d93e99329bcd43e327_35.png)

![](img/d7db4f3578fbc7d93e99329bcd43e327_65.png)

![](img/d7db4f3578fbc7d93e99329bcd43e327_67.png)

生成的图表中：
*   黑色散点是观测的真实值。
*   绿色线条是模型的预测值。
*   浅绿色区域是预测的置信区间。

![](img/d7db4f3578fbc7d93e99329bcd43e327_69.png)

这样就完成了一个基本模型的构建和可视化。

![](img/d7db4f3578fbc7d93e99329bcd43e327_71.png)

---

## 分析季节性成分

![](img/d7db4f3578fbc7d93e99329bcd43e327_73.png)

除了整体预测图，我们还可以调用`model.plot_components(forecast)`来深入查看模型分解出的各项季节性趋势。

![](img/d7db4f3578fbc7d93e99329bcd43e327_75.png)

![](img/d7db4f3578fbc7d93e99329bcd43e327_77.png)

![](img/d7db4f3578fbc7d93e99329bcd43e327_43.png)

![](img/d7db4f3578fbc7d93e99329bcd43e327_79.png)

这张图展示了多个子图：
1.  **趋势项**：展示了从2015年到2018年，股价整体的上升趋势。
2.  **月度季节性**：展示了一个月内股价的典型波动模式。图中显示月初有上升趋势，月末有下降趋势。
3.  **年度季节性**：展示了一年不同月份对股价的影响。例如，图中显示12月和1月股价上浮最大，而7月、9月和10月则呈现下降趋势。

这些分析能提供有价值的交易洞察，例如：
*   **月份效应**：年底和年初可能是上涨概率较大的时期。
*   **月末效应**：每月底股价可能呈现下降趋势，此时或许不是买入的好时机。

![](img/d7db4f3578fbc7d93e99329bcd43e327_81.png)

如果你想分析以周为单位的季节性，可以在初始化模型时将`weekly_seasonality`参数设为`True`。

![](img/d7db4f3578fbc7d93e99329bcd43e327_83.png)

```python
model = Prophet(yearly_seasonality=True, weekly_seasonality=True, daily_seasonality=False, changepoint_prior_scale=0.05)
```

![](img/d7db4f3578fbc7d93e99329bcd43e327_85.png)

![](img/d7db4f3578fbc7d93e99329bcd43e327_87.png)

重新训练和绘图后，你就能看到股价在一周内的变化模式（需注意A股市场周六日不开盘，实际分析时应关注周一至周五）。

![](img/d7db4f3578fbc7d93e99329bcd43e327_89.png)

![](img/d7db4f3578fbc7d93e99329bcd43e327_91.png)

---

![](img/d7db4f3578fbc7d93e99329bcd43e327_93.png)

## 识别趋势突变点

![](img/d7db4f3578fbc7d93e99329bcd43e327_95.png)

![](img/d7db4f3578fbc7d93e99329bcd43e327_97.png)

`fbprophet`另一个强大功能是自动检测时间序列中的“突变点”，即趋势发生明显变化的时刻。

![](img/d7db4f3578fbc7d93e99329bcd43e327_99.png)

突变点可能是：由下降转为上升的拐点、增长速率突然加快或减慢的点。

![](img/d7db4f3578fbc7d93e99329bcd43e327_101.png)

![](img/d7db4f3578fbc7d93e99329bcd43e327_97.png)

![](img/d7db4f3578fbc7d93e99329bcd43e327_103.png)

模型内部通过观察时间序列的二阶导数来识别这些点。在代码中，我们可以这样获取和可视化突变点：

```python
# 从训练好的模型中获取所有突变点的日期
changepoints = model.changepoints
# 获取突变点处的趋势变化率（近似二阶导）
trend_deltas = model.params[‘delta‘].mean(0)

![](img/d7db4f3578fbc7d93e99329bcd43e327_105.png)

# 区分上升突变和下降突变
positive_cps = [cp for cp, delta in zip(changepoints, trend_deltas) if delta > 0]
negative_cps = [cp for cp, delta in zip(changepoints, trend_deltas) if delta < 0]

![](img/d7db4f3578fbc7d93e99329bcd43e327_107.png)

![](img/d7db4f3578fbc7d93e99329bcd43e327_109.png)

# 在图上用不同颜色标记
for cp in positive_cps:
    plt.axvline(x=cp, color=‘red‘, linestyle=‘--‘, alpha=0.5)
for cp in negative_cps:
    plt.axvline(x=cp, color=‘blue‘, linestyle=‘--‘, alpha=0.5)
```

在结果图中，红色虚线标记了趋势加速上升的突变点，蓝色虚线标记了趋势加速下降的突变点。这有助于我们理解趋势在何时发生了本质变化。

---

## 进行未来预测

最后，我们来看如何用模型预测未来的股价。这与历史拟合的关键区别在于`make_future_dataframe`的`periods`参数。

![](img/d7db4f3578fbc7d93e99329bcd43e327_111.png)

```python
# 预测未来180天
future = model.make_future_dataframe(periods=180)
forecast = model.predict(future)
```

![](img/d7db4f3578fbc7d93e99329bcd43e327_113.png)

![](img/d7db4f3578fbc7d93e99329bcd43e327_115.png)

当`periods=180`时，生成的`future`数据框会在历史日期之后，额外追加180个未来的日期。模型会对这些未来的日期也做出预测。

绘制预测图时，由于未来没有真实观测值（‘y‘），图中只有预测值（‘yhat‘）及其置信区间向前延伸。

![](img/d7db4f3578fbc7d93e99329bcd43e327_117.png)

![](img/d7db4f3578fbc7d93e99329bcd43e327_125.png)

![](img/d7db4f3578fbc7d93e99329bcd43e327_119.png)

在图中，黑色点（真实值）在2018年1月26日截止，而绿色预测线继续向后延伸了180天。模型基于历史趋势和季节性，给出了未来股价可能的走势。

你可能会注意到，这个预测看起来比较“保守”，没有延续历史末期剧烈的上涨趋势，反而预测了一个下降。**这主要受`changepoint_prior_scale`参数控制**。

---

![](img/d7db4f3578fbc7d93e99329bcd43e327_121.png)

## 核心参数：changepoint_prior_scale

![](img/d7db4f3578fbc7d93e99329bcd43e327_123.png)

![](img/d7db4f3578fbc7d93e99329bcd43e327_125.png)

`changepoint_prior_scale`是控制模型灵活度的关键。
*   **值调大（例如0.5）**：模型更“奔放”，倾向于紧跟历史数据的波动，未来预测的波动也会更大。
*   **值调小（例如0.01）**：模型更“保守”，趋势更平滑，会忽略一些短期的剧烈波动，未来预测也更平缓。

你可以通过调整这个参数，让模型的预测风格更符合你的预期或对市场的判断。

```python
# 更灵活的模型
model_flexible = Prophet(changepoint_prior_scale=0.5)
# 更保守的模型
model_conservative = Prophet(changepoint_prior_scale=0.01)
```

![](img/d7db4f3578fbc7d93e99329bcd43e327_127.png)

---

![](img/d7db4f3578fbc7d93e99329bcd43e327_129.png)

## 总结

![](img/d7db4f3578fbc7d93e99329bcd43e327_131.png)

本节课中，我们一起学习了使用`fbprophet`库进行时间序列预测的完整流程：

![](img/d7db4f3578fbc7d93e99329bcd43e327_133.png)

![](img/d7db4f3578fbc7d93e99329bcd43e327_135.png)

1.  **模型初始化**：通过`Prophet()`类创建模型，并理解`changepoint_prior_scale`等关键参数。
2.  **训练与拟合**：使用历史数据`.fit()`模型，并用`make_future_dataframe(periods=0)`进行历史拟合。
3.  **结果可视化**：绘制预测图及置信区间，并通过`plot_components()`分析趋势和季节性。
4.  **深入分析**：识别并可视化趋势的“突变点”，理解序列的结构性变化。
5.  **未来预测**：通过设置`make_future_dataframe(periods=N)`来预测未来N天的数据。

![](img/d7db4f3578fbc7d93e99329bcd43e327_137.png)

`fbprophet`是一个功能强大且易于使用的时间序列预测神器，它封装了复杂的统计模型，让我们能够通过简单的API调用完成专业的预测分析，并为量化交易提供数据驱动的洞察。