# 量化交易教程：P8：时间序列重采样与窗口操作 📊

在本节课中，我们将要学习时间序列分析中的两个核心操作：**重采样**与**时间窗口操作**。这些技术是金融数据分析的基础，能够帮助我们从不同时间维度观察数据，并计算滑动统计指标。

![](img/067bb8fb4975c67ec1f91b19af2e545f_1.png)

![](img/067bb8fb4975c67ec1f91b19af2e545f_3.png)

## 重采样操作

![](img/067bb8fb4975c67ec1f91b19af2e545f_5.png)

上一节我们介绍了时间序列的基础概念，本节中我们来看看如何改变数据的时间频率，即重采样。

![](img/067bb8fb4975c67ec1f91b19af2e545f_7.png)

![](img/067bb8fb4975c67ec1f91b19af2e545f_9.png)

重采样是指将时间序列数据从一个频率转换到另一个频率的过程。例如，原始数据是按天统计的，我们可以将其转换为按周、按月或以年为单位的统计数据。

![](img/067bb8fb4975c67ec1f91b19af2e545f_11.png)

以下是执行重采样的基本步骤：

![](img/067bb8fb4975c67ec1f91b19af2e545f_13.png)

![](img/067bb8fb4975c67ec1f91b19af2e545f_15.png)

1.  **指定目标频率**：使用 `resample()` 方法，并传入一个表示新频率的字符串参数，如 `‘W’` 表示周，`‘M’` 表示月。
2.  **指定聚合方法**：重采样后，需要决定如何聚合每个新时间窗口内的数据。常见的聚合方法包括取均值、第一个值、中位数或求和等。

```python
# 假设 df 是一个包含日期索引的 DataFrame
# 按周重采样，并计算每周的均值
weekly_data = df.resample(‘W’).mean()
# 按月重采样，并取每月的第一个值
monthly_data = df.resample(‘M’).first()
```

![](img/067bb8fb4975c67ec1f91b19af2e545f_17.png)

![](img/067bb8fb4975c67ec1f91b19af2e545f_19.png)

### 标签位置参数

在重采样时，还有一个重要参数是 `label`，它决定了新时间戳的标签是使用时间窗口的起始点（`label=‘left’`）还是结束点（`label=‘right’`）。

![](img/067bb8fb4975c67ec1f91b19af2e545f_21.png)

![](img/067bb8fb4975c67ec1f91b19af2e545f_23.png)

![](img/067bb8fb4975c67ec1f91b19af2e545f_25.png)

```python
# 使用窗口结束点作为标签（默认）
monthly_right = df.resample(‘M’, label=‘right’).mean()
# 使用窗口起始点作为标签
monthly_left = df.resample(‘M’, label=‘left’).mean()
```

![](img/067bb8fb4975c67ec1f91b19af2e545f_27.png)

例如，对于一月份的数据，`label=‘right’` 会将其标记为 `1月31日`，而 `label=‘left’` 会将其标记为 `1月1日`。在金融分析中，通常使用 `label=‘right’`，因为它更符合“截至到某日”的观察逻辑。

## 时间窗口（滑动窗口）操作

![](img/067bb8fb4975c67ec1f91b19af2e545f_29.png)

了解了如何改变数据频率后，我们来看看如何在固定的时间窗口上进行滑动计算，这对于计算移动平均线等指标至关重要。

![](img/067bb8fb4975c67ec1f91b19af2e545f_31.png)

![](img/067bb8fb4975c67ec1f91b19af2e545f_33.png)

时间窗口操作是指定义一个固定长度的窗口（例如10天），让这个窗口在时间序列上滑动。对于每一个窗口位置，我们都可以计算其内部数据的统计值（如最小值、最大值、均值、标准差等）。

![](img/067bb8fb4975c67ec1f91b19af2e545f_35.png)

以下是执行时间窗口操作的基本步骤：

![](img/067bb8fb4975c67ec1f91b19af2e545f_37.png)

