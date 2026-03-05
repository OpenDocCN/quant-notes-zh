# 金融量化：P36：fbprophet时间序列预测实例 📈

在本节课中，我们将学习如何使用Facebook开发的`fbprophet`库进行时间序列预测。我们将通过一个完整的实例，从数据建模、可视化到未来预测，一步步解析其核心功能与使用方法。

![](img/1926a81b7bafac0226f1ffa406719250_1.png)

![](img/1926a81b7bafac0226f1ffa406719250_2.png)

![](img/1926a81b7bafac0226f1ffa406719250_3.png)

---

## 概述与模型构建

![](img/1926a81b7bafac0226f1ffa406719250_5.png)

上一节我们介绍了时间序列的基本概念，本节中我们来看看如何使用`fbprophet`进行建模。

![](img/1926a81b7bafac0226f1ffa406719250_6.png)

![](img/1926a81b7bafac0226f1ffa406719250_7.png)

![](img/1926a81b7bafac0226f1ffa406719250_8.png)

执行`create_model`函数后，它会帮助我们完成建模和预测。建模后，它会生成预测结果并绘制图表。

![](img/1926a81b7bafac0226f1ffa406719250_9.png)

以下是构建模型的核心代码步骤：

```python
# 1. 导入Prophet类
from fbprophet import Prophet

![](img/1926a81b7bafac0226f1ffa406719250_11.png)

# 2. 实例化模型，并设置参数
model = Prophet(
    changepoint_prior_scale=0.05,  # 调整趋势灵活性的关键参数
    yearly_seasonality=True,       # 考虑年度季节性
    weekly_seasonality=False,      # 不考虑周季节性（可改为True）
    daily_seasonality=False        # 不考虑日季节性
)

![](img/1926a81b7bafac0226f1ffa406719250_13.png)

# 3. 添加自定义季节性（例如，以30.5天为周期）
model.add_seasonality(name='monthly', period=30.5, fourier_order=5)

![](img/1926a81b7bafac0226f1ffa406719250_14.png)

![](img/1926a81b7bafac0226f1ffa406719250_15.png)

# 4. 准备数据。数据框需包含两列：'ds' (日期) 和 'y' (观测值)
# df = pd.read_csv('your_data.csv')

# 5. 使用历史数据拟合模型
model.fit(df)
```

![](img/1926a81b7bafac0226f1ffa406719250_17.png)

![](img/1926a81b7bafac0226f1ffa406719250_18.png)

实例化模型时，可以指定多种参数来控制季节性成分。`changepoint_prior_scale`是一个关键参数，默认值为`0.05`，它控制着模型对趋势变化的敏感度。

---

## 模型拟合与历史预测

构建好模型后，下一步是使用历史数据进行拟合，并查看模型在历史数据上的表现。

![](img/1926a81b7bafac0226f1ffa406719250_20.png)

我们使用`model.fit()`方法在历史数据上进行训练。可以指定`training`参数来限定使用最近几年的数据进行训练，例如只用近三年的数据。

训练完成后，我们使用`model.predict()`进行预测。当参数`periods=0`时，表示只对历史数据进行“预测”（即模型拟合值的回测），而不预测未来时间点。

以下是相关步骤：

```python
# 使用历史数据拟合模型（例如，仅用最近3年数据）
history_df = df.tail(3*365)  # 假设数据是日频的
model.fit(history_df)

# 创建用于预测的数据框架（此时periods=0，仅历史数据）
future = model.make_future_dataframe(periods=0)
forecast = model.predict(future)

# forecast数据框包含多列，例如：
# 'ds': 日期
# 'yhat': 预测值
# 'yhat_lower': 预测区间下限
# 'yhat_upper': 预测区间上限
```

预测完成后，`forecast`对象中会包含预测值`yhat`以及置信区间`yhat_lower`和`yhat_upper`。

![](img/1926a81b7bafac0226f1ffa406719250_22.png)

![](img/1926a81b7bafac0226f1ffa406719250_23.png)

---

![](img/1926a81b7bafac0226f1ffa406719250_24.png)

## 结果可视化

![](img/1926a81b7bafac0226f1ffa406719250_26.png)

![](img/1926a81b7bafac0226f1ffa406719250_27.png)

我们可以将模型的拟合结果与原始观测数据一起绘制出来，直观地评估模型效果。

使用Matplotlib进行绘图，将观测值、预测值及置信区间展示在同一张图上。

