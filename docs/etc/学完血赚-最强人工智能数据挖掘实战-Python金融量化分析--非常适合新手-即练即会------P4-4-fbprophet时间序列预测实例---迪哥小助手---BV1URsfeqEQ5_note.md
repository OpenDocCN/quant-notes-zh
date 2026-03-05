# 人工智能数据挖掘实战：P4：fbprophet时间序列预测实例 📈

![](img/e641de0ebc72ee5c463a351704890efd_1.png)

![](img/e641de0ebc72ee5c463a351704890efd_2.png)

![](img/e641de0ebc72ee5c463a351704890efd_3.png)

在本节课中，我们将学习如何使用Facebook开源的Prophet库进行时间序列预测。我们将通过一个金融量化分析的实例，从模型构建、训练、可视化到未来预测，完整地走一遍流程。课程内容简单直白，非常适合初学者上手实践。

## 模型构建与训练 🏗️

![](img/e641de0ebc72ee5c463a351704890efd_5.png)

![](img/e641de0ebc72ee5c463a351704890efd_6.png)

![](img/e641de0ebc72ee5c463a351704890efd_7.png)

![](img/e641de0ebc72ee5c463a351704890efd_8.png)

![](img/e641de0ebc72ee5c463a351704890efd_9.png)

上一节我们介绍了数据准备，本节中我们来看看如何构建和训练Prophet模型。

![](img/e641de0ebc72ee5c463a351704890efd_10.png)

执行`create_model`函数后，程序会首先进行建模，然后基于模型进行预测。建模完成后，会生成一张图表。图表中的黑色点代表观测值（即历史真实数据），绿色点代表模型拟合出的预测值，而置信区间（confidence interval）则展示了预测的不确定性范围。

![](img/e641de0ebc72ee5c463a351704890efd_12.png)

这样，一个基本的时间序列模型就构建出来了。



![](img/e641de0ebc72ee5c463a351704890efd_14.png)

![](img/e641de0ebc72ee5c463a351704890efd_15.png)

![](img/e641de0ebc72ee5c463a351704890efd_16.png)

![](img/e641de0ebc72ee5c463a351704890efd_1.png)

## 模型趋势分析 📊

![](img/e641de0ebc72ee5c463a351704890efd_18.png)

![](img/e641de0ebc72ee5c463a351704890efd_19.png)

![](img/e641de0ebc72ee5c463a351704890efd_20.png)

![](img/e641de0ebc72ee5c463a351704890efd_21.png)

构建完模型后，我们可以利用Prophet框架自带的函数来可视化模型发现的各种规律。这些函数不是我们自己写的，而是框架内置的功能。

它可以自动展示数据按年份的整体趋势、按月份的趋势以及一年中每个月的季节性规律等。我们先来看看建模的具体代码是如何实现的。



![](img/e641de0ebc72ee5c463a351704890efd_23.png)

![](img/e641de0ebc72ee5c463a351704890efd_5.png)

回到代码，我们通过`F5`键进入`create_model`函数。第一步是重载绘图工具。然后，`model = c.c_model`会实例化一个Prophet模型。

以下是实例化模型的核心代码片段，其中指定了是否绘制季节性变化等参数：
```python
model = Prophet(
    yearly_seasonality=True,
    weekly_seasonality=False,
    daily_seasonality=False,
    changepoint_prior_scale=0.05
)
```
参数`changepoint_prior_scale`使用了默认值`0.05`，这个值控制着模型对趋势变化的灵活度。

如果你想深入了解每个参数的定义，可以按住`Ctrl`键点击函数名跳转到源码中阅读。源码中包含了是否指定年趋势（`yearly_seasonality`）、周趋势（`weekly_seasonality`）、日趋势以及节假日等众多可配置指标。由于源码内容较多，我们在此不深入阅读，感兴趣的同学可以自行研究。



![](img/e641de0ebc72ee5c463a351704890efd_25.png)

![](img/e641de0ebc72ee5c463a351704890efd_12.png)

![](img/e641de0ebc72ee5c463a351704890efd_27.png)

在代码中，我们传入了指定的参数，并额外添加了一个以30.5天为周期的变化趋势。这样，模型就能统计出以30.5天为周期的变化规律。最后，函数返回构建好的时间序列模型实例。

![](img/e641de0ebc72ee5c463a351704890efd_28.png)

![](img/e641de0ebc72ee5c463a351704890efd_29.png)

![](img/e641de0ebc72ee5c463a351704890efd_31.png)

## 模型拟合与历史预测 🔄

![](img/e641de0ebc72ee5c463a351704890efd_32.png)

拿到模型后，接下来传入数据进行训练。这里有一个关键操作：`self.training`。它的含义是，我们可以只使用最近几年的历史数据进行预测，例如只用近三年的数据来训练模型。

