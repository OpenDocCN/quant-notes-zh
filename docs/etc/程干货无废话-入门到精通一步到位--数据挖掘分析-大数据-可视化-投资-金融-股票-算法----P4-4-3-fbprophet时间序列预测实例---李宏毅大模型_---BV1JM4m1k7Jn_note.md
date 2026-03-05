# Python金融分析与量化交易实战课程：P4：4.3-fbprophet时间序列预测实例 📈

![](img/aace4a5b86ecbef713ab49594ed8f687_1.png)

![](img/aace4a5b86ecbef713ab49594ed8f687_3.png)

在本节课中，我们将学习如何使用Facebook开发的`fbprophet`库进行时间序列预测。我们将通过一个实例，从数据准备、模型构建、训练、可视化到未来预测，完整地走一遍流程。课程内容简单直白，旨在让初学者能够理解并上手实践。

![](img/aace4a5b86ecbef713ab49594ed8f687_5.png)

![](img/aace4a5b86ecbef713ab49594ed8f687_7.png)

![](img/aace4a5b86ecbef713ab49594ed8f687_9.png)

## 概述与数据准备

![](img/aace4a5b86ecbef713ab49594ed8f687_11.png)

![](img/aace4a5b86ecbef713ab49594ed8f687_13.png)

首先，我们来看一下整体的流程。当我们执行代码创建一个模型后，`fbprophet`会帮助我们进行建模和预测，并生成可视化图表。

![](img/aace4a5b86ecbef713ab49594ed8f687_15.png)

![](img/aace4a5b86ecbef713ab49594ed8f687_17.png)

上一节我们介绍了时间序列的基本概念，本节中我们来看看如何使用`fbprophet`进行实战。

![](img/aace4a5b86ecbef713ab49594ed8f687_19.png)

执行`create_model`函数后，我们会得到一个模型对象。这个模型会基于历史数据进行拟合，并可以用于预测。预测结果会以图表形式展示，其中通常包含观测值、预测值以及置信区间。

![](img/aace4a5b86ecbef713ab49594ed8f687_21.png)

以下是构建和训练模型的基本代码框架：

![](img/aace4a5b86ecbef713ab49594ed8f687_23.png)

![](img/aace4a5b86ecbef713ab49594ed8f687_25.png)

```python
# 导入必要的库
from fbprophet import Prophet
import pandas as pd

![](img/aace4a5b86ecbef713ab49594ed8f687_27.png)

# 1. 准备数据
# 数据需要包含两列：`ds` (日期) 和 `y` (观测值)
df = pd.read_csv('your_data.csv')

![](img/aace4a5b86ecbef713ab49594ed8f687_29.png)

![](img/aace4a5b86ecbef713ab49594ed8f687_31.png)

# 2. 创建并配置模型
model = Prophet(
    yearly_seasonality=True,  # 是否考虑年度季节性
    weekly_seasonality=True,  # 是否考虑周度季节性
    daily_seasonality=False,  # 是否考虑每日季节性（通常用于高频数据）
    changepoint_prior_scale=0.05  # 控制趋势灵活性的关键参数
)

![](img/aace4a5b86ecbef713ab49594ed8f687_33.png)

# 3. 使用历史数据拟合模型
model.fit(df)

# 4. 构建未来时间框架并进行预测
future = model.make_future_dataframe(periods=180)  # 预测未来180天
forecast = model.predict(future)

![](img/aace4a5b86ecbef713ab49594ed8f687_35.png)

# 5. 可视化结果
fig1 = model.plot(forecast)
```

## 模型结果可视化解读

模型训练并预测后，我们可以绘制图表来直观地理解结果。

在生成的图表中，黑色圆点代表历史观测值，绿色圆点代表模型拟合出的预测值。浅绿色的区域是置信区间，它表示了预测值可能波动的范围。这样，我们就得到了一个基本的时间序列预测图。

![](img/aace4a5b86ecbef713ab49594ed8f687_37.png)

除了整体的预测图，`fbprophet`还提供了一个非常实用的`plot_components`函数，可以分解并展示时间序列中的不同成分。

![](img/aace4a5b86ecbef713ab49594ed8f687_39.png)

以下是使用`plot_components`的代码：

![](img/aace4a5b86ecbef713ab49594ed8f687_41.png)

![](img/aace4a5b86ecbef713ab49594ed8f687_43.png)

