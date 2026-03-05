# Python金融分析量化交易：P4：fbprophet时间序列预测实例 📈

![](img/fecbac7126bf1bd65384945bec51701b_1.png)

在本节课中，我们将学习如何使用Facebook的Prophet库进行时间序列预测。我们将通过一个实例，从数据准备、模型构建、训练到结果可视化，完整地走一遍流程，并理解其中的关键概念和参数。

![](img/fecbac7126bf1bd65384945bec51701b_3.png)

## 概述

![](img/fecbac7126bf1bd65384945bec51701b_5.png)

![](img/fecbac7126bf1bd65384945bec51701b_7.png)

Prophet是Facebook开源的一个时间序列预测工具，它非常适合处理具有明显季节性、节假日效应和趋势变化的数据。本节我们将使用Prophet来预测股票价格，并分析其趋势和季节性规律。

![](img/fecbac7126bf1bd65384945bec51701b_9.png)

![](img/fecbac7126bf1bd65384945bec51701b_11.png)

## 模型构建与训练

![](img/fecbac7126bf1bd65384945bec51701b_13.png)

上一节我们介绍了时间序列的基本概念，本节中我们来看看如何具体使用Prophet构建模型。

![](img/fecbac7126bf1bd65384945bec51701b_15.png)

首先，执行`create_model`函数会创建一个Prophet模型实例并进行建模。建模完成后，模型会进行预测并生成图表。

![](img/fecbac7126bf1bd65384945bec51701b_17.png)

![](img/fecbac7126bf1bd65384945bec51701b_19.png)

![](img/fecbac7126bf1bd65384945bec51701b_1.png)

在生成的图表中，黑色点代表观测值（即历史真实数据），绿色点代表模型预测值，而浅绿色区域则表示预测的置信区间。这样就构成了一个基本的时间序列预测图。

![](img/fecbac7126bf1bd65384945bec51701b_21.png)

![](img/fecbac7126bf1bd65384945bec51701b_3.png)

![](img/fecbac7126bf1bd65384945bec51701b_23.png)

模型构造完成后，我们使用Prophet框架自带的`plot_components`函数来展示模型发现的规律。

![](img/fecbac7126bf1bd65384945bec51701b_25.png)

![](img/fecbac7126bf1bd65384945bec51701b_5.png)

这个函数不是我们自己写的，而是框架内置的。它可以直观地展示模型的各个组成部分。

![](img/fecbac7126bf1bd65384945bec51701b_27.png)

![](img/fecbac7126bf1bd65384945bec51701b_29.png)

![](img/fecbac7126bf1bd65384945bec51701b_7.png)

![](img/fecbac7126bf1bd65384945bec51701b_31.png)

训练完成后，模型会识别出数据中的规律。`plot_components`函数可以分别画出数据的整体趋势、年度趋势、月度趋势以及周度趋势（如果指定了的话）。

![](img/fecbac7126bf1bd65384945bec51701b_33.png)

![](img/fecbac7126bf1bd65384945bec51701b_9.png)

## 核心代码解析

现在，我们深入看一下建模的具体步骤。

![](img/fecbac7126bf1bd65384945bec51701b_35.png)

我们回到代码中，执行`model = cf.create_model()`。按F5或相应快捷键进入`create_model`函数内部。

![](img/fecbac7126bf1bd65384945bec51701b_15.png)

第一步是重载绘图工具。然后，`model = cf.create_model()`会实例化一个Prophet模型。

![](img/fecbac7126bf1bd65384945bec51701b_17.png)

在实例化时，需要指定一些参数。例如，`seasonality_mode`参数决定是否绘制季节性变化图，可以指定为`True`或`False`。

![](img/fecbac7126bf1bd65384945bec51701b_37.png)

一个关键参数是`changepoint_prior_scale`，它默认值为`0.05`。这个参数控制模型对趋势变化的灵活度，值越大，模型对趋势变化越敏感。

```python
# 实例化Prophet模型，关键参数示例
model = Prophet(
    seasonality_mode='additive', # 季节性模式
    changepoint_prior_scale=0.05, # 趋势变化灵活度
    yearly_seasonality=True, # 是否考虑年度季节性
    weekly_seasonality=False # 是否考虑周度季节性，可根据需要调整
)
```