以下是训练和进行历史预测（即“自回归”）的核心步骤：
```python
# 使用最近三年的数据训练
model.fit(historical_data.tail(3*365))
# 创建未来时间点（这里d=0表示不预测未来，只对历史数据做拟合预测）
future = model.make_future_dataframe(periods=0)
forecast = model.predict(future)
```
`make_future_dataframe`函数中的参数`d`（在代码中为`periods`）指定了需要预测的未来天数。当`d=0`时，表示不预测新的时间点，仅仅在历史数据上进行拟合和预测，用于检验模型效果。后面我们会演示`d=90`或`d=180`时，预测未来序列的效果。

![](img/e641de0ebc72ee5c463a351704890efd_34.png)

预测完成后，会得到一系列结果列，主要包括：
*   **`yhat`**: 预测值。
*   **`yhat_lower`** 和 **`yhat_upper`**: 预测值的置信区间下限和上限。

![](img/e641de0ebc72ee5c463a351704890efd_36.png)

我们可以将`yhat`作为核心预测指标，而`yhat_lower`和`yhat_upper`则定义了预测的波动范围。



![](img/e641de0ebc72ee5c463a351704890efd_38.png)

![](img/e641de0ebc72ee5c463a351704890efd_39.png)

![](img/e641de0ebc72ee5c463a351704890efd_40.png)

![](img/e641de0ebc72ee5c463a351704890efd_41.png)

![](img/e641de0ebc72ee5c463a351704890efd_23.png)

![](img/e641de0ebc72ee5c463a351704890efd_43.png)

![](img/e641de0ebc72ee5c463a351704890efd_44.png)

## 结果可视化 🎨

![](img/e641de0ebc72ee5c463a351704890efd_46.png)

![](img/e641de0ebc72ee5c463a351704890efd_47.png)

![](img/e641de0ebc72ee5c463a351704890efd_48.png)

下面是将拟合结果可视化的代码逻辑：
```python
# 绘制观测值（历史真实数据）
plt.plot(df[‘ds‘], df[‘y‘], ‘k.‘, label=‘Observation‘)
# 绘制模型拟合值
plt.plot(forecast[‘ds‘], forecast[‘yhat‘], color=‘green‘, label=‘Forecast‘)
# 绘制置信区间
plt.fill_between(forecast[‘ds‘], forecast[‘yhat_lower‘], forecast[‘yhat_upper‘], color=‘lightgreen‘, alpha=0.5)
```
图表中，黑色点是观测值，绿色线是模型的预测值，浅绿色区域是置信区间。这样，我们就完成了模型的构建和可视化。整个过程简洁明了：准备好数据，用Prophet拟合，然后绘图。



![](img/e641de0ebc72ee5c463a351704890efd_27.png)

![](img/e641de0ebc72ee5c463a351704890efd_50.png)

![](img/e641de0ebc72ee5c463a351704890efd_51.png)

![](img/e641de0ebc72ee5c463a351704890efd_52.png)

## 季节性分解图解读 📉

![](img/e641de0ebc72ee5c463a351704890efd_54.png)

![](img/e641de0ebc72ee5c463a351704890efd_55.png)

![](img/e641de0ebc72ee5c463a351704890efd_56.png)

模型还提供了`plot_components`函数来绘制季节性分解图，帮助我们理解数据内在的模式。



![](img/e641de0ebc72ee5c463a351704890efd_31.png)

![](img/e641de0ebc72ee5c463a351704890efd_58.png)

![](img/e641de0ebc72ee5c463a351704890efd_59.png)

![](img/e641de0ebc72ee5c463a351704890efd_60.png)

从分解图中我们可以观察到以下规律：
*   **年度趋势**：从2015年到2018年，整体呈现上升趋势。
*   **月度趋势**：在每个月内，价格通常月初上涨，月末下降。
*   **年度季节性**：一年中的不同月份表现不同，例如7月、9月和10月通常呈下降趋势，而12月和1月上涨幅度最大。

![](img/e641de0ebc72ee5c463a351704890efd_62.png)

![](img/e641de0ebc72ee5c463a351704890efd_63.png)

这些发现为我们提供了清晰的交易思路：在月初和年末年初可能更适合买入，而在月末及7、9、10月则需要谨慎。

![](img/e641de0ebc72ee5c463a351704890efd_64.png)

![](img/e641de0ebc72ee5c463a351704890efd_65.png)

此外，我们还可以通过修改参数（如将`weekly_seasonality`设为`True`）来查看以周为单位的趋势图。图中显示周五之后和周一之前有波动，但需注意A股市场周末休市，实际交易周期为周一到周五，因此周的规律需要结合实际情况判断。



![](img/e641de0ebc72ee5c463a351704890efd_67.png)

![](img/e641de0ebc72ee5c463a351704890efd_68.png)

![](img/e641de0ebc72ee5c463a351704890efd_69.png)

