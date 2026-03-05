# Python金融量化分析：P8：时间序列重采样与窗口操作 📊

在本节课中，我们将学习时间序列分析中的两个核心操作：**重采样**与**时间窗口操作**。重采样用于改变数据的时间频率，而窗口操作则用于计算滑动窗口内的统计指标。掌握这些技巧对于金融数据的分析和特征工程至关重要。

![](img/af3837e518e04e65bed3fd4a0c872cdc_1.png)

---

![](img/af3837e518e04e65bed3fd4a0c872cdc_3.png)

## 重采样操作 🔄

![](img/af3837e518e04e65bed3fd4a0c872cdc_5.png)

上一节我们介绍了时间序列的基础操作，本节中我们来看看如何改变数据的时间频率，即重采样。

![](img/af3837e518e04e65bed3fd4a0c872cdc_7.png)

![](img/af3837e518e04e65bed3fd4a0c872cdc_9.png)

重采样是指将数据从一个时间频率转换到另一个时间频率。例如，原始数据是按天统计的，我们可以将其转换为按周、按月或按年统计。在转换过程中，需要指定聚合函数（如均值、第一个值、中位数等）来计算新频率下的数据值。

![](img/af3837e518e04e65bed3fd4a0c872cdc_11.png)

以下是重采样的基本语法：

![](img/af3837e518e04e65bed3fd4a0c872cdc_13.png)

```python
# 假设df是一个以日期为索引的DataFrame
# 按周重采样，计算每周的均值
weekly_data = df.resample('W').mean()

# 按月重采样，计算每月的第一个值
monthly_data = df.resample('M').first()
```

### 重采样的标签参数

![](img/af3837e518e04e65bed3fd4a0c872cdc_15.png)

![](img/af3837e518e04e65bed3fd4a0c872cdc_17.png)

在重采样时，可以指定`label`参数来决定新时间点的标签是使用时间窗口的起始点（`'left'`）还是结束点（`'right'`）。

```python
# 使用时间窗口的结束点作为标签（默认）
monthly_right = df.resample('M', label='right').mean()

![](img/af3837e518e04e65bed3fd4a0c872cdc_19.png)

![](img/af3837e518e04e65bed3fd4a0c872cdc_21.png)

# 使用时间窗口的起始点作为标签
monthly_left = df.resample('M', label='left').mean()
```

![](img/af3837e518e04e65bed3fd4a0c872cdc_23.png)

![](img/af3837e518e04e65bed3fd4a0c872cdc_25.png)

**核心概念**：`label`参数决定了聚合后数据的索引标签。选择`'right'`（使用窗口结束日期）通常更符合逻辑，因为它代表该时间段内数据的汇总结果。

---

## 时间窗口操作 🪟

上一节我们介绍了重采样，本节中我们来看看如何对时间序列进行滑动窗口分析。

![](img/af3837e518e04e65bed3fd4a0c872cdc_27.png)

![](img/af3837e518e04e65bed3fd4a0c872cdc_29.png)

时间窗口操作，也称为滑动窗口，是指在一个固定大小的窗口内（例如10天）计算统计指标，然后让这个窗口在时间轴上滑动，从而得到一系列连续的统计结果。这种方法常用于计算移动平均线、波动率等指标。

以下是进行时间窗口操作的基本步骤：

![](img/af3837e518e04e65bed3fd4a0c872cdc_31.png)

```python
# 1. 首先处理缺失值，避免影响窗口计算
df_clean = df.dropna()

![](img/af3837e518e04e65bed3fd4a0c872cdc_33.png)

# 2. 定义窗口大小
window_size = 10

![](img/af3837e518e04e65bed3fd4a0c872cdc_35.png)

![](img/af3837e518e04e65bed3fd4a0c872cdc_37.png)

# 3. 对某一列数据计算滑动窗口的最小值
rolling_min = df_clean['column_name'].rolling(window=window_size).min()

# 4. 将计算结果添加到DataFrame中
df_clean['rolling_min'] = rolling_min
```

![](img/af3837e518e04e65bed3fd4a0c872cdc_39.png)

![](img/af3837e518e04e65bed3fd4a0c872cdc_41.png)

### 计算多个窗口统计指标

![](img/af3837e518e04e65bed3fd4a0c872cdc_43.png)

![](img/af3837e518e04e65bed3fd4a0c872cdc_45.png)

我们可以同时对同一个窗口计算多个统计量，如最小值、最大值、均值和标准差。

![](img/af3837e518e04e65bed3fd4a0c872cdc_47.png)

以下是具体操作：

![](img/af3837e518e04e65bed3fd4a0c872cdc_49.png)

![](img/af3837e518e04e65bed3fd4a0c872cdc_51.png)

```python
# 计算并添加多个滑动窗口统计指标
df_clean['rolling_min'] = df_clean['column_name'].rolling(window=window_size).min()
df_clean['rolling_max'] = df_clean['column_name'].rolling(window=window_size).max()
df_clean['rolling_mean'] = df_clean['column_name'].rolling(window=window_size).mean()
df_clean['rolling_std'] = df_clean['column_name'].rolling(window=window_size).std()

![](img/af3837e518e04e65bed3fd4a0c872cdc_53.png)

# 查看结果
print(df_clean.tail())
```

![](img/af3837e518e04e65bed3fd4a0c872cdc_55.png)

**核心概念**：`.rolling()`方法创建了一个滑动窗口视图，其后可以接`.min()`、`.max()`、`.mean()`、`.std()`等聚合函数来计算窗口内的统计值。

![](img/af3837e518e04e65bed3fd4a0c872cdc_57.png)

![](img/af3837e518e04e65bed3fd4a0c872cdc_59.png)

**重要提示**：在进行窗口操作前，务必先处理数据中的缺失值（`NaN`）。如果窗口内存在缺失值，会导致整个窗口的统计结果变为`NaN`。

---

![](img/af3837e518e04e65bed3fd4a0c872cdc_61.png)

![](img/af3837e518e04e65bed3fd4a0c872cdc_63.png)

## 总结 📝

![](img/af3837e518e04e65bed3fd4a0c872cdc_65.png)

![](img/af3837e518e04e65bed3fd4a0c872cdc_67.png)

本节课中我们一起学习了时间序列分析的两个重要工具：
1.  **重采样**：通过`df.resample()`方法改变数据的时间频率，并配合`label`参数控制结果标签。
2.  **时间窗口操作**：通过`df['column'].rolling()`方法创建滑动窗口，并计算窗口内的各种统计指标（如最小值、最大值、均值、标准差）。

![](img/af3837e518e04e65bed3fd4a0c872cdc_69.png)

![](img/af3837e518e04e65bed3fd4a0c872cdc_71.png)

这两个操作是金融时间序列特征提取和数据分析的基础，能够帮助我们以不同的时间维度观察数据，并提取出有意义的趋势和波动特征。