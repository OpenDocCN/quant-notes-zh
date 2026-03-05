# Python量化交易：P8：时间序列重采样与窗口操作 📊

在本节课中，我们将要学习时间序列分析中的两个核心操作：**重采样**与**时间窗口操作**。重采样用于改变数据的时间频率，而时间窗口操作则用于在滑动窗口上计算统计指标。掌握这些技能对于金融数据分析至关重要。

![](img/857ebdb1481f547af9c4ede249aa0481_1.png)

---

![](img/857ebdb1481f547af9c4ede249aa0481_3.png)

![](img/857ebdb1481f547af9c4ede249aa0481_4.png)

## 重采样操作 🔄

![](img/857ebdb1481f547af9c4ede249aa0481_6.png)

![](img/857ebdb1481f547af9c4ede249aa0481_7.png)

![](img/857ebdb1481f547af9c4ede249aa0481_8.png)

上一节我们介绍了时间序列的基础概念，本节中我们来看看如何改变数据的时间频率，即重采样。

![](img/857ebdb1481f547af9c4ede249aa0481_10.png)

重采样是指将数据从一个时间频率转换到另一个时间频率。例如，原始数据可能是按天统计的，我们可以将其转换为按周、按月或以年为单位的统计数据。

以下是重采样的基本步骤：
1.  指定新的时间频率单位（如‘W’代表周，‘M’代表月）。
2.  指定聚合数据时使用的统计方法（如均值、第一个值、中位数等）。
3.  可选地指定标签位置，决定使用窗口的起始日还是结束日作为新数据的索引标签。

![](img/857ebdb1481f547af9c4ede249aa0481_12.png)

在Pandas中，使用`resample()`方法进行重采样。其核心公式如下：
```python
# 假设df是一个以日期为索引的DataFrame
# 按周重采样，计算每周的均值
weekly_data = df.resample(‘W’).mean()
# 按月重采样，计算每月的第一个值
monthly_data = df.resample(‘M’).first()
```

![](img/857ebdb1481f547af9c4ede249aa0481_13.png)

![](img/857ebdb1481f547af9c4ede249aa0481_14.png)

![](img/857ebdb1481f547af9c4ede249aa0481_16.png)

### 标签参数详解

![](img/857ebdb1481f547af9c4ede249aa0481_17.png)

![](img/857ebdb1481f547af9c4ede249aa0481_18.png)

在重采样时，`label`参数决定了新数据点的索引标签使用窗口的哪一端。
*   **`label=‘right’` (默认)**：使用窗口的结束日期作为标签。例如，月度数据中，1月31日的值代表1月份整月的统计结果。
*   **`label=‘left’`**：使用窗口的起始日期作为标签。例如，月度数据中，12月31日的标签可能对应从该日开始的下一个月的数据统计。

对于大多数观察逻辑，使用`label=‘right’`更为直观和常见。

---

![](img/857ebdb1481f547af9c4ede249aa0481_20.png)

![](img/857ebdb1481f547af9c4ede249aa0481_21.png)

## 时间窗口（滑动窗口）操作 🪟

了解了如何改变数据频率后，我们来看看如何在连续的时间段上进行滑动分析，即时间窗口操作。

![](img/857ebdb1481f547af9c4ede249aa0481_23.png)

![](img/857ebdb1481f547af9c4ede249aa0481_24.png)

![](img/857ebdb1481f547af9c4ede249aa0481_25.png)

时间窗口操作是指定义一个固定长度的窗口（例如10天），让这个窗口在时间序列上滑动。对于每一个窗口位置，我们都可以计算其内部的统计指标（如最小值、最大值、均值、标准差等）。这有助于分析数据的局部趋势和波动。

以下是执行时间窗口操作的基本步骤：
1.  **数据预处理**：处理缺失值，确保窗口计算的有效性。
2.  **定义窗口**：使用`rolling()`方法并指定窗口大小。
3.  **应用统计函数**：对每个窗口应用聚合函数，如`.min()`, `.max()`, `.mean()`, `.std()`等。

![](img/857ebdb1481f547af9c4ede249aa0481_27.png)

![](img/857ebdb1481f547af9c4ede249aa0481_28.png)

![](img/857ebdb1481f547af9c4ede249aa0481_29.png)

其核心代码结构如下：
```python
# 1. 预处理：删除缺失值
df_clean = df.dropna()

![](img/857ebdb1481f547af9c4ede249aa0481_30.png)

![](img/857ebdb1481f547af9c4ede249aa0481_31.png)

![](img/857ebdb1481f547af9c4ede249aa0481_32.png)

# 2. 定义窗口大小并计算滚动统计量
window_size = 10
df_clean[‘rolling_min’] = df_clean[‘column_name’].rolling(window=window_size).min()
df_clean[‘rolling_max’] = df_clean[‘column_name’].rolling(window=window_size).max()
df_clean[‘rolling_mean’] = df_clean[‘column_name’].rolling(window=window_size).mean()
df_clean[‘rolling_std’] = df_clean[‘column_name’].rolling(window=window_size).std()
```

![](img/857ebdb1481f547af9c4ede249aa0481_34.png)

![](img/857ebdb1481f547af9c4ede249aa0481_36.png)

**重要提示**：在开始窗口计算前，务必进行数据预处理，剔除缺失值（NaN）。否则，任何包含NaN的窗口，其计算结果也将是NaN，导致大量数据无法正常展示。

![](img/857ebdb1481f547af9c4ede249aa0481_38.png)

![](img/857ebdb1481f547af9c4ede249aa0481_39.png)

---

## 总结 📝

![](img/857ebdb1481f547af9c4ede249aa0481_41.png)

![](img/857ebdb1481f547af9c4ede249aa0481_42.png)

![](img/857ebdb1481f547af9c4ede249aa0481_43.png)

![](img/857ebdb1481f547af9c4ede249aa0481_44.png)

本节课中我们一起学习了时间序列分析的两个强大工具：
1.  **重采样**：用于转换数据的时间频率（如日→周→月），通过`df.resample(freq).agg_func()`实现，并可利用`label`参数控制索引标签。
2.  **时间窗口（滑动窗口）操作**：用于在滑动的数据片段上计算统计指标，通过`df[‘col’].rolling(window).agg_func()`实现。**切记先使用`df.dropna()`清理数据**，这是保证分析结果准确的关键第一步。

![](img/857ebdb1481f547af9c4ede249aa0481_46.png)

![](img/857ebdb1481f547af9c4ede249aa0481_47.png)

![](img/857ebdb1481f547af9c4ede249aa0481_48.png)

掌握重采样和窗口操作，能让你从不同时间维度和局部视角深入洞察金融时间序列数据，为后续的建模与分析打下坚实基础。