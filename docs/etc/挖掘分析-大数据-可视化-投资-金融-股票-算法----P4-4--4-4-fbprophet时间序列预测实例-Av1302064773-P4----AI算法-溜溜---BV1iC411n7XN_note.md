# 量化交易与Python金融分析实战：P4：fbprophet时间序列预测实例 📈

![](img/f4934776bf2649597b30ab7f015fd7a1_1.png)

在本节课中，我们将学习如何使用Facebook开源的`fbprophet`库进行时间序列预测。我们将通过一个股票数据的实例，完整地演示从数据准备、模型构建、训练、可视化到未来预测的全过程。课程内容将尽可能简单直白，确保初学者能够理解并上手实践。

![](img/f4934776bf2649597b30ab7f015fd7a1_3.png)

![](img/f4934776bf2649597b30ab7f015fd7a1_5.png)

![](img/f4934776bf2649597b30ab7f015fd7a1_7.png)

---

![](img/f4934776bf2649597b30ab7f015fd7a1_9.png)

![](img/f4934776bf2649597b30ab7f015fd7a1_11.png)

## 模型构建与初步预测

![](img/f4934776bf2649597b30ab7f015fd7a1_13.png)

![](img/f4934776bf2649597b30ab7f015fd7a1_15.png)

上一节我们介绍了时间序列分析的基本概念，本节中我们来看看如何使用`fbprophet`构建一个具体的预测模型。

![](img/f4934776bf2649597b30ab7f015fd7a1_17.png)

![](img/f4934776bf2649597b30ab7f015fd7a1_19.png)

当我们执行创建模型的操作后，`fbprophet`会首先进行建模，然后基于模型进行预测，并生成可视化图表。

![](img/f4934776bf2649597b30ab7f015fd7a1_21.png)

![](img/f4934776bf2649597b30ab7f015fd7a1_1.png)

![](img/f4934776bf2649597b30ab7f015fd7a1_23.png)

上图展示了预测结果：黑色点代表观测到的真实值，绿色点代表模型预测出的值，而浅色区域则表示预测的置信区间。这样就构成了一个基本的时间序列预测图。

![](img/f4934776bf2649597b30ab7f015fd7a1_25.png)

![](img/f4934776bf2649597b30ab7f015fd7a1_27.png)

## 创建模型与参数设置

![](img/f4934776bf2649597b30ab7f015fd7a1_29.png)

构造出基本的数据序列后，下一步是创建预测模型。在Facebook的时间序列框架中，我们可以直接使用其内置的函数来创建模型。

![](img/f4934776bf2649597b30ab7f015fd7a1_31.png)

![](img/f4934776bf2649597b30ab7f015fd7a1_33.png)

![](img/f4934776bf2649597b30ab7f015fd7a1_35.png)

以下是创建模型的核心代码步骤：

![](img/f4934776bf2649597b30ab7f015fd7a1_37.png)

```python
# 导入fbprophet
from fbprophet import Prophet

![](img/f4934776bf2649597b30ab7f015fd7a1_39.png)

# 创建模型实例，并设置参数
model = Prophet(
    yearly_seasonality=True,  # 考虑年度季节性
    weekly_seasonality=True,  # 考虑周度季节性
    daily_seasonality=False,   # 不考虑日度季节性（根据数据特性决定）
    changepoint_prior_scale=0.05  # 突变点先验尺度，控制模型灵活性
)
```

模型实例化时，可以指定一系列参数来控制其行为。关键参数`changepoint_prior_scale`默认值为0.05，它影响着模型对趋势变化的敏感度。值越大，模型对数据中的变化越敏感（更“奔放”）；值越小，模型越平滑保守。

![](img/f4934776bf2649597b30ab7f015fd7a1_41.png)

![](img/f4934776bf2649597b30ab7f015fd7a1_43.png)

其他可指定的参数包括是否考虑节假日效应等。如果想深入了解每个参数的定义，可以按住Ctrl键（在大多数IDE中）点击`Prophet`进入其源码查看。

## 模型训练与历史数据拟合

模型创建完成后，我们需要用历史数据对其进行训练。通常，我们不会使用全部历史数据，而是指定一个训练年限。

以下是训练模型的核心步骤：

![](img/f4934776bf2649597b30ab7f015fd7a1_45.png)

![](img/f4934776bf2649597b30ab7f015fd7a1_47.png)

```python
# 假设df是一个包含‘ds’（日期）和‘y’（值）两列的Pandas DataFrame
# 1. 划分训练集（例如，使用最近3年的数据）
train_df = df.tail(3*365)  # 取最后3年的数据

# 2. 用训练数据拟合模型
model.fit(train_df)

![](img/f4934776bf2649597b30ab7f015fd7a1_49.png)

# 3. 构建用于预测的数据框架，periods=0表示只对历史数据点进行预测
future = model.make_future_dataframe(periods=0)
forecast = model.predict(future)
```