1.  **定义窗口大小**：确定窗口包含多少个时间单位（如 `window=10` 表示10天窗口）。
2.  **应用滚动方法**：使用 `rolling()` 方法创建滚动窗口对象。
3.  **计算统计指标**：对滚动窗口对象应用聚合函数，如 `.min()`, `.max()`, `.mean()`, `.std()`。

![](img/067bb8fb4975c67ec1f91b19af2e545f_39.png)

![](img/067bb8fb4975c67ec1f91b19af2e545f_41.png)

![](img/067bb8fb4975c67ec1f91b19af2e545f_43.png)

```python
# 计算10天窗口内的最小值
min_rolling = df[‘column_name’].rolling(window=10).min()
# 计算10天窗口内的均值
mean_rolling = df[‘column_name’].rolling(window=10).mean()
```

![](img/067bb8fb4975c67ec1f91b19af2e545f_45.png)

### 数据预处理的重要性

![](img/067bb8fb4975c67ec1f91b19af2e545f_47.png)

![](img/067bb8fb4975c67ec1f91b19af2e545f_49.png)

在进行窗口计算前，**必须处理缺失值**。如果原始数据中存在 `NaN`，那么任何包含该 `NaN` 的窗口的计算结果也会是 `NaN`。

![](img/067bb8fb4975c67ec1f91b19af2e545f_51.png)

![](img/067bb8fb4975c67ec1f91b19af2e545f_53.png)

```python
# 首先，删除或填充缺失值
df_clean = df.dropna()
# 然后，在清洗后的数据上进行窗口计算
rolling_min = df_clean[‘column_name’].rolling(window=10).min()
```

![](img/067bb8fb4975c67ec1f91b19af2e545f_55.png)

![](img/067bb8fb4975c67ec1f91b19af2e545f_57.png)

### 同时计算多个窗口统计量

![](img/067bb8fb4975c67ec1f91b19af2e545f_59.png)

![](img/067bb8fb4975c67ec1f91b19af2e545f_61.png)

我们可以方便地一次性为数据添加多个窗口统计列。

![](img/067bb8fb4975c67ec1f91b19af2e545f_63.png)

```python
window_size = 10
df_clean[‘rolling_min’] = df_clean[‘column_name’].rolling(window=window_size).min()
df_clean[‘rolling_max’] = df_clean[‘column_name’].rolling(window=window_size).max()
df_clean[‘rolling_mean’] = df_clean[‘column_name’].rolling(window=window_size).mean()
df_clean[‘rolling_std’] = df_clean[‘column_name’].rolling(window=window_size).std()
```

![](img/067bb8fb4975c67ec1f91b19af2e545f_65.png)

![](img/067bb8fb4975c67ec1f91b19af2e545f_67.png)

执行以上代码后，数据框中就会新增四列，分别代表每个时间点其前 `window_size` 个数据点的最小值、最大值、均值和标准差。

![](img/067bb8fb4975c67ec1f91b19af2e545f_69.png)

## 总结

![](img/067bb8fb4975c67ec1f91b19af2e545f_71.png)

![](img/067bb8fb4975c67ec1f91b19af2e545f_73.png)

![](img/067bb8fb4975c67ec1f91b19af2e545f_75.png)

本节课中我们一起学习了时间序列分析的两个关键操作：
1.  **重采样**：通过 `resample()` 方法改变数据的时间频率（如日转周、月），并配合聚合函数（如 `mean()`, `first()`）和 `label` 参数来获取新的时间维度视图。
2.  **时间窗口操作**：通过 `rolling()` 方法创建滑动窗口，可以计算窗口内的各种统计指标（如 `min()`, `max()`, `mean()`, `std()`），这是构建技术指标（如移动平均线）的基础。**切记，在进行窗口计算前务必处理好数据中的缺失值。**

![](img/067bb8fb4975c67ec1f91b19af2e545f_77.png)

![](img/067bb8fb4975c67ec1f91b19af2e545f_79.png)

掌握这两种操作，能够让你更灵活地从不同时间尺度分析和理解金融时间序列数据。