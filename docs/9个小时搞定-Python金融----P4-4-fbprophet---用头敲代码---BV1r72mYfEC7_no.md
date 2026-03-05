# Python金融量化分析：P4：fbprophet时间序列预测实例 📈

![](img/cf3d00ae340bc6157932b06f7542e6ae_1.png)

![](img/cf3d00ae340bc6157932b06f7542e6ae_3.png)

在本节课中，我们将学习如何使用Facebook的Prophet库进行时间序列预测。我们将通过一个实例，从数据准备、模型构建、训练、可视化到未来预测，完整地走一遍流程。课程内容力求简单直白，确保初学者能够理解并实践。

![](img/cf3d00ae340bc6157932b06f7542e6ae_5.png)

![](img/cf3d00ae340bc6157932b06f7542e6ae_7.png)

![](img/cf3d00ae340bc6157932b06f7542e6ae_9.png)

## 概述

![](img/cf3d00ae340bc6157932b06f7542e6ae_11.png)

![](img/cf3d00ae340bc6157932b06f7542e6ae_13.png)

Prophet是Facebook开源的一个时间序列预测工具，它非常适合具有季节性特征的数据。本节我们将使用Prophet对一个股票价格数据集进行建模，分析其趋势和季节性，并预测未来的价格走势。

![](img/cf3d00ae340bc6157932b06f7542e6ae_15.png)

![](img/cf3d00ae340bc6157932b06f7542e6ae_17.png)

---

![](img/cf3d00ae340bc6157932b06f7542e6ae_19.png)

![](img/cf3d00ae340bc6157932b06f7542e6ae_21.png)

## 模型构建与初步可视化

上一节我们介绍了数据准备，本节中我们来看看如何构建Prophet模型并进行初步的可视化。

![](img/cf3d00ae340bc6157932b06f7542e6ae_23.png)

![](img/cf3d00ae340bc6157932b06f7542e6ae_25.png)

执行`create_model`函数后，Prophet会帮我们完成建模和预测。下图展示了建模后的结果：

![](img/cf3d00ae340bc6157932b06f7542e6ae_27.png)

![](img/cf3d00ae340bc6157932b06f7542e6ae_1.png)

![](img/cf3d00ae340bc6157932b06f7542e6ae_29.png)

![](img/cf3d00ae340bc6157932b06f7542e6ae_31.png)

图中的黑色点代表观测值（即真实历史数据），绿色点代表模型拟合出的预测值。浅绿色的区域是置信区间，它表示了预测值可能的波动范围。这样，一个基本的时间序列模型就构建完成了。

![](img/cf3d00ae340bc6157932b06f7542e6ae_33.png)

![](img/cf3d00ae340bc6157932b06f7542e6ae_35.png)

模型构建完成后，我们可以使用Prophet框架自带的`plot_components`函数来深入观察模型发现的规律。

![](img/cf3d00ae340bc6157932b06f7542e6ae_37.png)

![](img/cf3d00ae340bc6157932b06f7542e6ae_3.png)

![](img/cf3d00ae340bc6157932b06f7542e6ae_39.png)

这个函数不是我们自己写的，而是Prophet库内置的。它能直观展示模型的各个组成部分：
*   **整体趋势**：展示数据在多年跨度上的变化方向。
*   **月度趋势**：展示数据在一个月周期内的变化规律。
*   **年度趋势**：展示数据在一年中不同月份的表现。

这些图表能帮助我们理解数据背后的周期性模式。

![](img/cf3d00ae340bc6157932b06f7542e6ae_41.png)

---

## 深入模型内部：参数与训练过程

了解了可视化结果后，我们深入代码，看看模型是如何构建和训练的。

我们回到代码中，通过`F5`（或IDE的跳转功能）进入`create_model`函数。第一步是重载绘图工具。然后，模型通过`cf.create_model`进行实例化。

![](img/cf3d00ae340bc6157932b06f7542e6ae_43.png)

