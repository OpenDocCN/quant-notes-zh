# Python量化交易：8.8.2：时间序列重采样与窗口操作 📊

在本节课中，我们将要学习时间序列分析中的两个核心操作：**重采样**与**时间窗口操作**。这些操作是金融数据分析与量化交易中处理时间序列数据的基础，能够帮助我们从不同时间维度观察和分析数据。

---

## 重采样操作

上一节我们介绍了时间序列的基本概念，本节中我们来看看如何改变数据的时间频率，即重采样。

![](img/4bf402b2ad5a2b493685bbaf7e09f4c4_1.png)

重采样是指将时间序列数据从一个频率转换到另一个频率的过程。例如，原始数据可能是按天统计的，我们可以将其转换为按周、按月或以年为统计单位。

![](img/4bf402b2ad5a2b493685bbaf7e09f4c4_3.png)

以下是重采样的基本操作步骤：

1.  **指定目标频率**：你需要告诉`resample`方法以什么时间单位进行转换，例如`'W'`代表周，`'M'`代表月，`'Y'`代表年。
2.  **指定聚合方法**：频率转换后，你需要指定如何聚合每个时间段内的数据。例如，是显示该时间段内的均值、第一个值、中位数还是其他统计指标。

核心操作代码如下：

```python
# 假设df是一个以日期为索引的DataFrame
# 按周重采样，并计算每周的均值
weekly_data = df.resample('W').mean()

![](img/4bf402b2ad5a2b493685bbaf7e09f4c4_5.png)

# 按月重采样，并取每月的第一个值
monthly_data = df.resample('M').first()
```

![](img/4bf402b2ad5a2b493685bbaf7e09f4c4_7.png)

执行重采样后，我们得到了一份按新频率统计的结果。



![](img/4bf402b2ad5a2b493685bbaf7e09f4c4_9.png)

![](img/4bf402b2ad5a2b493685bbaf7e09f4c4_1.png)

观察结果，我们发现索引日期发生了变化。这是因为重采样操作会按照指定的频率（如一周）划分时间窗口，并对每个窗口内的数据进行聚合。



![](img/4bf402b2ad5a2b493685bbaf7e09f4c4_11.png)

![](img/4bf402b2ad5a2b493685bbaf7e09f4c4_3.png)

除了按周重采样，我们也可以轻松地切换到其他频率，例如按月重采样。



![](img/4bf402b2ad5a2b493685bbaf7e09f4c4_5.png)

在重采样时，有一个重要的参数需要注意：**标签位置**。



![](img/4bf402b2ad5a2b493685bbaf7e09f4c4_7.png)

![](img/4bf402b2ad5a2b493685bbaf7e09f4c4_13.png)

标签位置决定了聚合后的数据点使用哪个日期作为其索引。以下是两种选项：

![](img/4bf402b2ad5a2b493685bbaf7e09f4c4_15.png)

*   **`label='right'`**（默认）：使用时间窗口的**结束日期**作为标签。例如，一月份的数据聚合后，标签为1月31日。
*   **`label='left'`**：使用时间窗口的**开始日期**作为标签。例如，一月份的数据聚合后，标签为1月1日（或上一个周期的最后一天）。



![](img/4bf402b2ad5a2b493685bbaf7e09f4c4_9.png)

我们可以通过代码来观察两者的区别：

![](img/4bf402b2ad5a2b493685bbaf7e09f4c4_17.png)

```python
# 默认使用右标签（结束日期）
monthly_data_right = df.resample('M', label='right').mean()

![](img/4bf402b2ad5a2b493685bbaf7e09f4c4_19.png)

# 使用左标签（开始日期）
monthly_data_left = df.resample('M', label='left').mean()
```



![](img/4bf402b2ad5a2b493685bbaf7e09f4c4_21.png)

![](img/4bf402b2ad5a2b493685bbaf7e09f4c4_11.png)

![](img/4bf402b2ad5a2b493685bbaf7e09f4c4_23.png)

对比结果，你会发现`label='left'`时，第一个数据的标签是12月31日，但其数值与`label='right'`时1月31日的数值完全相同。这表示它们统计的是同一时间段（一月份）的数据，只是命名方式不同。



![](img/4bf402b2ad5a2b493685bbaf7e09f4c4_13.png)

选择哪种标签取决于具体的分析任务。在大部分金融分析场景中，使用`label='right'`（结束日期）更为直观，因为它代表了该时间段结束时的情况。



![](img/4bf402b2ad5a2b493685bbaf7e09f4c4_15.png)

当然，不同任务可能有不同的需求，你可以根据实际情况进行选择。



![](img/4bf402b2ad5a2b493685bbaf7e09f4c4_17.png)

这就是时间序列的重采样操作，它让我们能够灵活地从不同时间尺度观察数据。



![](img/4bf402b2ad5a2b493685bbaf7e09f4c4_25.png)

![](img/4bf402b2ad5a2b493685bbaf7e09f4c4_19.png)

![](img/4bf402b2ad5a2b493685bbaf7e09f4c4_27.png)

---

## 时间窗口（滑动窗口）操作

接下来，我们学习另一个强大的工具：时间窗口操作，也称为滑动窗口操作。

假设我们有一系列连续的数据（例如100天的股价）。时间窗口操作是这样的：首先，我们截取第一段连续的数据（例如前10天）作为一个窗口；然后，窗口向后滑动一个单位，截取第二段数据（例如第2天到第11天）；接着再滑动，截取第三段（第3天到第12天），以此类推。



