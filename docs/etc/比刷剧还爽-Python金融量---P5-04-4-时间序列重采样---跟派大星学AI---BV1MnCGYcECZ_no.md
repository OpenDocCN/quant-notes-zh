# 量化交易全教程：P5：04-4-时间序列重采样与窗口操作 📊

在本节课中，我们将学习时间序列分析中的两个核心操作：重采样与时间窗口操作。重采样允许我们改变数据的时间频率，而时间窗口操作则用于计算滑动窗口内的统计指标。掌握这些技能对于金融数据的分析和特征工程至关重要。

![](img/b61d716d4bd6d52ba8824a74b67b34af_1.png)

![](img/b61d716d4bd6d52ba8824a74b67b34af_3.png)

## 重采样操作

上一节我们介绍了时间序列的基础操作，本节中我们来看看如何改变数据的时间粒度，即重采样。重采样是指将时间序列数据从一个频率转换到另一个频率的过程。例如，将每日数据聚合为每周或每月数据。

![](img/b61d716d4bd6d52ba8824a74b67b34af_5.png)

![](img/b61d716d4bd6d52ba8824a74b67b34af_7.png)

以下是重采样的基本语法：
```python
# 假设 df 是一个以日期为索引的DataFrame
# 以‘W’（周）为频率进行重采样，并计算均值
weekly_data = df.resample('W').mean()
```

![](img/b61d716d4bd6d52ba8824a74b67b34af_9.png)

在重采样时，我们需要指定新的时间频率，并选择一个聚合函数（如均值、总和、最大值等）来合并原始数据。

![](img/b61d716d4bd6d52ba8824a74b67b34af_11.png)

### 标签参数

在重采样操作中，`label`参数决定了聚合后数据点的标签日期。它有两个主要选项：`'left'`和`'right'`。

![](img/b61d716d4bd6d52ba8824a74b67b34af_13.png)

![](img/b61d716d4bd6d52ba8824a74b67b34af_15.png)

以下是`label`参数不同设置的效果：
```python
# 默认使用‘right’，标签为时间窗口的结束日期
monthly_data_right = df.resample('M', label='right').mean()

# 使用‘left’，标签为时间窗口的开始日期
monthly_data_left = df.resample('M', label='left').mean()
```

![](img/b61d716d4bd6d52ba8824a74b67b34af_17.png)

![](img/b61d716d4bd6d52ba8824a74b67b34af_19.png)

![](img/b61d716d4bd6d52ba8824a74b67b34af_21.png)

*   **`label='right'`（默认）**：使用时间窗口的**结束日期**作为该聚合数据的标签。例如，对于一月份的数据，标签是1月31日。这通常更符合逻辑，因为它代表该时间段结束时的汇总情况。
*   **`label='left'`**：使用时间窗口的**开始日期**作为标签。例如，对于一月份的数据，标签是1月1日。

![](img/b61d716d4bd6d52ba8824a74b67b34af_23.png)

选择哪种标签取决于具体的分析需求，但在金融数据分析中，使用`‘right’`更为常见。

## 时间窗口（滑动窗口）操作

了解了如何改变数据频率后，我们来看看如何在固定的时间窗口内进行滑动计算。时间窗口操作不是改变数据频率，而是在一个固定大小的“窗口”内（例如过去10天）沿着时间轴滑动，并计算每个窗口内的统计量。

![](img/b61d716d4bd6d52ba8824a74b67b34af_25.png)

![](img/b61d716d4bd6d52ba8824a74b67b34af_27.png)

以下是创建滑动窗口并计算统计量的基本步骤：
```python
# 1. 定义窗口大小
window_size = 10

# 2. 计算滑动窗口内的最小值
rolling_min = df['column_name'].rolling(window=window_size).min()

![](img/b61d716d4bd6d52ba8824a74b67b34af_29.png)

# 3. 计算其他统计量
rolling_max = df['column_name'].rolling(window=window_size).max()
rolling_mean = df['column_name'].rolling(window=window_size).mean()
rolling_std = df['column_name'].rolling(window=window_size).std()
```

![](img/b61d716d4bd6d52ba8824a74b67b34af_31.png)

### 数据预处理的重要性

![](img/b61d716d4bd6d52ba8824a74b67b34af_33.png)

在进行窗口计算前，必须处理数据中的缺失值（NaN）。因为只要窗口内存在一个缺失值，该窗口的计算结果就会是NaN。

![](img/b61d716d4bd6d52ba8824a74b67b34af_35.png)

以下是处理缺失值的步骤：
```python
# 删除含有缺失值的行
df_clean = df.dropna()

![](img/b61d716d4bd6d52ba8824a74b67b34af_37.png)

![](img/b61d716d4bd6d52ba8824a74b67b34af_39.png)

# 在清理后的数据上进行窗口计算
rolling_min_clean = df_clean['column_name'].rolling(window=window_size).min()
```

![](img/b61d716d4bd6d52ba8824a74b67b34af_41.png)

### 创建包含多个窗口统计量的DataFrame

![](img/b61d716d4bd6d52ba8824a74b67b34af_43.png)

我们可以将计算出的各种窗口统计量添加到原始DataFrame中，以便进行后续分析。

![](img/b61d716d4bd6d52ba8824a74b67b34af_45.png)

以下是如何将多个窗口统计量添加到DataFrame中：
```python
# 假设 df_clean 是已经清理过的DataFrame
window_size = 10

![](img/b61d716d4bd6d52ba8824a74b67b34af_47.png)

![](img/b61d716d4bd6d52ba8824a74b67b34af_49.png)

df_clean['rolling_min'] = df_clean['column_name'].rolling(window=window_size).min()
df_clean['rolling_max'] = df_clean['column_name'].rolling(window=window_size).max()
df_clean['rolling_mean'] = df_clean['column_name'].rolling(window=window_size).mean()
df_clean['rolling_std'] = df_clean['column_name'].rolling(window=window_size).std()

![](img/b61d716d4bd6d52ba8824a74b67b34af_51.png)

![](img/b61d716d4bd6d52ba8824a74b67b34af_53.png)

# 查看结果（通常查看尾部，因为前 window_size-1 个窗口值为NaN）
print(df_clean.tail())
```

![](img/b61d716d4bd6d52ba8824a74b67b34af_55.png)

![](img/b61d716d4bd6d52ba8824a74b67b34af_57.png)

本节课中我们一起学习了时间序列的两个重要操作。首先，我们掌握了**重采样**，它能够将数据聚合到不同的时间频率（如日变周、周变月）。其次，我们深入探讨了**时间窗口（滑动窗口）操作**，它可以在一个固定长度的窗口内滑动并计算统计指标（如移动平均线、波动率）。请务必记住，在进行窗口计算前，**处理缺失值**是必不可少的一步。这些技术是构建量化交易策略和进行金融数据分析的基础工具。