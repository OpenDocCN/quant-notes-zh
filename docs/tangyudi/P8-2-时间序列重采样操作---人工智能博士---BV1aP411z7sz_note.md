# 时间序列分析：P8：时间序列重采样与窗口操作 📊

在本节课中，我们将学习时间序列数据处理中的两个核心操作：**重采样**与**时间窗口操作**。重采样用于改变数据的时间频率，而窗口操作则用于在滑动的时间段内进行统计分析。掌握这些技能对于时间序列数据的分析和特征工程至关重要。

![](img/6bd1b17f3922b459b3b823d232522d1f_1.png)

---

![](img/6bd1b17f3922b459b3b823d232522d1f_3.png)

## 时间序列分析：P8-1：重采样操作 🔄

上一节我们介绍了时间序列的基础概念。本节中，我们来看看如何对时间序列数据进行**重采样**。

![](img/6bd1b17f3922b459b3b823d232522d1f_5.png)

重采样是指将数据从一个时间频率转换到另一个时间频率。例如，原始数据可能是按天记录的，我们可以将其转换为按周、按月或按年进行统计。

![](img/6bd1b17f3922b459b3b823d232522d1f_7.png)

![](img/6bd1b17f3922b459b3b823d232522d1f_9.png)

进行重采样操作时，至少需要指定一个参数，即新的时间频率单位（如“W”代表周，“M”代表月）。同时，你需要指定在聚合数据时使用哪种统计指标，例如均值、第一个值或中位数。

以下是重采样的基本代码示例：

![](img/6bd1b17f3922b459b3b823d232522d1f_11.png)

```python
# 假设 df 是一个以日期为索引的DataFrame
# 按周进行重采样，并计算每周的均值
weekly_resampled = df.resample('W').mean()
print(weekly_resampled.head())
```

执行上述代码后，你会得到以周为单位的聚合数据。结果中的日期标签默认是每个时间窗口的**最后一天**。

---

![](img/6bd1b17f3922b459b3b823d232522d1f_13.png)

### 关于标签位置的说明

![](img/6bd1b17f3922b459b3b823d232522d1f_15.png)

在重采样时，我们可以通过 `label` 参数控制聚合结果的日期标签是使用时间窗口的起始点（`left`）还是结束点（`right`）。

以下是两种不同标签设置的对比：

![](img/6bd1b17f3922b459b3b823d232522d1f_17.png)

![](img/6bd1b17f3922b459b3b823d232522d1f_19.png)

```python
# 默认使用右标签（时间窗口的结束日期）
resampled_right = df.resample('M', label='right').mean()
print(resampled_right.head())

![](img/6bd1b17f3922b459b3b823d232522d1f_21.png)

# 使用左标签（时间窗口的起始日期）
resampled_left = df.resample('M', label='left').mean()
print(resampled_left.head())
```

![](img/6bd1b17f3922b459b3b823d232522d1f_23.png)

选择 `left` 或 `right` 取决于具体分析需求。通常，使用 `right`（默认值）更符合逻辑，因为它表示该统计值所描述的时间段已经结束。

---

## 时间序列分析：P8-2：时间窗口（滑动窗口）操作 🪟

上一节我们介绍了重采样，它改变了数据的观察频率。本节中，我们来看看**时间窗口操作**，它用于在连续滑动的固定时间段内进行动态分析。

时间窗口操作，常被称为滑动窗口，其概念是：设定一个固定长度的窗口（例如10天），从序列起始点开始，计算该窗口内的统计量（如最小值、均值）。然后，窗口向后滑动一个时间单位（例如1天），再计算新窗口内的统计量，如此重复。

以下是实现滑动窗口操作的基本步骤。首先，我们需要处理数据中的缺失值，因为缺失值会影响窗口计算。

```python
# 1. 处理缺失值
df_cleaned = df.dropna()

![](img/6bd1b17f3922b459b3b823d232522d1f_25.png)

# 2. 定义窗口大小
window_size = 10

![](img/6bd1b17f3922b459b3b823d232522d1f_27.png)

# 3. 计算滑动窗口的最小值
rolling_min = df_cleaned['column_name'].rolling(window=window_size).min()
print(rolling_min.head(15))
```

在计算结果中，前 `window_size - 1` 个值通常是 `NaN`，因为最初的几个窗口数据不足。

![](img/6bd1b17f3922b459b3b823d232522d1f_29.png)

---

### 计算多个窗口统计量

![](img/6bd1b17f3922b459b3b823d232522d1f_31.png)

![](img/6bd1b17f3922b459b3b823d232522d1f_33.png)

我们可以一次性为每个滑动窗口计算多个统计指标，并将其作为新列添加到数据中。

以下是同时计算最小值、最大值、均值和标准差的示例：

```python
# 在清理后的数据上计算多个滚动统计量
df_cleaned['rolling_min'] = df_cleaned['column_name'].rolling(window=window_size).min()
df_cleaned['rolling_max'] = df_cleaned['column_name'].rolling(window=window_size).max()
df_cleaned['rolling_mean'] = df_cleaned['column_name'].rolling(window=window_size).mean()
df_cleaned['rolling_std'] = df_cleaned['column_name'].rolling(window=window_size).std()

![](img/6bd1b17f3922b459b3b823d232522d1f_35.png)

![](img/6bd1b17f3922b459b3b823d232522d1f_37.png)

# 查看结果
print(df_cleaned.tail())
```

通过这种方式，我们可以基于滑动窗口生成丰富的特征，用于后续的时间序列建模或分析。

![](img/6bd1b17f3922b459b3b823d232522d1f_39.png)

---

![](img/6bd1b17f3922b459b3b823d232522d1f_41.png)

## 总结 📝

![](img/6bd1b17f3922b459b3b823d232522d1f_43.png)

![](img/6bd1b17f3922b459b3b823d232522d1f_45.png)

本节课中我们一起学习了时间序列的两个重要操作：
1.  **重采样**：通过 `df.resample()` 方法改变数据的时间频率，并可使用 `label` 参数控制聚合结果的标签位置。
2.  **时间窗口操作**：通过 `df.rolling()` 方法创建滑动窗口，可以动态计算窗口内的各种统计量（如 `min()`, `max()`, `mean()`, `std()`），是构建时间序列特征的关键技术。

![](img/6bd1b17f3922b459b3b823d232522d1f_47.png)

进行这些操作前，务必记得**预处理数据**，特别是处理缺失值，以确保计算结果的准确性。