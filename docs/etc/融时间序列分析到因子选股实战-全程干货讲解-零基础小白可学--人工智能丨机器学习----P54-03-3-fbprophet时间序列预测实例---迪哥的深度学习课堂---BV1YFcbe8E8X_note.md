# Python金融分析与量化交易实战教程：P54：03-3-fbprophet时间序列预测实例 📈

![](img/4b3f7a4cabc1adf0426d607124b30f68_1.png)

在本节课中，我们将学习如何使用Facebook开源的Prophet库进行时间序列预测。我们将从构建一个基础模型开始，逐步探索其可视化功能、季节性分解、突变点检测，并最终实现未来数据的预测。

![](img/4b3f7a4cabc1adf0426d607124b30f68_3.png)

## 概述

![](img/4b3f7a4cabc1adf0426d607124b30f68_5.png)

![](img/4b3f7a4cabc1adf0426d607124b30f68_7.png)

Prophet是一个由Facebook核心数据科学团队发布的开源库，用于时间序列预测。它特别适合处理具有强烈季节性特征和多个历史数据周期的时间序列数据。本节我们将通过一个实例，完整地走一遍使用Prophet进行建模、分析和预测的流程。

![](img/4b3f7a4cabc1adf0426d607124b30f68_9.png)

![](img/4b3f7a4cabc1adf0426d607124b30f68_11.png)

## 基础模型构建与可视化

![](img/4b3f7a4cabc1adf0426d607124b30f68_13.png)

首先，我们来看一下构建一个基础Prophet模型并绘制预测结果的完整过程。

![](img/4b3f7a4cabc1adf0426d607124b30f68_15.png)

![](img/4b3f7a4cabc1adf0426d607124b30f68_17.png)

当我们执行 `create_model()` 函数后，Prophet会帮我们完成建模和预测，并生成可视化图表。

![](img/4b3f7a4cabc1adf0426d607124b30f68_1.png)

在上图中，黑色圆点代表我们观测到的历史真实值，绿色圆点代表模型拟合（预测）出的值。浅绿色的区域是置信区间。这样，一个基本的时间序列预测图就构造出来了。

构造出模型后，我们可以利用Prophet框架自带的函数来深入分析模型的规律。

![](img/4b3f7a4cabc1adf0426d607124b30f68_19.png)

![](img/4b3f7a4cabc1adf0426d607124b30f68_3.png)

Prophet框架自带了一个 `plot_components` 函数，它不是我们自己写的，而是框架内置的。这个函数可以直接展示模型识别出的各种规律。

![](img/4b3f7a4cabc1adf0426d607124b30f68_21.png)

![](img/4b3f7a4cabc1adf0426d607124b30f68_5.png)

训练完模型后，它会发现数据中的多种趋势，例如：
*   按年份的整体趋势。
*   按月份（一个月内）的周期性趋势。
*   按一年中不同月份的年度季节性趋势。

![](img/4b3f7a4cabc1adf0426d607124b30f68_7.png)

这些趋势都可以被可视化出来。现在，让我们深入看一下建模的具体步骤。

我们回到代码中，查看 `create_model` 函数。第一步是重载画图工具，然后实例化 `Prophet` 模型。

```python
model = cf.create_model()
```

跳进 `create_model` 函数，它会执行模型实例化。在实例化时，可以指定一系列参数。

```python
# 示例参数设置
model = Prophet(
    yearly_seasonality=True,  # 是否拟合年度季节性
    weekly_seasonality=False, # 是否拟合周季节性
    changepoint_prior_scale=0.05 # 调整趋势灵活度的关键参数
)
```

![](img/4b3f7a4cabc1adf0426d607124b30f68_23.png)

以下是关键参数说明：
*   `yearly_seasonality`, `weekly_seasonality`: 布尔值，决定是否画出相应的季节性变化。
*   `changepoint_prior_scale`: 这是一个关键参数，默认值为0.05，用于控制趋势变化的灵活度。我们暂时使用默认值。
*   `holidays`: 可以传入一个DataFrame来指定节假日效应。

