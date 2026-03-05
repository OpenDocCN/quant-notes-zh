# Python金融分析量化交易：P8：2-时间序列重采样操作 📊

在本节课中，我们将学习时间序列分析中的两个核心操作：**重采样**与**时间窗口（滚动）操作**。这些操作能帮助我们将数据从一种时间频率转换到另一种，并计算基于滑动窗口的统计指标，是金融数据分析中处理时间序列数据的基础技能。

![](img/be49d7c3262e6083f76595dab03d8662_1.png)

## 概述

![](img/be49d7c3262e6083f76595dab03d8662_3.png)

上一节我们介绍了时间序列数据的基本处理。本节中，我们来看看如何改变数据的时间频率（重采样）以及如何基于滑动窗口计算统计量。掌握这些方法，能让我们从不同时间维度观察和分析数据趋势。

## 时间序列重采样 🔄

![](img/be49d7c3262e6083f76595dab03d8662_5.png)

![](img/be49d7c3262e6083f76595dab03d8662_7.png)

重采样是指将时间序列数据从一个频率转换到另一个频率的过程。例如，将按天统计的数据转换为按周、按月或按年统计的数据。

![](img/be49d7c3262e6083f76595dab03d8662_9.png)

### 基本操作

![](img/be49d7c3262e6083f76595dab03d8662_11.png)

对`DataFrame`执行重采样操作，需要使用`.resample()`方法。该方法至少需要一个参数，即指定新的时间频率单位。

**核心代码**：
```python
# 假设 df 是一个以日期为索引的 DataFrame
# 以‘周’（‘W’）为单位进行重采样，并计算每周的均值
weekly_data = df.resample('W').mean()
```

在上述代码中，`‘W’`表示以周为单位。你可以指定不同的聚合函数，如`.mean()`（均值）、`.first()`（第一个值）、`.median()`（中位数）等。

![](img/be49d7c3262e6083f76595dab03d8662_13.png)

![](img/be49d7c3262e6083f76595dab03d8662_15.png)

### 标签位置参数

重采样时，可以指定聚合结果的标签是基于时间窗口的起始点（`label=‘left’`）还是结束点（`label=‘right’`）。默认是`‘right’`。

![](img/be49d7c3262e6083f76595dab03d8662_17.png)

![](img/be49d7c3262e6083f76595dab03d8662_19.png)

**核心代码**：
```python
# 以‘月’（‘M’）为单位重采样，标签使用时间窗口的起始日
monthly_data_left = df.resample('M', label='left').mean()

![](img/be49d7c3262e6083f76595dab03d8662_21.png)

![](img/be49d7c3262e6083f76595dab03d8662_23.png)

# 以‘月’（‘M’）为单位重采样，标签使用时间窗口的结束日（默认）
monthly_data_right = df.resample('M').mean() # 等价于 label='right'
```

以下是两种标签方式的区别：
*   **`label=‘right’`**：使用时间窗口的结束日期作为该窗口聚合结果的索引标签。例如，一月份的数据聚合后，标签是1月31日。这更符合大多数观察逻辑。
*   **`label=‘left’`**：使用时间窗口的起始日期作为索引标签。例如，一月份的数据聚合后，标签是1月1日。

选择哪种方式取决于具体的分析任务需求，但在金融数据分析中，使用结束日期（`‘right’`）作为标签更为常见和直观。

## 时间窗口（滚动）操作 📈

![](img/be49d7c3262e6083f76595dab03d8662_25.png)

![](img/be49d7c3262e6083f76595dab03d8662_27.png)

时间窗口操作，也称为滚动窗口或滑动窗口操作，是指在一个固定大小的窗口上沿时间轴滑动，并对窗口内的数据进行计算。例如，计算每连续10个交易日的移动平均值。

### 基本操作

使用`.rolling()`方法可以轻松实现滚动窗口计算。你需要指定窗口的大小。

![](img/be49d7c3262e6083f76595dab03d8662_29.png)

![](img/be49d7c3262e6083f76595dab03d8662_31.png)

**核心代码**：
```python
window_size = 10
# 对‘Close’列计算10天的滚动窗口最小值
rolling_min = df['Close'].rolling(window=window_size).min()
```

![](img/be49d7c3262e6083f76595dab03d8662_33.png)

### 处理缺失值

在窗口操作的初期，当数据点不足以填满整个窗口时（例如，前9天对于10天窗口），计算结果会是`NaN`（缺失值）。为了得到清晰的结果，通常需要先处理原始数据中的缺失值。

![](img/be49d7c3262e6083f76595dab03d8662_35.png)

![](img/be49d7c3262e6083f76595dab03d8662_37.png)

**核心代码**：
```python
# 首先，删除原始数据中的缺失值
df_clean = df.dropna()

![](img/be49d7c3262e6083f76595dab03d8662_39.png)

![](img/be49d7c3262e6083f76595dab03d8662_41.png)

# 然后，在清理后的数据上进行滚动计算
window_size = 10
rolling_min_clean = df_clean['Close'].rolling(window=window_size).min()
```

![](img/be49d7c3262e6083f76595dab03d8662_43.png)

![](img/be49d7c3262e6083f76595dab03d8662_45.png)

### 计算多种滚动统计量

![](img/be49d7c3262e6083f76595dab03d8662_47.png)

你可以一次性计算多个滚动统计指标，并将它们添加到数据框中。

![](img/be49d7c3262e6083f76595dab03d8662_49.png)

**核心代码**：
```python
window_size = 10

![](img/be49d7c3262e6083f76595dab03d8662_51.png)

![](img/be49d7c3262e6083f76595dab03d8662_53.png)

df_clean['Rolling_Min'] = df_clean['Close'].rolling(window=window_size).min()
df_clean['Rolling_Max'] = df_clean['Close'].rolling(window=window_size).max()
df_clean['Rolling_Mean'] = df_clean['Close'].rolling(window=window_size).mean()
df_clean['Rolling_Std'] = df_clean['Close'].rolling(window=window_size).std()

![](img/be49d7c3262e6083f76595dab03d8662_55.png)

# 查看添加了新列的数据前几行
print(df_clean.head(15))
```

通过以上代码，我们为数据添加了滚动窗口内的最小值、最大值、均值和标准差等统计量。

![](img/be49d7c3262e6083f76595dab03d8662_57.png)

![](img/be49d7c3262e6083f76595dab03d8662_59.png)

## 总结

![](img/be49d7c3262e6083f76595dab03d8662_61.png)

![](img/be49d7c3262e6083f76595dab03d8662_63.png)

本节课中我们一起学习了时间序列分析的两个重要操作。
1.  **重采样**：使用`.resample()`方法改变数据的时间频率（如日变周、月），并通过`label`参数控制聚合结果的标签。
2.  **时间窗口操作**：使用`.rolling()`方法创建滑动窗口，可以计算窗口内的各种统计指标（如移动平均、移动标准差）。进行此类操作前，务必注意处理数据中的缺失值，以避免结果中出现大量`NaN`。

![](img/be49d7c3262e6083f76595dab03d8662_65.png)

![](img/be49d7c3262e6083f76595dab03d8662_67.png)

掌握这些技能，能够帮助你从不同时间尺度洞察金融时间序列数据的特征与规律，为后续的量化分析与建模打下坚实基础。