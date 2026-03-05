# Python量化交易：4.3：fbprophet时间序列预测实例 📈

在本节课中，我们将学习如何使用Facebook Prophet框架进行时间序列预测。我们将通过一个实例，从模型构建、训练、可视化到预测未来数据，完整地走一遍流程。课程内容将尽可能简单直白，确保初学者能够理解。

![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_1.png)

![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_2.png)

![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_3.png)

## 概述

Prophet是Facebook开源的一个时间序列预测工具，它非常适合具有季节性特征的数据。本节我们将使用Prophet对一个股票价格数据集进行建模，分析其趋势和季节性，并预测未来的价格走势。

![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_5.png)

![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_6.png)

---

![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_7.png)

![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_8.png)

![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_9.png)

## 模型构建与初步可视化

![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_10.png)

上一节我们介绍了时间序列的基本概念，本节中我们来看看如何使用Prophet构建第一个预测模型。

![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_12.png)

执行 `create_model` 函数后，Prophet会帮助我们建立时间序列模型。建模完成后，它会进行预测并生成图表。

在生成的图表中：
*   黑色点代表观测到的历史真实值。
*   深蓝色线代表模型拟合（预测）出的值。
*   浅蓝色区域代表预测的置信区间。

![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_14.png)

![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_15.png)

![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_16.png)

这样，一个基本的时间序列模型及其可视化结果就构建出来了。

![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_1.png)
![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_2.png)
![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_3.png)

![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_18.png)

构造完模型后，我们可以使用Prophet框架自带的 `plot_components` 函数来展示模型发现的各种规律。

![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_19.png)

![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_20.png)

![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_21.png)

![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_22.png)

以下是该函数可以绘制的几种趋势分解图：
*   **整体趋势**：数据随时间（年份）的整体变化方向。
*   **年度季节性**：数据在一年中每个月份的变化规律。
*   **周度季节性**：数据在一周中每天的变化规律（如果启用）。

现在，让我们深入看一下建模的具体代码是如何实现的。

![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_5.png)
![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_6.png)
![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_7.png)
![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_8.png)
![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_9.png)
![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_10.png)

在 `create_model` 函数中，首先进行画图设置，然后实例化 `Prophet` 模型。

![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_24.png)

实例化时，需要指定一些参数。关键参数 `changepoint_prior_scale` 用于控制趋势变化的灵活性，这里我们使用默认值 `0.05`。

```python
model = Prophet(
    yearly_seasonality=True,
    weekly_seasonality=False,
    daily_seasonality=False,
    changepoint_prior_scale=0.05
)
```

如果想深入了解每个参数的含义，可以按住 `Ctrl` 键点击 `Prophet` 进入其源码查看。核心参数包括是否启用年、周、日的季节性，以及是否添加节假日效应等。由于源码内容较多，我们在此不深入阅读，感兴趣的同学可以自行研究。

![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_12.png)
![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_14.png)
![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_15.png)
![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_16.png)
![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_18.png)
![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_19.png)
![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_20.png)
![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_21.png)
![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_22.png)

我们将指定的参数传入，并添加了一个以30.5天为周期的月度季节性。最后，返回构建好的模型实例。

![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_26.png)

拿到时间序列模型后，需要传入数据进行训练。这里，`self.training` 变量用于控制使用多少年的历史数据进行训练，例如只使用最近三年的数据。

```python
# 使用最近三年的数据进行训练
history = self.df[self.df[‘ds’] > (self.df[‘ds’].max() - pd.Timedelta(days=365*3))]
model.fit(history)
```

![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_28.png)

![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_29.png)

![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_30.png)

训练完成后，使用 `model.predict` 进行预测。参数 `periods=0` 表示只对历史数据进行“预测”（即拟合），不预测未来的新时间点。

![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_32.png)

```python
# 对历史数据进行拟合，不预测未来
forecast = model.predict(history)
```

![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_33.png)

预测结果 `forecast` 是一个DataFrame，会自动添加几个关键列：
*   **`yhat`**：预测值。
*   **`yhat_lower`**：预测区间的下限。
*   **`yhat_upper`**：预测区间的上限。

我们可以用 `yhat` 作为预测指标，用 `yhat_lower` 和 `yhat_upper` 来描绘置信区间。

![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_24.png)
![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_26.png)

![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_35.png)

接下来是绘图步骤。我们在一张图上绘制：
1.  观测值（历史数据）：`ds` 和 `y`。
2.  预测值：`forecast[‘ds’]` 和 `forecast[‘yhat’]`。
3.  置信区间：`forecast[‘yhat_lower’]` 和 `forecast[‘yhat_upper’]`。

![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_37.png)

浅蓝色区域是置信区间，黑色点是真实观测值，深蓝色线是模型的预测值。这样，我们就得到了模型拟合的基本图表。

![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_39.png)

![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_40.png)

![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_28.png)
![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_29.png)
![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_30.png)

![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_41.png)

![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_42.png)

---

![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_44.png)

![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_45.png)

## 季节性分解分析

![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_47.png)

![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_48.png)

完成基础拟合后，我们可以进一步分析数据中蕴含的季节性规律。

![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_49.png)

执行 `model.plot_components(forecast)` 可以绘制趋势分解图。该图展示了模型从数据中发现的几种规律：

![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_32.png)
![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_33.png)

![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_51.png)

![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_52.png)