以下是绘图的核心代码：

![](img/1926a81b7bafac0226f1ffa406719250_29.png)

```python
import matplotlib.pyplot as plt

![](img/1926a81b7bafac0226f1ffa406719250_31.png)

fig, ax = plt.subplots(figsize=(10, 6))

# 绘制观测值（黑色散点）
ax.scatter(df['ds'], df['y'], color='k', label='Observed', s=1)

![](img/1926a81b7bafac0226f1ffa406719250_33.png)

![](img/1926a81b7bafac0226f1ffa406719250_34.png)

# 绘制预测值（蓝色线条）
ax.plot(forecast['ds'], forecast['yhat'], color='b', label='Forecast')

![](img/1926a81b7bafac0226f1ffa406719250_35.png)

![](img/1926a81b7bafac0226f1ffa406719250_36.png)

# 填充置信区间（浅蓝色区域）
ax.fill_between(forecast['ds'], forecast['yhat_lower'], forecast['yhat_upper'], color='lightblue', alpha=0.5, label='Confidence Interval')

![](img/1926a81b7bafac0226f1ffa406719250_38.png)

![](img/1926a81b7bafac0226f1ffa406719250_39.png)

ax.set_xlabel('Date')
ax.set_ylabel('Value')
ax.legend()
plt.show()
```

![](img/1926a81b7bafac0226f1ffa406719250_41.png)

生成的图表中，黑色点代表原始观测值，蓝色线代表模型拟合值，浅蓝色区域代表预测的置信区间。

![](img/1926a81b7bafac0226f1ffa406719250_42.png)

![](img/1926a81b7bafac0226f1ffa406719250_43.png)

---

## 季节性成分分析

![](img/1926a81b7bafac0226f1ffa406719250_45.png)

`fbprophet`的一个强大功能是能自动分解并可视化时间序列中的季节性成分。

![](img/1926a81b7bafac0226f1ffa406719250_46.png)

![](img/1926a81b7bafac0226f1ffa406719250_47.png)

模型拟合后，可以调用`model.plot_components(forecast)`方法来绘制趋势和季节性图表。

![](img/1926a81b7bafac0226f1ffa406719250_49.png)

以下是调用方法：

![](img/1926a81b7bafac0226f1ffa406719250_50.png)

![](img/1926a81b7bafac0226f1ffa406719250_51.png)

```python
# 绘制趋势和季节性成分
fig = model.plot_components(forecast)
```

该函数会生成一系列子图：
*   **整体趋势图**：展示时间序列在整个时间范围内的变化趋势。
*   **年度季节性**：展示数据在一年中不同月份或周数的典型模式。
*   **周度季节性**（如果启用）：展示数据在一周内不同日期的典型模式。
*   **自定义季节性**（如果添加）：展示自定义周期（如月度）的模式。

![](img/1926a81b7bafac0226f1ffa406719250_53.png)

例如，从示例图中可以发现，过去三年的整体趋势是向上的。在一年中，7月、9月和10月可能呈现下降趋势，而12月和1月的上浮幅度最大。这些洞察对于理解数据模式非常有帮助。

![](img/1926a81b7bafac0226f1ffa406719250_54.png)

![](img/1926a81b7bafac0226f1ffa406719250_55.png)

如果想分析周度季节性，只需在创建模型时将`weekly_seasonality`参数设为`True`。

![](img/1926a81b7bafac0226f1ffa406719250_57.png)

---

![](img/1926a81b7bafac0226f1ffa406719250_58.png)

![](img/1926a81b7bafac0226f1ffa406719250_59.png)

![](img/1926a81b7bafac0226f1ffa406719250_60.png)

## 突变点检测与调整

突变点是指时间序列趋势发生显著变化的点，例如从上升转为下降，或增长速率突然加快。

![](img/1926a81b7bafac0226f1ffa406719250_62.png)

`fbprophet`可以自动检测这些突变点，这对于理解趋势变化和调整模型预测行为至关重要。

![](img/1926a81b7bafac0226f1ffa406719250_63.png)

![](img/1926a81b7bafac0226f1ffa406719250_64.png)

![](img/1926a81b7bafac0226f1ffa406719250_65.png)

模型内部会计算时间序列的二阶导数，通过其正负来判断突变点的性质（正向突变或负向突变）。我们可以从拟合好的模型中提取这些突变点信息。

![](img/1926a81b7bafac0226f1ffa406719250_66.png)

