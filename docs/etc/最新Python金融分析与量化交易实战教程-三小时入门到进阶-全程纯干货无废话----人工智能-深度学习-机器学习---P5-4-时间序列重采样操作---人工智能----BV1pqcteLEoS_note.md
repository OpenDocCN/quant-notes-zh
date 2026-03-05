# Python金融分析与量化交易实战：P5：4-时间序列重采样操作 📊

在本节课中，我们将要学习时间序列分析中的两个核心操作：**重采样**与**时间窗口操作**。这些技术对于将数据转换到不同时间尺度（如从天到周、月）以及计算滑动统计指标至关重要，是金融时间序列分析的基础。

![](img/f308abcf58771dd740e56b133632a66d_1.png)

![](img/f308abcf58771dd740e56b133632a66d_3.png)

---

## 重采样操作 🔄

![](img/f308abcf58771dd740e56b133632a66d_5.png)

![](img/f308abcf58771dd740e56b133632a66d_7.png)

上一节我们介绍了时间序列的基本处理，本节中我们来看看如何改变数据的时间频率，即重采样。

![](img/f308abcf58771dd740e56b133632a66d_9.png)

重采样是指将时间序列数据从一个频率转换到另一个频率的过程。例如，原始数据可能是按天记录的，但我们希望按周、月或年进行汇总分析。

![](img/f308abcf58771dd740e56b133632a66d_11.png)

操作的核心是使用 `resample()` 方法。你需要指定目标频率（如 `‘W’` 代表周，`‘M’` 代表月），并选择一种聚合方法（如均值、首个值、中位数等）来计算新频率下的数据点。

以下是重采样的基本代码格式：
```python
# 假设 df 是一个以日期为索引的 DataFrame
# 按周重采样，并计算每周的均值
weekly_data = df.resample(‘W’).mean()
# 按月重采样，并计算每月的第一个值
monthly_data = df.resample(‘M’).first()
```

![](img/f308abcf58771dd740e56b133632a66d_13.png)

### 重采样的标签设置

![](img/f308abcf58771dd740e56b133632a66d_15.png)

在重采样时，还有一个重要参数是 `label`，它决定了新数据点的索引标签使用时间窗口的起始点（`label=‘left’`）还是结束点（`label=‘right’`）。

![](img/f308abcf58771dd740e56b133632a66d_17.png)

![](img/f308abcf58771dd740e56b133632a66d_19.png)

以下是两种标签设置的对比：
```python
# 使用时间窗口的结束点作为标签（默认）
data_right = df.resample(‘M’, label=‘right’).mean()
# 使用时间窗口的起始点作为标签
data_left = df.resample(‘M’, label=‘left’).mean()
```
例如，对一月份的数据进行月度汇总：
*   `label=‘right’` 会将结果标记为 “1月31日”，表示这是截至1月31日的数据统计。
*   `label=‘left’` 则会标记为 “1月1日”，表示这是从1月1日开始的数据统计。

![](img/f308abcf58771dd740e56b133632a66d_21.png)

![](img/f308abcf58771dd740e56b133632a66d_23.png)

在金融分析中，通常使用 `label=‘right’`（默认值），因为它更符合“报告期结束时”的观察逻辑。但具体选择需根据分析任务决定。

---

## 时间窗口（滑动窗口）操作 🪟

了解了如何改变数据频率后，我们来看看如何在原有频率上计算滑动统计量，即时间窗口操作。

![](img/f308abcf58771dd740e56b133632a66d_25.png)

![](img/f308abcf58771dd740e56b133632a66d_27.png)

时间窗口操作不是改变数据点的频率，而是在一个固定长度的“窗口”内（例如连续10天）计算统计量，然后让这个窗口随时间向前滑动，从而为每个时间点生成一个基于其附近数据的特征。

例如，我们有100天的股价数据。我们可以：
1.  计算第1天到第10天的平均值，作为第10天的一个特征。
2.  将窗口向右滑动一天，计算第2天到第11天的平均值，作为第11天的特征。
3.  以此类推。

![](img/f308abcf58771dd740e56b133632a66d_29.png)

这可以通过 `rolling()` 方法实现。

![](img/f308abcf58771dd740e56b133632a66d_31.png)

以下是创建滑动窗口并计算统计量的步骤：
```python
# 定义窗口大小
window_size = 10
# 对某一列数据应用滚动窗口，并计算窗口内的最小值
rolling_min = df[‘column_name’].rolling(window=window_size).min()
```

![](img/f308abcf58771dd740e56b133632a66d_33.png)

**重要提示**：在应用滚动窗口前，务必处理缺失值（NaN）。如果窗口内存在NaN，许多统计计算的结果也会是NaN。

![](img/f308abcf58771dd740e56b133632a66d_35.png)

以下是包含数据预处理和多项统计计算的完整示例：
```python
# 1. 处理缺失值
df_clean = df.dropna()

![](img/f308abcf58771dd740e56b133632a66d_37.png)

![](img/f308abcf58771dd740e56b133632a66d_39.png)

# 2. 定义窗口大小
window_size = 10

![](img/f308abcf58771dd740e56b133632a66d_41.png)

# 3. 计算并添加多个滚动统计列
df_clean[‘rolling_min’] = df_clean[‘column_name’].rolling(window=window_size).min()
df_clean[‘rolling_max’] = df_clean[‘column_name’].rolling(window=window_size).max()
df_clean[‘rolling_mean’] = df_clean[‘column_name’].rolling(window=window_size).mean()
df_clean[‘rolling_std’] = df_clean[‘column_name’].rolling(window=window_size).std()

![](img/f308abcf58771dd740e56b133632a66d_43.png)

# 查看后几行结果
print(df_clean.tail())
```
前 `window_size - 1` 个数据点由于窗口不满足长度，其滚动统计值将为NaN，这是正常现象。

![](img/f308abcf58771dd740e56b133632a66d_45.png)

---

![](img/f308abcf58771dd740e56b133632a66d_47.png)

![](img/f308abcf58771dd740e56b133632a66d_49.png)

## 总结 📝

![](img/f308abcf58771dd740e56b133632a66d_51.png)

![](img/f308abcf58771dd740e56b133632a66d_53.png)

本节课中我们一起学习了时间序列分析的两个关键操作：
1.  **重采样**：使用 `resample()` 方法改变数据的时间频率（如日→周），并通过 `label` 参数控制新数据点的标签位置。
2.  **时间窗口操作**：使用 `rolling()` 方法创建滑动窗口，计算窗口内的统计特征（如最小值、最大值、均值、标准差），这是构建技术指标的基础。**切记，在操作前需使用 `dropna()` 等方法清理缺失数据。**

![](img/f308abcf58771dd740e56b133632a66d_55.png)

![](img/f308abcf58771dd740e56b133632a66d_57.png)

掌握重采样和窗口操作，能够帮助你灵活地从不同时间维度观察数据，并提取出有意义的趋势与特征，为后续的金融建模与分析打下坚实基础。