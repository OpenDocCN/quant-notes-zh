# Python机器学习与量化交易：P7：8.8.2-时间序列重采样与窗口操作 📊

在本节课中，我们将要学习时间序列数据处理中的两个核心操作：**重采样**与**时间窗口操作**。这些操作对于调整数据频率、计算滚动统计指标至关重要，是金融数据分析与量化交易建模的基础。

![](img/84fa6a97f69a1e477497d41bcc41f8e6_1.png)

![](img/84fa6a97f69a1e477497d41bcc41f8e6_3.png)

---

## 重采样操作

![](img/84fa6a97f69a1e477497d41bcc41f8e6_5.png)

![](img/84fa6a97f69a1e477497d41bcc41f8e6_7.png)

上一节我们介绍了时间序列的基本概念，本节中我们来看看如何改变数据的时间频率，即重采样。

![](img/84fa6a97f69a1e477497d41bcc41f8e6_9.png)

重采样是指将时间序列数据从一个频率转换到另一个频率的过程。例如，原始数据是按天统计的，我们可以将其转换为按周、按月或以年为单位的统计数据。

![](img/84fa6a97f69a1e477497d41bcc41f8e6_11.png)

执行重采样操作，你需要使用 `resample` 方法，并至少指定一个参数，即新的时间频率单位。同时，你需要指定在重采样后，如何聚合每个时间段内的数据，例如计算均值、第一个值、中位数或其他统计指标。

以下是重采样的基本代码格式：
```python
# 假设 df 是一个以日期为索引的 DataFrame
# 以周为单位进行重采样，并计算每周的均值
weekly_data = df.resample('W').mean()
```

![](img/84fa6a97f69a1e477497d41bcc41f8e6_13.png)

![](img/84fa6a97f69a1e477497d41bcc41f8e6_15.png)

例如，将数据从按天统计转换为按周统计后，会得到每周的聚合结果。原始数据中可能从某一天开始，重采样后，结果会以周为窗口进行展示。

我们还可以将频率调整为月：
```python
monthly_data = df.resample('M').mean()
```

![](img/84fa6a97f69a1e477497d41bcc41f8e6_17.png)

![](img/84fa6a97f69a1e477497d41bcc41f8e6_19.png)

![](img/84fa6a97f69a1e477497d41bcc41f8e6_21.png)

在重采样时，有一个重要的参数是 `label`，它决定了每个聚合结果的标签（索引）使用窗口的哪一端。`label='right'`（默认）使用窗口的结束日期作为标签，`label='left'` 则使用窗口的开始日期。

![](img/84fa6a97f69a1e477497d41bcc41f8e6_23.png)

以下是不同标签的示例：
```python
# 使用窗口结束日期作为标签（默认）
monthly_right = df.resample('M', label='right').mean()
# 使用窗口开始日期作为标签
monthly_left = df.resample('M', label='left').mean()
```

选择 `left` 或 `right` 取决于具体任务的需求。在金融数据分析中，通常使用 `label='right'`，因为它更符合“用过去一段时间的数据来描述该时间段结束时的情况”这一逻辑。

---

![](img/84fa6a97f69a1e477497d41bcc41f8e6_25.png)

![](img/84fa6a97f69a1e477497d41bcc41f8e6_27.png)

## 时间窗口（滑动窗口）操作

接下来，我们学习时间窗口操作，也称为滑动窗口操作。这与重采样不同，它是在一个固定长度的窗口上滑动，并计算窗口内的统计量。

![](img/84fa6a97f69a1e477497d41bcc41f8e6_29.png)

例如，我们有一个包含100天数据的时间序列。我们可以定义一个长度为10天的窗口。第一个窗口包含第1天到第10天的数据，第二个窗口包含第2天到第11天的数据，第三个窗口包含第3天到第12天的数据，以此类推。窗口每次向后滑动一天。

![](img/84fa6a97f69a1e477497d41bcc41f8e6_31.png)

![](img/84fa6a97f69a1e477497d41bcc41f8e6_33.png)

以下是实现滑动窗口操作的基本步骤：

首先，我们需要对数据进行预处理，特别是处理缺失值，因为缺失值会影响窗口计算的结果。
```python
# 假设 data 是原始的 DataFrame
# 删除缺失值
data_clean = data.dropna()
```

![](img/84fa6a97f69a1e477497d41bcc41f8e6_35.png)

![](img/84fa6a97f69a1e477497d41bcc41f8e6_37.png)

![](img/84fa6a97f69a1e477497d41bcc41f8e6_39.png)

然后，使用 `rolling` 方法创建滑动窗口。你需要指定窗口的大小。
```python
window_size = 10
# 对清洗后的某一列数据创建滚动窗口对象
rolling_window = data_clean['column_name'].rolling(window=window_size)
```

![](img/84fa6a97f69a1e477497d41bcc41f8e6_41.png)

![](img/84fa6a97f69a1e477497d41bcc41f8e6_43.png)

创建窗口对象后，我们可以对其应用各种统计函数，例如计算每个窗口的最小值、最大值、均值或标准差。
```python
# 计算每个窗口的最小值
min_values = rolling_window.min()
# 计算每个窗口的最大值
max_values = rolling_window.max()
# 计算每个窗口的均值
mean_values = rolling_window.mean()
# 计算每个窗口的标准差
std_values = rolling_window.std()
```

![](img/84fa6a97f69a1e477497d41bcc41f8e6_45.png)

![](img/84fa6a97f69a1e477497d41bcc41f8e6_47.png)

为了方便分析，我们可以将这些滚动统计结果作为新列添加到数据集中。
```python
data_clean['rolling_min'] = rolling_window.min()
data_clean['rolling_max'] = rolling_window.max()
data_clean['rolling_mean'] = rolling_window.mean()
data_clean['rolling_std'] = rolling_window.std()
```

![](img/84fa6a97f69a1e477497d41bcc41f8e6_49.png)

需要注意的是，在窗口操作的初始阶段，当数据点不足以填满整个窗口时（例如前9天对于10天的窗口），计算结果会是 `NaN`。这是正常现象。

![](img/84fa6a97f69a1e477497d41bcc41f8e6_51.png)

![](img/84fa6a97f69a1e477497d41bcc41f8e6_53.png)

---

![](img/84fa6a97f69a1e477497d41bcc41f8e6_55.png)

## 总结

![](img/84fa6a97f69a1e477497d41bcc41f8e6_57.png)

![](img/84fa6a97f69a1e477497d41bcc41f8e6_59.png)

本节课中我们一起学习了时间序列数据处理的两个重要技术。

![](img/84fa6a97f69a1e477497d41bcc41f8e6_61.png)

![](img/84fa6a97f69a1e477497d41bcc41f8e6_63.png)

1.  **重采样**：通过 `resample()` 方法改变数据的时间频率（如从天到周、月），并配合聚合函数（如 `mean()`）得到新频率下的数据。需要注意 `label` 参数对结果标签的影响。
2.  **时间窗口操作**：通过 `rolling()` 方法创建固定长度的滑动窗口，用于计算滚动统计指标（如最小值、最大值、均值）。**在进行窗口计算前，务必对数据进行预处理，处理缺失值**，以避免结果中出现大量 `NaN`。

![](img/84fa6a97f69a1e477497d41bcc41f8e6_65.png)

![](img/84fa6a97f69a1e477497d41bcc41f8e6_67.png)

掌握重采样和窗口操作，能够帮助你灵活地处理和分析不同时间尺度下的金融数据，为后续的建模和策略开发打下坚实基础。