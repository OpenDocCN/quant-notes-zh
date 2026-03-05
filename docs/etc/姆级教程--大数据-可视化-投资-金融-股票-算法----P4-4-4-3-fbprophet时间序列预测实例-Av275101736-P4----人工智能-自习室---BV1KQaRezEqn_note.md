# Python金融量化交易：4.4.3：fbprophet时间序列预测实例 📈

![](img/e5c65a5b9361f55792f0bc3a60f84555_1.png)

在本节课中，我们将学习如何使用Facebook的Prophet库进行时间序列预测。我们将通过一个实例，从数据准备、模型构建、训练、可视化到未来预测，完整地走一遍流程。课程内容力求简单直白，让初学者能够轻松理解。

![](img/e5c65a5b9361f55792f0bc3a60f84555_3.png)

![](img/e5c65a5b9361f55792f0bc3a60f84555_5.png)

## 概述

![](img/e5c65a5b9361f55792f0bc3a60f84555_7.png)

![](img/e5c65a5b9361f55792f0bc3a60f84555_9.png)

![](img/e5c65a5b9361f55792f0bc3a60f84555_11.png)

Prophet是Facebook开源的一个时间序列预测工具，它非常适合具有季节性特征的数据。本节我们将使用Prophet对一个股票价格数据集进行建模和预测，并学习如何解读模型输出的各种图表。

![](img/e5c65a5b9361f55792f0bc3a60f84555_13.png)

---

![](img/e5c65a5b9361f55792f0bc3a60f84555_15.png)

![](img/e5c65a5b9361f55792f0bc3a60f84555_17.png)

## 模型构建与初步可视化

![](img/e5c65a5b9361f55792f0bc3a60f84555_19.png)

上一节我们介绍了数据准备，本节中我们来看看如何构建Prophet模型并进行初步的可视化。

![](img/e5c65a5b9361f55792f0bc3a60f84555_21.png)

当我们执行创建模型的操作后，Prophet会先进行建模，然后进行预测并绘制出图表。

![](img/e5c65a5b9361f55792f0bc3a60f84555_23.png)

![](img/e5c65a5b9361f55792f0bc3a60f84555_1.png)

![](img/e5c65a5b9361f55792f0bc3a60f84555_25.png)

上图展示了模型预测的基本结果。其中：
*   **黑色点** 代表我们的观测值（历史真实数据）。
*   **绿色点** 代表模型拟合（预测）出的值。
*   **浅绿色区域** 代表预测的置信区间。

![](img/e5c65a5b9361f55792f0bc3a60f84555_27.png)

这样，一个基本的时间序列预测图就构造出来了。

![](img/e5c65a5b9361f55792f0bc3a60f84555_29.png)

![](img/e5c65a5b9361f55792f0bc3a60f84555_31.png)

构造出模型后，我们还可以利用Prophet框架自带的函数来展示模型的更多细节。

![](img/e5c65a5b9361f55792f0bc3a60f84555_33.png)

![](img/e5c65a5b9361f55792f0bc3a60f84555_3.png)

以下是Prophet模型可以展示的几种趋势分解：
*   **年度趋势**：展示数据在多年跨度上的整体变化。
*   **月度趋势**：展示数据在一个月内的周期性变化。
*   **周度趋势**：展示数据在一周内的周期性变化。

这些趋势图都可以绘制出来，帮助我们理解数据的内在规律。

![](img/e5c65a5b9361f55792f0bc3a60f84555_35.png)

---

## 深入模型构建过程

现在，让我们深入看一下建模的具体步骤。

首先，我们通过 `cf.create_model()` 函数来创建模型。这个函数内部会实例化一个Prophet模型对象。

![](img/e5c65a5b9361f55792f0bc3a60f84555_37.png)

在实例化时，可以指定一系列参数来控制模型行为：
*   `yearly_seasonality`, `weekly_seasonality`, `daily_seasonality`：布尔值，控制是否绘制相应的季节性成分。
*   `changepoint_prior_scale`：一个关键参数，默认值为 `0.05`。这个参数控制模型对趋势变化的灵活度，值越大，模型对历史数据中的波动越敏感，预测趋势可能更“奔放”；值越小，模型则更“保守”，趋势更平滑。

以下是创建模型的核心代码逻辑：

![](img/e5c65a5b9361f55792f0bc3a60f84555_39.png)

```python
# 实例化Prophet模型，并设置参数
model = Prophet(
    yearly_seasonality=True,
    weekly_seasonality=False, # 初始设置为False，后续可调整
    daily_seasonality=False,
    changepoint_prior_scale=0.05
)
# 添加自定义周期（例如，以30.5天为周期）
model.add_seasonality(name='monthly', period=30.5, fourier_order=5)
```

![](img/e5c65a5b9361f55792f0bc3a60f84555_41.png)

![](img/e5c65a5b9361f55792f0bc3a60f84555_43.png)

