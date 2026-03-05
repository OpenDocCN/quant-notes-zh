# Python金融分析与量化交易实战：P4：fbprophet时间序列预测实例 📈

![](img/e780af65656bbe210c4fa685fae831c6_1.png)

在本节课中，我们将学习如何使用Facebook的Prophet库进行时间序列预测。我们将通过一个实例，从数据准备、模型构建、训练到可视化，完整地走一遍流程，并理解其中的核心概念。

![](img/e780af65656bbe210c4fa685fae831c6_3.png)

![](img/e780af65656bbe210c4fa685fae831c6_5.png)

## 模型构建与初步预测

![](img/e780af65656bbe210c4fa685fae831c6_7.png)

![](img/e780af65656bbe210c4fa685fae831c6_9.png)

上一节我们介绍了时间序列分析的基础，本节中我们来看看如何使用Prophet库构建一个预测模型。

![](img/e780af65656bbe210c4fa685fae831c6_11.png)

![](img/e780af65656bbe210c4fa685fae831c6_13.png)

执行`create_model`函数后，它会帮助我们建立模型并进行预测。下图展示了预测结果：

![](img/e780af65656bbe210c4fa685fae831c6_15.png)

![](img/e780af65656bbe210c4fa685fae831c6_1.png)

![](img/e780af65656bbe210c4fa685fae831c6_17.png)

![](img/e780af65656bbe210c4fa685fae831c6_19.png)

图中的黑色点代表观测值，绿色点代表模型预测值，浅绿色区域是置信区间。这样就构建出了一个基本的时间序列预测图。

![](img/e780af65656bbe210c4fa685fae831c6_21.png)

![](img/e780af65656bbe210c4fa685fae831c6_3.png)

模型构建完成后，我们可以利用Prophet框架自带的函数来展示模型发现的规律。

![](img/e780af65656bbe210c4fa685fae831c6_23.png)

![](img/e780af65656bbe210c4fa685fae831c6_5.png)

![](img/e780af65656bbe210c4fa685fae831c6_25.png)

以下是Prophet框架中自带的绘图函数，可以展示不同时间维度的趋势：

![](img/e780af65656bbe210c4fa685fae831c6_27.png)

![](img/e780af65656bbe210c4fa685fae831c6_29.png)

*   **年度趋势**：展示数据在年份尺度上的变化。
*   **月度趋势**：展示数据在一个月内的周期性变化。
*   **年度内月度趋势**：展示一年中不同月份的平均趋势。

![](img/e780af65656bbe210c4fa685fae831c6_31.png)

![](img/e780af65656bbe210c4fa685fae831c6_15.png)

![](img/e780af65656bbe210c4fa685fae831c6_33.png)

![](img/e780af65656bbe210c4fa685fae831c6_35.png)

现在，让我们深入看一下建模的具体步骤。

![](img/e780af65656bbe210c4fa685fae831c6_37.png)

## 深入模型构建过程

![](img/e780af65656bbe210c4fa685fae831c6_39.png)

上一节我们看到了预测结果，本节中我们来深入模型内部，看看它是如何工作的。

我们回到代码中`create_model`的部分。第一步是重载绘图工具。

![](img/e780af65656bbe210c4fa685fae831c6_41.png)

![](img/e780af65656bbe210c4fa685fae831c6_21.png)

然后，`model = cf.create_model()`会实例化一个Prophet模型。在实例化时，可以指定一系列参数。

以下是模型初始化时可以设置的关键参数：

*   `yearly_seasonality`：是否绘制年度季节性变化，可设为`True`或`False`。
*   `weekly_seasonality`：是否绘制周度季节性变化。
*   `changepoint_prior_scale`：这是一个关键参数，默认值为`0.05`，它控制着模型对趋势变化的敏感度。
*   `seasonality_prior_scale`：控制季节性成分的强度。
*   `holidays`：可以传入一个包含节假日信息的`DataFrame`。

![](img/e780af65656bbe210c4fa685fae831c6_35.png)

![](img/e780af65656bbe210c4fa685fae831c6_43.png)

在实例化模型后，代码中还添加了一个以30.5天为周期的季节性成分。这样，模型就能捕捉到大约每月一次的变化趋势。

![](img/e780af65656bbe210c4fa685fae831c6_39.png)

模型构建完成后，下一步是使用历史数据进行训练。这里，`self.training_years`指定了使用最近多少年的数据进行训练，例如设置为3，则使用最近3年的数据。

![](img/e780af65656bbe210c4fa685fae831c6_45.png)

