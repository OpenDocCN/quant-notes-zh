# Python金融分析与量化交易实战教程：P6：04-4-时间序列重采样操作 📊

在本节课中，我们将要学习时间序列分析中的两个核心操作：**重采样**与**时间窗口**。这些操作是金融数据分析中调整数据频率和计算滚动统计指标的基础。

![](img/0affb74b9d9e4fbd996fa54ee136b01d_1.png)

上一节我们介绍了时间序列的基本概念，本节中我们来看看如何对时间序列数据进行频率转换和滑动窗口计算。

![](img/0affb74b9d9e4fbd996fa54ee136b01d_3.png)

## 重采样操作 🔄

重采样是指改变时间序列数据的频率。例如，原始数据可能是按天记录的，我们可以将其转换为按周、按月或按年进行统计。

![](img/0affb74b9d9e4fbd996fa54ee136b01d_5.png)

![](img/0affb74b9d9e4fbd996fa54ee136b01d_7.png)

对数据进行重采样操作，需要使用 `resample` 方法。该方法至少需要一个参数，即指定新的时间频率单位（如‘W’代表周，‘M’代表月）。重采样后，还需要指定一个聚合函数（如均值、第一个值、中位数等）来计算新频率下的数据值。

![](img/0affb74b9d9e4fbd996fa54ee136b01d_9.png)

以下是重采样的基本代码示例：

![](img/0affb74b9d9e4fbd996fa54ee136b01d_11.png)

```python
# 假设 df 是一个以日期为索引的DataFrame
# 按周进行重采样，并计算每周的均值
weekly_data = df.resample('W').mean()
print(weekly_data.head())
```

执行上述代码后，数据会按周（7天一个窗口）进行分组，并计算每个窗口内数据的平均值。结果中的日期标签默认是每个时间窗口的**结束日期**（右边界）。

### 标签位置参数

![](img/0affb74b9d9e4fbd996fa54ee136b01d_13.png)

![](img/0affb74b9d9e4fbd996fa54ee136b01d_15.png)

在重采样时，我们可以通过 `label` 参数来控制结果索引的标签是使用时间窗口的起始日期（左边界）还是结束日期（右边界）。

以下是使用不同标签的示例：

![](img/0affb74b9d9e4fbd996fa54ee136b01d_17.png)

![](img/0affb74b9d9e4fbd996fa54ee136b01d_19.png)

```python
# 使用右边界标签（默认）
resampled_right = df.resample('M', label='right').mean()
# 使用左边界标签
resampled_left = df.resample('M', label='left').mean()
```

![](img/0affb74b9d9e4fbd996fa54ee136b01d_21.png)

![](img/0affb74b9d9e4fbd996fa54ee136b01d_23.png)

*   `label=‘right’`：使用时间窗口的结束日期作为标签。例如，一月份的数据会被标记为1月31日。这符合大多数观察逻辑，因为我们通常用周期末点来代表整个周期的统计结果。
*   `label=‘left’`：使用时间窗口的起始日期作为标签。例如，一月份的数据会被标记为1月1日。在某些特定分析场景下可能会用到。

大部分情况下，使用 `label=‘right’` 更为合适和直观。

## 时间窗口（滑动窗口）操作 🪟

时间窗口操作，也称为滑动窗口操作，用于计算数据在连续时间段内的滚动统计量。例如，我们有一个100天的序列，可以计算每连续10天（一个窗口）的统计指标（如最小值、最大值、均值），然后窗口向后滑动一天，计算下一个10天窗口的统计量，依此类推。

![](img/0affb74b9d9e4fbd996fa54ee136b01d_25.png)

![](img/0affb74b9d9e4fbd996fa54ee136b01d_27.png)

要实现滑动窗口计算，我们使用 `rolling()` 方法。你需要指定窗口的大小。

以下是创建滑动窗口并计算滚动最小值的基本步骤：

![](img/0affb74b9d9e4fbd996fa54ee136b01d_29.png)

```python
# 首先，处理数据中的缺失值，否则会影响窗口计算
df_clean = df.dropna()

![](img/0affb74b9d9e4fbd996fa54ee136b01d_31.png)

# 定义窗口大小
window_size = 10

# 对某一列数据应用滚动窗口，并计算每个窗口的最小值
rolling_min = df_clean[‘column_name’].rolling(window=window_size).min()
print(rolling_min.head(15))
```

![](img/0affb74b9d9e4fbd996fa54ee136b01d_33.png)

代码执行后，前 `window_size - 1` 个结果会是 `NaN`，因为第一个完整的窗口需要凑够10个数据点。从第10个数据点开始，才会输出有效的滚动统计值。

![](img/0affb74b9d9e4fbd996fa54ee136b01d_35.png)

### 计算多个滚动统计指标

![](img/0affb74b9d9e4fbd996fa54ee136b01d_37.png)

![](img/0affb74b9d9e4fbd996fa54ee136b01d_39.png)

我们可以方便地为一个数据集计算多个滚动统计指标，并将结果作为新列添加到DataFrame中。

![](img/0affb74b9d9e4fbd996fa54ee136b01d_41.png)

以下是同时计算滚动最小值、最大值、均值和标准差的示例：

```python
window_size = 10
df_clean = df.dropna()

![](img/0affb74b9d9e4fbd996fa54ee136b01d_43.png)

![](img/0affb74b9d9e4fbd996fa54ee136b01d_45.png)

# 计算各项滚动统计指标并添加为新列
df_clean[‘rolling_min’] = df_clean[‘column_name’].rolling(window=window_size).min()
df_clean[‘rolling_max’] = df_clean[‘column_name’].rolling(window=window_size).max()
df_clean[‘rolling_mean’] = df_clean[‘column_name’].rolling(window=window_size).mean()
df_clean[‘rolling_std’] = df_clean[‘column_name’].rolling(window=window_size).std()

# 查看结果
print(df_clean[[‘rolling_min‘， ’rolling_max‘， ’rolling_mean‘， ’rolling_std‘]].tail())
```

![](img/0affb74b9d9e4fbd996fa54ee136b01d_47.png)

![](img/0affb74b9d9e4fbd996fa54ee136b01d_49.png)

**重要提示**：在对时间序列进行窗口操作前，务必先处理缺失值（`NaN`）。如果原始数据存在缺失值，`rolling` 计算会产出大量的 `NaN` 结果，影响后续分析。通常使用 `df.dropna()` 或 `df.fillna()` 方法进行预处理。

![](img/0affb74b9d9e4fbd996fa54ee136b01d_51.png)

![](img/0affb74b9d9e4fbd996fa54ee136b01d_53.png)

---

![](img/0affb74b9d9e4fbd996fa54ee136b01d_55.png)

![](img/0affb74b9d9e4fbd996fa54ee136b01d_57.png)

本节课中我们一起学习了时间序列的两个关键操作：**重采样**与**时间窗口**。重采样（`resample`）用于改变数据的时间频率，而时间窗口（`rolling`）用于计算滚动时间区间内的连续统计指标。掌握这两个操作，能为金融时间序列的波动分析、趋势平滑和指标构建打下坚实基础。