# 量化交易教程：4.4：fbprophet时间序列预测实例 📈

![](img/b13c4b4a57e82f11c47cd1cc27eeb3c2_1.png)

![](img/b13c4b4a57e82f11c47cd1cc27eeb3c2_2.png)

在本节课中，我们将学习如何使用Facebook的Prophet库进行时间序列预测。我们将通过一个实例，从数据建模、可视化到未来预测，完整地走一遍流程，并理解其中的核心概念和参数。

![](img/b13c4b4a57e82f11c47cd1cc27eeb3c2_3.png)

## 概述

![](img/b13c4b4a57e82f11c47cd1cc27eeb3c2_5.png)

![](img/b13c4b4a57e82f11c47cd1cc27eeb3c2_6.png)

![](img/b13c4b4a57e82f11c47cd1cc27eeb3c2_7.png)

Prophet是一个由Facebook开发的开源时间序列预测库，它非常适合处理具有明显季节性、节假日效应和趋势变化的数据。本节我们将通过一个股票价格预测的实例，学习如何构建模型、分析趋势、识别突变点并进行未来预测。

![](img/b13c4b4a57e82f11c47cd1cc27eeb3c2_8.png)

![](img/b13c4b4a57e82f11c47cd1cc27eeb3c2_9.png)

![](img/b13c4b4a57e82f11c47cd1cc27eeb3c2_10.png)

---

![](img/b13c4b4a57e82f11c47cd1cc27eeb3c2_12.png)

## 模型构建与初步可视化

![](img/b13c4b4a57e82f11c47cd1cc27eeb3c2_14.png)

上一节我们介绍了时间序列的基本概念，本节中我们来看看如何使用Prophet进行具体的建模和预测。

![](img/b13c4b4a57e82f11c47cd1cc27eeb3c2_15.png)

![](img/b13c4b4a57e82f11c47cd1cc27eeb3c2_16.png)

首先，我们创建一个Prophet模型并进行拟合。执行`create_model`函数后，它会帮助我们完成建模和初步预测。

![](img/b13c4b4a57e82f11c47cd1cc27eeb3c2_18.png)

![](img/b13c4b4a57e82f11c47cd1cc27eeb3c2_19.png)

以下是创建和拟合模型的核心代码：

![](img/b13c4b4a57e82f11c47cd1cc27eeb3c2_20.png)

![](img/b13c4b4a57e82f11c47cd1cc27eeb3c2_21.png)

![](img/b13c4b4a57e82f11c47cd1cc27eeb3c2_22.png)

```python
# 实例化Prophet模型
model = Prophet(
    yearly_seasonality=True,
    weekly_seasonality=False,
    daily_seasonality=False,
    changepoint_prior_scale=0.05
)
# 拟合模型
model.fit(training_data)
```

![](img/b13c4b4a57e82f11c47cd1cc27eeb3c2_24.png)

拟合完成后，我们可以进行预测并绘制结果图。图中黑色点代表观测值（历史数据），绿色点代表模型预测值，浅绿色区域代表置信区间。

![](img/b13c4b4a57e82f11c47cd1cc27eeb3c2_26.png)

![](img/b13c4b4a57e82f11c47cd1cc27eeb3c2_1.png)
![](img/b13c4b4a57e82f11c47cd1cc27eeb3c2_2.png)
![](img/b13c4b4a57e82f11c47cd1cc27eeb3c2_3.png)

---

## 分析数据的季节性趋势

模型构建完成后，Prophet框架内置了`plot_components`函数，可以直观地展示数据中发现的规律。

![](img/b13c4b4a57e82f11c47cd1cc27eeb3c2_28.png)

以下是调用该函数的方法：

```python
# 绘制模型的趋势和季节性组件
fig = model.plot_components(forecast)
```

![](img/b13c4b4a57e82f11c47cd1cc27eeb3c2_30.png)

![](img/b13c4b4a57e82f11c47cd1cc27eeb3c2_31.png)

![](img/b13c4b4a57e82f11c47cd1cc27eeb3c2_32.png)

该函数会生成多张子图，分别展示数据的整体趋势、年度趋势、月度趋势等。例如，我们可以观察到价格在每年特定月份（如12月和1月）有上浮趋势，而在7月、9月和10月则有下降趋势。