模型实例化后，我们使用历史数据进行训练。这里，我们选择最近3年的数据作为训练集。

![](img/e5c65a5b9361f55792f0bc3a60f84555_45.png)

```python
# 获取最近3年的数据作为训练集
train_data = data[-3*365:]
# 使用模型拟合数据
model.fit(train_data)
```

![](img/e5c65a5b9361f55792f0bc3a60f84555_47.png)

训练完成后，我们使用 `model.make_future_dataframe()` 来生成用于预测的时间框架。参数 `periods` 指定了需要预测的未来天数。

*   当 `periods=0` 时，我们只对历史数据进行拟合和“预测”（即模型在历史数据上的表现），不预测未来。
*   当 `periods=180` 时，模型会生成包含未来180天日期的新数据框，并对其进行预测。

![](img/e5c65a5b9361f55792f0bc3a60f84555_49.png)

```python
# 生成包含未来180天的日期数据框
future = model.make_future_dataframe(periods=180)
# 进行预测
forecast = model.predict(future)
```

![](img/e5c65a5b9361f55792f0bc3a60f84555_51.png)

预测结果 `forecast` 是一个DataFrame，包含以下几列关键信息：
*   `ds`：日期时间列。
*   `yhat`：预测值。
*   `yhat_lower`：预测值的下限（置信区间）。
*   `yhat_upper`：预测值的上限（置信区间）。

![](img/e5c65a5b9361f55792f0bc3a60f84555_53.png)

![](img/e5c65a5b9361f55792f0bc3a60f84555_55.png)

最后，我们将观测值和预测值绘制在同一张图上。

![](img/e5c65a5b9361f55792f0bc3a60f84555_57.png)

![](img/e5c65a5b9361f55792f0bc3a60f84555_59.png)

```python
# 绘制观测值（黑色点）
plt.scatter(data['ds'], data['y'], color='black', label='Observed')
# 绘制预测值（绿色线）
plt.plot(forecast['ds'], forecast['yhat'], color='green', label='Forecast')
# 绘制置信区间（浅绿色区域）
plt.fill_between(forecast['ds'], forecast['yhat_lower'], forecast['yhat_upper'], color='lightgreen', alpha=0.5)
plt.legend()
plt.show()
```

![](img/e5c65a5b9361f55792f0bc3a60f84555_61.png)

![](img/e5c65a5b9361f55792f0bc3a60f84555_63.png)

![](img/e5c65a5b9361f55792f0bc3a60f84555_41.png)

![](img/e5c65a5b9361f55792f0bc3a60f84555_65.png)

上图就是模型拟合历史数据后绘制出的基本图表。

![](img/e5c65a5b9361f55792f0bc3a60f84555_67.png)

![](img/e5c65a5b9361f55792f0bc3a60f84555_69.png)

![](img/e5c65a5b9361f55792f0bc3a60f84555_71.png)

---

![](img/e5c65a5b9361f55792f0bc3a60f84555_73.png)

![](img/e5c65a5b9361f55792f0bc3a60f84555_75.png)

## 分析季节性成分

Prophet的强大之处在于它能自动分解并可视化数据的季节性。调用 `model.plot_components(forecast)` 即可得到分解图。

![](img/e5c65a5b9361f55792f0bc3a60f84555_47.png)

![](img/e5c65a5b9361f55792f0bc3a60f84555_77.png)

![](img/e5c65a5b9361f55792f0bc3a60f84555_79.png)

![](img/e5c65a5b9361f55792f0bc3a60f84555_81.png)

从分解图中我们可以分析出一些规律（以示例数据为例）：
*   **年度趋势**：从2015年到2018年，整体价格呈上升趋势。
*   **月度趋势**：在每个月内，价格呈现月初上升、月末下降的周期性 pattern。
*   **月份效应**：从全年来看，12月和1月的价格上浮最大，而7月、9月和10月则有下降趋势。

![](img/e5c65a5b9361f55792f0bc3a60f84555_83.png)

这些分析为我们提供了交易时机的参考思路，例如，根据模型，月底可能是价格低点。

如果你想分析周度趋势，只需在创建模型时将 `weekly_seasonality` 参数设为 `True`。

![](img/e5c65a5b9361f55792f0bc3a60f84555_85.png)

![](img/e5c65a5b9361f55792f0bc3a60f84555_87.png)

```python
model = Prophet(weekly_seasonality=True)
```

![](img/e5c65a5b9361f55792f0bc3a60f84555_89.png)

![](img/e5c65a5b9361f55792f0bc3a60f84555_91.png)

---

![](img/e5c65a5b9361f55792f0bc3a60f84555_93.png)

![](img/e5c65a5b9361f55792f0bc3a60f84555_95.png)

## 识别趋势突变点

![](img/e5c65a5b9361f55792f0bc3a60f84555_97.png)

![](img/e5c65a5b9361f55792f0bc3a60f84555_99.png)

突变点是指时间序列趋势发生显著变化的点，例如由上升转为下降，或增长速率突然加快/减慢。Prophet可以自动检测这些点。

