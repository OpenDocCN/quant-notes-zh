# Python金融分析与量化交易实战：P5：04-4-时间序列重采样与窗口操作 📊

在本节课中，我们将学习时间序列分析中的两个核心操作：**重采样**与**时间窗口操作**。重采样允许我们改变数据的时间频率，而时间窗口操作则用于计算滑动窗口内的统计指标。这些是金融数据分析中处理时间序列数据的基础技能。

![](img/56d0f8104b59f01a8fd0ec0ed91cda01_1.png)

![](img/56d0f8104b59f01a8fd0ec0ed91cda01_3.png)

## 重采样操作

![](img/56d0f8104b59f01a8fd0ec0ed91cda01_5.png)

![](img/56d0f8104b59f01a8fd0ec0ed91cda01_7.png)

上一节我们介绍了时间序列的基本概念，本节中我们来看看如何改变数据的时间频率，即重采样。

![](img/56d0f8104b59f01a8fd0ec0ed91cda01_9.png)

重采样是指将数据从一个时间频率转换到另一个时间频率。例如，原始数据可能是按天记录的，我们可以将其转换为按周、按月或按年进行统计。

![](img/56d0f8104b59f01a8fd0ec0ed91cda01_11.png)

在Pandas中，使用`resample()`方法即可完成重采样操作。你需要至少指定一个参数，即目标频率（如`‘W’`表示周，`‘M’`表示月）。重采样后，你需要指定一个聚合函数（如均值、第一个值、中位数等）来计算新频率下的数据点。

![](img/56d0f8104b59f01a8fd0ec0ed91cda01_13.png)

以下是重采样的基本代码示例：
```python
# 假设df是一个以日期为索引的DataFrame
# 按周重采样，并计算每周的均值
weekly_data = df.resample(‘W’).mean()
print(weekly_data.head())
```
执行上述代码后，你会得到以周为频率的均值数据。结果中的日期标签默认是每个时间窗口的**结束日期**（右标签）。

![](img/56d0f8104b59f01a8fd0ec0ed91cda01_15.png)

### 标签位置参数

![](img/56d0f8104b59f01a8fd0ec0ed91cda01_17.png)

![](img/56d0f8104b59f01a8fd0ec0ed91cda01_19.png)

![](img/56d0f8104b59f01a8fd0ec0ed91cda01_21.png)

在重采样时，你可以通过`label`参数控制日期标签是使用时间窗口的起始日期（`label=‘left’`）还是结束日期（`label=‘right’`）。

![](img/56d0f8104b59f01a8fd0ec0ed91cda01_23.png)

以下是不同标签设置的对比：
```python
# 使用右标签（默认，以窗口结束日期命名）
monthly_data_right = df.resample(‘M’, label=‘right’).mean()
# 使用左标签（以窗口开始日期命名）
monthly_data_left = df.resample(‘M’, label=‘left’).mean()
```
对比两者结果，你会发现数值相同，但索引日期不同。在金融分析中，通常使用`label=‘right’`（结束日期）更符合逻辑，因为它代表该时间段（如一个月）结束后最终统计的结果。

## 时间窗口（滑动窗口）操作

![](img/56d0f8104b59f01a8fd0ec0ed91cda01_25.png)

了解了如何改变数据频率后，我们接下来学习如何在时间序列上创建滑动窗口并进行计算。

时间窗口操作是指定义一个固定长度的窗口（例如10天），让这个窗口在时间序列上滑动。对于每一个窗口位置，我们都可以计算其内部的统计量（如最小值、最大值、均值等）。这常用于计算移动平均线等技术指标。

![](img/56d0f8104b59f01a8fd0ec0ed91cda01_27.png)

以下是创建并计算滑动窗口统计值的基本步骤。

![](img/56d0f8104b59f01a8fd0ec0ed91cda01_29.png)

首先，我们需要确保数据没有缺失值，否则会影响窗口计算。使用`dropna()`方法清理数据：
```python
# 清理缺失值
df_clean = df.dropna()
```

![](img/56d0f8104b59f01a8fd0ec0ed91cda01_31.png)

![](img/56d0f8104b59f01a8fd0ec0ed91cda01_33.png)

![](img/56d0f8104b59f01a8fd0ec0ed91cda01_35.png)

接着，使用`rolling()`方法创建窗口。例如，创建一个窗口大小为10的滚动窗口对象，并计算每个窗口的最小值：
```python
window_size = 10
# 对某一列数据创建滚动窗口，并计算每个窗口的最小值
rolling_min = df_clean[‘column_name’].rolling(window=window_size).min()
print(rolling_min.tail())
```
最初几个窗口（前9个）的结果会是`NaN`，因为数据不足以填满一个完整的窗口（需要10个数据点）。从第10个窗口开始，才会得到有效的计算结果。

![](img/56d0f8104b59f01a8fd0ec0ed91cda01_37.png)

![](img/56d0f8104b59f01a8fd0ec0ed91cda01_39.png)

### 计算多个窗口统计指标

![](img/56d0f8104b59f01a8fd0ec0ed91cda01_41.png)

我们可以一次性为每个窗口计算多个统计指标，并将结果添加到新的DataFrame中。

![](img/56d0f8104b59f01a8fd0ec0ed91cda01_43.png)

![](img/56d0f8104b59f01a8fd0ec0ed91cda01_45.png)

以下是为每个窗口同时计算最小值、最大值、均值和标准差的示例：
```python
# 创建新的DataFrame来存储窗口统计结果
window_stats = pd.DataFrame()
window_stats[‘min’] = df_clean[‘column_name’].rolling(window=window_size).min()
window_stats[‘max’] = df_clean[‘column_name’].rolling(window=window_size).max()
window_stats[‘mean’] = df_clean[‘column_name’].rolling(window=window_size).mean()
window_stats[‘std’] = df_clean[‘column_name’].rolling(window=window_size).std()
# 查看最后几行结果
print(window_stats.tail())
```
通过这种方式，我们可以高效地得到基于滑动窗口的多种特征，这些特征在量化策略开发和特征工程中非常有用。

![](img/56d0f8104b59f01a8fd0ec0ed91cda01_47.png)

## 总结

![](img/56d0f8104b59f01a8fd0ec0ed91cda01_49.png)

![](img/56d0f8104b59f01a8fd0ec0ed91cda01_51.png)

![](img/56d0f8104b59f01a8fd0ec0ed91cda01_53.png)

本节课中我们一起学习了时间序列分析的两个重要操作。
1.  **重采样**：我们学会了使用`df.resample(freq).agg_func()`来改变数据的时间频率（如从天到周），并了解了如何通过`label`参数控制结果标签。
2.  **时间窗口操作**：我们掌握了使用`df.rolling(window).agg_func()`创建滑动窗口，并计算窗口内的统计指标（如最小值、最大值）。**关键点**是操作前务必使用`df.dropna()`清理缺失数据。

![](img/56d0f8104b59f01a8fd0ec0ed91cda01_55.png)

![](img/56d0f8104b59f01a8fd0ec0ed91cda01_57.png)

![](img/56d0f8104b59f01a8fd0ec0ed91cda01_59.png)

这些技能是进行金融时间序列分析、构建技术指标和准备机器学习特征的基础。在后续课程中，我们将运用这些技术进行更深入的因子分析和策略回测。