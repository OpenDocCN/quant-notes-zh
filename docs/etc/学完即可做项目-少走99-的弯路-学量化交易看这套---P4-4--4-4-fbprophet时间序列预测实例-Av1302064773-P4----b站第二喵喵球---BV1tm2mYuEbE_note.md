# 量化交易：4.4：fbprophet时间序列预测实例 📈

![](img/4cd3a9bf1a1d75ef41c5b9f6bf643826_1.png)

![](img/4cd3a9bf1a1d75ef41c5b9f6bf643826_3.png)

![](img/4cd3a9bf1a1d75ef41c5b9f6bf643826_5.png)

![](img/4cd3a9bf1a1d75ef41c5b9f6bf643826_7.png)

![](img/4cd3a9bf1a1d75ef41c5b9f6bf643826_9.png)

![](img/4cd3a9bf1a1d75ef41c5b9f6bf643826_11.png)

![](img/4cd3a9bf1a1d75ef41c5b9f6bf643826_13.png)

在本节课中，我们将学习如何使用Facebook的Prophet库进行时间序列预测。我们将通过一个实例，了解如何构建模型、训练数据、可视化结果，并理解关键参数如`changepoint_prior_scale`的作用。

![](img/4cd3a9bf1a1d75ef41c5b9f6bf643826_15.png)

![](img/4cd3a9bf1a1d75ef41c5b9f6bf643826_17.png)

![](img/4cd3a9bf1a1d75ef41c5b9f6bf643826_19.png)

![](img/4cd3a9bf1a1d75ef41c5b9f6bf643826_21.png)

## 模型构建与训练

![](img/4cd3a9bf1a1d75ef41c5b9f6bf643826_23.png)

![](img/4cd3a9bf1a1d75ef41c5b9f6bf643826_25.png)

上一节我们介绍了时间序列的基本概念，本节中我们来看看如何使用Prophet库构建一个预测模型。

![](img/4cd3a9bf1a1d75ef41c5b9f6bf643826_27.png)

![](img/4cd3a9bf1a1d75ef41c5b9f6bf643826_29.png)

![](img/4cd3a9bf1a1d75ef41c5b9f6bf643826_31.png)

![](img/4cd3a9bf1a1d75ef41c5b9f6bf643826_33.png)

首先，创建一个Prophet模型实例。核心代码是：
```python
model = Prophet()
```
模型实例化时，可以指定一系列参数来控制其行为，例如是否绘制季节性变化图。一个关键参数是`changepoint_prior_scale`，它控制模型对趋势变化的灵活度，默认值为0.05。

![](img/4cd3a9bf1a1d75ef41c5b9f6bf643826_35.png)

![](img/4cd3a9bf1a1d75ef41c5b9f6bf643826_37.png)

![](img/4cd3a9bf1a1d75ef41c5b9f6bf643826_39.png)

以下是模型训练的基本步骤：
1.  **准备数据**：数据需要包含两列：`ds`（日期时间戳）和`y`（观测值）。
2.  **拟合模型**：使用历史数据对模型进行训练。例如，可以指定仅使用最近3年的数据进行训练。
    ```python
    model.fit(df_history)
    ```
3.  **生成预测框架**：使用`make_future_dataframe`方法创建包含未来日期的数据框架。参数`periods`指定要预测的未来天数。如果`periods=0`，则只对历史数据进行拟合和“预测”（即模型在历史数据上的表现）。

![](img/4cd3a9bf1a1d75ef41c5b9f6bf643826_41.png)

![](img/4cd3a9bf1a1d75ef41c5b9f6bf643826_43.png)

## 结果可视化

模型训练并预测后，我们可以将结果可视化，直观地比较观测值与预测值。

![](img/4cd3a9bf1a1d75ef41c5b9f6bf643826_45.png)

![](img/4cd3a9bf1a1d75ef41c5b9f6bf643826_47.png)

执行预测后，会得到一个包含多列的数据框，其中重要的列包括：
*   `yhat`：预测值。
*   `yhat_lower` 和 `yhat_upper`：预测值的置信区间下限和上限。

![](img/4cd3a9bf1a1d75ef41c5b9f6bf643826_49.png)

![](img/4cd3a9bf1a1d75ef41c5b9f6bf643826_51.png)