在实例化时，我们可以指定一系列参数。这些参数决定了模型的行为：

![](img/cf3d00ae340bc6157932b06f7542e6ae_45.png)

以下是创建Prophet模型时可以指定的部分关键参数：
```python
model = Prophet(
    yearly_seasonality=True,   # 是否考虑年度季节性
    weekly_seasonality=False,  # 是否考虑周度季节性（初始设为False）
    daily_seasonality=False,   # 是否考虑日度季节性
    changepoint_prior_scale=0.05, # 趋势变化灵活度，核心参数
    holidays=holidays_df        # 可以传入节假日数据框
)
```
*   `yearly_seasonality`, `weekly_seasonality`, `daily_seasonality`：控制是否绘制相应的季节性变化图。
*   `changepoint_prior_scale`：这是一个关键参数，默认值为0.05。它控制了模型趋势变化的灵活度，值越大模型对趋势变化越敏感，预测可能更“奔放”；值越小则模型更“保守”，趋势更平滑。
*   `holidays`：可以传入一个包含节假日的DataFrame，让模型考虑节假日效应。

![](img/cf3d00ae340bc6157932b06f7542e6ae_47.png)

如果想深入了解每个参数的设计，可以按住`Ctrl`键（在大多数IDE中）点击`Prophet`进入其源码查看。由于源码内容较多，我们在此不展开，感兴趣的同学可以自行研究。

![](img/cf3d00ae340bc6157932b06f7542e6ae_49.png)

![](img/cf3d00ae340bc6157932b06f7542e6ae_51.png)

在我们的实例中，除了默认参数，还添加了一个以30.5天为周期的月度季节性成分。实例化后，模型对象被返回。

![](img/cf3d00ae340bc6157932b06f7542e6ae_53.png)

拿到模型后，接下来是训练。这里用到了`self.training`参数，它的含义是：我们只用最近N年的历史数据进行训练。例如，`self.training = 3`表示只用最近3年的数据来训练模型。

训练通过`model.fit()`方法完成。训练之后，我们使用`model.make_future_dataframe()`来生成用于预测的时间序列。参数`periods`指定了需要向前预测的天数。

![](img/cf3d00ae340bc6157932b06f7542e6ae_55.png)

**核心概念：`periods`参数**
*   当 `periods = 0` 时，`make_future_dataframe`生成的时间序列截止到历史数据的最后一天。这意味着我们只对历史数据进行“拟合”和“回测”，并不预测未来。
*   当 `periods = 90` 时，模型会在历史数据的基础上，再生成90天的未来日期，并对这些日期进行预测。

![](img/cf3d00ae340bc6157932b06f7542e6ae_57.png)

在本节演示中，为了先讲解模型构建，我们设置`periods=0`，即不进行未来预测，只查看模型对历史数据的拟合效果。

![](img/cf3d00ae340bc6157932b06f7542e6ae_59.png)

![](img/cf3d00ae340bc6157932b06f7542e6ae_61.png)

![](img/cf3d00ae340bc6157932b06f7542e6ae_63.png)

执行预测（`model.predict`）后，结果DataFrame中会自动添加几个关键列：
*   `yhat`：预测值。
*   `yhat_lower` 和 `yhat_upper`：预测值的置信区间下限和上限。

![](img/cf3d00ae340bc6157932b06f7542e6ae_65.png)

![](img/cf3d00ae340bc6157932b06f7542e6ae_67.png)

我们可以用`yhat`作为预测指标，用`yhat_lower`和`yhat_upper`来描绘置信区间。

![](img/cf3d00ae340bc6157932b06f7542e6ae_69.png)

---

![](img/cf3d00ae340bc6157932b06f7542e6ae_71.png)

![](img/cf3d00ae340bc6157932b06f7542e6ae_73.png)

![](img/cf3d00ae340bc6157932b06f7542e6ae_75.png)