![](img/fecbac7126bf1bd65384945bec51701b_39.png)

如果你想查看所有参数的定义，可以在IDE中（如PyCharm）使用`Ctrl+鼠标左键`点击`Prophet`进入源码查看。源码中详细定义了`changepoint_prior_scale`、`yearly_seasonality`、`weekly_seasonality`、`daily_seasonality`等参数，你还可以传入`holidays`参数来指定节假日。

![](img/fecbac7126bf1bd65384945bec51701b_23.png)

![](img/fecbac7126bf1bd65384945bec51701b_41.png)

由于源码内容较多，我们在此不深入阅读。如果你感兴趣，可以在实际使用时仔细研究。

![](img/fecbac7126bf1bd65384945bec51701b_43.png)

![](img/fecbac7126bf1bd65384945bec51701b_45.png)

参数传入后，模型还添加了一个以30.5天为周期的季节性成分。最后，将构建好的时间序列模型返回。

![](img/fecbac7126bf1bd65384945bec51701b_47.png)

![](img/fecbac7126bf1bd65384945bec51701b_31.png)

模型构建好后，需要传入数据进行训练。这里，`self.training_years`参数指定使用最近多少年的历史数据进行训练，例如设置为3，则只使用近3年的数据。

```python
# 使用历史数据拟合模型
model.fit(history_data)
```

![](img/fecbac7126bf1bd65384945bec51701b_49.png)

训练使用`model.fit()`方法。拟合完成后，使用`model.make_future_dataframe`方法生成用于预测的时间框架。

![](img/fecbac7126bf1bd65384945bec51701b_51.png)

![](img/fecbac7126bf1bd65384945bec51701b_35.png)

![](img/fecbac7126bf1bd65384945bec51701b_53.png)

这里，`periods`参数指定为0，意味着我们只对历史数据进行“预测”（即模型拟合），而不预测未来的新时间点。这有助于我们评估模型在历史数据上的表现。稍后我们会演示将`periods`设置为90或180来进行真正未来预测的情况。

![](img/fecbac7126bf1bd65384945bec51701b_55.png)

![](img/fecbac7126bf1bd65384945bec51701b_57.png)

`make_future_dataframe`生成的时间框架，当`periods=0`时，就是历史数据本身。然后使用`model.predict`方法进行预测。

![](img/fecbac7126bf1bd65384945bec51701b_59.png)

```python
# 生成历史数据的预测（不包含未来）
future = model.make_future_dataframe(periods=0)
forecast = model.predict(future)
```

![](img/fecbac7126bf1bd65384945bec51701b_61.png)

![](img/fecbac7126bf1bd65384945bec51701b_63.png)

预测过程可能需要一些时间。预测完成后，结果`forecast`是一个DataFrame，其中会自动添加几个关键列：`yhat`是预测值，`yhat_lower`和`yhat_upper`是预测值的置信区间下限和上限。

![](img/fecbac7126bf1bd65384945bec51701b_65.png)

![](img/fecbac7126bf1bd65384945bec51701b_37.png)

![](img/fecbac7126bf1bd65384945bec51701b_67.png)

![](img/fecbac7126bf1bd65384945bec51701b_69.png)

## 结果可视化

![](img/fecbac7126bf1bd65384945bec51701b_71.png)

![](img/fecbac7126bf1bd65384945bec51701b_73.png)

下面是将预测结果可视化的步骤。

![](img/fecbac7126bf1bd65384945bec51701b_75.png)

我们创建一张图，将历史观测值（`ds`和`y`）用黑色点画出来。然后，将预测值（`forecast[‘ds’]`和`forecast[‘yhat’]`）用绿色线画出来。同时，我们还可以用`fill_between`方法画出置信区间（`yhat_lower`和`yhat_upper`）。

```python
# 绘制预测结果图
fig1 = model.plot(forecast)
# 添加观测值散点
plt.scatter(history_data[‘ds’], history_data[‘y’], color=‘black’, s=5)
# 添加预测线
plt.plot(forecast[‘ds’], forecast[‘yhat’], color=‘green’, linewidth=1)
# 填充置信区间
plt.fill_between(forecast[‘ds’], forecast[‘yhat_lower’], forecast[‘yhat_upper’], color=‘lightgreen’, alpha=0.3)
```

