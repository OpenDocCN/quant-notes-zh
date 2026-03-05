# Python金融分析与量化交易实战课程：P4：4.4-时间序列重采样与窗口操作 📊

在本节课中，我们将学习时间序列分析中的两个核心操作：**重采样**与**时间窗口**。你将学会如何改变数据的时间频率，以及如何通过滑动窗口计算统计指标，这些是金融数据分析中处理时间序列数据的基础技能。

![](img/6477011658c4e5cbde8c92c8b463a206_1.png)

![](img/6477011658c4e5cbde8c92c8b463a206_3.png)

---

![](img/6477011658c4e5cbde8c92c8b463a206_5.png)

![](img/6477011658c4e5cbde8c92c8b463a206_7.png)

## 重采样操作

![](img/6477011658c4e5cbde8c92c8b463a206_9.png)

上一节我们介绍了时间序列的基本概念，本节中我们来看看如何改变数据的时间频率，即重采样。

![](img/6477011658c4e5cbde8c92c8b463a206_11.png)

重采样是指将数据从一个时间频率转换到另一个时间频率。例如，原始数据是按天统计的，我们可以将其转换为按周、按月或按年统计。在Pandas中，这通过`resample`方法实现。

**核心公式/代码**：
```python
# 假设df是一个以日期为索引的DataFrame
# 按周重采样，并计算每周的均值
weekly_data = df.resample('W').mean()
```

![](img/6477011658c4e5cbde8c92c8b463a206_13.png)

![](img/6477011658c4e5cbde8c92c8b463a206_15.png)

以下是重采样操作的关键步骤和参数：

![](img/6477011658c4e5cbde8c92c8b463a206_17.png)

![](img/6477011658c4e5cbde8c92c8b463a206_19.png)

1.  **指定频率**：你需要告诉`resample`方法以什么时间单位进行转换，例如`‘W’`代表周，`‘M’`代表月，`‘Y’`代表年。
2.  **指定聚合方法**：重采样后，一个时间段内会包含多个原始数据点。你需要指定如何聚合这些数据，例如计算均值（`.mean()`）、第一个值（`.first()`）、中位数（`.median()`）或求和（`.sum()`）等。
3.  **标签位置**：重采样后，每个时间段需要一个标签（通常是日期）。`label`参数决定使用时间段的起始日（`label=‘left’`）还是结束日（`label=‘right’`）作为标签。通常，使用结束日（`label=‘right’`）更符合逻辑。

![](img/6477011658c4e5cbde8c92c8b463a206_21.png)

![](img/6477011658c4e5cbde8c92c8b463a206_23.png)

例如，将日数据重采样为月数据并计算均值，默认使用月末作为标签：
```python
monthly_data = df.resample('M').mean()
```
若想使用月初作为标签，则可以：
```python
monthly_data_left = df.resample('M', label='left').mean()
```

---

## 时间窗口操作

![](img/6477011658c4e5cbde8c92c8b463a206_25.png)

![](img/6477011658c4e5cbde8c92c8b463a206_27.png)

了解了如何改变数据频率后，我们来看看另一个强大的工具：时间窗口（或滑动窗口）操作。

![](img/6477011658c4e5cbde8c92c8b463a206_29.png)

时间窗口操作不是改变数据频率，而是在一个固定长度的“窗口”内沿时间轴滑动，并对窗口内的数据进行计算。例如，有一个100天的序列，我们可以计算每连续10天的移动平均值、最小值等。

![](img/6477011658c4e5cbde8c92c8b463a206_31.png)

![](img/6477011658c4e5cbde8c92c8b463a206_33.png)

**核心公式/代码**：
```python
# 计算10天窗口的移动平均值
rolling_mean = df[‘column_name’].rolling(window=10).mean()
```

以下是执行时间窗口操作的步骤：

![](img/6477011658c4e5cbde8c92c8b463a206_35.png)

![](img/6477011658c4e5cbde8c92c8b463a206_37.png)

1.  **数据预处理**：在进行窗口计算前，**必须处理缺失值**。如果窗口内存在缺失值（NaN），计算结果也会是NaN。通常使用`.dropna()`方法移除含有缺失值的行。
    ```python
    df_clean = df.dropna()
    ```
2.  **创建滚动窗口对象**：使用`.rolling(window=n)`方法创建一个窗口大小为`n`的滚动窗口对象。
3.  **应用统计函数**：对滚动窗口对象应用所需的统计函数，如`.min()`（最小值）、`.max()`（最大值）、`.mean()`（均值）、`.std()`（标准差）等。

![](img/6477011658c4e5cbde8c92c8b463a206_39.png)

![](img/6477011658c4e5cbde8c92c8b463a206_41.png)

![](img/6477011658c4e5cbde8c92c8b463a206_43.png)

一个完整的例子是，计算清洗后数据中某列指标的10日窗口最小值、最大值、均值和标准差：

```python
# 假设 data_clean 是清洗后的DataFrame，'price’是我们要分析的列
window_size = 10
data_clean[‘min_10d’] = data_clean[‘price’].rolling(window=window_size).min()
data_clean[‘max_10d’] = data_clean[‘price’].rolling(window=window_size).max()
data_clean[‘mean_10d’] = data_clean[‘price’].rolling(window=window_size).mean()
data_clean[‘std_10d’] = data_clean[‘price’].rolling(window=window_size).std()
```
执行后，`data_clean`会新增四列，分别代表每个时间点向前回溯10天窗口内的各项统计值。前`window_size-1`个位置由于窗口未填满，结果会是NaN。

![](img/6477011658c4e5cbde8c92c8b463a206_45.png)

![](img/6477011658c4e5cbde8c92c8b463a206_47.png)

![](img/6477011658c4e5cbde8c92c8b463a206_49.png)

---

![](img/6477011658c4e5cbde8c92c8b463a206_51.png)

## 总结

![](img/6477011658c4e5cbde8c92c8b463a206_53.png)

![](img/6477011658c4e5cbde8c92c8b463a206_55.png)

![](img/6477011658c4e5cbde8c92c8b463a206_57.png)

本节课中我们一起学习了时间序列分析的两个重要操作：
1.  **重采样**：用于转换数据的时间频率（如日变周、月），核心是`df.resample(freq).agg_func()`方法，需注意`label`参数对结果标签的影响。
2.  **时间窗口操作**：用于计算滑动窗口内的统计指标（如10日移动平均），核心是`df[‘col’].rolling(window=n).agg_func()`方法。**切记在操作前使用`.dropna()`处理缺失数据**，这是保证结果正确的关键一步。

![](img/6477011658c4e5cbde8c92c8b463a206_59.png)

![](img/6477011658c4e5cbde8c92c8b463a206_61.png)

掌握这两种方法，你就能更灵活地分析和汇总不同时间维度下的金融数据，为后续的量化策略构建打下坚实基础。