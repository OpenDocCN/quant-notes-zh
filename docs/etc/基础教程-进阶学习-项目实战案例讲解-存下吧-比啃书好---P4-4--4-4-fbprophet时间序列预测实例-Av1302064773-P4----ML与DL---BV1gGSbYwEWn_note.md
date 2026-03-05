# 量化交易教程：4.4：fbprophet时间序列预测实例 📈

![](img/64a67da8f50137a32d63adf0d70dff73_1.png)

![](img/64a67da8f50137a32d63adf0d70dff73_3.png)

在本节课中，我们将学习如何使用Facebook的Prophet库进行时间序列预测。我们将通过一个实例，从模型构建、训练、可视化到预测未来趋势，完整地走一遍流程。课程内容将尽可能简单直白，确保初学者能够理解。

![](img/64a67da8f50137a32d63adf0d70dff73_5.png)

![](img/64a67da8f50137a32d63adf0d70dff73_7.png)

![](img/64a67da8f50137a32d63adf0d70dff73_9.png)

---

![](img/64a67da8f50137a32d63adf0d70dff73_11.png)

![](img/64a67da8f50137a32d63adf0d70dff73_13.png)

## 模型构建与初步可视化

![](img/64a67da8f50137a32d63adf0d70dff73_15.png)

![](img/64a67da8f50137a32d63adf0d70dff73_17.png)

![](img/64a67da8f50137a32d63adf0d70dff73_19.png)

上一节我们介绍了时间序列分析的基础概念，本节中我们来看看如何使用Prophet库进行具体的建模和预测。

![](img/64a67da8f50137a32d63adf0d70dff73_21.png)

首先，我们执行代码创建一个Prophet模型。创建模型后，它会自动进行建模和预测，并生成可视化图表。

![](img/64a67da8f50137a32d63adf0d70dff73_23.png)

![](img/64a67da8f50137a32d63adf0d70dff73_1.png)

![](img/64a67da8f50137a32d63adf0d70dff73_25.png)

在生成的图表中：
*   黑色点代表**观测值**，即我们的历史数据。
*   绿色点代表**模型预测值**。
*   浅绿色区域代表**置信区间**，表示预测的不确定性范围。

![](img/64a67da8f50137a32d63adf0d70dff73_27.png)

![](img/64a67da8f50137a32d63adf0d70dff73_29.png)

这样，一个基本的时间序列模型就构建完成了。

![](img/64a67da8f50137a32d63adf0d70dff73_31.png)

![](img/64a67da8f50137a32d63adf0d70dff73_33.png)

---

![](img/64a67da8f50137a32d63adf0d70dff73_35.png)

![](img/64a67da8f50137a32d63adf0d70dff73_37.png)

## 探索季节性趋势

![](img/64a67da8f50137a32d63adf0d70dff73_39.png)

构造出模型后，我们还可以利用Prophet框架自带的函数来深入分析数据中的规律。

![](img/64a67da8f50137a32d63adf0d70dff73_3.png)

![](img/64a67da8f50137a32d63adf0d70dff73_41.png)

![](img/64a67da8f50137a32d63adf0d70dff73_43.png)

Prophet可以自动分解并展示数据中的不同趋势成分：
*   **整体趋势**：数据在多年跨度上的变化方向。
*   **年度趋势**：数据在一年中不同月份的变化规律。
*   **月度趋势**：数据在一个月内不同日期的变化规律。

以下是Prophet模型分解出的趋势图示例：

![](img/64a67da8f50137a32d63adf0d70dff73_15.png)

![](img/64a67da8f50137a32d63adf0d70dff73_45.png)

![](img/64a67da8f50137a32d63adf0d70dff73_47.png)

从图中我们可以观察到：
*   **整体趋势**：从2015年到2018年，价格呈上升趋势。
*   **月度趋势**：月初价格有上升趋势，月末则有下降趋势。
*   **年度趋势**：例如，7月、9月和10月价格倾向于下降，而12月和1月价格上浮最大。

这些分析为我们提供了交易时机的参考思路，例如月底可能不是买入股票的好时机。

![](img/64a67da8f50137a32d63adf0d70dff73_49.png)

---

![](img/64a67da8f50137a32d63adf0d70dff73_51.png)