在源码中（可以通过IDE的Ctrl+鼠标左键点击查看），有更多参数可以调整。例如，我们还可以添加以30.5天为周期的季节性成分。

![](img/4b3f7a4cabc1adf0426d607124b30f68_25.png)

```python
model.add_seasonality(name='monthly', period=30.5, fourier_order=5)
```

实例化模型后，将其返回，我们就得到了一个基础的时间序列模型。

![](img/4b3f7a4cabc1adf0426d607124b30f68_27.png)

![](img/4b3f7a4cabc1adf0426d607124b30f68_29.png)

拿到模型后，需要传入数据进行训练。这里有一个细节：`self.training` 参数。

![](img/4b3f7a4cabc1adf0426d607124b30f68_31.png)

```python
# 使用最近3年的数据进行训练
history = df.tail(3*365) # 假设df是日数据
model.fit(history)
```

![](img/4b3f7a4cabc1adf0426d607124b30f68_33.png)

`self.training` 的意思是，我们可以只用最近几年的数据（例如3年）进行训练，而不是全部历史数据。这通过 `training_years` 参数指定。

训练完成后，使用 `make_future_dataframe` 方法生成预测用的时间框架。

```python
# 生成未来0天的预测框架（即只对历史数据做拟合）
future = model.make_future_dataframe(periods=0)
forecast = model.predict(future)
```

![](img/4b3f7a4cabc1adf0426d607124b30f68_35.png)

当 `periods=0` 时，表示我们不需要预测未来的新时间点，只对历史数据进行拟合和“预测”（即模型在历史数据上的表现）。稍后我们会演示 `periods=90` 或 `100` 来预测未来序列。

![](img/4b3f7a4cabc1adf0426d607124b30f68_37.png)

执行预测后，程序可能会稍慢。预测结果 `forecast` 是一个DataFrame，会自动添加几个关键列：
*   `yhat`: 预测值。
*   `yhat_lower`: 预测区间的下限。
*   `yhat_upper`: 预测区间的上限。

![](img/4b3f7a4cabc1adf0426d607124b30f68_39.png)

![](img/4b3f7a4cabc1adf0426d607124b30f68_41.png)

我们可以用 `yhat` 作为预测指标，用 `yhat_lower` 和 `yhat_upper` 来定义置信区间。

![](img/4b3f7a4cabc1adf0426d607124b30f68_43.png)

![](img/4b3f7a4cabc1adf0426d607124b30f68_45.png)

接下来是画图部分：

![](img/4b3f7a4cabc1adf0426d607124b30f68_47.png)

```python
fig1 = model.plot(forecast)
```

![](img/4b3f7a4cabc1adf0426d607124b30f68_49.png)

![](img/4b3f7a4cabc1adf0426d607124b30f68_51.png)

`model.plot()` 函数会绘制一张图。图中，黑色圆点是观测值（历史数据），绿色圆点是预测值 (`yhat`)。浅绿色区域是置信区间 (`yhat_lower`, `yhat_upper`)。

![](img/4b3f7a4cabc1adf0426d607124b30f68_53.png)

![](img/4b3f7a4cabc1adf0426d607124b30f68_23.png)

![](img/4b3f7a4cabc1adf0426d607124b30f68_55.png)

![](img/4b3f7a4cabc1adf0426d607124b30f68_57.png)

这样，我们就得到了模型拟合的基本图表。画图很简单，只需要有数据并调用 `plot` 函数即可。

![](img/4b3f7a4cabc1adf0426d607124b30f68_59.png)

![](img/4b3f7a4cabc1adf0426d607124b30f68_61.png)

## 季节性成分分解

上一节我们看到了模型的整体拟合图，本节我们来详细查看Prophet分解出的各项季节性成分。

使用 `model.plot_components(forecast)` 可以绘制成分图。这个过程可能需要一点时间。

![](img/4b3f7a4cabc1adf0426d607124b30f68_63.png)