## 结果解读与季节性分析

![](img/cf3d00ae340bc6157932b06f7542e6ae_77.png)

![](img/cf3d00ae340bc6157932b06f7542e6ae_79.png)

完成预测后，我们来解读结果并分析数据的季节性。

![](img/cf3d00ae340bc6157932b06f7542e6ae_81.png)

画图时，我们在同一张图上绘制了两条线：
*   **观测值**：使用历史数据中的日期`ds`和真实值`y`绘制，代表历史实际情况。
*   **预测值**：使用`make_future_dataframe`生成的日期`ds`和预测值`yhat`绘制，代表模型的拟合结果。

同时，我们使用`yhat_lower`和`yhat_upper`绘制了浅绿色的置信区间。最终得到的图表中，黑色点是真实观测值，绿色线是模型预测值，浅绿色区域是置信区间。

![](img/cf3d00ae340bc6157932b06f7542e6ae_83.png)

![](img/cf3d00ae340bc6157932b06f7542e6ae_85.png)

![](img/cf3d00ae340bc6157932b06f7542e6ae_47.png)

![](img/cf3d00ae340bc6157932b06f7542e6ae_87.png)

![](img/cf3d00ae340bc6157932b06f7542e6ae_89.png)

接下来，我们分析`plot_components`函数生成的季节性图表：

![](img/cf3d00ae340bc6157932b06f7542e6ae_91.png)

![](img/cf3d00ae340bc6157932b06f7542e6ae_53.png)

![](img/cf3d00ae340bc6157932b06f7542e6ae_93.png)

通过观察这些图表，我们可以总结出一些规律：
*   **整体趋势**：从2015年到2018年，价格整体呈上升趋势。
*   **月度趋势**：在每个月内，价格呈现月初上升、月末下降的规律。
*   **年度趋势**：在一年当中，12月和1月的价格上涨幅度最大，而7月、9月和10月则呈现下降趋势。

![](img/cf3d00ae340bc6157932b06f7542e6ae_95.png)

![](img/cf3d00ae340bc6157932b06f7542e6ae_97.png)

![](img/cf3d00ae340bc6157932b06f7542e6ae_99.png)

这些发现为我们提供了交易策略的思路：例如，可以考虑在月底价格较低时买入，在12月或1月初价格较高时卖出。

![](img/cf3d00ae340bc6157932b06f7542e6ae_101.png)

![](img/cf3d00ae340bc6157932b06f7542e6ae_103.png)

如果我们想观察以“周”为单位的季节性，只需在创建模型时将`weekly_seasonality`参数设为`True`，然后重新训练和绘图即可。

![](img/cf3d00ae340bc6157932b06f7542e6ae_105.png)

![](img/cf3d00ae340bc6157932b06f7542e6ae_107.png)

---

![](img/cf3d00ae340bc6157932b06f7542e6ae_109.png)

![](img/cf3d00ae340bc6157932b06f7542e6ae_111.png)

## 识别趋势突变点

![](img/cf3d00ae340bc6157932b06f7542e6ae_113.png)

除了季节性，Prophet还能自动识别时间序列中的“突变点”。

![](img/cf3d00ae340bc6157932b06f7542e6ae_115.png)

突变点是指时间序列趋势发生显著变化的点，例如：
*   由下降转为上升。
*   由缓慢上升转为快速上升。
*   由快速下降转为缓慢下降。

![](img/cf3d00ae340bc6157932b06f7542e6ae_117.png)

在Prophet中，这被称为`changepoint`。模型通过观察时间序列的二阶导数的正负变化来识别这些点。

![](img/cf3d00ae340bc6157932b06f7542e6ae_119.png)

![](img/cf3d00ae340bc6157932b06f7542e6ae_121.png)

