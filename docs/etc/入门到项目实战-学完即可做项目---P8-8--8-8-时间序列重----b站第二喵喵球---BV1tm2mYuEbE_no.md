# 量化交易实战：P8：时间序列重采样与窗口操作 📊

在本节课中，我们将要学习时间序列分析中的两个核心操作：**重采样**与**时间窗口操作**。这些技术对于将数据转换到不同时间尺度（如从天到周、月）以及计算滑动统计指标（如移动平均值）至关重要，是金融数据分析与量化建模的基础。

![](img/373cff992c55b8f60c57c832c1a1a37b_1.png)

## 概述

![](img/373cff992c55b8f60c57c832c1a1a37b_3.png)

上一节我们介绍了时间序列的基本概念。本节中，我们来看看如何对时间序列数据进行**重采样**和**滑动窗口**计算。重采样可以改变数据的时间频率，而窗口操作则用于计算一系列连续时间区间内的统计指标。

![](img/373cff992c55b8f60c57c832c1a1a37b_5.png)

![](img/373cff992c55b8f60c57c832c1a1a37b_7.png)

## 时间序列重采样操作 🔄

![](img/373cff992c55b8f60c57c832c1a1a37b_9.png)

![](img/373cff992c55b8f60c57c832c1a1a37b_11.png)

重采样是指改变时间序列数据频率的过程。例如，原始数据可能是按天统计的，我们可以将其转换为按周、按月或按年进行统计。

### 如何进行重采样

![](img/373cff992c55b8f60c57c832c1a1a37b_13.png)

![](img/373cff992c55b8f60c57c832c1a1a37b_15.png)

在Pandas中，使用 `resample()` 方法进行重采样。你需要指定一个新的时间频率规则（rule），并选择一个聚合函数（如均值、求和）来合并原频率下的数据。

以下是重采样的基本代码公式：

![](img/373cff992c55b8f60c57c832c1a1a37b_17.png)

```python
# 假设df是一个以日期为索引的DataFrame
# ‘W’ 表示按周重采样，‘M’ 表示按月重采样
weekly_data = df.resample('W').mean()  # 按周重采样并计算每周均值
monthly_data = df.resample('M').sum()   # 按月重采样并计算每月总和
```

![](img/373cff992c55b8f60c57c832c1a1a37b_19.png)

![](img/373cff992c55b8f60c57c832c1a1a37b_21.png)

### 重采样中的标签设置

![](img/373cff992c55b8f60c57c832c1a1a37b_23.png)

重采样后，每个新时间区间的索引标签默认使用该区间的**结束日期**（右边界）。你可以通过 `label` 参数将其设置为起始日期（左边界）。

![](img/373cff992c55b8f60c57c832c1a1a37b_25.png)

![](img/373cff992c55b8f60c57c832c1a1a37b_27.png)

以下是不同标签设置的示例：

![](img/373cff992c55b8f60c57c832c1a1a37b_29.png)

```python
# 默认使用右边界标签
monthly_right = df.resample('M', label='right').mean()
# 使用左边界标签
monthly_left = df.resample('M', label='left').mean()
```

**核心概念**：`label` 参数决定了新数据点的索引是使用时间窗口的起始点（`'left'`）还是结束点（`'right'`）。在金融分析中，通常使用 `'right'` 更为直观，因为它表示截至该日期为止的统计结果。

![](img/373cff992c55b8f60c57c832c1a1a37b_31.png)

## 时间窗口（滑动窗口）操作 🪟

![](img/373cff992c55b8f60c57c832c1a1a37b_33.png)

时间窗口操作，也称为滑动窗口操作，用于计算数据在一个连续、滑动的固定长度窗口内的统计量。例如，计算股票价格的10日移动平均线。

![](img/373cff992c55b8f60c57c832c1a1a37b_35.png)

### 如何进行窗口操作

![](img/373cff992c55b8f60c57c832c1a1a37b_37.png)

