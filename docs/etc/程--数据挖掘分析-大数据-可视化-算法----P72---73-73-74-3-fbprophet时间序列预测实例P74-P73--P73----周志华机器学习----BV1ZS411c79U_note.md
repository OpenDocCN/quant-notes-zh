# Python量化交易：P72-P74：fbprophet时间序列预测实例 📈

![](img/1d2fd17e1c6f785ed36ab367aa2a6250_1.png)

![](img/1d2fd17e1c6f785ed36ab367aa2a6250_3.png)

![](img/1d2fd17e1c6f785ed36ab367aa2a6250_5.png)

在本节课中，我们将学习如何使用Facebook开发的`fbprophet`库进行时间序列预测。我们将从构建基础模型开始，逐步深入到模型的可视化、趋势分析以及未来预测，并了解如何调整关键参数来优化预测结果。

![](img/1d2fd17e1c6f785ed36ab367aa2a6250_7.png)

![](img/1d2fd17e1c6f785ed36ab367aa2a6250_9.png)

![](img/1d2fd17e1c6f785ed36ab367aa2a6250_11.png)

## 模型构建与初步可视化 🔧

![](img/1d2fd17e1c6f785ed36ab367aa2a6250_13.png)

![](img/1d2fd17e1c6f785ed36ab367aa2a6250_15.png)

![](img/1d2fd17e1c6f785ed36ab367aa2a6250_17.png)

上一节我们介绍了时间序列数据的基础处理。本节中，我们来看看如何使用`fbprophet`构建一个预测模型并进行初步的可视化。

![](img/1d2fd17e1c6f785ed36ab367aa2a6250_19.png)

![](img/1d2fd17e1c6f785ed36ab367aa2a6250_21.png)

首先，执行`create_model`函数来构建模型。该函数会完成模型的实例化、训练和初步预测。

![](img/1d2fd17e1c6f785ed36ab367aa2a6250_23.png)

```python
model = cf.create_model()
```

![](img/1d2fd17e1c6f785ed36ab367aa2a6250_25.png)

![](img/1d2fd17e1c6f785ed36ab367aa2a6250_27.png)

模型训练并预测后，会生成一张图表。图表中的**黑色点**代表观测值（即历史真实数据），**绿色点**代表模型预测值，而**浅绿色区域**则代表预测的置信区间。

![](img/1d2fd17e1c6f785ed36ab367aa2a6250_1.png)

![](img/1d2fd17e1c6f785ed36ab367aa2a6250_29.png)

这样，一个基本的时间序列预测结果就呈现出来了。

## 分析数据的季节性趋势 📊

![](img/1d2fd17e1c6f785ed36ab367aa2a6250_31.png)

构建完模型后，我们可以利用`fbprophet`框架自带的函数来深入分析数据中蕴含的规律。

以下是`fbprophet`模型可以自动分析并展示的几种趋势：

*   **年度趋势**：展示数据在整个年份周期内的变化规律。
*   **月度趋势**：展示数据在一个月内的变化规律。
*   **周度趋势**：展示数据在一周内的变化规律（需手动开启）。

![](img/1d2fd17e1c6f785ed36ab367aa2a6250_33.png)

执行`model.plot_components(forecast)`函数，即可绘制出这些趋势分解图。

![](img/1d2fd17e1c6f785ed36ab367aa2a6250_15.png)

![](img/1d2fd17e1c6f785ed36ab367aa2a6250_35.png)

通过观察这些图表，我们可以获得一些直观的洞察。例如，从示例的年度趋势图可以看出，过去三年整体趋势是向上的，但每年的7月、9月和10月可能出现下降，而12月和1月则可能是上涨幅度最大的月份。

## 深入模型构建过程 ⚙️

![](img/1d2fd17e1c6f785ed36ab367aa2a6250_37.png)

现在，让我们回到代码，详细看看`create_model`函数内部是如何工作的。

![](img/1d2fd17e1c6f785ed36ab367aa2a6250_39.png)

![](img/1d2fd17e1c6f785ed36ab367aa2a6250_41.png)

![](img/1d2fd17e1c6f785ed36ab367aa2a6250_43.png)

当调用`cf.create_model()`时，程序会跳转到`create_model`函数内部。第一步是实例化`Prophet`模型对象，并传入一系列参数。

![](img/1d2fd17e1c6f785ed36ab367aa2a6250_45.png)

![](img/1d2fd17e1c6f785ed36ab367aa2a6250_47.png)

```python
model = Prophet(
    yearly_seasonality=True,
    weekly_seasonality=False,
    changepoint_prior_scale=0.05
)
```

![](img/1d2fd17e1c6f785ed36ab367aa2a6250_49.png)

![](img/1d2fd17e1c6f785ed36ab367aa2a6250_51.png)

![](img/1d2fd17e1c6f785ed36ab367aa2a6250_53.png)

以下是关键参数的说明：

![](img/1d2fd17e1c6f785ed36ab367aa2a6250_55.png)

![](img/1d2fd17e1c6f785ed36ab367aa2a6250_57.png)

![](img/1d2fd17e1c6f785ed36ab367aa2a6250_59.png)

*   `yearly_seasonality` / `weekly_seasonality`: 布尔值，控制是否考虑年/周季节性。
*   `changepoint_prior_scale`: 这是一个关键参数，默认值为`0.05`。它控制模型对趋势变化的灵活度，值越大，模型对趋势变化越敏感。

![](img/1d2fd17e1c6f785ed36ab367aa2a6250_61.png)

![](img/1d2fd17e1c6f785ed36ab367aa2a6250_63.png)