![](img/4cd3a9bf1a1d75ef41c5b9f6bf643826_53.png)

以下是绘制结果图的步骤：
1.  绘制观测值（历史数据）作为黑色散点。
2.  绘制预测值（`yhat`）作为绿色线条。
3.  可选地，使用浅绿色区域填充置信区间（`yhat_lower` 到 `yhat_upper`），以表示预测的不确定性范围。

![](img/4cd3a9bf1a1d75ef41c5b9f6bf643826_55.png)

![](img/4cd3a9bf1a1d75ef41c5b9f6bf643826_57.png)

最终得到的图表中，黑色点代表真实观测值，绿色线代表模型的预测值，浅绿色区域代表预测的置信区间。

![](img/4cd3a9bf1a1d75ef41c5b9f6bf643826_59.png)

![](img/4cd3a9bf1a1d75ef41c5b9f6bf643826_61.png)

## 季节性分解分析

![](img/4cd3a9bf1a1d75ef41c5b9f6bf643826_63.png)

![](img/4cd3a9bf1a1d75ef41c5b9f6bf643826_65.png)

![](img/4cd3a9bf1a1d75ef41c5b9f6bf643826_67.png)

![](img/4cd3a9bf1a1d75ef41c5b9f6bf643826_69.png)

![](img/4cd3a9bf1a1d75ef41c5b9f6bf643826_71.png)

Prophet框架内置了`plot_components`函数，可以自动分解并展示时间序列中的不同成分。

![](img/4cd3a9bf1a1d75ef41c5b9f6bf643826_73.png)

![](img/4cd3a9bf1a1d75ef41c5b9f6bf643826_75.png)

![](img/4cd3a9bf1a1d75ef41c5b9f6bf643826_77.png)

![](img/4cd3a9bf1a1d75ef41c5b9f6bf643826_79.png)

该函数可以生成以下趋势图：
*   **整体趋势**：展示整个时间范围内序列的变化趋势。
*   **年度趋势**：展示数据在一年中不同月份或季节的典型模式。
*   **月度趋势**：展示数据在一个月内不同日期的典型模式。
*   **周度趋势**（如果启用）：展示数据在一周内不同星期几的典型模式。

![](img/4cd3a9bf1a1d75ef41c5b9f6bf643826_81.png)

![](img/4cd3a9bf1a1d75ef41c5b9f6bf643826_83.png)

![](img/4cd3a9bf1a1d75ef41c5b9f6bf643826_85.png)

通过观察这些成分图，我们可以发现一些规律。例如，在示例数据中，整体趋势是向上的；在一年中，12月和1月可能上涨幅度最大，而7月、9月和10月可能呈现下降趋势；在每月内，月底可能出现下降趋势。

![](img/4cd3a9bf1a1d75ef41c5b9f6bf643826_87.png)

![](img/4cd3a9bf1a1d75ef41c5b9f6bf643826_89.png)

![](img/4cd3a9bf1a1d75ef41c5b9f6bf643826_91.png)

![](img/4cd3a9bf1a1d75ef41c5b9f6bf643826_93.png)

## 突变点检测

![](img/4cd3a9bf1a1d75ef41c5b9f6bf643826_95.png)

![](img/4cd3a9bf1a1d75ef41c5b9f6bf643826_97.png)

时间序列中的“突变点”是指趋势发生显著变化的点，例如从上升转为下降，或变化速率突然加快/减慢。

![](img/4cd3a9bf1a1d75ef41c5b9f6bf643826_99.png)

![](img/4cd3a9bf1a1d75ef41c5b9f6bf643826_101.png)

![](img/4cd3a9bf1a1d75ef41c5b9f6bf643826_103.png)

![](img/4cd3a9bf1a1d75ef41c5b9f6bf643826_105.png)

![](img/4cd3a9bf1a1d75ef41c5b9f6bf643826_107.png)

![](img/4cd3a9bf1a1d75ef41c5b9f6bf643826_109.png)

Prophet模型可以自动检测这些突变点（`changepoints`）。检测原理基于观察序列的二阶导数（变化率的变化率）的正负情况。
*   二阶导数由负转正（或正值显著增大）的点，可视为正向突变点（趋势加速向上或由降转升）。
*   二阶导数由正转负（或负值显著减小）的点，可视为负向突变点（趋势加速向下或由升转降）。