![](img/4bf402b2ad5a2b493685bbaf7e09f4c4_29.png)

![](img/4bf402b2ad5a2b493685bbaf7e09f4c4_23.png)

这种操作可以用于计算移动平均线、滚动标准差等指标，是技术分析的基础。

![](img/4bf402b2ad5a2b493685bbaf7e09f4c4_31.png)

以下是实现滑动窗口操作的步骤：

![](img/4bf402b2ad5a2b493685bbaf7e09f4c4_33.png)

1.  **定义窗口大小**：确定每个窗口包含多少个数据点（例如`window=10`）。
2.  **应用`rolling`方法**：在Pandas中，我们使用`.rolling()`方法来创建滑动窗口。
3.  **应用聚合函数**：对每个窗口内的数据应用统计函数，如`.min()`, `.max()`, `.mean()`, `.std()`等。

核心操作代码如下：

```python
# 假设df['close']是收盘价序列，我们创建一个窗口大小为10的滚动对象
rolling_window = df['close'].rolling(window=10)

![](img/4bf402b2ad5a2b493685bbaf7e09f4c4_35.png)

# 计算每个窗口的最小值
min_values = rolling_window.min()

![](img/4bf402b2ad5a2b493685bbaf7e09f4c4_37.png)

# 计算每个窗口的最大值
max_values = rolling_window.max()
```



![](img/4bf402b2ad5a2b493685bbaf7e09f4c4_39.png)

![](img/4bf402b2ad5a2b493685bbaf7e09f4c4_25.png)

![](img/4bf402b2ad5a2b493685bbaf7e09f4c4_41.png)

在应用窗口操作时，有一个**至关重要的预处理步骤**：**处理缺失值**。



![](img/4bf402b2ad5a2b493685bbaf7e09f4c4_43.png)

![](img/4bf402b2ad5a2b493685bbaf7e09f4c4_31.png)

![](img/4bf402b2ad5a2b493685bbaf7e09f4c4_45.png)

如果原始数据中存在`NaN`（缺失值），那么任何包含该`NaN`的窗口，其计算结果也会是`NaN`。因此，我们需要先清理数据。

![](img/4bf402b2ad5a2b493685bbaf7e09f4c4_47.png)

```python
# 首先，删除含有缺失值的行
df_clean = df.dropna()

# 然后，在清理后的数据上进行窗口操作
rolling_window_clean = df_clean['close'].rolling(window=10)
min_values_clean = rolling_window_clean.min()
```



![](img/4bf402b2ad5a2b493685bbaf7e09f4c4_49.png)

![](img/4bf402b2ad5a2b493685bbaf7e09f4c4_35.png)

![](img/4bf402b2ad5a2b493685bbaf7e09f4c4_51.png)

清理后，前几个窗口的结果可能仍是`NaN`，这是因为窗口尚未填满（例如，前9个数据点无法构成一个长度为10的窗口），这是正常现象。从第10个数据点开始，我们就能得到有效的计算结果了。



![](img/4bf402b2ad5a2b493685bbaf7e09f4c4_37.png)

![](img/4bf402b2ad5a2b493685bbaf7e09f4c4_53.png)

我们可以一次性为数据框添加多个滚动统计列，以便进行综合分析。

![](img/4bf402b2ad5a2b493685bbaf7e09f4c4_55.png)

```python
# 在清理后的数据上，同时计算多个滚动统计量
df_clean['roll_min'] = df_clean['close'].rolling(window=10).min()
df_clean['roll_max'] = df_clean['close'].rolling(window=10).max()
df_clean['roll_mean'] = df_clean['close'].rolling(window=10).mean()
df_clean['roll_std'] = df_clean['close'].rolling(window=10).std()

![](img/4bf402b2ad5a2b493685bbaf7e09f4c4_57.png)

# 查看结果
print(df_clean.tail())
```



![](img/4bf402b2ad5a2b493685bbaf7e09f4c4_49.png)

这样，我们就得到了基于滑动窗口计算出的最小值、最大值、均值和标准差等一系列统计指标。



![](img/4bf402b2ad5a2b493685bbaf7e09f4c4_59.png)

![](img/4bf402b2ad5a2b493685bbaf7e09f4c4_53.png)

![](img/4bf402b2ad5a2b493685bbaf7e09f4c4_61.png)

---

![](img/4bf402b2ad5a2b493685bbaf7e09f4c4_63.png)

## 总结

![](img/4bf402b2ad5a2b493685bbaf7e09f4c4_65.png)

本节课中我们一起学习了时间序列分析的两个关键操作：

![](img/4bf402b2ad5a2b493685bbaf7e09f4c4_67.png)

1.  **重采样**：通过`df.resample()`方法改变数据的时间频率（如日变周、月），并配合聚合函数（如`mean()`, `first()`）来汇总数据。我们还需要注意`label`参数，它决定了聚合后数据点的标签日期。
2.  **时间窗口（滑动窗口）操作**：通过`df.rolling()`方法在数据上创建固定大小的滑动窗口，用于计算滚动统计指标（如移动平均、滚动波动率）。**在进行此操作前，务必对数据做预处理，清除缺失值**，以避免结果出现大量`NaN`。

![](img/4bf402b2ad5a2b493685bbaf7e09f4c4_69.png)

掌握这两种操作，你就能从不同时间维度和动态变化的角度，更深入地分析和理解金融时间序列数据，为后续的量化策略开发打下坚实基础。