![](img/e780af65656bbe210c4fa685fae831c6_41.png)

![](img/e780af65656bbe210c4fa685fae831c6_47.png)

![](img/e780af65656bbe210c4fa685fae831c6_49.png)

训练通过`model.fit()`完成。之后，使用`model.make_future_dataframe()`生成用于预测的时间框架。当参数`periods=0`时，表示不预测未来新的时间点，只对历史数据进行拟合和“预测”，这常用于模型验证。

![](img/e780af65656bbe210c4fa685fae831c6_51.png)

执行预测后，`forecast`对象会包含几个重要的列：

![](img/e780af65656bbe210c4fa685fae831c6_53.png)

*   `yhat`：预测值。
*   `yhat_lower`：预测区间的下限。
*   `yhat_upper`：预测区间的上限。

![](img/e780af65656bbe210c4fa685fae831c6_43.png)

最后是绘图部分。代码会绘制观测值（历史数据）、预测值以及置信区间。

![](img/e780af65656bbe210c4fa685fae831c6_55.png)

![](img/e780af65656bbe210c4fa685fae831c6_45.png)

![](img/e780af65656bbe210c4fa685fae831c6_57.png)

![](img/e780af65656bbe210c4fa685fae831c6_59.png)

至此，一个基础的预测模型就构建并可视化完成了。整个过程的核心代码如下：

![](img/e780af65656bbe210c4fa685fae831c6_61.png)

```python
# 1. 导入库并初始化模型
from prophet import Prophet
model = Prophet(yearly_seasonality=True, changepoint_prior_scale=0.05)

![](img/e780af65656bbe210c4fa685fae831c6_63.png)

![](img/e780af65656bbe210c4fa685fae831c6_65.png)

# 2. 准备历史数据 (df 需要包含 ‘ds’ 和 ‘y’ 两列)
# df = ...

![](img/e780af65656bbe210c4fa685fae831c6_67.png)

![](img/e780af65656bbe210c4fa685fae831c6_69.png)

# 3. 拟合模型
model.fit(df)

![](img/e780af65656bbe210c4fa685fae831c6_71.png)

# 4. 构建未来时间框架 (这里 periods=0 表示不预测未来)
future = model.make_future_dataframe(periods=0)

![](img/e780af65656bbe210c4fa685fae831c6_73.png)

![](img/e780af65656bbe210c4fa685fae831c6_75.png)

# 5. 进行预测
forecast = model.predict(future)

![](img/e780af65656bbe210c4fa685fae831c6_77.png)

![](img/e780af65656bbe210c4fa685fae831c6_79.png)

# 6. 绘制结果
fig1 = model.plot(forecast)
```

![](img/e780af65656bbe210c4fa685fae831c6_81.png)

## 季节性分解与趋势分析

上一节我们完成了基础建模，本节中我们利用Prophet的`plot_components`功能来分解时间序列，观察其内在规律。

调用`model.plot_components(forecast)`函数，可以绘制出分解后的趋势图。

![](img/e780af65656bbe210c4fa685fae831c6_83.png)

![](img/e780af65656bbe210c4fa685fae831c6_85.png)

![](img/e780af65656bbe210c4fa685fae831c6_53.png)

![](img/e780af65656bbe210c4fa685fae831c6_87.png)

![](img/e780af65656bbe210c4fa685fae831c6_89.png)

从分解图中我们可以观察到以下规律：

*   **整体趋势**：从2015年到2018年，价格呈现上升趋势。
*   **月度模式**：在每个月内，月初价格有上升趋势，月末则有下降趋势。
*   **年度模式**：在一年之中，12月和1月的价格上涨幅度最大，而7月、9月和10月则呈现下降趋势。

![](img/e780af65656bbe210c4fa685fae831c6_91.png)

![](img/e780af65656bbe210c4fa685fae831c6_93.png)

![](img/e780af65656bbe210c4fa685fae831c6_63.png)

![](img/e780af65656bbe210c4fa685fae831c6_95.png)

这些分析为我们提供了交易思路：例如，在月底价格通常下降时，或许不是买入股票的好时机。

![](img/e780af65656bbe210c4fa685fae831c6_97.png)

![](img/e780af65656bbe210c4fa685fae831c6_99.png)

我们也可以修改参数来观察其他季节性成分，例如将`weekly_seasonality`设置为`True`，重新建模后即可看到以周为单位的趋势图。

![](img/e780af65656bbe210c4fa685fae831c6_101.png)

![](img/e780af65656bbe210c4fa685fae831c6_103.png)

