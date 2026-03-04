# Python金融分析与量化交易实战：7.4：时间序列重采样与窗口操作 📊

在本节课中，我们将学习时间序列分析中的两个核心操作：重采样与时间窗口操作。我们将了解如何改变数据的时间频率，以及如何通过滑动窗口计算统计指标。

---

![](img/87b4c72158873246be5fe2b009a0da42_1.png)

## 概述

![](img/87b4c72158873246be5fe2b009a0da42_3.png)

上一节我们介绍了时间序列的基本处理。本节中，我们来看看如何对时间序列进行**重采样**和**时间窗口**操作。重采样可以改变数据的时间粒度（例如，从天到周），而时间窗口操作则允许我们计算滑动窗口内的统计量（如均值、最小值等）。

---

![](img/87b4c72158873246be5fe2b009a0da42_5.png)

![](img/87b4c72158873246be5fe2b009a0da42_7.png)

## 重采样操作

![](img/87b4c72158873246be5fe2b009a0da42_9.png)

重采样是指改变时间序列数据频率的过程。例如，原始数据以天为单位，我们可以将其转换为以周、月或年为单位。在Pandas中，这通过 `resample` 方法实现。

进行重采样时，需要指定新的时间频率单位。同时，你需要决定如何聚合每个新时间区间内的数据，例如使用均值、第一个值或中位数等指标。

![](img/87b4c72158873246be5fe2b009a0da42_11.png)

以下是重采样的基本代码格式：

```python
# 假设 df 是一个以日期为索引的DataFrame
# 以周为单位进行重采样，并计算每周的均值
weekly_data = df.resample('W').mean()
```

让我们看一个具体例子。假设我们有一份按天统计的数据，现在想转换为按周统计。

![](img/87b4c72158873246be5fe2b009a0da42_13.png)

```python
# 执行重采样，以周('W')为单位，并显示前几行结果
weekly_resample = data.resample('W').mean()
print(weekly_resample.head())
```

![](img/87b4c72158873246be5fe2b009a0da42_15.png)

执行后，结果会显示每周的聚合数据。需要注意的是，由于原始数据起始日期可能不是一周的开始，重采样后的第一个数据点可能会从有数据的日期开始计算。

![](img/87b4c72158873246be5fe2b009a0da42_17.png)

我们不仅可以按周重采样，还可以按月('M')、年('Y')等单位进行。

![](img/87b4c72158873246be5fe2b009a0da42_19.png)

```python
# 以月为单位进行重采样
monthly_resample = data.resample('M').mean()
print(monthly_resample.head())
```

![](img/87b4c72158873246be5fe2b009a0da42_21.png)

![](img/87b4c72158873246be5fe2b009a0da42_23.png)

在重采样时，还有一个重要参数是 `label`。它决定了每个聚合区间使用哪一天作为标签。

*   **`label='right'`**（默认）：使用区间的最后一天作为标签。
*   **`label='left'`**：使用区间的第一天作为标签。

例如，按月重采样时，`label='right'` 会用当月的最后一天（如1月31日）来标识该月的数据；而 `label='left'` 则会用当月的第一天（如1月1日）。选择哪种方式取决于分析逻辑，通常使用 `label='right'` 更为直观，因为它表示该时间段结束时的情况。

```python
# 使用‘left’标签进行按月重采样
monthly_resample_left = data.resample('M', label='left').mean()
print(monthly_resample_left.head())
```

---

![](img/87b4c72158873246be5fe2b009a0da42_25.png)

![](img/87b4c72158873246be5fe2b009a0da42_27.png)

## 时间窗口（滑动窗口）操作

接下来，我们学习时间窗口操作。这不是重采样，而是创建一个滑动的数据窗口。例如，我们有一个100天的数据序列，可以设置一个长度为10天的窗口。

第一个窗口包含第1到第10天的数据，第二个窗口包含第2到第11天的数据，第三个窗口包含第3到第12天的数据，以此类推。这种操作对于计算移动平均线等指标非常有用。

![](img/87b4c72158873246be5fe2b009a0da42_29.png)

在Pandas中，我们使用 `rolling` 方法来实现滑动窗口。

![](img/87b4c72158873246be5fe2b009a0da42_31.png)

以下是创建滑动窗口并计算统计量的步骤：

首先，定义窗口大小并应用 `rolling` 方法。

![](img/87b4c72158873246be5fe2b009a0da42_33.png)

```python
window_size = 10
# 对数据的某一列应用滚动窗口，并计算每个窗口的最小值
rolling_min = data['column_name'].rolling(window=window_size).min()
print(rolling_min.head(15))
```

![](img/87b4c72158873246be5fe2b009a0da42_35.png)

**重要提示**：在应用窗口操作前，必须处理缺失值（NaN）。如果原始数据中存在缺失值，会导致窗口计算结果也出现NaN。因此，第一步通常是清理数据。

![](img/87b4c72158873246be5fe2b009a0da42_37.png)

```python
# 清理缺失值
data_clean = data.dropna()
# 然后对清理后的数据进行窗口操作
rolling_min_clean = data_clean['column_name'].rolling(window=window_size).min()
```

![](img/87b4c72158873246be5fe2b009a0da42_39.png)

![](img/87b4c72158873246be5fe2b009a0da42_41.png)

我们可以一次性为每个窗口计算多个统计指标，并将结果添加到新列中。

![](img/87b4c72158873246be5fe2b009a0da42_43.png)

以下是同时计算最小值、最大值、均值和标准差的示例：

```python
# 在清理后的数据上计算多个滚动统计量
data_clean['rolling_min'] = data_clean['column_name'].rolling(window=window_size).min()
data_clean['rolling_max'] = data_clean['column_name'].rolling(window=window_size).max()
data_clean['rolling_mean'] = data_clean['column_name'].rolling(window=window_size).mean()
data_clean['rolling_std'] = data_clean['column_name'].rolling(window=window_size).std()

![](img/87b4c72158873246be5fe2b009a0da42_45.png)

# 查看结果的后几行
print(data_clean.tail())
```

![](img/87b4c72158873246be5fe2b009a0da42_47.png)

这样，我们就得到了每个滑动窗口内的关键统计特征。

---

![](img/87b4c72158873246be5fe2b009a0da42_49.png)

## 总结

![](img/87b4c72158873246be5fe2b009a0da42_51.png)

![](img/87b4c72158873246be5fe2b009a0da42_53.png)

本节课中我们一起学习了时间序列的两个重要操作。

![](img/87b4c72158873246be5fe2b009a0da42_55.png)

![](img/87b4c72158873246be5fe2b009a0da42_57.png)

1.  **重采样**：使用 `resample()` 方法改变数据的时间频率（如日→周），并可指定聚合方式和标签位置。
2.  **时间窗口操作**：使用 `rolling()` 方法创建滑动窗口，以计算窗口内的统计指标（如最小值、均值等）。进行此操作前，务必先处理数据中的缺失值。

![](img/87b4c72158873246be5fe2b009a0da42_59.png)

掌握这两种方法，能帮助你更灵活地从不同时间维度分析和理解金融时间序列数据。