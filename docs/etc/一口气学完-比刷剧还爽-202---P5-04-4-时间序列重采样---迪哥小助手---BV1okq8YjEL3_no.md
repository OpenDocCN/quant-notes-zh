# Python金融量化+股票交易：P5：04-4-时间序列重采样操作 📊

在本节课中，我们将要学习时间序列分析中的两个核心操作：**重采样**与**时间窗口**。你将学会如何改变数据的时间频率，以及如何通过滑动窗口计算数据的统计特征。

---

## 重采样操作

![](img/76b05b14c8260ffd4254d5c9e30d6b60_1.png)

![](img/76b05b14c8260ffd4254d5c9e30d6b60_3.png)

上一节我们介绍了时间序列的基本操作，本节中我们来看看如何对时间序列进行重采样。重采样是指改变数据的时间频率。例如，原始数据是按天统计的，我们可以将其转换为按周、按月或按年统计。

进行重采样操作时，需要使用 `resample()` 方法。你需要指定新的时间频率，并选择聚合数据的方式（如计算均值、第一个值、中位数等）。

以下是重采样的基本语法：
```python
# 假设df是一个以日期为索引的DataFrame
# 按周重采样，并计算每周的均值
weekly_data = df.resample('W').mean()
```

![](img/76b05b14c8260ffd4254d5c9e30d6b60_5.png)

![](img/76b05b14c8260ffd4254d5c9e30d6b60_7.png)

接下来，我们通过具体例子来理解。

![](img/76b05b14c8260ffd4254d5c9e30d6b60_9.png)

### 按周重采样

首先，我们尝试将数据按周进行重采样，并查看结果。

![](img/76b05b14c8260ffd4254d5c9e30d6b60_11.png)

```python
# 按周重采样，默认使用右标签（right）
weekly_resample = data.resample('W').mean()
print(weekly_resample.head())
```

执行上述代码后，结果会显示以周为单位的聚合数据。索引日期通常是该周的最后一天（例如1月10日），这是因为原始数据可能从该周开始才有记录。

### 按月重采样

除了按周，我们也可以按月进行重采样。

![](img/76b05b14c8260ffd4254d5c9e30d6b60_13.png)

![](img/76b05b14c8260ffd4254d5c9e30d6b60_15.png)

```python
# 按月重采样
monthly_resample = data.resample('M').mean()
print(monthly_resample.head())
```

### 理解标签参数：`label`

![](img/76b05b14c8260ffd4254d5c9e30d6b60_17.png)

在重采样时，`label` 参数决定了聚合结果的索引标签使用时间窗口的哪一端。

![](img/76b05b14c8260ffd4254d5c9e30d6b60_19.png)

*   **`label='right'`**（默认）：使用时间窗口的**结束日期**作为标签。
*   **`label='left'`**：使用时间窗口的**开始日期**作为标签。

![](img/76b05b14c8260ffd4254d5c9e30d6b60_21.png)

以下是两种设置的对比：

![](img/76b05b14c8260ffd4254d5c9e30d6b60_23.png)

```python
# 使用左标签（窗口开始日期）
monthly_left = data.resample('M', label='left').mean()
print(monthly_left.head())

# 使用右标签（窗口结束日期，默认）
monthly_right = data.resample('M', label='right').mean()
print(monthly_right.head())
```

对比结果你会发现，`monthly_left` 中1月份数据的标签是“12-31”，而 `monthly_right` 中1月份数据的标签是“1-31”，但两者统计的数值是相同的。在金融分析中，通常使用 `label='right'`（默认值），因为它更符合我们观察已发生周期的逻辑。

---

## 时间窗口（滑动窗口）操作

![](img/76b05b14c8260ffd4254d5c9e30d6b60_25.png)

![](img/76b05b14c8260ffd4254d5c9e30d6b60_27.png)

上一节我们介绍了重采样，本节中我们来看看时间窗口操作。时间窗口操作，也称为滑动窗口，用于在时间序列上计算指定窗口大小内的统计指标。例如，我们有一个100天的股价序列，可以计算每连续10天的移动平均值、最小值等。

