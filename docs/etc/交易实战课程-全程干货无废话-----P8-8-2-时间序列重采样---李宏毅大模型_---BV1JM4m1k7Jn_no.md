# Python金融分析与量化交易实战课程：P8：8.2-时间序列重采样与窗口操作 📊

在本节课中，我们将要学习时间序列分析中的两个核心操作：**重采样**与**时间窗口操作**。这些技术对于将数据转换到不同时间尺度（如从天到周）以及计算滑动统计指标（如移动平均值）至关重要。

## 概述

上一节我们介绍了时间序列的基础概念。本节中，我们来看看如何改变数据的时间频率以及如何通过滑动窗口计算统计量。掌握这些操作，能帮助我们更灵活地从不同时间维度分析金融数据。

![](img/ec0d88c89d1973405f71f3e8fa7a8f43_1.png)

![](img/ec0d88c89d1973405f71f3e8fa7a8f43_3.png)

---

## 时间序列重采样操作 🔄

![](img/ec0d88c89d1973405f71f3e8fa7a8f43_5.png)

重采样是指改变时间序列数据频率的过程。例如，原始数据可能是按天统计的，我们可以将其转换为按周、按月或按年统计。

![](img/ec0d88c89d1973405f71f3e8fa7a8f43_7.png)

进行重采样操作时，需要使用 `resample` 方法。该方法至少需要一个参数，即指定新的时间频率单位。

![](img/ec0d88c89d1973405f71f3e8fa7a8f43_9.png)

![](img/ec0d88c89d1973405f71f3e8fa7a8f43_11.png)

以下是重采样的基本语法：
```python
# 假设 df 是一个以日期为索引的 DataFrame
# ‘W’ 表示按周重采样，并计算每周数据的平均值
weekly_data = df.resample('W').mean()
```

在重采样过程中，你需要指定使用哪个统计指标来聚合数据。例如，是显示窗口内的**均值**、**第一个值**、**中位数**还是其他指标。

![](img/ec0d88c89d1973405f71f3e8fa7a8f43_13.png)

以下是常用的重采样频率代码示例：
*   `'D'`： 日历日
*   `'W'`： 周
*   `'M'`： 月
*   `'Q'`： 季度
*   `'Y'`： 年

例如，将日数据重采样为周数据并计算每周的开盘价均值：
```python
weekly_open = df['Open'].resample('W').mean()
print(weekly_open.head())
```

执行后，结果索引会显示为每周的结束日期（默认是周期右边界，即周日）。

---

![](img/ec0d88c89d1973405f71f3e8fa7a8f43_15.png)

### 重采样中的标签参数

![](img/ec0d88c89d1973405f71f3e8fa7a8f43_17.png)

在重采样时，需要注意 `label` 参数。它决定了使用窗口的哪一天作为结果数据的索引标签。

*   `label='right'`（默认）： 使用窗口的**最后一天**作为标签。
*   `label='left'`： 使用窗口的**第一天**作为标签。

![](img/ec0d88c89d1973405f71f3e8fa7a8f43_19.png)

![](img/ec0d88c89d1973405f71f3e8fa7a8f43_21.png)

以下是不同标签参数的对比示例：
```python
# 默认使用右标签（月末）
monthly_right = df.resample('M', label='right').mean()
# 使用左标签（月初）
monthly_left = df.resample('M', label='left').mean()

![](img/ec0d88c89d1973405f71f3e8fa7a8f43_23.png)

print("右标签示例（索引为月末日期）:")
print(monthly_right.head(1))
print("\n左标签示例（索引为月初日期）:")
print(monthly_left.head(1))
```

![](img/ec0d88c89d1973405f71f3e8fa7a8f43_25.png)

选择 `label='right'` 通常更符合逻辑，因为它表示该统计值是基于截至标签日期的数据计算得出的。例如，标签为“1月31日”的数据，描述的是整个1月份的情况。当然，具体选择需根据分析任务的需求而定。

---

## 时间窗口（滑动窗口）操作 🪟

接下来，我们学习时间窗口操作。这不是重采样，而是创建一个在时间轴上滑动的窗口，并计算每个窗口内的统计量。

其概念是：假设有100天的数据。我们首先截取第1到第10天作为第一个窗口并计算统计量（如最小值）；然后，窗口向后滑动一天，截取第2到第11天作为第二个窗口并计算统计量；接着是第3到第12天……以此类推。这种操作称为**滑动窗口**或**时间窗口**。

![](img/ec0d88c89d1973405f71f3e8fa7a8f43_27.png)

![](img/ec0d88c89d1973405f71f3e8fa7a8f43_29.png)

---

### 如何执行窗口操作