*   **整体趋势图**：展示了从2015年到2018年，价格随时间（年份）的整体变化情况。
*   **年度季节性图**：展示了数据在一年中每个月份的变化模式。例如，图中显示年初可能略有下降，随后上升，在年末又出现下降趋势。
*   **月度季节性图**（由于我们添加了30.5天周期）：展示了数据在一个月内的大致波动情况。

![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_53.png)

![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_35.png)
![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_37.png)

![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_55.png)

![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_56.png)

我们也可以启用周度季节性分析。只需在创建模型时将 `weekly_seasonality` 参数设为 `True`。

![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_57.png)

```python
model = Prophet(weekly_seasonality=True)
```

重新训练和绘图后，可以得到数据在一周内每天的变化趋势。需要注意的是，股市通常在周末（周六、日）休市，因此周度季节性主要反映周一至周五的规律。

![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_59.png)

![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_60.png)

![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_61.png)

![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_39.png)
![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_40.png)
![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_41.png)
![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_42.png)
![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_44.png)
![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_45.png)

从分解图中我们可以总结出一些观察（以本例数据为例）：
*   **整体趋势**：长期来看是向上的。
*   **年度规律**：7月、9月和10月普遍呈下降趋势；12月和1月上涨幅度最大。
*   **月度规律**：每月月底（如26号至30号）价格容易出现下降。

![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_63.png)

![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_64.png)

![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_65.png)

![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_66.png)

这些规律为我们提供了分析思路。当然，这些结论是否与真实市场情况相符，需要更多实际经验来验证。

![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_47.png)
![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_48.png)
![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_49.png)
![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_51.png)
![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_52.png)
![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_53.png)
![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_55.png)
![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_56.png)
![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_57.png)

![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_68.png)

---

![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_69.png)

![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_70.png)

![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_71.png)

## 突变点（Changepoint）检测

![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_72.png)

![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_74.png)

除了季节性，Prophet还能自动检测时间序列中的“突变点”。突变点是指趋势发生明显变化的时刻，例如从上升转为下降，或增长速率突然加快/减慢。

Prophet通过观察时间序列趋势项的二阶导数的正负变化来识别这些点。

![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_76.png)

![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_59.png)
![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_60.png)
![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_61.png)
![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_63.png)
![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_64.png)
![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_65.png)
![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_66.png)

![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_77.png)

![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_79.png)

在代码中，训练完模型后，可以通过 `model.changepoints` 属性获取模型识别到的所有突变点。

![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_80.png)

```python
changepoints = model.changepoints
```

我们可以对这些突变点进行分析，例如计算它们对应的趋势变化方向（二阶导数的正负）。

以下是分析突变点的步骤：
1.  获取突变点的日期。
2.  对突变点排序，例如选取最重要的前10个。
3.  计算每个突变点处的趋势变化量（`model.params[‘delta’]` 中存储）。
4.  根据变化量的正负，将突变点分为正向（增长加速或下降减速）和负向（增长减速或下降加速）。

在图表中，我们可以用不同的颜色（如绿色和红色）的垂直线来标记这些正向和负向的突变点。

![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_82.png)

![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_83.png)

![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_68.png)
![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_69.png)
![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_70.png)
![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_71.png)
![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_72.png)
![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_74.png)
![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_76.png)
![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_77.png)
![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_79.png)
![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_80.png)
![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_82.png)
![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_83.png)
![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_84.png)

![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_84.png)

---

## 未来预测

![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_86.png)

最后，我们来看如何用训练好的模型预测未来。假设当前日期是2018年7月21日，我们想预测未来180天的价格走势。

预测的关键在于在调用 `model.predict` 时，传入一个包含未来日期的DataFrame。Prophet的 `make_future_dataframe` 方法可以方便地生成这个DataFrame。

![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_88.png)

```python
# 创建一个包含历史及未来180天的日期DataFrame
future = model.make_future_dataframe(periods=180)
# 进行预测
forecast_future = model.predict(future)
```

![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_90.png)

![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_91.png)

在这里，`periods=180` 表示在历史数据末尾追加180个时间点（天）。模型会基于学习到的规律，对这些未来的时间点进行预测。

![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_92.png)

![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_86.png)
![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_88.png)

得到预测结果后，绘图方式与之前类似。区别在于，对于未来的部分，我们只有预测线（深蓝色）和置信区间（浅蓝色），而没有黑色的真实观测点。

从预测图中可以看到，模型基于历史趋势，推测价格在未来一段时间内可能会先经历一个下降过程，然后再恢复上升。这种相对“保守”的预测风格，与模型参数（特别是之前提到的 `changepoint_prior_scale`）的设置有关。调整这个参数可以控制模型对趋势变化的敏感度，从而让预测曲线变得更“平滑保守”或更“灵活奔放”。

![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_90.png)
![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_91.png)
![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_92.png)
![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_94.png)
![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_95.png)
![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_96.png)
![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_97.png)
![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_98.png)
![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_99.png)

![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_94.png)

![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_95.png)

![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_96.png)

## 总结

![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_97.png)

![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_98.png)

![](img/6b384a7bb63090e7fd4ba8b2f011ecc1_99.png)

本节课中我们一起学习了Facebook Prophet时间序列预测框架的核心应用。我们完成了从模型构建、参数理解、数据训练、季节性分解、突变点检测到最终未来预测的完整流程。Prophet作为一个功能强大且易于上手的工具，通过简单的几行代码就能完成复杂的时序分析和预测，是量化分析中一个非常实用的“神器”。关键步骤包括使用 `Prophet()` 初始化模型，用 `fit()` 方法训练，用 `predict()` 方法进行预测和拟合，以及用 `plot_components()` 来可视化数据的内在规律。