```python
# 绘制模型的各个组成部分
fig2 = model.plot_components(forecast)
```

![](img/aace4a5b86ecbef713ab49594ed8f687_45.png)

![](img/aace4a5b86ecbef713ab49594ed8f687_47.png)

这个函数会自动生成多张子图，分别展示：
*   **趋势**：时间序列整体的长期变化方向。
*   **年度季节性**：数据在一年中不同月份呈现的规律性波动。
*   **周度季节性**：数据在一周中不同日期（如工作日与周末）呈现的规律性波动。

通过观察这些子图，我们可以清晰地看到数据在不同时间尺度上的变化模式。例如，可能发现每年7月价格普遍较低，而每周一价格有上涨趋势等。

![](img/aace4a5b86ecbef713ab49594ed8f687_49.png)

## 深入模型参数：以`changepoint_prior_scale`为例

![](img/aace4a5b86ecbef713ab49594ed8f687_51.png)

`fbprophet`模型的预测行为可以通过参数进行精细调整。其中一个核心参数是`changepoint_prior_scale`，它控制着模型对趋势变化的敏感度。

![](img/aace4a5b86ecbef713ab49594ed8f687_53.png)

![](img/aace4a5b86ecbef713ab49594ed8f687_55.png)

上一节我们看到了模型的默认输出，本节中我们来看看如何通过调整参数来改变预测的“保守”或“奔放”程度。

![](img/aace4a5b86ecbef713ab49594ed8f687_57.png)

![](img/aace4a5b86ecbef713ab49594ed8f687_59.png)

*   **较小的值（如0.01）**：会使模型更加“保守”。趋势线会更加平滑，对历史数据中的短期波动不敏感，预测未来时变化也会更平缓。
*   **较大的值（如0.5）**：会使模型更加“灵活”或“奔放”。趋势线会更多地拟合历史数据中的波动，预测未来时可能出现更大幅度的变化。

![](img/aace4a5b86ecbef713ab49594ed8f687_61.png)

![](img/aace4a5b86ecbef713ab49594ed8f687_63.png)

以下是调整该参数的示例：

![](img/aace4a5b86ecbef713ab49594ed8f687_65.png)

![](img/aace4a5b86ecbef713ab49594ed8f687_67.png)

![](img/aace4a5b86ecbef713ab49594ed8f687_69.png)

```python
# 创建一个更“灵活”的模型
model_flexible = Prophet(changepoint_prior_scale=0.5)
model_flexible.fit(df)
forecast_flexible = model_flexible.predict(future)

![](img/aace4a5b86ecbef713ab49594ed8f687_71.png)

![](img/aace4a5b86ecbef713ab49594ed8f687_73.png)

# 创建一个更“保守”的模型
model_conservative = Prophet(changepoint_prior_scale=0.01)
model_conservative.fit(df)
forecast_conservative = model_conservative.predict(future)

![](img/aace4a5b86ecbef713ab49594ed8f687_75.png)

# 比较不同参数下的预测结果
# ... 绘制对比图 ...
```

![](img/aace4a5b86ecbef713ab49594ed8f687_77.png)

在实际应用中，需要根据业务背景和数据特点来选择合适的参数值。通常可以通过在历史数据上划分训练集和验证集，评估不同参数下的预测精度来决定。

![](img/aace4a5b86ecbef713ab49594ed8f687_79.png)

![](img/aace4a5b86ecbef713ab49594ed8f687_81.png)

![](img/aace4a5b86ecbef713ab49594ed8f687_83.png)

## 检测趋势突变点

`fbprophet`模型能够自动检测时间序列中的“突变点”。突变点是指趋势发生显著变化的时刻，例如从上升趋势转为下降趋势，或者增长速率突然加快/减慢。

![](img/aace4a5b86ecbef713ab49594ed8f687_85.png)

![](img/aace4a5b86ecbef713ab49594ed8f687_87.png)

模型内部通过观察时间序列趋势的变化率（可以近似理解为二阶导数）来识别这些点。我们可以将这些突变点可视化出来，以分析历史数据中发生重大变化的时间。

![](img/aace4a5b86ecbef713ab49594ed8f687_89.png)

![](img/aace4a5b86ecbef713ab49594ed8f687_91.png)

以下是获取和可视化突变点的代码思路：

![](img/aace4a5b86ecbef713ab49594ed8f687_93.png)

![](img/aace4a5b86ecbef713ab49594ed8f687_95.png)