![](img/e641de0ebc72ee5c463a351704890efd_36.png)

![](img/e641de0ebc72ee5c463a351704890efd_70.png)

![](img/e641de0ebc72ee5c463a351704890efd_71.png)

![](img/e641de0ebc72ee5c463a351704890efd_73.png)

## 突变点检测 🔍

接下来我们看看突变点检测。突变点是指时间序列趋势发生显著变化的点，例如从上升转为下降，或增长速率突然加快/减慢。

![](img/e641de0ebc72ee5c463a351704890efd_75.png)

![](img/e641de0ebc72ee5c463a351704890efd_76.png)

Prophet模型可以自动检测这些突变点。以下是相关的代码流程：
```python
# 创建并训练模型
model = Prophet()
model.fit(training_data)
# 获取预测结果和训练集数据，并按日期合并
forecast = model.predict(future)
results = pd.merge(forecast, training_data, on=‘ds‘)
# 模型自动计算出的突变点
changepoints = model.changepoints
# 获取突变点的日期
changepoint_dates = training_data.loc[training_data[‘ds‘].isin(changepoints)]
```
检测到突变点后，我们可以查看该点处的二阶导数（趋势变化率）。Prophet框架会帮我们计算出这个值。根据二阶导数大于零或小于零，可以判断该点是正向突变（增长加速或下降减速）还是负向突变（增长减速或下降加速）。

![](img/e641de0ebc72ee5c463a351704890efd_78.png)

![](img/e641de0ebc72ee5c463a351704890efd_79.png)

在可视化时，可以用不同颜色的竖线标记这些突变点，例如绿色线表示正向突变，红色线表示负向突变。



![](img/e641de0ebc72ee5c463a351704890efd_58.png)

## 未来预测 🚀

![](img/e641de0ebc72ee5c463a351704890efd_81.png)

![](img/e641de0ebc72ee5c463a351704890efd_82.png)

![](img/e641de0ebc72ee5c463a351704890efd_83.png)

最后，我们进行未来预测。这同样是使用`create_model`函数，但关键是指定一个大于0的`d`值（例如`d=180`），表示要预测未来180天的数据。

![](img/e641de0ebc72ee5c463a351704890efd_85.png)

`make_future_dataframe`函数会生成一个包含历史日期和未来180天新日期的数据框。模型将在这个扩展的时间序列上进行预测。



![](img/e641de0ebc72ee5c463a351704890efd_86.png)

![](img/e641de0ebc72ee5c463a351704890efd_85.png)

![](img/e641de0ebc72ee5c463a351704890efd_88.png)

预测结果如下图所示。黑色点依然是历史观测值，绿色线是模型的拟合及预测值。在历史数据结束点（2018年1月26日）之后，绿色线继续延伸，代表了模型对未来趋势的预测。

![](img/e641de0ebc72ee5c463a351704890efd_90.png)

![](img/e641de0ebc72ee5c463a351704890efd_91.png)

![](img/e641de0ebc72ee5c463a351704890efd_92.png)

从图中看，模型预测未来会有一个下降趋势。这可能是模型基于“物极必反”的历史规律做出的相对保守的判断。模型的保守或激进程度，可以通过我们之前提到的`changepoint_prior_scale`参数来调整。调高该值会使模型对趋势变化更敏感，预测更灵活；调低则使模型更平滑、更保守。



![](img/e641de0ebc72ee5c463a351704890efd_90.png)

## 总结 📝

![](img/e641de0ebc72ee5c463a351704890efd_94.png)

本节课中我们一起学习了使用Facebook Prophet库进行时间序列预测的全过程。
1.  **模型构建**：我们学习了如何实例化Prophet模型，并理解关键参数如`changepoint_prior_scale`的作用。
2.  **训练与拟合**：我们使用历史数据训练模型，并进行了历史数据的拟合预测，通过`yhat`、`yhat_lower`、`yhat_upper`等指标评估模型。
3.  **可视化分析**：我们绘制了拟合曲线、置信区间以及季节性分解图，从中解读出年度、月度趋势和季节性规律。
4.  **突变点检测**：利用模型内置功能自动检测趋势发生显著变化的突变点，并理解其含义。
5.  **未来预测**：通过指定未来天数，让模型预测未来的时间序列值，并认识到可以通过参数调整预测的保守程度。

![](img/e641de0ebc72ee5c463a351704890efd_95.png)

![](img/e641de0ebc72ee5c463a351704890efd_96.png)

![](img/e641de0ebc72ee5c463a351704890efd_97.png)

![](img/e641de0ebc72ee5c463a351704890efd_98.png)

![](img/e641de0ebc72ee5c463a351704890efd_99.png)

Prophet作为一个功能强大且易于使用的“神器”，封装了复杂的时间序列分析过程，让初学者也能快速上手进行预测分析。