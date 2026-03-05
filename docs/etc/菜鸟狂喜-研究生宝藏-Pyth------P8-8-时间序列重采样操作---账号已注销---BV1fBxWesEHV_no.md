# Python金融量化分析：P8：时间序列重采样与窗口操作 📊

在本节课中，我们将学习时间序列分析中的两个核心操作：**重采样**与**滑动窗口**。你将学会如何改变数据的时间频率，以及如何基于时间窗口进行统计计算，这是金融数据分析中处理时间序列数据的基础技能。

![](img/9b2cc69d79970f33750a583938e21e7c_1.png)

---

![](img/9b2cc69d79970f33750a583938e21e7c_3.png)

## 重采样操作 🔄

![](img/9b2cc69d79970f33750a583938e21e7c_5.png)

上一节我们介绍了时间序列的基本概念，本节中我们来看看如何改变数据的时间频率，即重采样。

![](img/9b2cc69d79970f33750a583938e21e7c_7.png)

![](img/9b2cc69d79970f33750a583938e21e7c_9.png)

重采样是指将时间序列数据从一个频率转换到另一个频率的过程。例如，原始数据可能是按天统计的，我们可以将其转换为按周、按月或以年为单位的统计数据。

![](img/9b2cc69d79970f33750a583938e21e7c_11.png)

### 如何进行重采样

在Pandas中，使用 `resample()` 方法可以轻松实现重采样。你需要指定一个新的时间频率，并选择一种聚合方法（如均值、求和、第一个值等）。

以下是重采样的基本代码格式：
```python
# 假设 df 是一个以日期为索引的DataFrame
# 按周重采样，并计算每周的均值
weekly_data = df.resample('W').mean()
# 按月重采样，并取每月的第一个值
monthly_data = df.resample('M').first()
```

![](img/9b2cc69d79970f33750a583938e21e7c_13.png)

![](img/9b2cc69d79970f33750a583938e21e7c_15.png)

### 重采样的标签设置

![](img/9b2cc69d79970f33750a583938e21e7c_17.png)

在重采样时，你可以指定聚合区间的标签是使用区间的开始日期（`label='left'`）还是结束日期（`label='right'`）。

![](img/9b2cc69d79970f33750a583938e21e7c_19.png)

![](img/9b2cc69d79970f33750a583938e21e7c_21.png)

以下是不同标签设置的示例：
```python
# 默认使用右标签（区间结束日期）
monthly_right = df.resample('M', label='right').mean()
# 使用左标签（区间开始日期）
monthly_left = df.resample('M', label='left').mean()
```

![](img/9b2cc69d79970f33750a583938e21e7c_23.png)

**核心概念**：`label` 参数决定了聚合结果索引的日期是使用时间窗口的起点还是终点。在金融分析中，通常使用 `label='right'`（默认值），因为它更符合“截至到某日”的观察逻辑。

---

![](img/9b2cc69d79970f33750a583938e21e7c_25.png)

## 滑动窗口操作 🪟

了解了如何改变数据频率后，我们来看看如何基于一个滑动的时间窗口进行连续计算。

![](img/9b2cc69d79970f33750a583938e21e7c_27.png)

![](img/9b2cc69d79970f33750a583938e21e7c_29.png)

滑动窗口操作不是改变数据频率，而是在一个固定大小的窗口上（例如10天）沿着时间轴滑动，并对每个窗口内的数据进行计算（如计算最小值、最大值、均值等）。这常用于计算移动平均线等技术指标。

### 如何进行滑动窗口计算

![](img/9b2cc69d79970f33750a583938e21e7c_31.png)

Pandas提供了 `rolling()` 方法来创建滑动窗口对象，然后可以对其应用各种统计函数。

![](img/9b2cc69d79970f33750a583938e21e7c_33.png)

![](img/9b2cc69d79970f33750a583938e21e7c_35.png)

以下是创建并计算10天窗口最小值的代码：
```python
# 创建一个窗口大小为10的滚动窗口对象
rolling_window = df['column_name'].rolling(window=10)
# 计算每个窗口的最小值
min_values = rolling_window.min()
```

### 处理缺失值

![](img/9b2cc69d79970f33750a583938e21e7c_37.png)

![](img/9b2cc69d79970f33750a583938e21e7c_39.png)

在进行窗口计算前，必须处理原始数据中的缺失值（NaN），否则会导致窗口计算结果出现大量NaN。

![](img/9b2cc69d79970f33750a583938e21e7c_41.png)

![](img/9b2cc69d79970f33750a583938e21e7c_43.png)

以下是处理缺失值的步骤：
```python
# 1. 删除包含缺失值的行
df_clean = df.dropna()
# 2. 在清洗后的数据上进行窗口计算
rolling_result = df_clean['column_name'].rolling(window=10).min()
```

![](img/9b2cc69d79970f33750a583938e21e7c_45.png)

![](img/9b2cc69d79970f33750a583938e21e7c_47.png)

### 计算多个窗口统计量

![](img/9b2cc69d79970f33750a583938e21e7c_49.png)

你可以一次性为每个窗口计算多个统计指标，并将结果添加到数据中。

![](img/9b2cc69d79970f33750a583938e21e7c_51.png)

以下是同时计算最小值、最大值、均值和标准差的示例：
```python
# 在清洗后的数据上定义窗口
df_clean['min'] = df_clean['column_name'].rolling(window=10).min()
df_clean['max'] = df_clean['column_name'].rolling(window=10).max()
df_clean['mean'] = df_clean['column_name'].rolling(window=10).mean()
df_clean['std'] = df_clean['column_name'].rolling(window=10).std()
# 查看结果
print(df_clean.tail())
```

![](img/9b2cc69d79970f33750a583938e21e7c_53.png)

![](img/9b2cc69d79970f33750a583938e21e7c_55.png)

![](img/9b2cc69d79970f33750a583938e21e7c_57.png)

**核心概念**：`rolling(window=n)` 创建了一个大小为 `n` 的滑动窗口。在窗口未填满数据（即前 `n-1` 个数据点）时，计算结果默认为NaN。

---

![](img/9b2cc69d79970f33750a583938e21e7c_59.png)

![](img/9b2cc69d79970f33750a583938e21e7c_61.png)

## 总结 📝

![](img/9b2cc69d79970f33750a583938e21e7c_63.png)

![](img/9b2cc69d79970f33750a583938e21e7c_65.png)

本节课中我们一起学习了时间序列分析的两个重要操作：
1.  **重采样**：使用 `resample()` 方法改变数据的时间频率（如从天到周、月），并通过 `label` 参数控制聚合结果的标签日期。
2.  **滑动窗口**：使用 `rolling()` 方法在固定大小的窗口上进行滑动计算，常用于生成移动统计量。**切记在进行窗口计算前，务必使用 `dropna()` 等方法处理数据中的缺失值。**

![](img/9b2cc69d79970f33750a583938e21e7c_67.png)

![](img/9b2cc69d79970f33750a583938e21e7c_69.png)

掌握重采样和窗口操作，能够帮助你更灵活地从不同时间维度分析金融数据，为后续构建量化模型打下坚实基础。