# 量化交易完全可自学教程：P7：时间序列重采样与窗口操作 📊

在本节课中，我们将要学习时间序列分析中的两个核心操作：**重采样**与**时间窗口**。通过调整数据的时间粒度与滑动窗口统计，我们可以从不同维度观察和分析金融数据，这是构建量化策略的重要基础。

![](img/372727f72e4385dd5e189d9e84fd1ece_1.png)

## 概述

![](img/372727f72e4385dd5e189d9e84fd1ece_3.png)

上一节我们介绍了时间序列的基本概念。本节中，我们来看看如何对时间序列数据进行**重采样**和**窗口操作**。重采样可以改变数据的时间频率（如从天到周），而窗口操作则允许我们计算滑动窗口内的统计指标（如移动平均）。

![](img/372727f72e4385dd5e189d9e84fd1ece_5.png)

![](img/372727f72e4385dd5e189d9e84fd1ece_7.png)

---

![](img/372727f72e4385dd5e189d9e84fd1ece_9.png)

![](img/372727f72e4385dd5e189d9e84fd1ece_11.png)

## 时间序列重采样操作

![](img/372727f72e4385dd5e189d9e84fd1ece_13.png)

正常的数据通常以天为单位。有时我们需要调整这个单位，例如，将按天统计的数据转换为按周、按月或按年统计。这可以通过对数据执行 **`resample`** 操作来实现。

重采样过程需要指定一个关键参数：新的时间单位。例如，可以指定以“周”（‘W’）为单位。同时，你需要指定在重采样后，如何聚合每个新时间单位内的数据，例如计算均值、第一个值或中位数。

以下是重采样的基本代码结构：
```python
# 假设 df 是一个以日期为索引的DataFrame
# 以周为单位进行重采样，并计算每周的均值
weekly_data = df.resample('W').mean()
print(weekly_data.head())
```

![](img/372727f72e4385dd5e189d9e84fd1ece_15.png)

![](img/372727f72e4385dd5e189d9e84fd1ece_17.png)

执行上述代码后，你将得到以周为单位的重采样结果。原始数据中，如果某周的前几天没有数据，重采样操作会基于该周内已有的数据进行计算。

我们不仅可以按周重采样，还可以按月（‘M’）、按年（‘Y’）或其他频率进行。例如：
```python
monthly_data = df.resample('M').mean()
```

![](img/372727f72e4385dd5e189d9e84fd1ece_19.png)

![](img/372727f72e4385dd5e189d9e84fd1ece_21.png)

![](img/372727f72e4385dd5e189d9e84fd1ece_23.png)

在重采样时，有一个重要参数 **`label`** 需要注意。它决定了新时间段的标签使用该时间段的起始日（‘left’）还是结束日（‘right’）。

![](img/372727f72e4385dd5e189d9e84fd1ece_25.png)

以下是两种标签方式的对比：
```python
# 默认使用右标签（‘right’），即用时间段的最后一天作为标签
monthly_right = df.resample('M', label='right').mean()
# 使用左标签（‘left’），即用时间段的起始天作为标签
monthly_left = df.resample('M', label='left').mean()
```

使用‘left’标签时，可能会出现标签日期在原始数据中不存在的情况（例如，用1月1日代表整个1月，但1月1日可能无数据）。因此，在大多数分析场景下，使用默认的 **`label='right'`** 更为合理，因为它用已发生的时间段末尾作为标签，更符合观察逻辑。当然，具体选择需根据任务需求决定。

总而言之，时间序列重采样操作简单直接，可以让我们灵活地将数据转换到所需的任何时间频率。

![](img/372727f72e4385dd5e189d9e84fd1ece_27.png)

---

![](img/372727f72e4385dd5e189d9e84fd1ece_29.png)

## 时间窗口（滑动窗口）操作

![](img/372727f72e4385dd5e189d9e84fd1ece_31.png)

接下来，我们探讨时间窗口操作。假设我们有一系列数据（例如100天的股价）。窗口操作的意思是：首先，我们截取前10天数据作为一个窗口；然后，窗口向后滑动一天，截取第2天到第11天的数据作为第二个窗口；接着再滑动，截取第3天到第12天的数据，以此类推。这被称为**滑动窗口**或**滚动窗口**。