![](img/4b3f7a4cabc1adf0426d607124b30f68_33.png)

![](img/4b3f7a4cabc1adf0426d607124b30f68_65.png)

![](img/4b3f7a4cabc1adf0426d607124b30f68_67.png)

生成的图表展示了数据中不同时间维度的趋势：
1.  **整体趋势 (Trend)**: 从2015年到2018年，价格整体呈上升趋势。
2.  **月度季节性 (Weekly)**: 展示了在一个月内，价格的变化模式。图中显示，月初可能上涨，月末可能下降。
3.  **年度季节性 (Yearly)**: 展示了一年之中，不同月份的平均效应。例如，可能显示7月、9月、10月是下降的，而12月和1月是上涨幅度最大的。

这些分解为我们提供了清晰的洞察。例如，从“月度季节性”图中，我们看到每个月的月底（大约26号到30号）价格有下降趋势。从“年度季节性”图中，我们看到12月和1月是上涨潜力最大的月份。

![](img/4b3f7a4cabc1adf0426d607124b30f68_69.png)

这些发现可以转化为策略思路：或许在月底时应谨慎买入股票。

![](img/4b3f7a4cabc1adf0426d607124b30f68_71.png)

![](img/4b3f7a4cabc1adf0426d607124b30f68_73.png)

## 调整与扩展季节性分析

![](img/4b3f7a4cabc1adf0426d607124b30f68_75.png)

我们之前将 `weekly_seasonality` 设为了 `False`。如果想观察以周为周期的变化，可以将其设为 `True`。

![](img/4b3f7a4cabc1adf0426d607124b30f68_77.png)

![](img/4b3f7a4cabc1adf0426d607124b30f68_79.png)

```python
model = Prophet(weekly_seasonality=True)
model.fit(history)
model.plot_components(forecast)
```

![](img/4b3f7a4cabc1adf0426d607124b30f68_81.png)

![](img/4b3f7a4cabc1adf0426d607124b30f68_83.png)

![](img/4b3f7a4cabc1adf0426d607124b30f68_63.png)

![](img/4b3f7a4cabc1adf0426d607124b30f68_85.png)

![](img/4b3f7a4cabc1adf0426d607124b30f68_87.png)

设置后，重新创建模型并进行拟合，结果中就会包含周季节性成分。需要注意的是，A股市场周末不开盘，所以我们通常只观察周一到周五。图中显示周一至周五的趋势相对平缓，这或许符合市场特征。炒股经验丰富的同学可以验证这个模式是否与实际感受相符。如果相似，说明模型捕捉到了有效的规律。

![](img/4b3f7a4cabc1adf0426d607124b30f68_89.png)

## 突变点检测与分析

![](img/4b3f7a4cabc1adf0426d607124b30f68_91.png)

除了季节性，Prophet还能自动检测时间序列中的“突变点”。突变点是指趋势发生明显变化的时刻，例如从下降转为上升，或从快速上升变为缓慢上升。

在代码中，模型训练后，我们可以通过 `model.changepoints` 属性获取这些突变点的日期。

```python
changepoints = model.changepoints # 获取突变点日期
```

![](img/4b3f7a4cabc1adf0426d607124b30f68_93.png)

![](img/4b3f7a4cabc1adf0426d607124b30f68_95.png)

然后，我们可以检查这些突变点处趋势的变化率（可以理解为二阶导数的符号）：
*   变化率大于零 (`positive`): 表示在该点趋势加速向上。
*   变化率小于零 (`negative`): 表示在该点趋势加速向下或向上减速。

![](img/4b3f7a4cabc1adf0426d607124b30f68_97.png)

以下代码演示了如何识别并可视化突变点：

```python
# 获取突变点
changepoints = model.changepoints
# 计算趋势的变化率（简化表示，实际可通过模型参数获取）
# 这里假设我们有一种方式判断变化方向
positive_cps = [] # 存储正向突变点
negative_cps = [] # 存储负向突变点
# ... （根据变化率筛选逻辑）
# 画图时，用不同颜色标记正向（如绿色）和负向（如红色）突变点
```

