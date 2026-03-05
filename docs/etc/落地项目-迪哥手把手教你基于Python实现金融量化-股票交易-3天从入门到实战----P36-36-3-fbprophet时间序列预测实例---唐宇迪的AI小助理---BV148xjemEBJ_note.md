# Python金融分析与量化交易实战：P36：fbprophet时间序列预测实例 📈

![](img/948faf8dac703cd16151e26ac8120055_1.png)

![](img/948faf8dac703cd16151e26ac8120055_3.png)

在本节课中，我们将学习如何使用Facebook的Prophet库进行时间序列预测。我们将通过一个股票数据的实例，从模型构建、训练、可视化到预测，完整地走一遍流程，并理解其中的关键概念和参数。

![](img/948faf8dac703cd16151e26ac8120055_5.png)

![](img/948faf8dac703cd16151e26ac8120055_7.png)

![](img/948faf8dac703cd16151e26ac8120055_9.png)

## 概述

![](img/948faf8dac703cd16151e26ac8120055_11.png)

Prophet是Facebook开源的一个时间序列预测工具，它非常适合具有季节性特征的数据。本节我们将基于历史股票数据，使用Prophet构建预测模型，并分析其趋势和季节性规律。

![](img/948faf8dac703cd16151e26ac8120055_13.png)

![](img/948faf8dac703cd16151e26ac8120055_15.png)

![](img/948faf8dac703cd16151e26ac8120055_17.png)

---

## 模型构建与初步可视化 🖼️

![](img/948faf8dac703cd16151e26ac8120055_19.png)

![](img/948faf8dac703cd16151e26ac8120055_21.png)

上一节我们介绍了Prophet的基本概念，本节中我们来看看如何具体构建模型并可视化结果。

![](img/948faf8dac703cd16151e26ac8120055_23.png)

执行`create_model`函数后，Prophet会帮我们完成建模和预测。下图展示了预测结果：

![](img/948faf8dac703cd16151e26ac8120055_25.png)

![](img/948faf8dac703cd16151e26ac8120055_1.png)

![](img/948faf8dac703cd16151e26ac8120055_27.png)

![](img/948faf8dac703cd16151e26ac8120055_29.png)

图中，**黑色点**代表观测到的真实值，**绿色点**代表模型预测的值。**浅绿色区域**是置信区间，它表示了预测值可能的波动范围。这样，一个基本的时间序列预测图就构造出来了。

构造出模型后，我们还可以使用Prophet框架自带的函数来展示模型发现的规律。

![](img/948faf8dac703cd16151e26ac8120055_13.png)

![](img/948faf8dac703cd16151e26ac8120055_31.png)

该函数可以绘制出数据在不同时间尺度上的趋势，例如：
*   **年度趋势**：数据随年份变化的整体走势。
*   **月度趋势**：数据在一个月内的周期性变化。
*   **周度趋势**：数据在一周内的变化规律。

接下来，我们深入看一下建模的具体步骤。

## 深入代码：模型初始化与训练 🔧

我们回到代码中，查看`create_model`函数的内部逻辑。

```python
model = cf.create_model()
```

![](img/948faf8dac703cd16151e26ac8120055_33.png)

`create_model`函数首先会实例化一个Prophet模型。在实例化时，可以指定一系列参数：

![](img/948faf8dac703cd16151e26ac8120055_35.png)

![](img/948faf8dac703cd16151e26ac8120055_37.png)

![](img/948faf8dac703cd16151e26ac8120055_39.png)

```python
# 示例参数设置
model = Prophet(
    yearly_seasonality=True,  # 是否考虑年度季节性
    weekly_seasonality=False, # 是否考虑周度季节性
    changepoint_prior_scale=0.05 # 突变点灵敏度参数
)
```

![](img/948faf8dac703cd16151e26ac8120055_41.png)

以下是关键参数说明：
*   `yearly_seasonality`, `weekly_seasonality`: 布尔值，控制是否绘制相应周期的季节性变化图。
*   `changepoint_prior_scale`: 这是一个关键参数，默认值为0.05。它控制了模型对趋势变化的敏感度，值越大，模型对趋势变化的捕捉越灵活（也可能更过拟合）；值越小，趋势线越平滑。