![](img/64a67da8f50137a32d63adf0d70dff73_53.png)

## 模型构建过程详解

![](img/64a67da8f50137a32d63adf0d70dff73_55.png)

![](img/64a67da8f50137a32d63adf0d70dff73_57.png)

接下来，我们深入看一下建模的具体步骤。核心是调用 `cf.create_model()` 函数。

```python
model = cf.create_model(...)
```

![](img/64a67da8f50137a32d63adf0d70dff73_59.png)

该函数首先实例化一个Prophet模型对象。在实例化时，可以指定一系列参数来控制模型行为：

![](img/64a67da8f50137a32d63adf0d70dff73_61.png)

```python
# 示例参数
changepoint_prior_scale=0.05  # 控制趋势灵活度的关键参数
yearly_seasonality=True       # 是否考虑年度季节性
weekly_seasonality=False      # 是否考虑周度季节性
daily_seasonality=False       # 是否考虑每日季节性
```

![](img/64a67da8f50137a32d63adf0d70dff73_63.png)

![](img/64a67da8f50137a32d63adf0d70dff73_65.png)

![](img/64a67da8f50137a32d63adf0d70dff73_67.png)

其中，`changepoint_prior_scale` 是一个关键参数，它控制了模型对趋势变化的敏感度，值越大模型越灵活，对历史数据的拟合度可能更高，但也可能更容易过拟合。Prophet的默认值是0.05。

![](img/64a67da8f50137a32d63adf0d70dff73_69.png)

![](img/64a67da8f50137a32d63adf0d70dff73_71.png)

模型实例化后，传入历史数据进行训练。在示例中，我们只使用了最近3年的数据进行训练。

![](img/64a67da8f50137a32d63adf0d70dff73_73.png)

![](img/64a67da8f50137a32d63adf0d70dff73_75.png)

```python
# 使用最近3年数据训练
model.fit(historical_data_last_3_years)
```

![](img/64a67da8f50137a32d63adf0d70dff73_77.png)

![](img/64a67da8f50137a32d63adf0d70dff73_79.png)

训练完成后，我们使用 `model.make_future_dataframe()` 函数生成待预测的时间段。当参数 `periods=0` 时，表示不预测未来，只对历史数据进行拟合和预测，用于验证模型效果。

![](img/64a67da8f50137a32d63adf0d70dff73_81.png)

![](img/64a67da8f50137a32d63adf0d70dff73_83.png)

![](img/64a67da8f50137a32d63adf0d70dff73_85.png)

```python
# 不预测未来，仅拟合历史数据
future = model.make_future_dataframe(periods=0)
forecast = model.predict(future)
```

预测结果 `forecast` 是一个DataFrame，其中包含几个重要的列：
*   `yhat`: 预测值。
*   `yhat_lower`: 预测区间的下限。
*   `yhat_upper`: 预测区间的上限。

![](img/64a67da8f50137a32d63adf0d70dff73_87.png)

最后，我们将观测值（历史数据）、预测值及置信区间绘制在同一张图上，就得到了最初的模型效果图。

![](img/64a67da8f50137a32d63adf0d70dff73_89.png)

![](img/64a67da8f50137a32d63adf0d70dff73_91.png)

![](img/64a67da8f50137a32d63adf0d70dff73_93.png)

---

## 检测突变点

![](img/64a67da8f50137a32d63adf0d70dff73_95.png)

![](img/64a67da8f50137a32d63adf0d70dff73_97.png)

在时间序列分析中，识别趋势发生显著变化的点（突变点）非常重要。Prophet可以自动检测这些点。

![](img/64a67da8f50137a32d63adf0d70dff73_99.png)

![](img/64a67da8f50137a32d63adf0d70dff73_101.png)

![](img/64a67da8f50137a32d63adf0d70dff73_103.png)

突变点可能表现为：
*   由下降趋势转为上升趋势。
*   上升速度突然加快或减慢。
*   下降速度突然变化。

![](img/64a67da8f50137a32d63adf0d70dff73_105.png)

![](img/64a67da8f50137a32d63adf0d70dff73_107.png)

![](img/64a67da8f50137a32d63adf0d70dff73_109.png)

以下是检测突变点的代码逻辑：