以下是查找和可视化突变点的步骤：
1.  创建并训练模型（与之前步骤相同）。
2.  从训练好的模型中获取突变点列表：`changepoints = model.changepoints`。
3.  对于每个突变点，检查其趋势变化方向（通过二阶导数判断）。二阶导数大于零表示趋势加速向上（Positive），小于零表示趋势加速向下（Negative）。
4.  使用不同颜色的竖线在原始序列图上标出这些突变点。

最终，我们可以在图表上看到，绿色竖线标记了趋势加速向上的点，红色竖线标记了趋势加速向下的点。

![](img/cf3d00ae340bc6157932b06f7542e6ae_125.png)

![](img/cf3d00ae340bc6157932b06f7542e6ae_123.png)

---

![](img/cf3d00ae340bc6157932b06f7542e6ae_125.png)

![](img/cf3d00ae340bc6157932b06f7542e6ae_127.png)

## 进行未来预测

![](img/cf3d00ae340bc6157932b06f7542e6ae_129.png)

最后，我们使用模型对未来进行预测。这是大家最感兴趣的部分。

![](img/cf3d00ae340bc6157932b06f7542e6ae_131.png)

![](img/cf3d00ae340bc6157932b06f7542e6ae_133.png)

预测的步骤与之前完全一致，唯一的关键区别在于`make_future_dataframe`函数中的`periods`参数。现在，我们将其设置为一个正数，例如180，表示我们要预测未来180天的数据。

**核心代码：未来预测**
```python
# 创建模型
model = Prophet(yearly_seasonality=True, changepoint_prior_scale=0.05)
# 训练模型
model.fit(history_df)
# 生成包含未来180天的日期数据框
future = model.make_future_dataframe(periods=180)
# 进行预测
forecast = model.predict(future)
```
执行后，`forecast`数据框将包含历史日期和未来180天日期的预测值。绘图时，历史部分（有真实值的部分）仍然用黑点表示，而未来部分的预测值则用绿色线延伸出去。

![](img/cf3d00ae340bc6157932b06f7542e6ae_135.png)

![](img/cf3d00ae340bc6157932b06f7542e6ae_139.png)

![](img/cf3d00ae340bc6157932b06f7542e6ae_137.png)

![](img/cf3d00ae340bc6157932b06f7542e6ae_139.png)

从预测图中我们可以看到，模型基于历史趋势，对未来做出了延续性预测。预测线可能会根据历史规律出现上升或下降。预测的“保守”与“奔放”程度，很大程度上由之前提到的核心参数`changepoint_prior_scale`控制。通过调整这个参数，我们可以让模型对未来变化更敏感或更平滑。

---

## 总结

![](img/cf3d00ae340bc6157932b06f7542e6ae_141.png)

![](img/cf3d00ae340bc6157932b06f7542e6ae_143.png)

本节课中，我们一起学习了使用Facebook Prophet库进行时间序列预测的完整流程：

![](img/cf3d00ae340bc6157932b06f7542e6ae_145.png)

![](img/cf3d00ae340bc6157932b06f7542e6ae_147.png)

1.  **模型构建**：实例化Prophet模型，并理解关键参数（如季节性开关和核心参数`changepoint_prior_scale`）的作用。
2.  **模型训练**：使用历史数据对模型进行拟合。
3.  **结果可视化**：绘制观测值、预测值和置信区间，直观评估模型拟合效果。
4.  **季节性分析**：利用`plot_components`函数分解并分析数据的年度、月度等季节性规律。
5.  **突变点检测**：识别时间序列中趋势发生显著变化的点。
6.  **未来预测**：通过设置`periods`参数，让模型对未来时间段进行预测，并将结果可视化。

![](img/cf3d00ae340bc6157932b06f7542e6ae_149.png)

![](img/cf3d00ae340bc6157932b06f7542e6ae_151.png)

Prophet是一个功能强大且易于使用的时间序列预测工具，通过本节的实例，希望大家能够掌握其基本用法，并应用于自己的金融量化分析或其他时间序列预测任务中。