Pandas提供了 `rolling()` 方法来创建滑动窗口视图，然后可以对其应用各种统计函数。

![](img/373cff992c55b8f60c57c832c1a1a37b_39.png)

![](img/373cff992c55b8f60c57c832c1a1a37b_41.png)

以下是创建窗口并计算统计值的基本代码公式：

![](img/373cff992c55b8f60c57c832c1a1a37b_43.png)

![](img/373cff992c55b8f60c57c832c1a1a37b_45.png)

```python
# 创建一个窗口大小为10的滚动窗口对象
rolling_window = df['column_name'].rolling(window=10)
# 计算每个窗口的最小值
min_values = rolling_window.min()
# 计算每个窗口的最大值
max_values = rolling_window.max()
# 计算每个窗口的平均值
mean_values = rolling_window.mean()
```

![](img/373cff992c55b8f60c57c832c1a1a37b_47.png)

### 窗口操作的预处理要点

![](img/373cff992c55b8f60c57c832c1a1a37b_49.png)

![](img/373cff992c55b8f60c57c832c1a1a37b_51.png)

在进行窗口计算前，**必须处理数据中的缺失值**。否则，包含缺失值的窗口会导致计算结果也为缺失值。

![](img/373cff992c55b8f60c57c832c1a1a37b_53.png)

![](img/373cff992c55b8f60c57c832c1a1a37b_55.png)

以下是预处理和窗口计算的完整步骤：

![](img/373cff992c55b8f60c57c832c1a1a37b_57.png)

![](img/373cff992c55b8f60c57c832c1a1a37b_59.png)

```python
# 1. 处理缺失值
df_clean = df.dropna()

![](img/373cff992c55b8f60c57c832c1a1a37b_61.png)

# 2. 定义窗口大小
window_size = 10

![](img/373cff992c55b8f60c57c832c1a1a37b_63.png)

# 3. 计算各种窗口统计指标并添加到新列
df_clean['min'] = df_clean['target_column'].rolling(window=window_size).min()
df_clean['max'] = df_clean['target_column'].rolling(window=window_size).max()
df_clean['mean'] = df_clean['target_column'].rolling(window=window_size).mean()
df_clean['std'] = df_clean['target_column'].rolling(window=window_size).std()

![](img/373cff992c55b8f60c57c832c1a1a37b_65.png)

![](img/373cff992c55b8f60c57c832c1a1a37b_67.png)

# 查看结果
print(df_clean.tail())
```

![](img/373cff992c55b8f60c57c832c1a1a37b_69.png)

**核心概念**：`rolling(window=10)` 创建了一个宽度为10个时间单位的“窗口”，这个窗口沿着时间轴逐行滑动。对于每一行，计算都基于当前行及前9行数据（共10行）进行。

![](img/373cff992c55b8f60c57c832c1a1a37b_71.png)

![](img/373cff992c55b8f60c57c832c1a1a37b_73.png)

## 总结

![](img/373cff992c55b8f60c57c832c1a1a37b_75.png)

![](img/373cff992c55b8f60c57c832c1a1a37b_77.png)

本节课中我们一起学习了时间序列分析的两个关键操作：
1.  **重采样**：通过 `resample()` 方法改变数据的时间频率，并使用 `label` 参数控制新索引的标签。
2.  **时间窗口操作**：通过 `rolling()` 方法创建滑动窗口，计算窗口内的统计指标（如最小值、最大值、均值、标准差）。**切记，进行窗口计算前需先使用 `dropna()` 等方法清理数据中的缺失值。**

![](img/373cff992c55b8f60c57c832c1a1a37b_79.png)

![](img/373cff992c55b8f60c57c832c1a1a37b_81.png)

掌握重采样和窗口操作，能够帮助你灵活地转换数据视角，并提取出有意义的趋势与特征，为后续的量化策略开发打下坚实基础。