实例化后，模型使用指定的历史数据进行训练。在示例中，我们使用最近3年的数据（`self.training_years=3`）进行拟合。

```python
model.fit(history_data)
```

![](img/1d2fd17e1c6f785ed36ab367aa2a6250_65.png)

![](img/1d2fd17e1c6f785ed36ab367aa2a6250_67.png)

![](img/1d2fd17e1c6f785ed36ab367aa2a6250_69.png)

训练完成后，我们使用`model.make_future_dataframe`函数生成一个包含历史日期和未来日期的数据框架。当参数`days=0`时，表示只生成历史日期，用于在历史数据上进行“预测”（即模型拟合效果评估）。之后，调用`model.predict`得到预测结果`forecast`。

![](img/1d2fd17e1c6f785ed36ab367aa2a6250_71.png)

`forecast`对象中包含几个重要的列：
*   `yhat`: 预测值。
*   `yhat_lower` / `yhat_upper`: 预测值的置信区间下限和上限。

![](img/1d2fd17e1c6f785ed36ab367aa2a6250_73.png)

![](img/1d2fd17e1c6f785ed36ab367aa2a6250_75.png)

最后，使用`matplotlib`将观测值（黑色点）、预测值（绿色线）和置信区间（浅绿色区域）绘制在同一张图上。

![](img/1d2fd17e1c6f785ed36ab367aa2a6250_77.png)

![](img/1d2fd17e1c6f785ed36ab367aa2a6250_79.png)

![](img/1d2fd17e1c6f785ed36ab367aa2a6250_81.png)

## 检测趋势突变点 🔍

![](img/1d2fd17e1c6f785ed36ab367aa2a6250_83.png)

![](img/1d2fd17e1c6f785ed36ab367aa2a6250_85.png)

在时间序列中，趋势发生显著变化的点被称为“突变点”（Changepoint）。`fbprophet`可以自动检测这些点。

![](img/1d2fd17e1c6f785ed36ab367aa2a6250_87.png)

![](img/1d2fd17e1c6f785ed36ab367aa2a6250_89.png)

![](img/1d2fd17e1c6f785ed36ab367aa2a6250_91.png)

模型训练后，可以通过`model.changepoints`属性获取所有检测到的突变点日期。通过计算这些点趋势变化的二阶导数，可以判断突变是正向（增长加速或下降减速）还是负向（增长减速或下降加速）。

在示例代码中，我们提取了前10个突变点，并根据其二阶导数的正负，用不同的颜色（如红色代表正向，另一种颜色代表负向）在趋势图上将它们标记出来。

![](img/1d2fd17e1c6f785ed36ab367aa2a6250_93.png)

![](img/1d2fd17e1c6f785ed36ab367aa2a6250_95.png)

![](img/1d2fd17e1c6f785ed36ab367aa2a6250_101.png)

![](img/1d2fd17e1c6f785ed36ab367aa2a6250_97.png)

这样，我们就能直观地看到历史数据中趋势发生关键转折的位置。

## 进行未来预测 🚀

![](img/1d2fd17e1c6f785ed36ab367aa2a6250_99.png)

预测未来是时间序列模型的核心应用。要进行预测，只需在生成未来数据框架时，指定一个大于0的`days`参数（例如`days=180`）。

![](img/1d2fd17e1c6f785ed36ab367aa2a6250_101.png)

![](img/1d2fd17e1c6f785ed36ab367aa2a6250_103.png)

```python
future = model.make_future_dataframe(periods=180)
forecast = model.predict(future)
```

![](img/1d2fd17e1c6f785ed36ab367aa2a6250_105.png)

![](img/1d2fd17e1c6f785ed36ab367aa2a6250_107.png)

此时，`forecast`对象将包含对未来180天的预测值。绘制图表时，黑色观测点只到当前日期，而绿色的预测线则会向后延伸，展示未来的趋势走向。

![](img/1d2fd17e1c6f785ed36ab367aa2a6250_111.png)

![](img/1d2fd17e1c6f785ed36ab367aa2a6250_109.png)

![](img/1d2fd17e1c6f785ed36ab367aa2a6250_111.png)

预测结果的“保守”或“激进”程度，很大程度上由之前提到的`changepoint_prior_scale`参数控制。调高此值会使模型更倾向于捕捉历史数据中的波动，可能导致未来预测的波动更大；调低此值则会使模型趋势更加平滑，预测更保守。

## 总结 📝

![](img/1d2fd17e1c6f785ed36ab367aa2a6250_113.png)

本节课中我们一起学习了`fbprophet`库在时间序列预测中的实战应用。

![](img/1d2fd17e1c6f785ed36ab367aa2a6250_115.png)

![](img/1d2fd17e1c6f785ed36ab367aa2a6250_117.png)

![](img/1d2fd17e1c6f785ed36ab367aa2a6250_119.png)

我们首先构建了基础模型并可视化拟合结果，然后分析了数据的年度、月度等季节性趋势。通过深入代码，我们了解了模型参数的意义和训练预测的流程。我们还学习了如何检测并可视化趋势的突变点。最后，我们演示了如何对未来进行预测，并指出通过调整`changepoint_prior_scale`参数可以影响预测趋势的灵活度。

![](img/1d2fd17e1c6f785ed36ab367aa2a6250_121.png)

![](img/1d2fd17e1c6f785ed36ab367aa2a6250_123.png)

`fbprophet`作为一个功能强大且易于使用的工具，能够帮助我们快速从时间序列数据中提取规律并做出预测，为量化分析提供了有力的支持。