![](img/aace4a5b86ecbef713ab49594ed8f687_97.png)

```python
# 模型拟合后，可以从其中提取突变点信息
model.fit(df)

![](img/aace4a5b86ecbef713ab49594ed8f687_99.png)

![](img/aace4a5b86ecbef713ab49594ed8f687_101.png)

# 获取模型检测到的所有突变点的日期
changepoints = model.changepoints

![](img/aace4a5b86ecbef713ab49594ed8f687_103.png)

![](img/aace4a5b86ecbef713ab49594ed8f687_105.png)

# 通常我们只关注最重要的前N个突变点
# 可以通过趋势变化率的大小来排序和筛选
# ... 处理changepoints数据 ...

![](img/aace4a5b86ecbef713ab49594ed8f687_107.png)

# 在原始预测图上标注出突变点
fig = model.plot(forecast)
for changepoint in important_changepoints:
    plt.axvline(x=changepoint, color='gray', linestyle='--', alpha=0.5)
```

在生成的图表中，我们可以在趋势线上标注出这些突变点。分析这些点有助于我们理解影响时间序列的关键事件或周期。

![](img/aace4a5b86ecbef713ab49594ed8f687_109.png)

![](img/aace4a5b86ecbef713ab49594ed8f687_111.png)

![](img/aace4a5b86ecbef713ab49594ed8f687_113.png)

## 进行未来预测

最终，我们使用训练好的模型对未来进行预测。这是时间序列分析的核心应用之一。

我们使用`make_future_dataframe`方法创建一个包含未来日期的数据框架，然后调用`predict`方法得到预测值。

![](img/aace4a5b86ecbef713ab49594ed8f687_115.png)

以下是进行未来预测的关键步骤：

![](img/aace4a5b86ecbef713ab49594ed8f687_117.png)

![](img/aace4a5b86ecbef713ab49594ed8f687_119.png)

```python
# 假设我们想预测未来180天
future_days = 180

![](img/aace4a5b86ecbef713ab49594ed8f687_121.png)

# 创建包含历史及未来日期的数据框架
future = model.make_future_dataframe(periods=future_days)

![](img/aace4a5b86ecbef713ab49594ed8f687_123.png)

# 进行预测
forecast = model.predict(future)

# 查看预测结果
# `forecast` DataFrame 包含多列，其中最重要的是：
# `ds`: 日期
# `yhat`: 预测值
# `yhat_lower`: 预测下限
# `yhat_upper`: 预测上限
print(forecast[['ds', 'yhat', 'yhat_lower', 'yhat_upper']].tail())

![](img/aace4a5b86ecbef713ab49594ed8f687_125.png)

![](img/aace4a5b86ecbef713ab49594ed8f687_127.png)

# 绘制包含未来预测的完整图表
fig = model.plot(forecast)
```

![](img/aace4a5b86ecbef713ab49594ed8f687_129.png)

在最终的预测图中，历史部分同时包含观测值（黑点）和拟合值（绿线），而未来部分只有模型的预测值（绿线）及其置信区间（浅绿色区域）。预测的趋势会基于历史模式向后延伸。

## 总结

![](img/aace4a5b86ecbef713ab49594ed8f687_131.png)

![](img/aace4a5b86ecbef713ab49594ed8f687_133.png)

本节课中我们一起学习了`fbprophet`库在时间序列预测中的实战应用。

![](img/aace4a5b86ecbef713ab49594ed8f687_135.png)

![](img/aace4a5b86ecbef713ab49594ed8f687_137.png)

我们首先概述了从数据准备到模型预测的完整流程。接着，学习了如何解读模型生成的可视化图表，包括整体预测图和分解后的季节性成分图。然后，我们深入探讨了`changepoint_prior_scale`这个关键参数，它决定了模型对趋势变化的敏感度。我们还介绍了模型自动检测趋势突变点的功能。最后，我们完成了核心任务——使用拟合好的模型对未来一段时间进行预测。

![](img/aace4a5b86ecbef713ab49594ed8f687_139.png)

![](img/aace4a5b86ecbef713ab49594ed8f687_141.png)

`fbprophet`是一个功能强大且易于上手的工具，它封装了复杂的时间序列模型，让使用者能够更专注于业务逻辑和数据本身的分析。通过本节的实例，希望你能够掌握其基本用法，并应用于自己的数据分析项目中。