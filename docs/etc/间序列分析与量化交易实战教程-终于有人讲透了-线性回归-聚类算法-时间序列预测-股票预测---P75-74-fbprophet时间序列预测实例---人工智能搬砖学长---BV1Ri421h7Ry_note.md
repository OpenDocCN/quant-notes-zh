# Python金融时间序列分析与量化交易实战教程：P75：fbprophet时间序列预测实例 📈

![](img/03ee92079a228f561d65ed21df5170c9_1.png)

![](img/03ee92079a228f561d65ed21df5170c9_3.png)

![](img/03ee92079a228f561d65ed21df5170c9_5.png)

在本节课中，我们将学习如何使用Facebook的Prophet库进行时间序列预测。我们将通过一个实例，了解如何构建模型、进行预测、分析季节性趋势以及识别突变点。

![](img/03ee92079a228f561d65ed21df5170c9_7.png)

![](img/03ee92079a228f561d65ed21df5170c9_9.png)

![](img/03ee92079a228f561d65ed21df5170c9_11.png)

## 概述

![](img/03ee92079a228f561d65ed21df5170c9_13.png)

![](img/03ee92079a228f561d65ed21df5170c9_15.png)

![](img/03ee92079a228f561d65ed21df5170c9_17.png)

Prophet是一个由Facebook开发的开源时间序列预测库，它能够处理具有季节性、节假日效应和趋势变化的数据。本节我们将通过一个股票数据的实例，演示Prophet的基本使用方法。

![](img/03ee92079a228f561d65ed21df5170c9_19.png)

![](img/03ee92079a228f561d65ed21df5170c9_21.png)

## 模型构建与预测

![](img/03ee92079a228f561d65ed21df5170c9_23.png)

首先，我们来看一下如何构建一个基本的Prophet模型并进行预测。

![](img/03ee92079a228f561d65ed21df5170c9_25.png)

执行`create_model`函数后，它会帮助我们建立模型并进行预测。预测结果会以图表形式展示。

![](img/03ee92079a228f561d65ed21df5170c9_27.png)

![](img/03ee92079a228f561d65ed21df5170c9_1.png)

![](img/03ee92079a228f561d65ed21df5170c9_29.png)

在上图中，黑色点代表观测值（即历史真实数据），绿色点代表模型预测值，浅绿色区域代表置信区间。这样就构成了一个基本的时间序列预测图。

## 模型创建与参数设置

![](img/03ee92079a228f561d65ed21df5170c9_31.png)

接下来，我们深入查看`create_model`函数内部是如何工作的。

以下是创建模型的核心代码步骤：

```python
# 实例化Prophet模型，并设置参数
model = Prophet(
    yearly_seasonality=True,  # 是否考虑年度季节性
    weekly_seasonality=False, # 是否考虑周度季节性（初始设为False）
    changepoint_prior_scale=0.05 # 趋势变化灵活度的关键参数
)
# 添加月度季节性（周期为30.5天）
model.add_seasonality(name='monthly', period=30.5, fourier_order=5)
```

![](img/03ee92079a228f561d65ed21df5170c9_33.png)

模型实例化时，可以指定多个参数：
*   `yearly_seasonality`: 是否绘制年度趋势变化图。
*   `weekly_seasonality`: 是否绘制周度趋势变化图。
*   `changepoint_prior_scale`: 这是一个关键参数，默认值为0.05，它控制模型对趋势变化的敏感度。值越大，模型越灵活，对历史数据的拟合度可能更高，但也可能更容易过拟合。

![](img/03ee92079a228f561d65ed21df5170c9_35.png)

如果想深入了解每个参数的定义，可以在IDE中通过`Ctrl+鼠标左键`点击`Prophet`跳转到源码进行查看。

![](img/03ee92079a228f561d65ed21df5170c9_37.png)

## 数据训练与历史拟合

![](img/03ee92079a228f561d65ed21df5170c9_39.png)

模型创建后，我们需要用历史数据对其进行训练。