![](img/e780af65656bbe210c6_79.png)

![](img/e780af65656bbe210c4fa685fae831c6_105.png)

## 识别突变点

![](img/e780af65656bbe210c4fa685fae831c6_107.png)

![](img/e780af65656bbe210c4fa685fae831c6_109.png)

上一节我们分析了季节性，本节中我们来看看如何识别时间序列中的“突变点”。

![](img/e780af65656bbe210c4fa685fae831c6_111.png)

突变点是指时间序列趋势发生显著变化的点，例如从上升转为下降，或变化速率突然加快/减慢。在Prophet中，这被称为`changepoint`。

![](img/e780af65656bbe210c4fa685fae831c6_113.png)

以下代码演示了如何提取和可视化突变点：

```python
# 从拟合好的模型中获取突变点
changepoints = model.changepoints

![](img/e780af65656bbe210c4fa685fae831c6_115.png)

# 计算突变点处的趋势变化（例如通过二阶导数的符号）
# positive 表示趋势加速向上，negative 表示趋势加速向下或转为向下
```

![](img/e780af65656bbe210c4fa685fae831c6_117.png)

![](img/e780af65656bbe210c4fa685fae831c6_119.png)

![](img/e780af65656bbe210c6_107.png)

在可视化时，可以用不同的颜色（如绿色代表正向突变，红色代表负向突变）将突变点标记在原始序列图上。这有助于我们理解序列在哪些关键时间点发生了结构性变化。

## 进行未来预测

前面我们都在分析历史数据，本节中我们来看看如何用模型预测未来。

![](img/e780af65656bbe210c4fa685fae831c6_121.png)

预测未来的关键步骤是在`make_future_dataframe`函数中指定`periods`参数，例如`periods=180`表示预测未来180天。

![](img/e780af65656bbe210c4fa685fae831c6_123.png)

```python
# 构建包含未来180天的时间框架
future = model.make_future_dataframe(periods=180)

![](img/e780af65656bbe210c4fa685fae831c6_125.png)

# 进行预测
forecast = model.predict(future)

![](img/e780af65656bbe210c4fa685fae831c6_127.png)

# 绘图，预测部分将延伸至未来时间点
fig = model.plot(forecast)
```

![](img/e780af65656bbe210c4fa685fae831c6_129.png)

![](img/e780af65656bbe210c6_131.png)

在预测结果图中，黑色点依然是历史观测值，绿色线是模型拟合及预测值。可以看到，模型基于历史规律，对未来趋势做出了延伸。预测的保守或激进程度，很大程度上受到`changepoint_prior_scale`参数的影响。

## 调整模型敏感度

![](img/e780af65656bbe210c4fa685fae831c6_131.png)

![](img/e780af65656bbe210c4fa685fae831c6_133.png)

上一节我们看到了预测结果，本节中我们探讨如何通过关键参数调整预测行为。

![](img/e780af65656bbe210c4fa685fae831c6_135.png)

`changepoint_prior_scale`参数是控制模型对趋势变化敏感度的核心。它的值越大，模型越倾向于认为历史数据中存在更多的突变点，从而在预测未来时更“灵活”或更“激进”，可能产生波动更大的预测线。反之，值越小，模型越保守，认为趋势越平滑稳定。

![](img/e780af65656bbe210c6_139.png)

在实践中，需要通过交叉验证等方法来调整这个参数，以在拟合优度和预测稳定性之间取得平衡。

![](img/e780af65656bbe210c4fa685fae831c6_137.png)

![](img/e780af65656bbe210c4fa685fae831c6_139.png)

## 课程总结

![](img/e780af65656bbe210c4fa685fae831c6_141.png)

本节课中我们一起学习了使用Facebook Prophet库进行时间序列预测的完整流程。

![](img/e780af65656bbe210c4fa685fae831c6_143.png)

![](img/e780af65656bbe210c4fa685fae831c6_145.png)

我们首先构建了一个基础模型，并对历史数据进行了拟合和可视化。接着，我们利用`plot_components`函数分解了时间序列，分析了其年度、月度等季节性规律。然后，我们学习了如何识别并可视化序列中的突变点。最后，我们扩展模型对未来进行了预测，并了解了通过`changepoint_prior_scale`参数调整模型预测风格的方法。

![](img/e780af65656bbe210c4fa685fae831c6_147.png)

Prophet作为一个强大的时间序列预测工具，封装了许多复杂步骤，让使用者能够通过相对简单的接口完成从建模到预测的全过程，是金融数据分析与量化交易中的一个实用利器。