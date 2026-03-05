# 金融量化分析：P7：4-时间序列重采样与窗口操作 📊

在本节课中，我们将要学习时间序列分析中的两个核心操作：**重采样**与**滑动窗口**。这些操作是金融数据分析的基础，能够帮助我们将原始数据转换为不同时间尺度或计算滚动统计指标，为后续建模和策略制定做准备。

---

![](img/7e970516b478c8764c660b59e41f52dd_1.png)

## 重采样操作 🔄

![](img/7e970516b478c8764c660b59e41f52dd_3.png)

上一节我们介绍了时间序列的基本概念，本节中我们来看看如何改变数据的时间频率，即重采样。

重采样是指将数据从一个时间频率转换到另一个时间频率。例如，原始数据可能是按天记录的，我们可以将其转换为按周、按月或按年进行统计。

![](img/7e970516b478c8764c660b59e41f52dd_5.png)

![](img/7e970516b478c8764c660b59e41f52dd_6.png)

操作的核心是使用 `resample` 方法。你需要指定新的时间频率（如‘W’代表周，‘M’代表月），并选择一个聚合函数（如均值、首个值、最大值等）来计算新频率下的数据点。

![](img/7e970516b478c8764c660b59e41f52dd_7.png)

以下是重采样的基本代码示例：

![](img/7e970516b478c8764c660b59e41f52dd_9.png)

```python
# 假设 df 是一个以日期为索引的DataFrame
# 按周进行重采样，并计算每周的均值
weekly_data = df.resample('W').mean()
```

### 标签参数：`label`

在重采样时，你还可以指定 `label` 参数，它决定了新数据点的索引标签使用窗口的起始日（`label='left'`）还是结束日（`label='right'`）。

- **`label='right'`（默认）**：使用窗口的结束日期作为标签。这通常更符合逻辑，因为它代表该时间段内数据的统计结果。
- **`label='left'`**：使用窗口的起始日期作为标签。这可能导致标签日期在原始数据中不存在，需要根据具体任务谨慎选择。

![](img/7e970516b478c8764c660b59e41f52dd_11.png)

![](img/7e970516b478c8764c660b59e41f52dd_12.png)

以下是使用不同标签的示例：

![](img/7e970516b478c8764c660b59e41f52dd_13.png)

```python
# 按月重采样，使用窗口结束日作为标签（默认）
monthly_right = df.resample('M', label='right').mean()

![](img/7e970516b478c8764c660b59e41f52dd_15.png)

![](img/7e970516b478c8764c660b59e41f52dd_16.png)

![](img/7e970516b478c8764c660b59e41f52dd_17.png)

# 按月重采样，使用窗口起始日作为标签
monthly_left = df.resample('M', label='left').mean()
```

---

## 滑动窗口操作 🪟

了解了如何改变数据频率后，我们来看看如何在固定长度的连续时间段内进行滚动计算，即滑动窗口操作。

滑动窗口操作不是改变数据频率，而是在一个固定大小的“窗口”内（例如10天）沿着时间轴滑动，并对每个窗口内的数据进行计算（如最小值、最大值、均值等）。这常用于计算移动平均线等技术指标。

![](img/7e970516b478c8764c660b59e41f52dd_19.png)

![](img/7e970516b478c8764c660b59e41f52dd_20.png)

操作的核心是使用 `rolling` 方法。你需要指定窗口的大小（如 `window=10`），然后调用聚合函数。

以下是滑动窗口操作的基本代码示例：

```python
# 对某一列数据计算10天的滚动最小值
rolling_min = df['column_name'].rolling(window=10).min()
```

![](img/7e970516b478c8764c660b59e41f52dd_22.png)

![](img/7e970516b478c8764c660b59e41f52dd_23.png)

### 数据预处理的重要性

在进行窗口计算前，**必须处理缺失值**。如果窗口内存在缺失值（NaN），计算结果也会是NaN，导致数据不完整。

以下是处理缺失值并进行窗口计算的完整流程：

![](img/7e970516b478c8764c660b59e41f52dd_25.png)

```python
# 1. 处理缺失值
df_clean = df.dropna()

![](img/7e970516b478c8764c660b59e41f52dd_26.png)

![](img/7e970516b478c8764c660b59e41f52dd_27.png)

![](img/7e970516b478c8764c660b59e41f52dd_28.png)

# 2. 定义窗口大小
window_size = 10

![](img/7e970516b478c8764c660b59e41f52dd_30.png)

# 3. 计算多个滚动统计指标
df_clean['rolling_min'] = df_clean['column_name'].rolling(window=window_size).min()
df_clean['rolling_max'] = df_clean['column_name'].rolling(window=window_size).max()
df_clean['rolling_mean'] = df_clean['column_name'].rolling(window=window_size).mean()
df_clean['rolling_std'] = df_clean['column_name'].rolling(window=window_size).std()

# 查看结果
print(df_clean[['rolling_min', 'rolling_max', 'rolling_mean', 'rolling_std']].tail())
```

![](img/7e970516b478c8764c660b59e41f52dd_32.png)

---

## 总结 📝

![](img/7e970516b478c8764c660b59e41f52dd_34.png)

本节课中我们一起学习了时间序列分析的两个关键操作：

![](img/7e970516b478c8764c660b59e41f52dd_35.png)

![](img/7e970516b478c8764c660b59e41f52dd_36.png)

![](img/7e970516b478c8764c660b59e41f52dd_37.png)

1.  **重采样**：使用 `resample()` 方法改变数据的时间频率（如日变周、月），并通过 `label` 参数控制索引标签的显示方式。
2.  **滑动窗口**：使用 `rolling()` 方法在固定长度的窗口内进行滚动计算，以得到如移动平均、标准差等动态统计指标。

特别需要注意的是，在进行任何分析操作（尤其是窗口计算）之前，**数据预处理（如处理缺失值）是必不可少的第一步**，它直接影响到结果的准确性和可用性。

![](img/7e970516b478c8764c660b59e41f52dd_39.png)

![](img/7e970516b478c8764c660b59e41f52dd_40.png)

![](img/7e970516b478c8764c660b59e41f52dd_41.png)

掌握重采样和滑动窗口操作，能够帮助你灵活地转换和分析金融时间序列数据，为构建量化交易模型打下坚实的基础。