![](img/b13c4b4a57e82f11c47cd1cc27eeb3c2_34.png)

![](img/b13c4b4a57e82f11c47cd1cc27eeb3c2_35.png)

![](img/b13c4b4a57e82f11c47cd1cc27eeb3c2_5.png)
![](img/b13c4b4a57e82f11c47cd1cc27eeb3c2_6.png)
![](img/b13c4b4a57e82f11c47cd1cc27eeb3c2_7.png)

如果你想分析以周为单位的趋势，可以在创建模型时将`weekly_seasonality`参数设置为`True`。

![](img/b13c4b4a57e82f11c47cd1cc27eeb3c2_37.png)

```python
model = Prophet(weekly_seasonality=True)
```

![](img/b13c4b4a57e82f11c47cd1cc27eeb3c2_39.png)

---

![](img/b13c4b4a57e82f11c47cd1cc27eeb3c2_41.png)

![](img/b13c4b4a57e82f11c47cd1cc27eeb3c2_42.png)

## 识别时间序列中的突变点

![](img/b13c4b4a57e82f11c47cd1cc27eeb3c2_43.png)

![](img/b13c4b4a57e82f11c47cd1cc27eeb3c2_44.png)

![](img/b13c4b4a57e82f11c47cd1cc27eeb3c2_46.png)

在时间序列分析中，突变点是指趋势发生显著变化的时刻，例如从上升转为下降，或变化速率突然加快/减慢。

![](img/b13c4b4a57e82f11c47cd1cc27eeb3c2_47.png)

![](img/b13c4b4a57e82f11c47cd1cc27eeb3c2_49.png)

Prophet可以自动检测这些突变点。其原理是观察时间序列的二阶导数，通过导数的正负来判断趋势变化的方向。

![](img/b13c4b4a57e82f11c47cd1cc27eeb3c2_50.png)

![](img/b13c4b4a57e82f11c47cd1cc27eeb3c2_51.png)

以下是查找和可视化突变点的步骤：

1.  拟合模型后，从模型中获取突变点信息。
2.  计算突变点处趋势的变化方向（正增长或负增长）。
3.  用不同颜色的垂直线在图上标出这些点。

![](img/b13c4b4a57e82f11c47cd1cc27eeb3c2_53.png)

![](img/b13c4b4a57e82f11c47cd1cc27eeb3c2_54.png)

![](img/b13c4b4a57e82f11c47cd1cc27eeb3c2_55.png)

核心代码如下：

![](img/b13c4b4a57e82f11c47cd1cc27eeb3c2_57.png)

![](img/b13c4b4a57e82f11c47cd1cc27eeb3c2_58.png)

![](img/b13c4b4a57e82f11c47cd1cc27eeb3c2_59.png)

```python
# 从拟合好的模型中获取突变点
changepoints = model.changepoints
# 计算趋势变化量
trend_deltas = model.params['delta'].mean(0)
# 根据变化量的正负进行分类
positive_points = changepoints[trend_deltas > 0]
negative_points = changepoints[trend_deltas < 0]
```

在结果图中，我们通常用绿色竖线表示趋势转为正向增长的点，用红色竖线表示趋势转为负向增长的点。

![](img/b13c4b4a57e82f11c47cd1cc27eeb3c2_61.png)

![](img/b13c4b4a57e82f11c47cd1cc27eeb3c2_62.png)

![](img/b13c4b4a57e82f11c47cd1cc27eeb3c2_63.png)

![](img/b13c4b4a57e82f11c47cd1cc27eeb3c2_70.png)
![](img/b13c4b4a57e82f11c47cd1cc27eeb3c2_71.png)

![](img/b13c4b4a57e82f11c47cd1cc27eeb3c2_65.png)

![](img/b13c4b4a57e82f11c47cd1cc27eeb3c2_66.png)

---

![](img/b13c4b4a57e82f11c47cd1cc27eeb3c2_67.png)

![](img/b13c4b4a57e82f11c47cd1cc27eeb3c2_68.png)

## 进行未来预测

![](img/b13c4b4a57e82f11c47cd1cc27eeb3c2_70.png)

![](img/b13c4b4a57e82f11c47cd1cc27eeb3c2_71.png)

我们构建模型的最终目的往往是预测未来的值。在Prophet中，这通过指定一个未来的时间范围来实现。