![](img/ec0d88c89d1973405f71f3e8fa7a8f43_31.png)

要执行窗口操作，主要使用 `rolling()` 方法。你需要指定窗口的大小。

以下是创建窗口并计算每个窗口内最小值的基本步骤：
```python
# 1. 定义窗口大小
window_size = 10

![](img/ec0d88c89d1973405f71f3e8fa7a8f43_33.png)

![](img/ec0d88c89d1973405f71f3e8fa7a8f43_35.png)

# 2. 对指定列应用滚动窗口，并计算每个窗口的最小值
rolling_min = df['Close'].rolling(window=window_size).min()

![](img/ec0d88c89d1973405f71f3e8fa7a8f43_37.png)

print(rolling_min.head(15))
```

![](img/ec0d88c89d1973405f71f3e8fa7a8f43_39.png)

注意，输出结果的前 `window_size - 1` 个值会是 `NaN`（空值），因为前9个窗口的数据点不足10个，无法计算一个完整的窗口统计量。

![](img/ec0d88c89d1973405f71f3e8fa7a8f43_41.png)

---

### 数据预处理的重要性

![](img/ec0d88c89d1973405f71f3e8fa7a8f43_43.png)

![](img/ec0d88c89d1973405f71f3e8fa7a8f43_45.png)

在进行窗口操作前，**必须处理原始数据中的缺失值**。如果原始数据序列中存在 `NaN`，那么任何包含该 `NaN` 的窗口的计算结果也会是 `NaN`，这会导致大量数据不可用。

![](img/ec0d88c89d1973405f71f3e8fa7a8f43_47.png)

以下是预处理缺失值的示例：
```python
# 创建数据副本并删除缺失值
df_clean = df.dropna()

![](img/ec0d88c89d1973405f71f3e8fa7a8f43_49.png)

# 在清理后的数据上执行窗口操作
window_size = 10
rolling_min_clean = df_clean['Close'].rolling(window=window_size).min()

![](img/ec0d88c89d1973405f71f3e8fa7a8f43_51.png)

print(rolling_min_clean.head(15))
```
现在，输出中只有在窗口未满时才会出现 `NaN`，而不会因为原始数据的缺失导致问题。

![](img/ec0d88c89d1973405f71f3e8fa7a8f43_53.png)

![](img/ec0d88c89d1973405f71f3e8fa7a8f43_55.png)

---

### 计算多个窗口统计量

![](img/ec0d88c89d1973405f71f3e8fa7a8f43_57.png)

我们可以方便地为一个滑动窗口计算多种统计指标，并将它们作为新列添加到数据框中。

![](img/ec0d88c89d1973405f71f3e8fa7a8f43_59.png)

以下是同时计算最小值、最大值、均值和标准差的示例：
```python
window_size = 10

![](img/ec0d88c89d1973405f71f3e8fa7a8f43_61.png)

![](img/ec0d88c89d1973405f71f3e8fa7a8f43_63.png)

df_clean['Rolling_Min'] = df_clean['Close'].rolling(window=window_size).min()
df_clean['Rolling_Max'] = df_clean['Close'].rolling(window=window_size).max()
df_clean['Rolling_Mean'] = df_clean['Close'].rolling(window=window_size).mean()
df_clean['Rolling_Std'] = df_clean['Close'].rolling(window=window_size).std()

# 查看计算结果的后几行
print(df_clean[['Close', 'Rolling_Min', 'Rolling_Max', 'Rolling_Mean', 'Rolling_Std']].tail())
```

通过这种方式，我们可以基于滑动窗口生成丰富的衍生特征，用于更深入的时间序列分析和建模。

![](img/ec0d88c89d1973405f71f3e8fa7a8f43_65.png)

![](img/ec0d88c89d1973405f71f3e8fa7a8f43_67.png)

---

![](img/ec0d88c89d1973405f71f3e8fa7a8f43_69.png)

## 总结

![](img/ec0d88c89d1973405f71f3e8fa7a8f43_71.png)

![](img/ec0d88c89d1973405f71f3e8fa7a8f43_73.png)

本节课中我们一起学习了时间序列的两个关键操作：
1.  **重采样**： 使用 `resample()` 方法改变数据的时间频率（如日变周），并通过 `label` 参数控制结果标签。
2.  **时间窗口操作**： 使用 `rolling()` 方法创建滑动窗口，计算窗口内的各种统计量（如移动平均、移动标准差）。**特别需要注意的是，在执行窗口操作前，务必对数据进行预处理，处理缺失值**，以确保计算结果的准确性。

![](img/ec0d88c89d1973405f71f3e8fa7a8f43_75.png)

这些技能是金融时间序列分析的基础，能帮助我们从不同时间维度和动态视角洞察数据规律。