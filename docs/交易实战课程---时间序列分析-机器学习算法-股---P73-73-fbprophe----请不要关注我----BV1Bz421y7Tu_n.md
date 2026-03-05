# 量化交易实战课程：P73：fbprophet时间序列预测实例

![](img/07219e670e72b797ae049caee62633f3_1.png)

在本节课中，我们将学习如何使用Facebook的Prophet库进行时间序列预测。我们将通过一个实例，了解如何构建模型、训练、可视化结果，并最终预测未来趋势。

![](img/07219e670e72b797ae049caee62633f3_3.png)

![](img/07219e670e72b797ae049caee62633f3_5.png)

## 模型构建与初步预测

![](img/07219e670e72b797ae049caee62633f3_7.png)

![](img/07219e670e72b797ae049caee62633f3_9.png)

上一节我们介绍了时间序列分析的基本概念，本节中我们来看看如何使用Prophet库进行建模。

![](img/07219e670e72b797ae049caee62633f3_11.png)

![](img/07219e670e72b797ae049caee62633f3_13.png)

执行`create_model`函数后，系统会首先进行建模，然后基于模型进行预测，并生成可视化图表。

![](img/07219e670e72b797ae049caee62633f3_15.png)

![](img/07219e670e72b797ae049caee62633f3_17.png)

![](img/07219e670e72b797ae049caee62633f3_1.png)

![](img/07219e670e72b797ae049caee62633f3_19.png)

图表中，黑色点代表观测值，即历史真实数据。绿色点代表模型拟合出的预测值。阴影区域代表置信区间，表示预测值可能的波动范围。这样就构成了一个基本的时间序列预测图。

## 模型趋势分解

![](img/07219e670e72b797ae049caee62633f3_21.png)

![](img/07219e670e72b797ae049caee62633f3_23.png)

模型构建完成后，我们可以利用Prophet框架自带的函数来深入分析数据的内在规律。

![](img/07219e670e72b797ae049caee62633f3_25.png)

Prophet框架内置了`plot_components`函数，它可以帮助我们可视化模型识别出的各种趋势，而无需我们自己编写代码。

![](img/07219e670e72b797ae049caee62633f3_3.png)

![](img/07219e670e72b797ae049caee62633f3_27.png)

该函数可以展示多种趋势：
*   **整体趋势**：数据在多年跨度上的变化方向。
*   **年度季节性**：数据在一年中不同月份的变化规律。
*   **月度季节性**：数据在一个月内不同日期的变化规律。

以下是趋势分解图的示例：

![](img/07219e670e72b797ae049caee62633f3_15.png)

![](img/07219e670e72b797ae049caee62633f3_29.png)

## 核心代码解析

现在，让我们深入看一下建模的具体实现。

核心建模步骤如下：
1.  首先，我们实例化一个Prophet模型对象。
2.  在实例化时，可以指定一系列参数来控制模型行为。

```python
model = cf.create_model()
```

![](img/07219e670e72b797ae049caee62633f3_31.png)

跳转到`create_model`函数内部，可以看到模型初始化的过程。关键参数包括：
*   `yearly_seasonality`, `weekly_seasonality`, `daily_seasonality`: 布尔值，控制是否拟合相应的季节性成分。
*   `changepoint_prior_scale`: 这是一个关键参数，默认值为`0.05`，它控制模型对趋势变化的灵活度。值越大，模型对历史数据中的波动越敏感，预测未来时可能更“奔放”；值越小，模型趋势越平滑，预测更“保守”。

![](img/07219e670e72b797ae049caee62633f3_33.png)

```python
# 参数示例
model = Prophet(
    yearly_seasonality=True,
    weekly_seasonality=False,
    changepoint_prior_scale=0.05
)
```

如果想深入了解每个参数的定义，可以在IDE（如PyCharm）中通过`Ctrl+点击`函数名查看源码。源码中还可以指定节假日效应（`holidays`）等高级特性。

![](img/07219e670e72b797ae049caee62633f3_35.png)