如果想深入了解所有参数，可以在IDE中（如PyCharm或Eclipse）使用`Ctrl+鼠标左键`点击`Prophet`进入其源码查看。

![](img/948faf8dac703cd16151e26ac8120055_43.png)

实例化模型后，我们传入数据并指定训练集。这里，代码使用最近3年的历史数据进行训练：

![](img/948faf8dac703cd16151e26ac8120055_45.png)

```python
model.fit(historical_data) # historical_data 是最近3年的数据
```

![](img/948faf8dac703cd16151e26ac8120055_47.png)

![](img/948faf8dac703cd16151e26ac8120055_49.png)

![](img/948faf8dac703cd16151e26ac8120055_51.png)

训练完成后，我们使用`make_future_dataframe`函数生成用于预测的时间序列。当参数`periods=0`时，表示不预测未来，只在历史数据上进行“预测”（相当于模型在历史数据上的拟合值）。

![](img/948faf8dac703cd16151e26ac8120055_53.png)

![](img/948faf8dac703cd16151e26ac8120055_55.png)

```python
future = model.make_future_dataframe(periods=0)
forecast = model.predict(future)
```

![](img/948faf8dac703cd16151e26ac8120055_57.png)

![](img/948faf8dac703cd16151e26ac8120055_59.png)

预测结果`forecast`是一个DataFrame，会自动添加几个关键列：
*   `yhat`: 预测值。
*   `yhat_lower`: 预测区间的下限。
*   `yhat_upper`: 预测区间的上限。

![](img/948faf8dac703cd16151e26ac8120055_61.png)

![](img/948faf8dac703cd16151e26ac8120055_63.png)

我们可以用`yhat`作为预测指标，用`yhat_lower`和`yhat_upper`来定义置信区间。

![](img/948faf8dac703cd16151e26ac8120055_65.png)

![](img/948faf8dac703cd16151e26ac8120055_67.png)

![](img/948faf8dac703cd16151e26ac8120055_69.png)

## 结果可视化与趋势分析 📊

得到预测结果后，我们将其可视化。

![](img/948faf8dac703cd16151e26ac8120055_71.png)

```python
# 绘制观测值（历史真实数据）
plt.plot(historical_data['ds'], historical_data['y'], 'k.')
# 绘制预测值
plt.plot(forecast['ds'], forecast['yhat'], 'g-')
# 绘制置信区间
plt.fill_between(forecast['ds'], forecast['yhat_lower'], forecast['yhat_upper'], color='lightgreen', alpha=0.5)
```

![](img/948faf8dac703cd16151e26ac8120055_73.png)

![](img/948faf8dac703cd16151e26ac8120055_75.png)

![](img/948faf8dac703cd16151e26ac8120055_77.png)

执行上述代码，便得到了之前看到的预测图。黑色点是真实观测值，绿色线是模型预测值，浅绿色区域是置信区间。

此外，我们还可以调用`model.plot_components(forecast)`来绘制详细的趋势分解图：

![](img/948faf8dac703cd16151e26ac8120055_79.png)

![](img/948faf8dac703cd16151e26ac8120055_81.png)

![](img/948faf8dac703cd16151e26ac8120055_41.png)

![](img/948faf8dac703cd16151e26ac8120055_83.png)

![](img/948faf8dac703cd16151e26ac8120055_85.png)

![](img/948faf8dac703cd16151e26ac8120055_87.png)

从趋势分解图中，我们可以分析出一些规律：
*   **整体趋势**：近三年股价呈上升趋势。
*   **月度趋势**：图中显示，7月、9月和10月股价有下降趋势，而12月和1月上涨幅度最大。
*   **月内趋势**：每个月的月底（如26号至30号）股价容易出现下降趋势。这或许提示在月底应谨慎操作。

![](img/948faf8dac703cd16151e26ac8120055_89.png)

![](img/948faf8dac703cd16151e26ac8120055_91.png)