![](img/64a67da8f50137a32d63adf0d70dff73_111.png)

![](img/64a67da8f50137a32d63adf0d70dff73_113.png)

```python
# 训练模型后，可以从模型中获取突变点
changepoints = model.changepoints

![](img/64a67da8f50137a32d63adf0d70dff73_115.png)

![](img/64a67da8f50137a32d63adf0d70dff73_117.png)

# 通常我们查看最重要的前N个突变点
top_changepoints = changepoints[:10]

# 对于每个突变点，可以查看其趋势变化方向（通过二阶导数判断）
# 二阶导数 > 0: 趋势加速上升 (positive)
# 二阶导数 < 0: 趋势加速下降 (negative)
```

![](img/64a67da8f50137a32d63adf0d70dff73_119.png)

![](img/64a67da8f50137a32d63adf0d70dff73_121.png)

我们可以将突变点在原图上标注出来，例如用绿色竖线表示趋势加速上升的点，用红色竖线表示趋势加速下降的点，从而直观地看到趋势发生转折的关键时刻。

![](img/64a67da8f50137a32d63adf0d70dff73_123.png)

![](img/64a67da8f50137a32d63adf0d70dff73_125.png)

![](img/64a67da8f50137a32d63adf0d70dff73_127.png)

---

## 进行未来预测

![](img/64a67da8f50137a32d63adf0d70dff73_127.png)

预测未来是时间序列模型的最终目的。使用Prophet进行未来预测非常简单，只需在生成未来数据框时指定要预测的天数。

![](img/64a67da8f50137a32d63adf0d70dff73_129.png)

![](img/64a67da8f50137a32d63adf0d70dff73_131.png)

```python
# 预测未来180天
future = model.make_future_dataframe(periods=180)
forecast = model.predict(future)
```

![](img/64a67da8f50137a32d63adf0d70dff73_133.png)

此时，`future` 数据框会包含历史日期和未来180天的日期，`forecast` 则包含对所有日期的预测值。对于未来的日期，我们只有预测值 `yhat` 和置信区间，没有真实的观测值 `y`。

![](img/64a67da8f50137a32d63adf0d70dff73_135.png)

![](img/64a67da8f50137a32d63adf0d70dff73_137.png)

将预测结果绘图，可以看到模型基于历史趋势对未来做出的推断。预测线会从历史数据的末端开始，按照模型学习到的规律向前延伸。

![](img/64a67da8f50137a32d63adf0d70dff73_143.png)

![](img/64a67da8f50137a32d63adf0d70dff73_139.png)

![](img/64a67da8f50137a32d63adf0d70dff73_141.png)

预测的保守或激进程度，可以通过调整 `changepoint_prior_scale` 参数来控制。该参数值越大，模型越倾向于认为未来会出现大的趋势变化，预测曲线可能更“奔放”；值越小，模型越倾向于延续历史平滑趋势，预测更“保守”。

![](img/64a67da8f50137a32d63adf0d70dff73_143.png)

---

## 总结

![](img/64a67da8f50137a32d63adf0d70dff73_145.png)

![](img/64a67da8f50137a32d63adf0d70dff73_147.png)

本节课中我们一起学习了使用Facebook Prophet库进行时间序列预测的全过程。

![](img/64a67da8f50137a32d63adf0d70dff73_149.png)

![](img/64a67da8f50137a32d63adf0d70dff73_151.png)

我们首先**构建并训练**了一个基础模型，看到了如何将历史数据、预测值和置信区间可视化。接着，我们利用Prophet的**季节性分解**功能，深入分析了数据在年、月等周期上的内在规律。然后，我们探讨了模型如何自动检测**趋势突变点**，并识别其变化方向。最后，我们演示了如何用训练好的模型**预测未来**一段时间的数据，并了解到通过调整 `changepoint_prior_scale` 参数可以控制预测的灵活度。

![](img/64a67da8f50137a32d63adf0d70dff73_153.png)

![](img/64a67da8f50137a32d63adf0d70dff73_155.png)

Prophet作为一个功能强大且易于使用的“神器”，封装了复杂的时间序列建模过程，让分析师能够更专注于业务逻辑和结果解读，为量化交易提供了有力的分析工具。