![](img/4b3f7a4cabc1adf0426d607124b30f68_101.png)

最终，我们可以在趋势图上用垂直线标出这些突变点，并用不同颜色区分其方向。这有助于我们理解时间序列在哪些关键时间点发生了趋势性转变。

![](img/4b3f7a4cabc1adf0426d607124b30f68_99.png)

## 未来预测

![](img/4b3f7a4cabc1adf0426d607124b30f68_101.png)

![](img/4b3f7a4cabc1adf0426d607124b30f68_103.png)

接下来是大家最感兴趣的部分：如何预测未来。假设当前时间是2018年7月21日，我们想预测未来180天的走势。

预测步骤与之前类似，关键区别在于 `make_future_dataframe` 时指定 `periods=180`。

![](img/4b3f7a4cabc1adf0426d607124b30f68_105.png)

```python
# 创建模型，这里可以包含之前设定的所有季节性参数
model = create_model() # 假设此函数返回配置好的Prophet模型
# 使用历史数据拟合模型
model.fit(history_df)
# 生成包含未来180天日期的数据框架
future = model.make_future_dataframe(periods=180)
# 进行预测
forecast = model.predict(future)
```

![](img/4b3f7a4cabc1adf0426d607124b30f68_107.png)

`future` 数据框架会从历史数据开始日期，一直延伸到历史结束日期之后的第180天。Prophet会在这个扩展的时间框架上进行预测。

画图方式不变：

![](img/4b3f7a4cabc1adf0426d607124b30f68_109.png)

```python
fig = model.plot(forecast)
```

![](img/4b3f7a4cabc1adf0426d607124b30f68_111.png)

![](img/4b3f7a4cabc1adf0426d607124b30f68_113.png)

![](img/4b3f7a4cabc1adf0426d607124b30f68_113.png)

在结果图中，黑色圆点（观测值）只到历史数据结束点（例如2018年1月26日）。绿色圆点（预测值）则会向后延伸180天。图中显示，模型预测未来价格会延续历史趋势，但可能包含一些下降调整。

模型预测相对“保守”，没有出现极端大幅上涨，这是因为趋势的灵活度受到了 `changepoint_prior_scale` 参数的控制。这个参数是调整预测“保守”与“奔放”程度的核心。

*   **较小的值 (如0.01)**: 约束趋势变化，使模型更保守，平滑，可能欠拟合。
*   **较大的值 (如0.5)**: 允许趋势更大程度的变化，使模型更灵活，可能过拟合。

![](img/4b3f7a4cabc1adf0426d607124b30f68_115.png)

![](img/4b3f7a4cabc1adf0426d607124b30f68_117.png)

通过调整这个参数，我们可以让模型更好地适应数据的特点。

![](img/4b3f7a4cabc1adf0426d607124b30f68_119.png)

## 总结

![](img/4b3f7a4cabc1adf0426d607124b30f68_121.png)

![](img/4b3f7a4cabc1adf0426d607124b30f68_123.png)

本节课我们一起学习了Facebook Prophet时间序列预测神器的基本用法。我们通过代码演示了如何：
1.  **构建与拟合模型**：实例化Prophet，调整季节性参数，并用历史数据拟合。
2.  **结果可视化**：绘制包含观测值、预测值和置信区间的综合图表。
3.  **分解季节性**：使用 `plot_components` 深入理解年度、月度等季节性效应。
4.  **检测突变点**：识别时间序列中趋势发生显著变化的关键时间点。
5.  **进行未来预测**：通过指定 `periods` 参数，预测未来一段时间的数据走势。

![](img/4b3f7a4cabc1adf0426d607124b30f68_125.png)

Prophet的强大之处在于其高度封装且功能全面，即使对时间序列分析了解不深的初学者，也能通过它快速得到有指导意义的分析和预测结果。核心参数 `changepoint_prior_scale` 为我们提供了调整模型复杂度的开关，是实践中需要重点调试的对象。