如果你想观察以“周”为单位的趋势，可以在初始化模型时将`weekly_seasonality`参数设为`True`。

![](img/948faf8dac703cd16151e26ac8120055_93.png)

![](img/948faf8dac703cd16151e26ac8120055_95.png)

```python
model = Prophet(weekly_seasonality=True)
```

![](img/948faf8dac703cd16151e26ac8120055_97.png)

![](img/948faf8dac703cd16151e26ac8120055_99.png)

## 识别突变点 🔍

![](img/948faf8dac703cd16151e26ac8120055_101.png)

突变点是指时间序列趋势发生显著变化的点，例如由上升转为下降，或增长速率突然加快/减慢。Prophet可以自动检测这些突变点。

在代码中，训练完模型后，可以通过`model.changepoints`获取模型识别出的突变点日期。我们还可以进一步分析这些突变点是正向（趋势增强）还是负向（趋势减弱），这通过查看趋势在突变点处的二阶导数正负来判断。

![](img/948faf8dac703cd16151e26ac8120055_103.png)

![](img/948faf8dac703cd16151e26ac8120055_105.png)

![](img/948faf8dac703cd16151e26ac8120055_107.png)

```python
changepoints = model.changepoints # 获取突变点日期
# 通常可以取突变点处趋势的一阶或二阶导数进行分析
```

下图展示了对突变点的可视化，其中可能用不同颜色的竖线标记了正向和负向的突变点。

![](img/948faf8dac703cd16151e26ac8120055_111.png)

![](img/948faf8dac703cd16151e26ac8120055_109.png)

## 进行未来预测 🚀

![](img/948faf8dac703cd16151e26ac8120055_111.png)

![](img/948faf8dac703cd16151e26ac8120055_113.png)

预测未来是时间序列模型的核心应用。我们只需要在生成未来数据框时，指定想要预测的天数。

![](img/948faf8dac703cd16151e26ac8120055_115.png)

```python
# 预测未来180天
future = model.make_future_dataframe(periods=180)
forecast = model.predict(future)
```

然后，用同样的方法绘制预测图。此时，图形右侧的延伸部分就是模型对未来180天的预测值。

![](img/948faf8dac703cd16151e26ac8120055_121.png)

![](img/948faf8dac703cd16151e26ac8120055_117.png)

![](img/948faf8dac703cd16151e26ac8120055_119.png)

从预测图可以看到，黑色观测值截止到当前日期，而绿色预测线继续向后延伸。模型基于历史规律，预测未来股价可能先经历一个小幅下降，然后再上升。这个预测可能显得比较“保守”，没有给出特别大幅的上涨。

![](img/948faf8dac703cd16151e26ac8120055_121.png)

**这正是`changepoint_prior_scale`参数可以调节的地方**。通过调整这个参数，我们可以让模型对历史趋势的变化更敏感（预测更“奔放”）或更不敏感（预测更“保守”）。

## 总结

![](img/948faf8dac703cd16151e26ac8120055_123.png)

![](img/948faf8dac703cd16151e26ac8120055_125.png)

本节课中我们一起学习了使用Facebook Prophet库进行时间序列预测的全过程：

![](img/948faf8dac703cd16151e26ac8120055_127.png)

![](img/948faf8dac703cd16151e26ac8120055_129.png)

1.  **模型构建**：初始化Prophet模型，理解关键参数如`changepoint_prior_scale`的作用。
2.  **训练与拟合**：使用历史数据训练模型，并在历史数据上生成拟合值。
3.  **可视化分析**：绘制预测图及趋势分解图，分析数据的整体趋势、季节性及月内规律。
4.  **突变点检测**：利用模型自动识别趋势发生显著变化的点。
5.  **未来预测**：通过指定`periods`参数，预测未来时间点的值，并理解预测结果的特性。

![](img/948faf8dac703cd16151e26ac8120055_131.png)

![](img/948faf8dac703cd16151e26ac8120055_133.png)

Prophet作为一个强大的时间序列预测工具，通过简单的接口提供了深度的分析能力，是金融数据分析与量化交易中一个非常实用的“神器”。