![](img/372727f72e4385dd5e189d9e84fd1ece_33.png)

以下是实现滑动窗口操作的基本步骤。首先，我们需要数据并定义一个窗口大小（例如10天）：
```python
window_size = 10
```

![](img/372727f72e4385dd5e189d9e84fd1ece_35.png)

然后，对数据的某一列使用 **`.rolling()`** 方法：
```python
# 对‘close’列进行窗口大小为10的滚动操作
rolling_window = df['close'].rolling(window=window_size)
```

![](img/372727f72e4385dd5e189d9e84fd1ece_37.png)

![](img/372727f72e4385dd5e189d9e84fd1ece_39.png)

`.rolling()` 方法返回的是一个窗口对象。要得到有意义的统计结果，我们需要对这个对象应用聚合函数。例如，计算每个窗口的最小值：
```python
min_values = rolling_window.min()
print(min_values.head(15))
```

![](img/372727f72e4385dd5e189d9e84fd1ece_41.png)

![](img/372727f72e4385dd5e189d9e84fd1ece_43.png)

在初次运行上述代码时，你可能会发现前几个结果是 `NaN`（空值）。这是因为前9个窗口的数据不足10个，无法计算。为了后续分析方便，我们通常需要先处理数据中的缺失值。

![](img/372727f72e4385dd5e189d9e84fd1ece_45.png)

在进行窗口操作前，一个良好的实践是**先对数据进行预处理，剔除缺失值**：
```python
# 删除含有NaN值的行
df_clean = df.dropna()
# 使用清洗后的数据进行窗口操作
rolling_window_clean = df_clean['close'].rolling(window=window_size)
min_values_clean = rolling_window_clean.min()
```

![](img/372727f72e4385dd5e189d9e84fd1ece_47.png)

![](img/372727f72e4385dd5e189d9e84fd1ece_49.png)

现在，`min_values_clean` 中的前几个值将不再是 `NaN`（前提是清洗后的数据从起始点就有足够长的连续数据）。我们可以基于清洗后的数据，为每个窗口计算多种统计指标。

![](img/372727f72e4385dd5e189d9e84fd1ece_51.png)

以下是如何为数据同时添加多个窗口统计列（如最小值、最大值、均值、标准差）的示例：
```python
df_clean['rolling_min'] = df_clean['close'].rolling(window=window_size).min()
df_clean['rolling_max'] = df_clean['close'].rolling(window=window_size).max()
df_clean['rolling_mean'] = df_clean['close'].rolling(window=window_size).mean()
df_clean['rolling_std'] = df_clean['close'].rolling(window=window_size).std()
# 查看结果
print(df_clean.tail())
```

![](img/372727f72e4385dd5e189d9e84fd1ece_53.png)

![](img/372727f72e4385dd5e189d9e84fd1ece_55.png)

通过以上操作，我们就能基于滑动窗口，计算出时间序列上各种有意义的统计特征，这些特征常用于构建技术指标或进行趋势分析。

![](img/372727f72e4385dd5e189d9e84fd1ece_57.png)

---

![](img/372727f72e4385dd5e189d9e84fd1ece_59.png)

![](img/372727f72e4385dd5e189d9e84fd1ece_61.png)

## 总结

![](img/372727f72e4385dd5e189d9e84fd1ece_63.png)

![](img/372727f72e4385dd5e189d9e84fd1ece_65.png)

本节课中我们一起学习了时间序列分析的两个关键操作：
1.  **重采样**：使用 **`df.resample()`** 方法改变数据的时间频率（如日→周），并通过 `label` 参数控制时间标签的显示方式。
2.  **时间窗口操作**：使用 **`.rolling()`** 方法创建滑动窗口，并计算窗口内的统计指标（如最小值、移动平均）。**切记，在进行窗口计算前，应先处理数据中的缺失值**，以确保结果的准确性。

![](img/372727f72e4385dd5e189d9e84fd1ece_67.png)

![](img/372727f72e4385dd5e189d9e84fd1ece_69.png)

掌握这两种操作，能帮助你从不同时间尺度洞察数据规律，为后续的量化建模打下坚实基础。