核心方法是 `rolling()`。

### 基础滑动窗口计算

![](img/76b05b14c8260ffd4254d5c9e30d6b60_29.png)

假设我们想计算某个指标列（如收盘价）的10日滑动窗口最小值。

```python
# 定义窗口大小
window_size = 10

![](img/76b05b14c8260ffd4254d5c9e30d6b60_31.png)

# 对指定列进行滑动窗口计算，并求最小值
rolling_min = data['close'].rolling(window=window_size).min()
print(rolling_min.head(15))
```

注意，输出结果的前9个（`window_size - 1`）值会是 `NaN`，因为前9天无法构成一个完整的10天窗口。

![](img/76b05b14c8260ffd4254d5c9e30d6b60_33.png)

### 处理缺失值的影响

![](img/76b05b14c8260ffd4254d5c9e30d6b60_35.png)

如果原始数据本身存在缺失值（`NaN`），那么任何包含该缺失值的窗口计算结果都会是 `NaN`。因此，在进行窗口计算前，通常需要先处理缺失值。

![](img/76b05b14c8260ffd4254d5c9e30d6b60_37.png)

以下是处理步骤：
```python
# 1. 创建一份处理过缺失值的数据副本
data_clean = data.dropna()

![](img/76b05b14c8260ffd4254d5c9e30d6b60_39.png)

# 2. 在清洗后的数据上进行窗口计算
window_size = 10
rolling_min_clean = data_clean['close'].rolling(window=window_size).min()
print(rolling_min_clean.head(15))
```
现在，输出结果中只有在窗口未满时才会出现 `NaN`，而不会因为原始数据缺失导致问题。

![](img/76b05b14c8260ffd4254d5c9e30d6b60_41.png)

### 计算多个窗口统计量

我们可以一次性为每个窗口计算多个统计指标，并将结果添加到数据表中。

以下是同时计算滑动窗口的最小值、最大值、均值和标准差的示例：

![](img/76b05b14c8260ffd4254d5c9e30d6b60_43.png)

![](img/76b05b14c8260ffd4254d5c9e30d6b60_45.png)

```python
# 在清洗后的数据上操作
data_clean['roll_min'] = data_clean['close'].rolling(window=window_size).min()
data_clean['roll_max'] = data_clean['close'].rolling(window=window_size).max()
data_clean['roll_mean'] = data_clean['close'].rolling(window=window_size).mean()
data_clean['roll_std'] = data_clean['close'].rolling(window=window_size).std()

# 查看计算结果（查看尾部数据，因为头部有NaN）
print(data_clean[['close', 'roll_min', 'roll_max', 'roll_mean', 'roll_std']].tail())
```

![](img/76b05b14c8260ffd4254d5c9e30d6b60_47.png)

执行后，数据表中会新增四列，分别代表每个时间点上，向前回溯10天窗口内的各项统计值。

![](img/76b05b14c8260ffd4254d5c9e30d6b60_49.png)

---

![](img/76b05b14c8260ffd4254d5c9e30d6b60_51.png)

![](img/76b05b14c8260ffd4254d5c9e30d6b60_53.png)

## 总结

![](img/76b05b14c8260ffd4254d5c9e30d6b60_55.png)

本节课中我们一起学习了时间序列的两个重要操作：
1.  **重采样**：使用 `resample()` 方法改变数据的时间频率（如日变周、周变月），并通过 `label` 参数控制聚合结果的标签。
2.  **时间窗口**：使用 `rolling()` 方法在滑动窗口上计算统计指标（如最小值、移动平均）。进行此操作前，务必**先处理原始数据中的缺失值**，以避免计算结果出现大量 `NaN`。

![](img/76b05b14c8260ffd4254d5c9e30d6b60_57.png)

掌握这两个操作，能帮助你从不同时间维度和数据片段中提取有价值的信息，为后续的金融分析和建模打下坚实基础。