# Python金融分析与量化交易实战：P8：时间序列重采样与窗口操作 📊

在本节课中，我们将要学习时间序列分析中的两个核心操作：**重采样**与**时间窗口操作**。重采样用于改变数据的时间频率，而时间窗口操作则用于计算滑动窗口内的统计指标。掌握这两个技巧对于金融数据的分析和特征工程至关重要。

---

![](img/78a8a7068b0bb31e647da1145bbd051f_1.png)

## 重采样操作 🔄

![](img/78a8a7068b0bb31e647da1145bbd051f_3.png)

上一节我们介绍了时间序列的基础知识，本节中我们来看看如何改变数据的时间频率，即重采样。

重采样是指将数据从一个时间频率转换到另一个时间频率。例如，原始数据可能是按天统计的，我们可以将其调整为按周、按月或按年统计。

![](img/78a8a7068b0bb31e647da1145bbd051f_5.png)

![](img/78a8a7068b0bb31e647da1145bbd051f_6.png)

![](img/78a8a7068b0bb31e647da1145bbd051f_7.png)

在重采样过程中，你需要指定新的时间单位（如“W”代表周，“M”代表月）以及一个聚合函数（如均值、第一个值、最后一个值等）来计算新频率下的数据点。

以下是重采样的基本语法：

![](img/78a8a7068b0bb31e647da1145bbd051f_9.png)

```python
# 假设 df 是一个以日期为索引的 DataFrame
# 按周重采样，计算每周的均值
weekly_data = df.resample('W').mean()

# 按月重采样，计算每月的第一个值
monthly_data = df.resample('M').first()
```

执行重采样后，你会得到一份按新时间频率聚合后的数据结果。

### 重采样的标签参数

![](img/78a8a7068b0bb31e647da1145bbd051f_11.png)

![](img/78a8a7068b0bb31e647da1145bbd051f_12.png)

![](img/78a8a7068b0bb31e647da1145bbd051f_13.png)

在重采样时，还可以指定 `label` 参数，它决定了新数据点的索引标签使用窗口的起始点（`label='left'`）还是结束点（`label='right'`）。

![](img/78a8a7068b0bb31e647da1145bbd051f_15.png)

- **`label='right'`（默认）**：使用时间窗口的结束日期作为标签。这通常更符合逻辑，因为它代表该时间段内数据的汇总结果。
- **`label='left'`**：使用时间窗口的起始日期作为标签。

![](img/78a8a7068b0bb31e647da1145bbd051f_16.png)

![](img/78a8a7068b0bb31e647da1145bbd051f_17.png)

例如，按月重采样时，`label='right'` 会使用每月的最后一天（如1月31日）作为该月数据的标签，而 `label='left'` 则会使用每月的第一天（如1月1日）。在大多数分析场景中，使用 `label='right'` 更为合适。

---

## 时间窗口（滑动窗口）操作 🪟

接下来，我们学习时间窗口操作，也称为滑动窗口操作。这与重采样不同，它不是改变数据频率，而是在一个固定大小的窗口上滑动，并计算窗口内的统计量。

![](img/78a8a7068b0bb31e647da1145bbd051f_19.png)

![](img/78a8a7068b0bb31e647da1145bbd051f_20.png)

例如，你有一个包含100天数据的序列。你可以定义一个大小为10天的窗口：
- 第一个窗口：第1天到第10天。
- 第二个窗口：第2天到第11天（滑动了一个单位）。
- 第三个窗口：第3天到第12天。
- 以此类推...

这个过程可以计算每个窗口内的统计指标，如最小值、最大值、均值、标准差等，常用于计算移动平均线等技术指标。

### 如何执行窗口操作

![](img/78a8a7068b0bb31e647da1145bbd051f_22.png)

以下是使用Pandas进行滑动窗口计算的基本步骤：

![](img/78a8a7068b0bb31e647da1145bbd051f_23.png)

![](img/78a8a7068b0bb31e647da1145bbd051f_24.png)

1.  **数据准备**：首先，处理数据中的缺失值（NaN），因为缺失值会影响窗口计算的结果。
2.  **应用窗口**：使用 `.rolling()` 方法创建窗口对象。
3.  **计算统计量**：在窗口对象上应用聚合函数。

以下是具体代码示例：

```python
import pandas as pd

![](img/78a8a7068b0bb31e647da1145bbd051f_26.png)

# 1. 准备数据，假设 df 是原始数据，并处理缺失值
df_clean = df.dropna()

![](img/78a8a7068b0bb31e647da1145bbd051f_27.png)

![](img/78a8a7068b0bb31e647da1145bbd051f_28.png)

![](img/78a8a7068b0bb31e647da1145bbd051f_29.png)

# 2. 定义窗口大小
window_size = 10

![](img/78a8a7068b0bb31e647da1145bbd051f_30.png)

![](img/78a8a7068b0bb31e647da1145bbd051f_31.png)

# 3. 对某一列数据应用窗口，并计算每个窗口的最小值
min_values = df_clean['column_name'].rolling(window=window_size).min()

![](img/78a8a7068b0bb31e647da1145bbd051f_33.png)

# 4. 可以同时计算多个统计指标
df_clean['min'] = df_clean['column_name'].rolling(window=window_size).min()
df_clean['max'] = df_clean['column_name'].rolling(window=window_size).max()
df_clean['mean'] = df_clean['column_name'].rolling(window=window_size).mean()
df_clean['std'] = df_clean['column_name'].rolling(window=window_size).std()

![](img/78a8a7068b0bb31e647da1145bbd051f_35.png)

# 查看结果
print(df_clean[['min', 'max', 'mean', 'std']].tail())
```

![](img/78a8a7068b0bb31e647da1145bbd051f_37.png)

![](img/78a8a7068b0bb31e647da1145bbd051f_38.png)

**关键点**：在进行窗口操作前，务必先处理缺失值（例如使用 `.dropna()`），否则包含NaN的窗口会导致计算结果也为NaN。

---

## 总结 📝

![](img/78a8a7068b0bb31e647da1145bbd051f_40.png)

![](img/78a8a7068b0bb31e647da1145bbd051f_41.png)

本节课中我们一起学习了时间序列分析的两个重要操作：

![](img/78a8a7068b0bb31e647da1145bbd051f_42.png)

![](img/78a8a7068b0bb31e647da1145bbd051f_43.png)

![](img/78a8a7068b0bb31e647da1145bbd051f_44.png)

1.  **重采样**：通过 `df.resample(freq).agg_func()` 改变数据的时间频率（如从天到周、月），并可利用 `label` 参数控制索引标签的显示方式。
2.  **时间窗口操作**：通过 `df[‘column’].rolling(window=size).agg_func()` 在滑动窗口上计算各种统计指标（如最小值、最大值、均值、标准差）。**操作前务必进行数据清洗，处理缺失值**。

![](img/78a8a7068b0bb31e647da1145bbd051f_45.png)

![](img/78a8a7068b0bb31e647da1145bbd051f_47.png)

这两个工具是金融时间序列分析和构建量化交易特征的基础，熟练掌握它们将为后续更复杂的模型分析打下坚实基础。