![](img/e5c65a5b9361f55792f0bc3a60f84555_101.png)

![](img/e5c65a5b9361f55792f0bc3a60f84555_103.png)

模型内部通过观察时间序列的二阶导数来识别突变点。二阶导数大于零的点意味着趋势在加速（正突变），小于零则意味着趋势在减速或反转（负突变）。

![](img/e5c65a5b9361f55792f0bc3a60f84555_105.png)

以下是识别和绘制突变点的步骤：

![](img/e5c65a5b9361f55792f0bc3a60f84555_107.png)

1.  训练模型并获得预测结果。
2.  从模型中提取检测到的突变点 (`model.changepoints`)。
3.  计算每个突变点处的趋势变化率（斜率变化）。
4.  根据变化率的正负，将突变点分类并绘制。

```python
# 获取突变点日期
changepoints = model.changepoints
# 获取趋势变化率
trend_deltas = model.params['delta'].mean(0)

![](img/e5c65a5b9361f55792f0bc3a60f84555_109.png)

![](img/e5c65a5b9361f55792f0bc3a60f84555_111.png)

# 区分正负突变点
positive_cp = changepoints[trend_deltas > 0]
negative_cp = changepoints[trend_deltas < 0]

![](img/e5c65a5b9361f55792f0bc3a60f84555_113.png)

# 在原始图表上标注突变点
plt.scatter(positive_cp, [max(data['y'])]*len(positive_cp), color='red', marker='^', label='Positive Changepoint')
plt.scatter(negative_cp, [min(data['y'])]*len(negative_cp), color='blue', marker='v', label='Negative Changepoint')
plt.legend()
```

![](img/e5c65a5b9361f55792f0bc3a60f84555_117.png)

上图展示了在历史序列中识别出的突变点，红色三角代表正突变（趋势加速），蓝色倒三角代表负突变（趋势减速或转向）。

![](img/e5c65a5b9361f55792f0bc3a60f84555_115.png)

---

![](img/e5c65a5b9361f55792f0bc3a60f84555_117.png)

![](img/e5c65a5b9361f55792f0bc3a60f84555_119.png)

## 进行未来预测

最后，我们利用训练好的模型对未来进行预测。只需在 `make_future_dataframe` 中指定一个大于0的 `periods` 参数。

![](img/e5c65a5b9361f55792f0bc3a60f84555_121.png)

![](img/e5c65a5b9361f55792f0bc3a60f84555_123.png)

```python
# 预测未来180天
future = model.make_future_dataframe(periods=180)
forecast = model.predict(future)
```

绘制包含未来预测的完整图表。

![](img/e5c65a5b9361f55792f0bc3a60f84555_129.png)

![](img/e5c65a5b9361f55792f0bc3a60f84555_125.png)

![](img/e5c65a5b9361f55792f0bc3a60f84555_127.png)

在最终的预测图中：
*   **黑色点**：截至“当前日期”（如2018-01-26）的历史观测值。
*   **绿色线及区域**：模型对整个时间范围的拟合和预测值及其置信区间。绿色线在历史数据之后的部分，即是对未来180天的预测。

![](img/e5c65a5b9361f55792f0bc3a60f84555_129.png)

可以看到，模型基于历史规律，预测未来价格在短期上升后可能会有所回落。预测的“保守”或“奔放”程度，很大程度上由 `changepoint_prior_scale` 参数控制。调高该值，模型会更贴合历史波动，未来预测的波动也可能更大；调低该值，预测趋势会更平缓。

---

![](img/e5c65a5b9361f55792f0bc3a60f84555_131.png)

## 总结

![](img/e5c65a5b9361f55792f0bc3a60f84555_133.png)

![](img/e5c65a5b9361f55792f0bc3a60f84555_135.png)

本节课中我们一起学习了使用Facebook Prophet库进行时间序列预测的完整流程：

![](img/e5c65a5b9361f55792f0bc3a60f84555_137.png)

![](img/e5c65a5b9361f55792f0bc3a60f84555_139.png)

1.  **模型构建**：实例化Prophet对象，并理解关键参数（如季节性开关和 `changepoint_prior_scale`）的作用。
2.  **模型训练**：使用历史数据对模型进行拟合。
3.  **结果可视化**：绘制观测值、拟合值及置信区间的基本预测图。
4.  **季节性分析**：利用 `plot_components` 功能分解并查看数据的年度、月度、周度等季节性趋势，从中挖掘交易规律。
5.  **突变点检测**：识别时间序列中趋势发生显著变化的点，并理解其含义。
6.  **未来预测**：扩展时间范围，让模型对未来进行预测，并解读预测结果。

![](img/e5c65a5b9361f55792f0bc3a60f84555_141.png)

Prophet是一个功能强大且易于使用的时间序列预测工具，通过本节的实例，希望你能够掌握其基本用法，并将其应用到自己的量化分析项目中。