```python
# 使用最近3年的数据进行训练
train_years = 3
history_data = data[-train_years*365:] # 假设数据按天记录
model.fit(history_data)

![](img/03ee92079a228f561d65ed21df5170c9_41.png)

# 创建未来数据框架，days=0表示只对历史数据进行拟合，不预测未来
future = model.make_future_dataframe(periods=0)
forecast = model.predict(future)
```

![](img/03ee92079a228f561d65ed21df5170c9_43.png)

![](img/03ee92079a228f561d65ed21df5170c9_45.png)

![](img/03ee92079a228f561d65ed21df5170c9_47.png)

这里有几个关键点：
1.  **训练数据**：我们指定`train_years=3`，意味着只使用最近3年的历史数据进行训练，而不是全部数据。
2.  **拟合与预测**：`make_future_dataframe(periods=0)`中的`periods=0`表示我们只对历史数据的时间点进行预测（即模型拟合），而不生成未来的日期。这用于检验模型在历史数据上的表现。
3.  **预测结果**：`forecast`对象中包含多个列，最重要的是：
    *   `yhat`: 预测值。
    *   `yhat_lower` 和 `yhat_upper`: 预测值的置信区间下限和上限。

![](img/03ee92079a228f561d65ed21df5170c9_49.png)

![](img/03ee92079a228f561d65ed21df5170c9_51.png)

训练完成后，我们可以绘制图表，将观测值（黑色点）、预测值（绿色线）和置信区间（浅绿色区域）展示出来。

![](img/03ee92079a228f561d65ed21df5170c9_53.png)

![](img/03ee92079a228f561d65ed21df5170c9_55.png)

![](img/03ee92079a228f561d65ed21df5170c9_57.png)

![](img/03ee92079a228f561d65ed21df5170c9_35.png)

![](img/03ee92079a228f561d65ed21df5170c9_59.png)

![](img/03ee92079a228f561d65ed21df5170c9_61.png)

![](img/03ee92079a228f561d65ed21df5170c9_63.png)

## 季节性成分分析

![](img/03ee92079a228f561d65ed21df5170c9_65.png)

![](img/03ee92079a228f561d65ed21df5170c9_67.png)

Prophet的一个强大功能是可以自动分解并可视化时间序列中的季节性成分。

使用`model.plot_components(forecast)`函数，可以绘制出趋势和季节性图表。

![](img/03ee92079a228f561d65ed21df5170c9_69.png)

![](img/03ee92079a228f561d65ed21df5170c9_71.png)

![](img/03ee92079a228f561d65ed21df5170c9_73.png)

![](img/03ee92079a228f561d65ed21df5170c9_39.png)

![](img/03ee92079a228f561d65ed21df5170c9_75.png)

上图展示了三个子图：
1.  **整体趋势**：显示了从2015年到2018年数据的整体上升趋势。
2.  **年度季节性**：显示了数据在一年中不同月份的变化模式。例如，图中可能显示12月和1月有最大的正向波动，而7月、9月和10月则呈现下降趋势。
3.  **月度（周内）模式**：显示了数据在一个月内不同日期的典型变化。例如，图表可能揭示出每月月底（如26号至30号）价格容易出现下降趋势。

![](img/03ee92079a228f561d65ed21df5170c9_77.png)

![](img/03ee92079a228f561d65ed21df5170c9_79.png)

如果想分析周度季节性，可以在创建模型时将`weekly_seasonality`参数设为`True`，然后重新训练和绘图，即可看到以周为周期的变化模式。

![](img/03ee92079a228f561d65ed21df5170c9_81.png)

![](img/03ee92079a228f561d65ed21df5170c9_83.png)

## 突变点检测

![](img/03ee92079a228f561d65ed21df5170c9_85.png)

![](img/03ee92079a228f561d65ed21df5170c9_87.png)

![](img/03ee92079a228f561d65ed21df5170c9_89.png)