![](img/b13c4b4a57e82f11c47cd1cc27eeb3c2_72.png)

![](img/b13c4b4a57e82f11c47cd1cc27eeb3c2_73.png)

![](img/b13c4b4a57e82f11c47cd1cc27eeb3c2_74.png)

例如，如果我们想预测未来180天的数据，可以在调用`make_future_dataframe`方法时传入参数`periods=180`。

![](img/b13c4b4a57e82f11c47cd1cc27eeb3c2_76.png)

以下是进行未来预测的核心代码：

![](img/b13c4b4a57e82f11c47cd1cc27eeb3c2_78.png)

![](img/b13c4b4a57e82f11c47cd1cc27eeb3c2_79.png)

```python
# 创建一个包含历史及未来日期的DataFrame
future = model.make_future_dataframe(periods=180)
# 进行预测
forecast = model.predict(future)
```

![](img/b13c4b4a57e82f11c47cd1cc27eeb3c2_81.png)

![](img/b13c4b4a57e82f11c47cd1cc27eeb3c2_82.png)

预测结果`forecast`是一个DataFrame，其中包含历史数据的拟合值和未来日期的预测值。对于未来的日期，只有预测值`yhat`和置信区间，没有真实的观测值`y`。

![](img/b13c4b4a57e82f11c47cd1cc27eeb3c2_93.png)
![](img/b13c4b4a57e82f11c47cd1cc27eeb3c2_94.png)

从预测图中可以看到，黑色观测值在某个时间点后终止，而绿色的预测曲线会继续向前延伸，展示出模型对未来趋势的判断。

![](img/b13c4b4a57e82f11c47cd1cc27eeb3c2_84.png)

---

![](img/b13c4b4a57e82f11c47cd1cc27eeb3c2_85.png)

![](img/b13c4b4a57e82f11c47cd1cc27eeb3c2_86.png)

## 调整模型灵活度：Changepoint Prior Scale

![](img/b13c4b4a57e82f11c47cd1cc27eeb3c2_88.png)

![](img/b13c4b4a57e82f11c47cd1cc27eeb3c2_89.png)

在最初的模型实例化中，我们设置了一个参数 `changepoint_prior_scale=0.05`。这个参数至关重要，它控制了模型对趋势变化的敏感度。

*   **值调大（如0.5）**：模型会更“奔放”，更倾向于认为数据中存在较多的趋势变化，拟合的曲线波动会更大，对历史数据的拟合可能更紧密。
*   **值调小（如0.001）**：模型会更“保守”，认为趋势是平滑稳定的，拟合的曲线会更加平缓，对历史数据的拟合可能更宽松。

![](img/b13c4b4a57e82f11c47cd1cc27eeb3c2_91.png)

你可以通过调整这个参数，让模型的预测风格更符合你对数据特性的先验认知。

![](img/b13c4b4a57e82f11c47cd1cc27eeb3c2_93.png)

![](img/b13c4b4a57e82f11c47cd1cc27eeb3c2_94.png)

![](img/b13c4b4a57e82f11c47cd1cc27eeb3c2_95.png)

---

## 总结

本节课中我们一起学习了使用Facebook Prophet库进行时间序列预测的完整流程。

![](img/b13c4b4a57e82f11c47cd1cc27eeb3c2_97.png)

![](img/b13c4b4a57e82f11c47cd1cc27eeb3c2_98.png)

我们首先学习了如何构建一个基础的Prophet模型，并对历史数据进行拟合与可视化。接着，我们利用`plot_components`函数深入分析了数据中的年度、月度等季节性趋势。然后，我们探讨了如何识别并可视化时间序列中的关键突变点。最后，我们掌握了如何进行未来时间点的预测，并了解了通过`changepoint_prior_scale`参数调整模型灵活度的方法。

![](img/b13c4b4a57e82f11c47cd1cc27eeb3c2_99.png)

![](img/b13c4b4a57e82f11c47cd1cc27eeb3c2_100.png)

![](img/b13c4b4a57e82f11c47cd1cc27eeb3c2_101.png)

![](img/b13c4b4a57e82f11c47cd1cc27eeb3c2_102.png)

Prophet是一个功能强大且易于上手的工具，通过本节的实例，你应该已经具备了使用它进行基础时间序列分析与预测的能力。