![](img/1926a81b7bafac0226f1ffa406719250_68.png)

以下是检测和可视化突变点的代码：

```python
# 从拟合的模型中获取突变点日期
changepoints = model.changepoints

# 计算突变点处的趋势变化率（一阶差分）
trend_deltas = model.params['delta'].mean(axis=0)

![](img/1926a81b7bafac0226f1ffa406719250_70.png)

# 将突变点与原始数据合并，便于分析
changepoint_df = pd.DataFrame({
    'ds': changepoints,
    'delta': trend_deltas
})

![](img/1926a81b7bafac0226f1ffa406719250_72.png)

![](img/1926a81b7bafac0226f1ffa406719250_73.png)

# 根据变化率正负分类
positive_cp = changepoint_df[changepoint_df['delta'] > 0]
negative_cp = changepoint_df[changepoint_df['delta'] < 0]

# 可视化：在原始序列图上标注突变点
fig, ax = plt.subplots(figsize=(10, 6))
ax.plot(df['ds'], df['y'], 'k.', label='Observed')
ax.plot(forecast['ds'], forecast['yhat'], 'b-', label='Forecast')

# 用竖线标注正向突变点（绿色）和负向突变点（红色）
for cp in positive_cp['ds']:
    ax.axvline(x=cp, color='green', linestyle='--', alpha=0.5)
for cp in negative_cp['ds']:
    ax.axvline(x=cp, color='red', linestyle='--', alpha=0.5)

ax.legend()
plt.show()
```

通过调整`changepoint_prior_scale`参数，可以控制模型对突变点的敏感度。值越大，模型越灵活，能捕捉更多的趋势变化，但也可能更易过拟合；值越小，模型趋势越平滑、保守。

![](img/1926a81b7bafac0226f1ffa406719250_75.png)

![](img/1926a81b7bafac0226f1ffa406719250_76.png)

![](img/1926a81b7bafac0226f1ffa406719250_77.png)

---

## 未来预测

![](img/1926a81b7bafac0226f1ffa406719250_79.png)

最终，我们可以使用训练好的模型对未来进行预测。

只需在调用`make_future_dataframe`时，指定`periods`参数为想要预测的未来天数（例如180天），模型便会生成包含未来日期的数据框并进行预测。

以下是未来预测的代码：

![](img/1926a81b7bafac0226f1ffa406719250_81.png)

```python
# 创建包含未来180天的数据框
future = model.make_future_dataframe(periods=180)

![](img/1926a81b7bafac0226f1ffa406719250_82.png)

![](img/1926a81b7bafac0226f1ffa406719250_83.png)

# 进行预测
forecast_extended = model.predict(future)

# 可视化（包含历史拟合和未来预测）
fig = model.plot(forecast_extended)
ax = fig.gca()
# 可以添加一条竖线标记历史与未来的分界点
ax.axvline(x=df['ds'].max(), color='gray', linestyle='--', label='Present')
ax.legend()
plt.show()
```

在结果图中，黑色观测点只到当前日期（例如2018年1月26日），而蓝色的预测线会向后延伸180天，展示模型对未来趋势的判断。预测的置信区间也会一同显示。模型可能会基于历史周期性和趋势，预测未来会出现回调或继续增长，具体的预测行为可以通过调整`changepoint_prior_scale`等参数来优化。

---

## 总结

![](img/1926a81b7bafac0226f1ffa406719250_85.png)

![](img/1926a81b7bafac0226f1ffa406719250_86.png)

![](img/1926a81b7bafac0226f1ffa406719250_87.png)

本节课中我们一起学习了`fbprophet`库的核心应用。我们从实例化模型、拟合历史数据开始，学会了如何可视化拟合结果和分解季节性成分。接着，我们探讨了突变点的检测与意义，并了解了如何通过关键参数调整模型的灵活度。最后，我们完成了对未来时间段的价格预测。

![](img/1926a81b7bafac0226f1ffa406719250_88.png)

![](img/1926a81b7bafac0226f1ffa406719250_89.png)

![](img/1926a81b7bafac0226f1ffa406719250_90.png)

`fbprophet`以其自动化程度高、可解释性强、功能全面（如季节性分解、突变点检测、置信区间计算）的特点，成为了时间序列预测，尤其是金融数据分析中的一个实用工具。通过本节的实例，你应该能够掌握其基本工作流程，并应用于自己的项目中。