# Python金融量化与股票交易：P4：4.4.3：fbprophet时间序列预测实例 📈

![](img/7c7e0bd4186ec02238d8fdf720147892_1.png)

在本节课中，我们将学习如何使用Facebook开源的Prophet库进行时间序列预测。我们将通过一个股票数据的实例，从模型构建、训练、可视化到未来预测，完整地走一遍流程。课程内容力求简单直白，让初学者能够理解并上手实践。

![](img/7c7e0bd4186ec02238d8fdf720147892_3.png)

---

![](img/7c7e0bd4186ec02238d8fdf720147892_5.png)

![](img/7c7e0bd4186ec02238d8fdf720147892_7.png)

## 模型构建与初步可视化

![](img/7c7e0bd4186ec02238d8fdf720147892_9.png)

![](img/7c7e0bd4186ec02238d8fdf720147892_11.png)

上一节我们介绍了时间序列的基本概念，本节中我们来看看如何使用Prophet构建模型并进行初步的可视化。

执行`create_model`函数后，Prophet会帮助我们建立模型并进行预测。预测结果会以图表形式展示。

![](img/7c7e0bd4186ec02238d8fdf720147892_13.png)

![](img/7c7e0bd4186ec02238d8fdf720147892_15.png)

![](img/7c7e0bd4186ec02238d8fdf720147892_1.png)

![](img/7c7e0bd4186ec02238d8fdf720147892_17.png)

![](img/7c7e0bd4186ec02238d8fdf720147892_19.png)

在生成的图表中：
*   黑色点代表观测到的真实值。
*   绿色点代表模型预测出的拟合值。
*   浅绿色区域代表预测的置信区间。

这样，一个基本的时间序列模型就构建完成了。

![](img/7c7e0bd4186ec02238d8fdf720147892_21.png)

![](img/7c7e0bd4186ec02238d8fdf720147892_3.png)

![](img/7c7e0bd4186ec02238d8fdf720147892_23.png)

模型构建完成后，我们可以利用Prophet框架自带的`plot_components`函数来深入分析数据的内在规律。

![](img/7c7e0bd4186ec02238d8fdf720147892_25.png)

![](img/7c7e0bd4186ec02238d8fdf720147892_5.png)

这个函数不是我们自己写的，而是框架内置的功能。它能自动展示模型识别出的多种趋势。

![](img/7c7e0bd4186ec02238d8fdf720147892_27.png)

![](img/7c7e0bd4186ec02238d8fdf720147892_29.png)

![](img/7c7e0bd4186ec02238d8fdf720147892_7.png)

![](img/7c7e0bd4186ec02238d8fdf720147892_31.png)

以下是`plot_components`函数可以展示的几种趋势分解图：
*   **整体趋势**：数据在多年跨度内的变化情况。
*   **年度季节性**：数据在一年中不同月份的变化规律。
*   **月度季节性**：数据在一个月内不同日期的变化规律。

![](img/7c7e0bd4186ec02238d8fdf720147892_33.png)

![](img/7c7e0bd4186ec02238d8fdf720147892_9.png)

---

## 深入模型构建过程

![](img/7c7e0bd4186ec02238d8fdf720147892_35.png)

了解了可视化结果后，我们深入代码，看看模型是如何一步步构建的。

我们回到`create_model`函数的定义处。第一步是重载画图工具的相关设置。

![](img/7c7e0bd4186ec02238d8fdf720147892_15.png)

然后，`model = cf.create_model()` 会实例化一个Prophet模型。实例化时可以指定多个参数。

![](img/7c7e0bd4186ec02238d8fdf720147892_17.png)

以下是实例化Prophet模型时可以配置的一些关键参数：
*   `yearly_seasonality`：是否拟合年度季节性，值为`True`或`False`。
*   `weekly_seasonality`：是否拟合周度季节性。
*   `daily_seasonality`：是否拟合每日季节性。
*   `changepoint_prior_scale`：这是一个关键参数，默认值为`0.05`，它控制模型对趋势变化的灵活度。
*   `holidays`：可以传入一个包含节假日信息的DataFrame。

![](img/7c7e0bd4186ec02238d8fdf720147892_37.png)

![](img/7c7e0bd4186ec02238d8fdf720147892_19.png)

如果想深入了解每个参数的设计，可以按住`Ctrl`键点击`Prophet`进入其源码查看。由于源码内容较多，我们在此不深入探讨。

![](img/7c7e0bd4186ec02238d8fdf720147892_39.png)

![](img/7c7e0bd4186ec02238d8fdf720147892_21.png)

在示例中，我们还添加了一个以30.5天为周期的季节性成分。实例化完成后，将模型返回。

![](img/7c7e0bd4186ec02238d8fdf720147892_41.png)

![](img/7c7e0bd4186ec02238d8fdf720147892_43.png)

![](img/7c7e0bd4186ec02238d8fdf720147892_23.png)

![](img/7c7e0bd4186ec02238d8fdf720147892_45.png)

拿到模型后，需要传入数据进行训练。这里有一个细节：`self.training`参数用于指定使用多少年的历史数据进行训练。