![](img/4cd3a9bf1a1d75ef41c5b9f6bf643826_111.png)

![](img/4cd3a9bf1a1d75ef41c5b9f6bf643826_113.png)

![](img/4cd3a9bf1a1d75ef41c5b9f6bf643826_115.png)

![](img/4cd3a9bf1a1d75ef41c5b9f6bf643826_117.png)

在代码中，我们可以从训练好的模型中获取突变点列表，并根据其对应的二阶导数值将其分类。通常，可以用不同颜色的竖线在趋势图上标出这些突变点，例如绿色线表示正向突变，红色线表示负向突变。

![](img/4cd3a9bf1a1d75ef41c5b9f6bf643826_119.png)

![](img/4cd3a9bf1a1d75ef41c5b9f6bf643826_121.png)

## 未来预测

![](img/4cd3a9bf1a1d75ef41c5b9f6bf643826_123.png)

![](img/4cd3a9bf1a1d75ef41c5b9f6bf643826_125.png)

构建模型的核心目的之一是预测未来。通过调整`make_future_dataframe`函数中的`periods`参数（例如设为180），我们可以让模型生成未来180天的日期框架，并基于已训练好的模型对这些未来日期进行预测。

![](img/4cd3a9bf1a1d75ef41c5b9f6bf643826_127.png)

预测结果的绘图方式与之前类似，但未来时间段只有绿色的预测线及置信区间，而没有黑色的观测值点。预测线会基于历史趋势和季节性模式进行外推。

![](img/4cd3a9bf1a1d75ef41c5b9f6bf643826_129.png)

![](img/4cd3a9bf1a1d75ef41c5b9f6bf643826_131.png)

![](img/4cd3a9bf1a1d75ef41c5b9f6bf643826_133.png)

需要注意的是，预测的“激进”或“保守”程度可以通过`changepoint_prior_scale`参数来调整。该值越大，模型越倾向于认为趋势会发生更多、更大的变化，预测曲线可能更灵活、波动更大；该值越小，模型越倾向于认为趋势是平缓稳定的，预测曲线会更平滑。

![](img/4cd3a9bf1a1d75ef41c5b9f6bf643826_135.png)

![](img/4cd3a9bf1a1d75ef41c5b9f6bf643826_137.png)

## 参数调整与探索

![](img/4cd3a9bf1a1d75ef41c5b9f6bf643826_139.png)

![](img/4cd3a9bf1a1d75ef41c5b9f6bf643826_141.png)

![](img/4cd3a9bf1a1d75ef41c5b9f6bf643826_143.png)

Prophet模型提供了许多参数供用户调整，以更好地拟合特定数据。除了核心的`changepoint_prior_scale`，还包括：
*   `seasonality_mode`：季节性模式（‘additive’或‘multiplicative’）。
*   `seasonality_prior_scale`：控制季节性成分的强度。
*   `holidays`：可以传入一个节假日数据框，让模型考虑节假日效应。
*   `weekly_seasonality` / `yearly_seasonality`：是否启用周度或年度季节性成分。

![](img/4cd3a9bf1a1d75ef41c5b9f6bf643826_145.png)

![](img/4cd3a9bf1a1d75ef41c5b9f6bf643826_147.png)

用户可以通过阅读Prophet的官方文档或直接查看其源代码来深入理解每个参数的含义。在集成开发环境（如PyCharm或Eclipse）中，通常可以通过Ctrl+点击函数或类名跳转到源码进行查看。

![](img/4cd3a9bf1a1d75ef41c5b9f6bf643826_149.png)

![](img/4cd3a9bf1a1d75ef41c5b9f6bf643826_151.png)

![](img/4cd3a9bf1a1d75ef41c5b9f6bf643826_153.png)

![](img/4cd3a9bf1a1d75ef41c5b9f6bf643826_155.png)

本节课中我们一起学习了使用Facebook Prophet库进行时间序列预测的全流程。我们从模型构建、训练、结果可视化开始，进而分析了序列的季节性成分和突变点，最后实现了对未来值的预测。理解并调整`changepoint_prior_scale`等关键参数，是使预测模型更贴合实际数据趋势的重要环节。这套工具为量化交易中的价格预测提供了强大且易用的解决方案。