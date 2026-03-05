# Python金融量化分析：P4：fbprophet时间序列预测实例 📈

![](img/9ced51a98f0a378442228b486c50a311_1.png)

![](img/9ced51a98f0a378442228b486c50a311_3.png)

![](img/9ced51a98f0a378442228b486c50a311_5.png)

![](img/9ced51a98f0a378442228b486c50a311_7.png)

![](img/9ced51a98f0a378442228b486c50a311_9.png)

在本节课中，我们将学习如何使用Facebook的Prophet库进行时间序列预测。我们将通过一个实例，从模型构建、训练、可视化到预测，完整地走一遍流程，并理解其中的核心概念。

![](img/9ced51a98f0a378442228b486c50a311_11.png)

![](img/9ced51a98f0a378442228b486c50a311_13.png)

![](img/9ced51a98f0a378442228b486c50a311_15.png)

## 概述

![](img/9ced51a98f0a378442228b486c50a311_17.png)

![](img/9ced51a98f0a378442228b486c50a311_19.png)

Prophet是Facebook开源的一个时间序列预测工具，它非常适合具有季节性特征的数据。本节我们将使用Prophet对一个股票价格数据集进行建模和预测，并分析其趋势、季节性以及突变点。

![](img/9ced51a98f0a378442228b486c50a311_21.png)

![](img/9ced51a98f0a378442228b486c50a311_23.png)

## 模型构建与训练

![](img/9ced51a98f0a378442228b486c50a311_25.png)

![](img/9ced51a98f0a378442228b486c50a311_27.png)

上一节我们介绍了数据准备，本节中我们来看看如何构建和训练Prophet模型。

![](img/9ced51a98f0a378442228b486c50a311_29.png)

![](img/9ced51a98f0a378442228b486c50a311_31.png)

![](img/9ced51a98f0a378442228b486c50a311_33.png)

首先，我们创建一个Prophet模型实例。在创建时，可以指定一些参数来控制模型的行为。

```python
model = cf.create_model()
```

![](img/9ced51a98f0a378442228b486c50a311_35.png)

以下是创建模型时可以指定的关键参数：
*   `yearly_seasonality`: 是否考虑年度季节性趋势。
*   `weekly_seasonality`: 是否考虑周度季节性趋势。
*   `daily_seasonality`: 是否考虑每日季节性趋势。
*   `changepoint_prior_scale`: 控制趋势灵活性的关键参数，默认值为`0.05`。值越大，模型对趋势变化的捕捉越敏感（更“奔放”）；值越小，趋势线越平滑（更“保守”）。
*   `holidays`: 可以传入一个包含节假日信息的DataFrame。

模型实例化后，我们用历史数据进行拟合。这里我们使用最近3年的数据进行训练。

![](img/9ced51a98f0a378442228b486c50a311_37.png)

```python
model.fit(data.tail(3*365)) # 使用最近3年数据
```

![](img/9ced51a98f0a378442228b486c50a311_39.png)

训练完成后，我们可以使用模型进行预测。`make_future_dataframe`方法用于生成包含未来日期的数据框。

![](img/9ced51a98f0a378442228b486c50a311_41.png)

![](img/9ced51a98f0a378442228b486c50a311_43.png)

```python
future = model.make_future_dataframe(periods=0) # periods=0表示不预测未来，只对历史数据进行拟合
forecast = model.predict(future)
```

![](img/9ced51a98f0a378442228b486c50a311_45.png)

![](img/9ced51a98f0a378442228b486c50a311_47.png)

预测结果`forecast`是一个DataFrame，其中包含以下几列核心预测值：
*   `yhat`: 预测值。
*   `yhat_lower`: 预测区间的下限。
*   `yhat_upper`: 预测区间的上限。

![](img/9ced51a98f0a378442228b486c50a311_49.png)

## 结果可视化

![](img/9ced51a98f0a378442228b486c50a311_51.png)

![](img/9ced51a98f0a378442228b486c50a311_53.png)

训练和预测完成后，我们可以将结果可视化，直观地理解模型的拟合效果。

![](img/9ced51a98f0a378442228b486c50a311_55.png)

![](img/9ced51a98f0a378442228b486c50a311_57.png)

![](img/9ced51a98f0a378442228b486c50a311_59.png)

以下是绘制观测值、预测值及置信区间的代码逻辑：

![](img/9ced51a98f0a378442228b486c50a311_61.png)

![](img/9ced51a98f0a378442228b486c50a311_63.png)

![](img/9ced51a98f0a378442228b486c50a311_65.png)

![](img/9ced51a98f0a378442228b486c50a311_67.png)

```python
# 绘制观测值（历史真实数据）
plt.plot(historical_data[‘ds‘], historical_data[‘y‘], ‘k.‘, label=‘Observation‘)
# 绘制预测值
plt.plot(forecast[‘ds‘], forecast[‘yhat‘], ‘g-‘, label=‘Prediction‘)
# 绘制置信区间
plt.fill_between(forecast[‘ds‘], forecast[‘yhat_lower‘], forecast[‘yhat_upper‘], color=‘lightgreen‘, alpha=0.5)
plt.legend()
plt.show()
```

![](img/9ced51a98f0a378442228b486c50a311_69.png)

![](img/9ced51a98f0a378442228b486c50a311_71.png)

