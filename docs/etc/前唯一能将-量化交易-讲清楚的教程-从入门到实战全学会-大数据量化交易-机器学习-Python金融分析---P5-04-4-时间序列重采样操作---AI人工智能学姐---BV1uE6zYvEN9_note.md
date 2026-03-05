# Python金融量化：04-4：时间序列重采样与窗口操作 📊

在本节课中，我们将学习时间序列分析中的两个核心操作：**重采样**与**时间窗口**。你将学会如何改变数据的时间频率，以及如何通过滑动窗口计算各种统计指标。

![](img/227cadcb9fd4293c96aa02794abde90e_1.png)

---

![](img/227cadcb9fd4293c96aa02794abde90e_3.png)

## 概述

![](img/227cadcb9fd4293c96aa02794abde90e_5.png)

时间序列数据通常以固定的时间间隔记录，例如每日股价。但在分析时，我们可能需要以不同的频率（如每周、每月）来观察数据，这就是**重采样**。此外，为了分析数据在连续时间段内的趋势，我们会使用**时间窗口**，它像一个滑动的“观察框”，让我们能计算每个窗口内的统计量（如均值、最小值）。

![](img/227cadcb9fd4293c96aa02794abde90e_7.png)

![](img/227cadcb9fd4293c96aa02794abde90e_9.png)

---

## 重采样操作

![](img/227cadcb9fd4293c96aa02794abde90e_11.png)

上一节我们介绍了时间序列的基础。本节中，我们来看看如何改变数据的时间粒度，即进行重采样。

重采样是指将时间序列数据从一个频率转换到另一个频率的过程。例如，将每日数据聚合为每周或每月数据。在Pandas中，这通过`resample()`方法实现。

![](img/227cadcb9fd4293c96aa02794abde90e_13.png)

![](img/227cadcb9fd4293c96aa02794abde90e_15.png)

**核心公式/代码**：
```python
# 假设df是一个以日期为索引的DataFrame
# 以‘W’（周）为频率进行重采样，并计算每周的均值
weekly_data = df.resample('W').mean()
```

以下是重采样的关键参数和步骤：

![](img/227cadcb9fd4293c96aa02794abde90e_17.png)

![](img/227cadcb9fd4293c96aa02794abde90e_19.png)

1.  **指定频率**：你需要告诉`resample()`方法目标频率是什么。常用参数包括：
    *   `'D'`：日
    *   `'W'`：周
    *   `'M'`：月
    *   `'Y'`：年

![](img/227cadcb9fd4293c96aa02794abde90e_21.png)

![](img/227cadcb9fd4293c96aa02794abde90e_23.png)

2.  **指定聚合方法**：重采样后，每个新时间区间内的多个数据点需要聚合为一个值。你可以指定：
    *   `.mean()`：计算均值
    *   `.sum()`：计算总和
    *   `.first()`：取第一个值
    *   `.last()`：取最后一个值

3.  **标签位置**：重采样后，每个新区间需要一个日期标签。`label`参数决定使用区间的开始（`'left'`）还是结束（`'right'`）日期作为标签。通常，使用结束日期（`label='right'`）更符合逻辑，因为它代表该区间数据的总结。

**示例**：将数据从日频率重采样为月频率，并使用月末作为标签。
```python
monthly_data = df.resample('M', label='right').mean()
```

---

![](img/227cadcb9fd4293c96aa02794abde90e_25.png)

![](img/227cadcb9fd4293c96aa02794abde90e_27.png)

## 时间窗口操作

了解了如何改变数据频率后，我们来看看如何分析数据在连续时间段内的局部特征，这需要使用时间窗口。

![](img/227cadcb9fd4293c96aa02794abde90e_29.png)

时间窗口操作，也称为滚动窗口或滑动窗口，是指定义一个固定大小的“窗口”（例如10天），让这个窗口在时间序列上滑动。对于每一个窗口位置，我们都可以计算其内部数据的统计指标。

![](img/227cadcb9fd4293c96aa02794abde90e_31.png)

**核心概念**：假设有100天的数据，窗口大小为10天。
*   第一个窗口：第1天到第10天
*   第二个窗口：第2天到第11天
*   第三个窗口：第3天到第12天
*   ...以此类推

在Pandas中，这通过`rolling()`方法实现。

![](img/227cadcb9fd4293c96aa02794abde90e_33.png)

![](img/227cadcb9fd4293c96aa02794abde90e_35.png)

**核心公式/代码**：
```python
# 计算10天窗口内的滚动最小值
rolling_min = df['column_name'].rolling(window=10).min()
```

![](img/227cadcb9fd4293c96aa02794abde90e_37.png)

以下是执行时间窗口操作的步骤和注意事项：

![](img/227cadcb9fd4293c96aa02794abde90e_39.png)

![](img/227cadcb9fd4293c96aa02794abde90e_41.png)

1.  **数据预处理**：**非常重要的一步**。在进行窗口计算前，必须处理原始数据中的缺失值（NaN）。否则，任何包含NaN的窗口的计算结果都会是NaN。
    ```python
    df_clean = df.dropna() # 删除含有缺失值的行
    ```

2.  **定义窗口并计算**：对清理后的数据使用`rolling()`方法，指定`window`参数（窗口大小），然后链接所需的统计函数。
    ```python
    window_size = 10
    # 在清理后的数据上计算滚动统计量
    df_clean['rolling_min'] = df_clean['column_name'].rolling(window=window_size).min()
    df_clean['rolling_max'] = df_clean['column_name'].rolling(window=window_size).max()
    df_clean['rolling_mean'] = df_clean['column_name'].rolling(window=window_size).mean()
    df_clean['rolling_std'] = df_clean['column_name'].rolling(window=window_size).std()
    ```

![](img/227cadcb9fd4293c96aa02794abde90e_43.png)

![](img/227cadcb9fd4293c96aa02794abde90e_45.png)

3.  **理解结果**：窗口计算的结果序列长度与原数据相同。前 `window_size - 1` 个位置的值通常是NaN，因为直到第 `window_size` 个数据点，第一个完整的窗口才形成。

---

![](img/227cadcb9fd4293c96aa02794abde90e_47.png)

![](img/227cadcb9fd4293c96aa02794abde90e_49.png)

## 总结

![](img/227cadcb9fd4293c96aa02794abde90e_51.png)

![](img/227cadcb9fd4293c96aa02794abde90e_53.png)

本节课中我们一起学习了时间序列分析的两个强大工具：
1.  **重采样**：用于改变数据的时间频率（如日变周、月），通过`df.resample(freq).agg_func()`实现，需注意`label`参数的选择。
2.  **时间窗口**：用于分析数据在连续时间段内的局部统计特征，通过`df.rolling(window).agg_func()`实现。**切记先使用`df.dropna()`清理缺失数据**，这是保证计算正确的关键。

![](img/227cadcb9fd4293c96aa02794abde90e_55.png)

![](img/227cadcb9fd4293c96aa02794abde90e_57.png)

掌握这两种操作，能帮助你从不同维度和尺度深入分析金融时间序列数据，为后续的量化策略构建打下坚实基础。