执行上述代码后，就得到了预测图。图中，浅绿色区域是置信区间，黑色点是真实观测值，绿色线是模型的预测值。

![](img/fecbac7126bf1bd65384945bec51701b_77.png)

![](img/fecbac7126bf1bd65384945bec51701b_79.png)

![](img/fecbac7126bf1bd65384945bec51701b_39.png)

![](img/fecbac7126bf1bd65384945bec51701b_81.png)

画图的过程很简单，核心是准备好数据（`forecast`）后调用绘图函数。

![](img/fecbac7126bf1bd65384945bec51701b_83.png)

![](img/fecbac7126bf1bd65384945bec51701b_41.png)

## 季节性分解分析

![](img/fecbac7126bf1bd65384945bec51701b_85.png)

![](img/fecbac7126bf1bd65384945bec51701b_87.png)

接下来，我们使用`plot_components`来分析数据的季节性。

![](img/fecbac7126bf1bd65384945bec51701b_89.png)

执行`model.plot_components(forecast)`，它会绘制多张子图来展示趋势和季节性。

![](img/fecbac7126bf1bd65384945bec51701b_91.png)

![](img/fecbac7126bf1bd65384945bec51701b_93.png)

![](img/fecbac7126bf1bd65384945bec51701b_45.png)

![](img/fecbac7126bf1bd65384945bec51701b_95.png)

![](img/fecbac7126bf1bd65384945bec51701b_97.png)

以下是该函数生成的主要图表类型：
*   **整体趋势图**：展示从起始日期（如2015年）到结束日期（如2018年）的长期价格变化趋势。
*   **年度季节性图**：展示一年中不同月份的平均效应。例如，图中可能显示12月和1月是上涨幅度最大的月份，而7月、9月和10月则呈现下降趋势。
*   **月度季节性图**：展示一个月内不同日期的平均效应。例如，月初可能呈现上升趋势，而月末则呈现下降趋势。
*   **周度季节性图**（如果启用）：展示一周内不同星期几的效应。需要注意的是，股市在周末（周六、周日）通常不交易，因此周度模式主要反映周一至周五的变化。

![](img/fecbac7126bf1bd65384945bec51701b_99.png)

![](img/fecbac7126bf1bd65384945bec51701b_47.png)

![](img/fecbac7126bf1bd65384945bec51701b_101.png)

![](img/fecbac7126bf1bd65384945bec51701b_103.png)

这些图表为我们提供了清晰的交易思路。例如，从年度季节性可以看出，年底和年初可能是较好的投资时机；从月度季节性可以看出，每月底市场可能较为疲软，需谨慎操作。

![](img/fecbac7126bf1bd65384945bec51701b_105.png)

![](img/fecbac7126bf1bd65384945bec51701b_57.png)

![](img/fecbac7126bf1bd65384945bec51701b_107.png)

你可以通过设置模型参数来控制是否显示这些成分。例如，在创建模型时，将`weekly_seasonality`设置为`True`，就可以在分解图中看到周度趋势。

![](img/fecbac7126bf1bd65384945bec51701b_51.png)

![](img/fecbac7126bf1bd65384945bec51701b_109.png)

## 突变点分析

![](img/fecbac7126bf1bd65384945bec51701b_111.png)

![](img/fecbac7126bf1bd65384945bec51701b_113.png)

除了季节性，Prophet还能自动检测时间序列中的“突变点”。

突变点是指时间序列趋势发生显著变化的点，例如从上升趋势转为下降趋势，或增长速率突然加快/减慢的点。Prophet称其为`changepoint`。

以下是分析突变点的步骤：
1.  同样地，创建并训练模型。
2.  模型训练后，可以从`model.changepoints`属性中获取检测到的突变点日期。
3.  对于每个突变点，可以查看其趋势变化的方向（通过计算或模型提供的二阶导数信息）。趋势向上增强的点标记为“positive”，趋势向下减弱的点标记为“negative”。
4.  最后，在原始序列图上，用不同颜色的竖线（如绿色代表向上，红色代表向下）标出这些突变点。