![](img/7c7e0bd4186ec02238d8fdf720147892_47.png)

```python
# 例如，使用最近3年的数据进行训练
training_years = 3
```
通过`df[-training_years*365:]`这样的切片操作，可以只选取最近3年的数据。然后用`model.fit()`方法完成训练。

![](img/7c7e0bd4186ec02238d8fdf720147892_25.png)

训练完成后，使用`model.make_future_dataframe()`函数。当参数`periods=0`时，表示不预测未来的新日期，只对历史数据的时间点进行拟合预测，这常用于模型检验。

![](img/7c7e0bd4186ec02238d8fdf720147892_49.png)

```python
# 不预测未来，只拟合历史
future = model.make_future_dataframe(periods=0)
```
后续我们会演示`periods=90`或`100`时，对未来进行预测的情况。

![](img/7c7e0bd4186ec02238d8fdf720147892_51.png)

![](img/7c7e0bd4186ec02238d8fdf720147892_27.png)

![](img/7c7e0bd4186ec02238d8fdf720147892_53.png)

![](img/7c7e0bd4186ec02238d8fdf720147892_55.png)

执行预测（`model.predict(future)`）后，结果DataFrame中会自动添加几列：
*   `yhat`：预测值。
*   `yhat_lower`：预测区间的下限。
*   `yhat_upper`：预测区间的上限。

![](img/7c7e0bd4186ec02238d8fdf720147892_57.png)

我们可以用`yhat`作为预测指标，用`yhat_lower`和`yhat_upper`构成置信区间。

![](img/7c7e0bd4186ec02238d8fdf720147892_59.png)

![](img/7c7e0bd4186ec02238d8fdf720147892_61.png)

![](img/7c7e0bd4186ec02238d8fdf720147892_29.png)

![](img/7c7e0bd4186ec02238d8fdf720147892_63.png)

以下是绘制拟合效果图的步骤：
1.  创建画布。
2.  绘制观测值（`ds`和`y`），即历史数据。
3.  绘制预测值（`ds`和`yhat`）。
4.  用`fill_between`函数绘制置信区间（`yhat_lower` 到 `yhat_upper`）。

![](img/7c7e0bd4186ec02238d8fdf720147892_65.png)

![](img/7c7e0bd4186ec02238d8fdf720147892_31.png)

![](img/7c7e0bd4186ec02238d8fdf720147892_67.png)

![](img/7c7e0bd4186ec02238d8fdf720147892_69.png)

最终生成的图表中，黑色散点是真实观测值，绿色散点是模型拟合值，浅绿色区域是置信区间。这样就完成了一个基本模型的构建和可视化。

![](img/7c7e0bd4186ec02238d8fdf720147892_71.png)

![](img/7c7e0bd4186ec02238d8fdf720147892_73.png)

![](img/7c7e0bd4186ec02238d8fdf720147892_33.png)

![](img/7c7e0bd4186ec02238d8fdf720147892_75.png)

---

## 季节性分解分析

模型拟合完成后，我们可以通过`plot_components`函数更细致地观察数据中的季节性规律。

![](img/7c7e0bd4186ec02238d8fdf720147892_77.png)

![](img/7c7e0bd4186ec02238d8fdf720147892_79.png)

执行`model.plot_components(forecast)`会生成分解图，这个过程可能需要一些时间。

![](img/7c7e0bd4186ec02238d8fdf720147892_81.png)

![](img/7c7e0bd4186ec02238d8fdf720147892_83.png)

![](img/7c7e0bd4186ec02238d8fdf720147892_35.png)

生成的图表展示了不同时间尺度下的趋势：
*   **整体趋势图**：显示从2015年到2018年，股价整体呈上升趋势。
*   **年度季节性图**：显示在一年中，12月和1月股价上浮最大，7月、9月和10月则有下降趋势。
*   **月度季节性图**：显示在单月内，月初股价有上升趋势，到月末则呈现下降趋势。

![](img/7c7e0bd4186ec02238d8fdf720147892_85.png)

![](img/7c7e0bd4186ec02238d8fdf720147892_37.png)

![](img/7c7e0bd4186ec02238d8fdf720147892_87.png)

这些规律为我们提供了交易思路：例如，在月底股价通常下降时，或许不是买入的好时机。

![](img/7c7e0bd4186ec02238d8fdf720147892_89.png)

![](img/7c7e0bd4186ec02238d8fdf720147892_91.png)

![](img/7c7e0bd4186ec02238d8fdf720147892_39.png)

![](img/7c7e0bd4186ec02238d8fdf720147892_93.png)

我们也可以修改参数来查看其他季节性成分。例如，在创建模型时将`weekly_seasonality`设为`True`。

![](img/7c7e0bd4186ec02238d8fdf720147892_95.png)

![](img/7c7e0bd4186ec02238d8fdf720147892_97.png)

```python
model = Prophet(weekly_seasonality=True)
```
重新训练和绘图后，就可以看到以周为单位的季节性变化。需要注意的是，A股市场周末不开盘，所以周度模式主要反映周一至周五的变化。