在实例化后，我们还可以通过`add_seasonality`方法添加自定义周期，例如一个以30.5天为周期的季节性成分。

![](img/07219e670e72b797ae049caee62633f3_37.png)

```python
model.add_seasonality(name='monthly', period=30.5, fourier_order=5)
```

模型对象构建完成后，接下来是训练和预测。

## 数据训练与历史拟合

![](img/07219e670e72b797ae049caee62633f3_39.png)

模型准备好后，需要加载历史数据进行训练。

![](img/07219e670e72b797ae049caee62633f3_41.png)

![](img/07219e670e72b797ae049caee62633f3_43.png)

在训练时，可以指定只使用最近几年的数据。例如，`self.training_years=3`表示仅使用最近3年的数据进行模型训练。

![](img/07219e670e72b797ae049caee62633f3_45.png)

![](img/07219e670e72b797ae049caee62633f3_47.png)

```python
# 使用最近3年数据训练
model.fit(historical_data_last_3_years)
```

![](img/07219e670e72b797ae049caee62633f3_49.png)

![](img/07219e670e72b797ae049caee62633f3_51.png)

训练完成后，使用`make_future_dataframe`函数生成待预测的时间框架。参数`periods`指定需要预测的未来天数。

![](img/07219e670e72b797ae049caee62633f3_53.png)

*   当`periods=0`时，表示不预测未来，只对历史数据进行拟合和“预测”（相当于在历史时间点上验证模型）。此时，`future`数据框与历史数据时间范围一致。
*   当`periods=180`时，表示在历史数据基础上，额外预测未来180天。

![](img/07219e670e72b797ae049caee62633f3_55.png)

![](img/07219e670e72b797ae049caee62633f3_57.png)

```python
# 不预测未来，仅拟合历史
future = model.make_future_dataframe(periods=0)
# 预测未来180天
future = model.make_future_dataframe(periods=180)
```

![](img/07219e670e72b797ae049caee62633f3_59.png)

![](img/07219e670e72b797ae049caee62633f3_61.png)

![](img/07219e670e72b797ae049caee62633f3_63.png)

然后，调用`predict`方法进行预测。这个过程可能较慢，取决于数据量和CPU性能。

![](img/07219e670e72b797ae049caee62633f3_65.png)

```python
forecast = model.predict(future)
```

预测结果`forecast`是一个DataFrame，会自动添加多个列，其中最重要的是：
*   `yhat`: 预测值。
*   `yhat_lower`: 预测置信区间的下限。
*   `yhat_upper`: 预测置信区间的上限。

![](img/07219e670e72b797ae049caee62633f3_67.png)

我们可以将`yhat`作为核心预测指标，`yhat_lower`和`yhat_upper`用于衡量预测的不确定性。

![](img/07219e670e72b797ae049caee62633f3_69.png)

![](img/07219e670e72b797ae049caee62633f3_71.png)

## 结果可视化

![](img/07219e670e72b797ae049caee62633f3_73.png)

预测完成后，我们可以将结果绘制出来。

![](img/07219e670e72b797ae049caee62633f3_75.png)

以下是绘图的核心代码逻辑：
1.  创建一个图形。
2.  绘制历史观测值（黑色散点）。
3.  绘制模型预测值（绿色线条）。
4.  填充预测置信区间（浅绿色区域）。

![](img/07219e670e72b797ae049caee62633f3_77.png)

```python
fig1 = model.plot(forecast)
```

![](img/07219e670e72b797ae049caee62633f3_79.png)

![](img/07219e670e72b797ae049caee62633f3_81.png)

最终生成的图表如下所示：

![](img/07219e670e72b797ae049caee62633f3_83.png)

![](img/07219e670e72b797ae049caee62633f3_85.png)

![](img/07219e670e72b797ae049caee62633f3_35.png)

![](img/07219e670e72b797ae049caee62633f3_87.png)

![](img/07219e670e72b797ae049caee62633f3_89.png)

图中，黑色点是真实观测值，绿色线是模型拟合及预测值，浅绿色区域是置信区间。