```python
# 检测并绘制突变点
changepoints = model.changepoints # 获取突变点日期
# 通常可以取突变点对应的预测趋势变化率进行分析
for cp in changepoints:
    # 判断该点的趋势变化方向，并相应标记
    pass
# 在整体趋势图上添加突变点标记
fig = model.plot(forecast)
for cp in changepoints:
    plt.axvline(x=cp, color=‘red’, linestyle=‘--’, alpha=0.5) # 示例：用红色虚线标记
```

![](img/fecbac7126bf1bd65384945bec51701b_103.png)

![](img/fecbac7126bf1bd65384945bec51701b_115.png)

这样，我们就能直观地看到历史数据中趋势发生重大转变的时刻。

![](img/fecbac7126bf1bd65384945bec51701b_117.png)

![](img/fecbac7126bf1bd65384945bec51701b_119.png)

![](img/fecbac7126bf1bd65384945bec51701b_109.png)

## 未来预测

![](img/fecbac7126bf1bd65384945bec51701b_121.png)

最后，我们来看如何用训练好的模型进行真正的未来预测。

![](img/fecbac7126bf1bd65384945bec51701b_123.png)

![](img/fecbac7126bf1bd65384945bec51701b_125.png)

预测未来的步骤与之前类似，关键区别在于`make_future_dataframe`函数中的`periods`参数。之前我们设为0，现在设为想要预测的未来天数，例如180天。

```python
# 预测未来180天
future = model.make_future_dataframe(periods=180)
forecast = model.predict(future)
```

此时，生成的`future`时间框架会包含历史日期和未来180天的日期。`predict`方法会对所有这些日期进行预测。

![](img/fecbac7126bf1bd65384945bec51701b_127.png)

![](img/fecbac7126bf1bd65384945bec51701b_129.png)

![](img/fecbac7126bf1bd65384945bec51701b_121.png)

![](img/fecbac7126bf1bd65384945bec51701b_131.png)

绘图方法不变。在结果图中，黑色观测点只到历史数据的最后日期（例如2018年1月26日），而绿色的预测线会向后延伸180天，展示模型对未来趋势的判断。

![](img/fecbac7126bf1bd65384945bec51701b_127.png)

从预测结果看，模型基于历史规律，预测未来价格可能会先经历一段下降，然后再上升。这可能是因为模型捕捉到了“物极必反”的周期性规律。当然，实际的未来走势可能更加复杂，模型的预测可能相对保守。

![](img/fecbac7126bf1bd65384945bec51701b_131.png)

![](img/fecbac7126bf1bd65384945bec51701b_133.png)

![](img/fecbac7126bf1bd65384945bec51701b_135.png)

模型的保守或激进程度，可以通过我们之前提到的`changepoint_prior_scale`参数来调整。增大该值会使模型更灵活，更能捕捉剧烈的趋势变化，预测可能更“奔放”；减小该值则使模型更平滑，预测更“保守”。

![](img/fecbac7126bf1bd65384945bec51701b_137.png)

## 总结

![](img/fecbac7126bf1bd65384945bec51701b_139.png)

![](img/fecbac7126bf1bd65384945bec51701b_141.png)

本节课中我们一起学习了Facebook Prophet时间序列预测工具的核心应用。我们通过实例完成了以下内容：
1.  **模型构建与训练**：学习了如何实例化Prophet模型，设置关键参数（如季节性模式和`changepoint_prior_scale`），并使用历史数据拟合模型。
2.  **结果可视化**：掌握了如何绘制预测结果图，包括观测值、预测值和置信区间。
3.  **季节性分解**：使用`plot_components`函数深入分析了数据的整体趋势、年度、月度及周度季节性规律，从中获得交易时机的洞察。
4.  **突变点检测**：了解了Prophet自动检测趋势突变点的功能，并学会如何将其可视化。
5.  **未来预测**：最后，我们演示了如何调整参数来预测未来一段时间的数据，并理解了模型预测行为背后的逻辑。

![](img/fecbac7126bf1bd65384945bec51701b_143.png)

Prophet是一个功能强大且易于使用的时间序列预测神器，通过本节课的学习，你已经掌握了其基本用法，可以开始尝试用它来分析自己的金融数据或其他时间序列数据了。