突变点是指时间序列趋势发生显著变化的点，例如从上升转为下降，或增长速率突然加快/减慢。

![](img/03ee92079a228f561d65ed21df5170c9_91.png)

![](img/03ee92079a228f561d65ed21df5170c9_93.png)

Prophet可以自动检测这些突变点。

![](img/03ee92079a228f561d65ed21df5170c9_95.png)

```python
# 获取模型检测到的所有突变点
changepoints = model.changepoints
# 计算突变点处趋势的变化方向（通过二阶导数判断）
trend_deltas = model.params['delta'].mean(0)
# 区分正向和负向突变
positive_cps = changepoints[trend_deltas > 0]
negative_cps = changepoints[trend_deltas < 0]
```

![](img/03ee92079a228f561d65ed21df5170c9_97.png)

检测到突变点后，我们可以在原始序列图上将其标记出来。通常用绿色竖线标记趋势加速上升的点（二阶导大于0），用红色竖线标记趋势加速下降的点（二阶导小于0）。

![](img/03ee92079a228f561d65ed21df5170c9_99.png)

![](img/03ee92079a228f561d65ed21df5170c9_101.png)

![](img/03ee92079a228f561d65ed21df5170c9_103.png)

通过分析突变点，我们可以更好地理解数据在哪些时刻发生了结构性变化。

## 未来预测

![](img/03ee92079a228f561d65ed21df5170c9_103.png)

![](img/03ee92079a228f561d65ed21df5170c9_105.png)

最后，我们来看如何用训练好的模型预测未来。

![](img/03ee92079a228f561d65ed21df5170c9_107.png)

关键步骤是将`make_future_dataframe`函数中的`periods`参数设置为一个正数，例如180，表示预测未来180天。

![](img/03ee92079a228f561d65ed21df5170c9_109.png)

![](img/03ee92079a228f561d65ed21df5170c9_111.png)

```python
# 创建包含未来180天日期的数据框架
future = model.make_future_dataframe(periods=180)
# 进行预测
forecast = model.predict(future)
# 绘图（历史数据+未来预测）
fig = model.plot(forecast)
```

![](img/03ee92079a228f561d65ed21df5170c9_115.png)

![](img/03ee92079a228f561d65ed21df5170c9_113.png)

![](img/03ee92079a228f561d65ed21df5170c9_115.png)

在结果图中，黑色观测点只到当前日期（例如2018年1月26日），而绿色的预测线会向后延伸180天，显示出模型对未来趋势的判断。预测线周围的浅绿色区域代表了未来预测的不确定性范围。

预测的保守或激进程度，很大程度上受到`changepoint_prior_scale`参数的影响。调高此值会使模型更贴合历史数据的波动，预测未来时可能更“奔放”；调低此值则使模型趋势更平滑，预测更“保守”。用户需要根据实际情况进行调整。

![](img/03ee92079a228f561d65ed21df5170c9_117.png)

## 总结

![](img/03ee92079a228f561d65ed21df5170c9_119.png)

![](img/03ee92079a228f561d65ed21df5170c9_121.png)

![](img/03ee92079a228f561d65ed21df5170c9_123.png)

本节课我们一起学习了Facebook Prophet库在时间序列预测中的应用。我们掌握了：
1.  **模型构建**：如何实例化Prophet模型并设置关键参数。
2.  **训练与拟合**：如何使用历史数据训练模型，并评估其在历史数据上的拟合效果。
3.  **季节性分析**：如何利用Prophet分解并可视化数据的年度、月度等季节性模式。
4.  **突变点检测**：如何识别时间序列中趋势发生显著变化的关键点。
5.  **未来预测**：如何扩展时间范围，对未来的值进行预测，并理解预测结果的不确定性。

![](img/03ee92079a228f561d65ed21df5170c9_125.png)

![](img/03ee92079a228f561d65ed21df5170c9_127.png)

Prophet作为一个功能强大且易于使用的工具，为时间序列分析提供了强大的支持，特别适用于具有明显季节性和趋势的数据。