![](img/07219e670e72b797ae049caee62633f3_91.png)

## 季节性分析实例

![](img/07219e670e72b797ae049caee62633f3_93.png)

通过`model.plot_components(forecast)`函数，我们可以得到详细的趋势分解图。

![](img/07219e670e72b797ae049caee62633f3_37.png)

![](img/07219e670e72b797ae049caee62633f3_95.png)

![](img/07219e670e72b797ae049caee62633f3_97.png)

从图中我们可以分析出一些规律（以示例数据为例）：
*   **整体趋势**：近三年呈上升趋势。
*   **月度规律**：在每个月初期，价格有下行趋势；月中至中后期上升；到月底（如26-30号）又呈现下降趋势。因此，从模型角度看，月底可能不是买入股票的好时机。
*   **年度规律**：12月和1月价格上涨幅度最大，而7月、9月和10月则呈现下降趋势。

![](img/07219e670e72b797ae049caee62633f3_99.png)

这些分析为我们提供了数据驱动的洞察。同样，我们也可以通过设置`weekly_seasonality=True`来观察以周为单位的季节性规律。

## 突变点分析

除了季节性，Prophet还能自动检测时间序列中的“突变点”。

![](img/07219e670e72b797ae049caee62633f3_101.png)

突变点是指趋势发生明显变化的时刻，例如从上升转为下降，或增长速率突然加快/减慢的点。Prophet通过观察序列的二阶导数来识别这些点。

![](img/07219e670e72b797ae049caee62633f3_103.png)

![](img/07219e670e72b797ae049caee62633f3_105.png)

以下是分析突变点的步骤：
1.  训练模型后，可以从`model.changepoints`属性中获取算法检测到的突变点日期。
2.  对于每个突变点，可以查看其趋势变化方向（通过相关导数判断是正向突变还是负向突变）。
3.  在图上用不同颜色的竖线标记出正向（如绿色）和负向（如红色）突变点。

![](img/07219e670e72b797ae049caee62633f3_103.png)

![](img/07219e670e72b797ae049caee62633f3_107.png)

## 未来预测

![](img/07219e670e72b797ae049caee62633f3_109.png)

现在，我们来看如何预测未来。

只需在生成`future`数据框时，将`periods`参数设置为一个正数（如180），模型就会在拟合历史数据的基础上，对未来指定天数进行预测。

![](img/07219e670e72b797ae049caee62633f3_111.png)

```python
future = model.make_future_dataframe(periods=180)
forecast = model.predict(future)
model.plot(forecast)
```

![](img/07219e670e72b797ae049caee62633f3_113.png)

预测结果图如下：

![](img/07219e670e72b797ae049caee62633f3_113.png)

![](img/07219e670e72b797ae049caee62633f3_115.png)

图中，黑色观测值在2018年1月26日截止，之后的绿色线即为模型对未来180天的预测。预测趋势会基于历史模式进行外推。在本例中，模型预测未来会有小幅下降，这可能是因为模型设定（如`changepoint_prior_scale`较小）相对保守，没有进行大幅度的外推。

![](img/07219e670e72b797ae049caee62633f3_117.png)

![](img/07219e670e72b797ae049caee62633f3_119.png)

**调整预测风格**：通过调整`changepoint_prior_scale`参数，可以控制模型的灵活度，从而让预测结果看起来更“保守”或更“奔放”。

![](img/07219e670e72b797ae049caee62633f3_121.png)

![](img/07219e670e72b797ae049caee62633f3_123.png)

## 总结

![](img/07219e670e72b797ae049caee62633f3_125.png)

本节课中我们一起学习了Facebook Prophet时间序列预测工具的核心用法。我们掌握了如何构建模型、训练数据、可视化分解趋势以及检测突变点。最关键的是，我们学会了如何使用训练好的模型对未来进行预测，并了解了通过`changepoint_prior_scale`参数调整预测风格的方法。Prophet作为一个功能强大且易于使用的库，为金融时间序列分析和预测提供了极大的便利。