![](img/f4934776bf2649597b30ab7f015fd7a1_51.png)

![](img/f4934776bf2649597b30ab7f015fd7a1_53.png)

`model.fit()`方法会基于提供的历史数据训练模型。`make_future_dataframe(periods=0)`生成一个与历史数据时间点相同的未来数据框架，用于获取模型在历史时间点上的拟合值（即“预测”历史）。执行`predict`后，返回的`forecast` DataFrame会包含多个列，其中`yhat`是预测值，`yhat_lower`和`yhat_upper`构成了置信区间。

![](img/f4934776bf2649597b30ab7f015fd7a1_55.png)

![](img/f4934776bf2649597b30ab7f015fd7a1_57.png)

## 结果可视化

训练并预测后，我们可以将结果可视化，直观地对比观测值与预测值。

![](img/f4934776bf2649597b30ab7f015fd7a1_59.png)

以下是绘制结果图的代码逻辑：

![](img/f4934776bf2649597b30ab7f015fd7a1_61.png)

```python
import matplotlib.pyplot as plt

![](img/f4934776bf2649597b30ab7f015fd7a1_63.png)

![](img/f4934776bf2649597b30ab7f015fd7a1_65.png)

# 绘制观测值（历史真实数据）
fig = model.plot(forecast)
ax = fig.gca()
ax.plot(df['ds'], df['y'], 'k.', label='Observed')  # 黑色点，观测值

![](img/f4934776bf2649597b30ab7f015fd7a1_67.png)

![](img/f4934776bf2649597b30ab7f015fd7a1_69.png)

# 绘制预测值
ax.plot(forecast['ds'], forecast['yhat'], 'g-', label='Forecast')  # 绿色线，预测值

![](img/f4934776bf2649597b30ab7f015fd7a1_71.png)

![](img/f4934776bf2649597b30ab7f015fd7a1_73.png)

# 绘制置信区间
ax.fill_between(forecast['ds'], forecast['yhat_lower'], forecast['yhat_upper'], color='lightgreen', alpha=0.5, label='Confidence Interval')

![](img/f4934776bf2649597b30ab7f015fd7a1_75.png)

![](img/f4934776bf2649597b30ab7f015fd7a1_77.png)

ax.legend()
plt.show()
```

![](img/f4934776bf2649597b30ab7f015fd7a1_79.png)

![](img/f4934776bf2649597b30ab7f015fd7a1_81.png)

![](img/f4934776bf2649597b30ab7f015fd7a1_83.png)

执行上述代码后，我们会得到一张图表。其中，黑色散点是真实观测值，绿色线是模型的预测值，浅绿色区域是预测的置信区间。这样就完成了一个基本模型的构建和可视化。

![](img/f4934776bf2649597b30ab7f015fd7a1_85.png)

## 季节性分解分析

`fbprophet`一个强大的功能是能够自动分解时间序列中的不同季节性成分。我们可以使用`model.plot_components(forecast)`函数来查看趋势和季节性变化。

![](img/f4934776bf2649597b30ab7f015fd7a1_87.png)

![](img/f4934776bf2649597b30ab7f015fd7a1_89.png)

```python
# 绘制模型的各个成分（趋势、年度季节性、周度季节性等）
fig2 = model.plot_components(forecast)
plt.show()
```

![](img/f4934776bf2649597b30ab7f015fd7a1_91.png)

![](img/f4934776bf2649597b30ab7f015fd7a1_93.png)

![](img/f4934776bf2649597b30ab7f015fd7a1_15.png)

上图可能展示以下内容：
*   **整体趋势图**：显示数据在多年间的长期变化方向。
*   **年度季节性**：显示数据在一年内不同月份的变化规律。例如，可能发现12月和1月是上涨幅度最大的月份。
*   **周度季节性**：显示数据在一周内不同日期的变化规律。对于股票数据，通常只关注周一至周五（交易日）的模式。

![](img/f4934776bf2649597b30ab7f015fd7a1_95.png)

![](img/f4934776bf2649597b30ab7f015fd7a1_97.png)

通过分析这些成分，我们可以获得一些洞察。例如，如果发现每月月底普遍呈现下降趋势，那么在制定交易策略时，月底或许就需要更加谨慎。

![](img/f4934776bf2649597b30ab7f015fd7a1_99.png)

![](img/f4934776bf2649597b30ab7f015fd7a1_101.png)

![](img/f4934776bf2649597b30ab7f015fd7a1_103.png)

## 识别趋势突变点

![](img/f4934776bf2649597b30ab7f015fd7a1_105.png)

![](img/f4934776bf2649597b30ab7f015fd7a1_107.png)

在时间序列中，趋势发生显著变化的点被称为“突变点”。`fbprophet`可以自动检测这些点，这对于理解数据动态非常重要。

![](img/f4934776bf2649597b30ab7f015fd7a1_109.png)

![](img/f4934776bf2649597b30ab7f015fd7a1_111.png)