![](img/9ced51a98f0a378442228b486c50a311_73.png)

生成的图表中，黑色圆点代表观测值，绿色曲线代表模型的预测值，浅绿色区域代表预测的置信区间。

![](img/9ced51a98f0a378442228b486c50a311_75.png)

## 季节性分解

![](img/9ced51a98f0a378442228b486c50a311_77.png)

![](img/9ced51a98f0a378442228b486c50a311_79.png)

![](img/9ced51a98f0a378442228b486c50a311_81.png)

Prophet的一个强大功能是能够自动分解时间序列中的不同季节性成分。

![](img/9ced51a98f0a378442228b486c50a311_83.png)

![](img/9ced51a98f0a378442228b486c50a311_85.png)

我们可以使用`model.plot_components(forecast)`方法来绘制趋势和季节性组件。该方法会生成一系列子图：
*   **整体趋势图**：展示数据在整个时间范围内的变化趋势。
*   **年度季节性**：展示数据在一年中不同月份或季节的周期性规律。
*   **周度季节性**：展示数据在一周中不同日期的周期性规律（如果启用）。

![](img/9ced51a98f0a378442228b486c50a311_87.png)

![](img/9ced51a98f0a378442228b486c50a311_89.png)

![](img/9ced51a98f0a378442228b486c50a311_91.png)

例如，在我们的例子中，分析结果显示：
*   整体趋势在3年内是向上的。
*   在一年中，7月、9月和10月价格倾向于下降，而12月和1月价格上涨幅度最大。
*   在一个月内，月初价格有上升趋势，月末价格有下降趋势。

![](img/9ced51a98f0a378442228b486c50a311_93.png)

![](img/9ced51a98f0a378442228b486c50a311_95.png)

![](img/9ced51a98f0a378442228b486c50a311_97.png)

![](img/9ced51a98f0a378442228b486c50a311_99.png)

## 突变点分析

![](img/9ced51a98f0a378442228b486c50a311_101.png)

![](img/9ced51a98f0a378442228b486c50a311_103.png)

![](img/9ced51a98f0a378442228b486c50a311_105.png)

突变点（Changepoints）是指时间序列趋势发生显著变化的点。Prophet可以自动检测这些点。

![](img/9ced51a98f0a378442228b486c50a311_107.png)

模型内部会计算时间序列的二阶导数，并通过`changepoints`属性给出检测到的突变点列表。我们可以根据突变点处趋势变化的方向（二阶导正负）对其进行分类和可视化。

![](img/9ced51a98f0a378442228b486c50a311_109.png)

![](img/9ced51a98f0a378442228b486c50a311_111.png)

```python
# 获取模型检测到的突变点
changepoints = model.changepoints
# 计算突变点处的趋势变化方向（例如通过二阶导数的符号）
# ... 此处根据模型内部逻辑处理 ...
# 可视化：用不同颜色的竖线标记正向和负向突变点
```

![](img/9ced51a98f0a378442228b486c50a311_113.png)

在可视化结果中，我们可能会用绿色竖线标记趋势加速上升的点（正向突变），用红色竖线标记趋势加速下降的点（负向突变）。

## 未来预测

![](img/9ced51a98f0a378442228b486c50a311_115.png)

![](img/9ced51a98f0a378442228b486c50a311_117.png)

![](img/9ced51a98f0a378442228b486c50a311_119.png)

最后，我们可以使用训练好的模型对未来进行预测。

![](img/9ced51a98f0a378442228b486c50a311_121.png)

![](img/9ced51a98f0a378442228b486c50a311_123.png)

关键步骤是指定`make_future_dataframe`方法中的`periods`参数，例如`periods=180`表示预测未来180天。

```python
future = model.make_future_dataframe(periods=180)
forecast = model.predict(future)
```

![](img/9ced51a98f0a378442228b486c50a311_125.png)

![](img/9ced51a98f0a378442228b486c50a311_127.png)

![](img/9ced51a98f0a378442228b486c50a311_129.png)

绘制包含未来预测的图表时，我们会发现，在历史数据结束点之后，只有绿色的预测曲线及其置信区间在向前延伸，而没有黑色的观测值。

预测结果的“保守”或“奔放”程度，很大程度上由创建模型时的`changepoint_prior_scale`参数控制。调整这个参数，可以改变模型对历史数据中波动和趋势变化的反应强度。

![](img/9ced51a98f0a378442228b486c50a311_131.png)

![](img/9ced51a98f0a378442228b486c50a311_133.png)

![](img/9ced51a98f0a378442228b486c50a311_135.png)

## 总结

![](img/9ced51a98f0a378442228b486c50a311_137.png)

![](img/9ced51a98f0a378442228b486c50a311_139.png)

![](img/9ced51a98f0a378442228b486c50a311_141.png)

本节课中我们一起学习了使用Facebook Prophet库进行时间序列预测的完整流程。
我们首先构建并训练了模型，然后可视化了拟合结果和置信区间。
接着，我们利用Prophet的内置功能分解并查看了数据的季节性规律和突变点。
最后，我们演示了如何用模型预测未来值，并理解了`changepoint_prior_scale`参数对预测风格的影响。
Prophet以其易用性和对季节性数据的良好处理能力，成为了金融量化分析中时间序列预测的实用工具。