![](img/7c7e0bd4186ec02238d8fdf720147892_99.png)

![](img/7c7e0bd4186ec02238d8fdf720147892_101.png)

![](img/7c7e0bd4186ec02238d8fdf720147892_41.png)

![](img/7c7e0bd4186ec02238d8fdf720147892_103.png)

---

![](img/7c7e0bd4186ec02238d8fdf720147892_105.png)

## 识别趋势突变点

![](img/7c7e0bd4186ec02238d8fdf720147892_107.png)

除了季节性，Prophet还能自动识别时间序列中的“突变点”。突变点是指趋势发生显著变化的时刻，例如由上升转为下降，或增长速率突然加快/减慢。

在代码中，训练模型后，可以通过`model.changepoints`属性获取模型识别出的突变点日期。

![](img/7c7e0bd4186ec02238d8fdf720147892_109.png)

![](img/7c7e0bd4186ec02238d8fdf720147892_43.png)

![](img/7c7e0bd4186ec02238d8fdf720147892_111.png)

以下是识别并可视化突变点的步骤：
1.  获取突变点日期列表。
2.  对于每个突变点，查询模型计算出的趋势变化率（可理解为二阶导数的符号）。
3.  根据变化率大于零（正向突变）或小于零（负向突变），用不同颜色（如红色和绿色）的竖线在图上标出。

![](img/7c7e0bd4186ec02238d8fdf720147892_113.png)

![](img/7c7e0bd4186ec02238d8fdf720147892_45.png)

最终，我们可以在趋势图上看到这些标识出的突变点，从而理解序列在哪些时刻发生了趋势转折。

![](img/7c7e0bd4186ec02238d8fdf720147892_47.png)

---

![](img/7c7e0bd4186ec02238d8fdf720147892_115.png)

## 进行未来预测

![](img/7c7e0bd4186ec02238d8fdf720147892_117.png)

![](img/7c7e0bd4186ec02238d8fdf720147892_119.png)

最后，我们来看如何用训练好的模型预测未来。这是大家最感兴趣的部分。

预测未来的关键是在`make_future_dataframe`函数中指定`periods`参数，例如`periods=180`表示预测未来180天。

![](img/7c7e0bd4186ec02238d8fdf720147892_121.png)

```python
future = model.make_future_dataframe(periods=180)
```
此时生成的`future` DataFrame不仅包含历史日期，还会向后延伸180个未来的日期。然后对这个`future`进行预测。

![](img/7c7e0bd4186ec02238d8fdf720147892_123.png)

![](img/7c7e0bd4186ec02238d8fdf720147892_49.png)

预测结果的绘图方式与之前类似。区别在于，对于未来的日期，只有绿色的预测值（`yhat`）和置信区间，而没有黑色的真实观测值。

![](img/7c7e0bd4186ec02238d8fdf720147892_51.png)

![](img/7c7e0bd4186ec02238d8fdf720147892_125.png)

从预测图可以看到，模型基于历史趋势，对未来做出了延续性预测。预测结果可能显得比较“保守”，没有出现特别剧烈的波动。这是因为模型默认的`changepoint_prior_scale`参数值较小，控制了趋势变化的灵活性。

![](img/7c7e0bd4186ec02238d8fdf720147892_127.png)

![](img/7c7e0bd4186ec02238d8fdf720147892_129.png)

![](img/7c7e0bd4186ec02238d8fdf720147892_53.png)

我们可以通过调整`changepoint_prior_scale`参数来改变模型的“保守”或“奔放”程度。调高该值会使模型更敏感，允许趋势发生更大变化；调低则使模型更平滑稳定。

```python
# 更灵活的模型
model = Prophet(changepoint_prior_scale=0.5)
# 更平滑的模型
model = Prophet(changepoint_prior_scale=0.01)
```

![](img/7c7e0bd4186ec02238d8fdf720147892_131.png)

---

![](img/7c7e0bd4186ec02238d8fdf720147892_133.png)

## 总结

![](img/7c7e0bd4186ec02238d8fdf720147892_135.png)

本节课中我们一起学习了使用Facebook Prophet库进行时间序列预测的全过程。

![](img/7c7e0bd4186ec02238d8fdf720147892_137.png)

![](img/7c7e0bd4186ec02238d8fdf720147892_139.png)

我们首先完成了模型的构建、训练和对历史数据的拟合可视化。然后，利用`plot_components`函数深入分析了数据的整体趋势、年度及月度季节性规律。接着，探索了模型自动识别趋势突变点的功能，并学会了如何将其可视化。最后，我们掌握了如何使用训练好的模型对未来进行预测，并了解到通过`changepoint_prior_scale`参数可以调节预测趋势的灵活度。

![](img/7c7e0bd4186ec02238d8fdf720147892_141.png)

Prophet作为一个强大的时间序列预测工具，封装了复杂的统计模型，让我们能够通过简单的接口完成专业的预测分析，是金融量化分析中的一个实用“神器”。