突变点可以通过观察序列二阶导数的正负变化来识别。例如，从下降趋势转为上升趋势的点，或者增长速率突然加快的点。

![](img/f4934776bf2649597b30ab7f015fd7a1_113.png)

![](img/f4934776bf2649597b30ab7f015fd7a1_115.png)

以下是识别和可视化突变点的代码：

![](img/f4934776bf2649597b30ab7f015fd7a1_117.png)

```python
# 从训练好的模型中获取突变点信息
changepoints = model.changepoints  # 突变点的日期
trend_changes = model.params['delta']  # 在突变点处趋势的变化量

![](img/f4934776bf2649597b30ab7f015fd7a1_119.png)

# 可视化突变点
fig = model.plot(forecast)
ax = fig.gca()
# 假设我们已经根据`trend_changes`的正负将突变点分为‘positive’和‘negative’两类
for cp in positive_changepoints:
    ax.axvline(x=cp, color='green', linestyle='--', alpha=0.8)
for cp in negative_changepoints:
    ax.axvline(x=cp, color='red', linestyle='--', alpha=0.8)

![](img/f4934776bf2649597b30ab7f015fd7a1_121.png)

plt.show()
```

![](img/f4934776bf2649597b30ab7f015fd7a1_123.png)

![](img/f4934776bf2649597b30ab7f015fd7a1_125.png)

![](img/f4934776bf2649597b30ab7f015fd7a1_113.png)

在上图中，绿色虚线可能标识趋势增强的突变点（正变化），红色虚线标识趋势减弱的突变点（负变化）。这有助于我们理解驱动时间序列变化的关键事件或时期。

## 进行未来预测

![](img/f4934776bf2649597b30ab7f015fd7a1_127.png)

最终，我们最关心的往往是利用模型预测未来。`fbprophet`进行未来预测非常简单，只需在构建预测数据框架时指定想要预测的未来天数。

![](img/f4934776bf2649597b30ab7f015fd7a1_129.png)

![](img/f4934776bf2649597b30ab7f015fd7a1_131.png)

以下是预测未来180天股价的示例：

![](img/f4934776bf2649597b30ab7f015fd7a1_133.png)

```python
# 创建模型（参数可与之前相同或调整）
model_for_future = Prophet(changepoint_prior_scale=0.05)
model_for_future.fit(df)  # 使用全部历史数据训练

![](img/f4934776bf2649597b30ab7f015fd7a1_135.png)

![](img/f4934776bf2649597b30ab7f015fd7a1_137.png)

# 构建包含未来180天的数据框架
future = model_for_future.make_future_dataframe(periods=180)
# 进行预测
forecast_future = model_for_future.predict(future)

# 绘制包含未来预测的图表
fig = model_for_future.plot(forecast_future)
plt.show()
```

![](img/f4934776bf2649597b30ab7f015fd7a1_139.png)

![](img/f4934776bf2649597b30ab7f015fd7a1_143.png)

![](img/f4934776bf2649597b30ab7f015fd7a1_141.png)

![](img/f4934776bf2649597b30ab7f015fd7a1_143.png)

在结果图中，黑色点依然是历史观测值，绿色线是模型的拟合和预测值。在历史数据结束点之后，绿色线及其置信区间延伸的部分就是对未来180天的预测。预测趋势会基于历史模式进行外推。模型的保守或奔放程度，很大程度上由之前提到的`changepoint_prior_scale`参数控制。

---

## 总结

![](img/f4934776bf2649597b30ab7f015fd7a1_145.png)

![](img/f4934776bf2649597b30ab7f015fd7a1_147.png)

本节课中，我们一起学习了使用`fbprophet`库进行时间序列预测的完整流程：

![](img/f4934776bf2649597b30ab7f015fd7a1_149.png)

![](img/f4934776bf2649597b30ab7f015fd7a1_151.png)

![](img/f4934776bf2649597b30ab7f015fd7a1_153.png)

1.  **模型构建**：通过实例化`Prophet`类并设置参数（如季节性、突变点先验尺度）来创建模型。
2.  **模型训练**：使用历史数据（如最近3年的股票数据）对模型进行拟合。
3.  **结果可视化**：绘制图表对比观测值与模型拟合值，并分析置信区间。
4.  **季节性分解**：利用内置函数分解并查看时间序列的长期趋势、年度季节性、周度季节性等成分。
5.  **突变点检测**：识别并可视化趋势发生显著变化的关键时间点。
6.  **未来预测**：通过指定`periods`参数，让模型预测未来一段时间的数据走势。

![](img/f4934776bf2649597b30ab7f015fd7a1_155.png)

`fbprophet`是一个功能强大且易于使用的时间序列预测工具，它封装了复杂的统计模型，使得即使没有深厚统计学背景的用户也能快速构建出有参考价值的预测模型。通过调整参数（尤其是`changepoint_prior_scale`），我们可以使预测结果更符合我们对数据特性的先验认知。