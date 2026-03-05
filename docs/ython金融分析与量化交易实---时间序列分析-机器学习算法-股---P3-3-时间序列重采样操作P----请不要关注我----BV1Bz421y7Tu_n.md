# 量化交易实战：P3：时间序列重采样与窗口操作 📊

![](img/4f7d903acbedab3d4112633bcad43dec_0.png)

在本节课中，我们将学习时间序列分析中的两个核心操作：**重采样**与**时间窗口（滑动窗口）**。这些操作是金融数据分析的基础，能帮助我们以不同的时间粒度观察数据，并计算移动统计指标。

## 概述

![](img/4f7d903acbedab3d4112633bcad43dec_2.png)

![](img/4f7d903acbedab3d4112633bcad43dec_4.png)

时间序列数据通常以固定的频率记录，例如每日股价。但在分析时，我们可能需要以周、月或年为单位来观察趋势，或者计算过去一段时间内的移动平均值等指标。本节将介绍如何使用Pandas库实现这些转换。

---

![](img/4f7d903acbedab3d4112633bcad43dec_6.png)

![](img/4f7d903acbedab3d4112633bcad43dec_8.png)

## 时间序列重采样 🔄

![](img/4f7d903acbedab3d4112633bcad43dec_10.png)

上一节我们介绍了时间序列的基本操作，本节中我们来看看如何改变数据的时间频率，即**重采样**。

![](img/4f7d903acbedab3d4112633bcad43dec_12.png)

原始数据可能以天为单位。重采样允许我们将数据调整为以周、月或年等其他时间单位进行统计。这通过Pandas的 `resample()` 方法实现。

![](img/4f7d903acbedab3d4112633bcad43dec_14.png)

重采样操作至少需要一个参数：指定新的时间频率单位。例如，`‘W’` 表示以周为单位。同时，你需要指定如何聚合每个新时间窗口内的数据，例如计算均值、第一个值或中位数。

以下是重采样的基本步骤：

1.  加载时间序列数据。
2.  使用 `df.resample(‘新频率’).聚合函数()` 进行重采样。

![](img/4f7d903acbedab3d4112633bcad43dec_16.png)

例如，将日数据重采样为周数据并计算每周的均值：
```python
weekly_data = df.resample(‘W’).mean()
```

### 重采样的标签设置

![](img/4f7d903acbedab3d4112633bcad43dec_18.png)

![](img/4f7d903acbedab3d4112633bcad43dec_20.png)

在重采样时，还有一个重要参数是 `label`，它决定了每个聚合结果使用哪个时间点作为标签（索引）。

*   `label=‘right’`（默认）：使用时间窗口的**结束日期**作为标签。
*   `label=‘left’`：使用时间窗口的**开始日期**作为标签。

例如，对1月份的数据按月重采样求均值：
*   若 `label=‘right’`，结果标签为 `‘2023-01-31’`，代表1月份整月的统计值。
*   若 `label=‘left’`，结果标签为 `‘2023-01-01’`，但代表的仍是1月份整月的统计值。

![](img/4f7d903acbedab3d4112633bcad43dec_22.png)

选择哪种标签取决于分析逻辑。通常，使用窗口结束日期 (`right`) 更符合认知习惯，因为它明确标识了该统计值所涵盖的时间段终点。

![](img/4f7d903acbedab3d4112633bcad43dec_24.png)

---

![](img/4f7d903acbedab3d4112633bcad43dec_26.png)

## 时间窗口（滑动窗口）操作 ⏩

![](img/4f7d903acbedab3d4112633bcad43dec_28.png)

了解了如何改变数据频率后，我们来看看如何在原有频率上计算移动统计量，即**时间窗口**或**滑动窗口**操作。

![](img/4f7d903acbedab3d4112633bcad43dec_30.png)

![](img/4f7d903acbedab3d4112633bcad43dec_32.png)

时间窗口操作不是改变数据频率，而是在原始时间序列上定义一个固定长度的“窗口”（如10天），让这个窗口在时间轴上滑动。对于每个窗口位置，我们计算其内部数据的统计量（如最小值、最大值、均值）。

![](img/4f7d903acbedab3d4112633bcad43dec_34.png)

![](img/4f7d903acbedab3d4112633bcad43dec_36.png)

其概念可以用以下滑动过程表示：
> 窗口1：数据[第1天 到 第10天] -> 计算统计量1
> 窗口2：数据[第2天 到 第11天] -> 计算统计量2
> 窗口3：数据[第3天 到 第12天] -> 计算统计量3
> ...

![](img/4f7d903acbedab3d4112633bcad43dec_38.png)

![](img/4f7d903acbedab3d4112633bcad43dec_40.png)

### 如何实现滑动窗口

![](img/4f7d903acbedab3d4112633bcad43dec_42.png)

![](img/4f7d903acbedab3d4112633bcad43dec_44.png)

我们使用Pandas的 `rolling()` 方法来实现滑动窗口计算。

以下是实现滑动窗口操作的关键步骤：

![](img/4f7d903acbedab3d4112633bcad43dec_46.png)

![](img/4f7d903acbedab3d4112633bcad43dec_48.png)

1.  **数据预处理**：这是**至关重要**的一步。必须处理原始数据中的缺失值（NaN），否则窗口计算会产生大量NaN结果。通常使用 `df.dropna()` 来删除含有缺失值的行。
2.  **定义窗口**：创建一个窗口对象，例如 `window = 10` 代表10个时间单位的窗口。
3.  **应用计算**：在数据列的Series上调用 `.rolling(window=窗口大小)`，然后接上聚合函数，如 `.mean()`, `.min()`, `.std()` 等。

![](img/4f7d903acbedab3d4112633bcad43dec_50.png)

![](img/4f7d903acbedab3d4112633bcad43dec_52.png)

例如，计算10日移动最小值：
```python
# 假设 ‘close’ 是收盘价列
data[‘close_min_10’] = data[‘close’].rolling(window=10).min()
```

![](img/4f7d903acbedab3d4112633bcad43dec_54.png)

### 同时计算多个窗口统计量

![](img/4f7d903acbedab3d4112633bcad43dec_56.png)

![](img/4f7d903acbedab3d4112633bcad43dec_58.png)

我们可以方便地为同一窗口计算多个统计指标，并将结果作为新列添加到数据框中。

![](img/4f7d903acbedab3d4112633bcad43dec_60.png)

以下是为一个时间序列同时添加10日窗口的最小值、最大值、均值和标准差的示例：
```python
data[‘min_10’] = data[‘close’].rolling(window=10).min()
data[‘max_10’] = data[‘close’].rolling(window=10).max()
data[‘mean_10’] = data[‘close’].rolling(window=10).mean()
data[‘std_10’] = data[‘close’].rolling(window=10).std()
```
执行后，数据框将新增四列，分别代表过去10个时间单位的各项移动统计值。

![](img/4f7d903acbedab3d4112633bcad43dec_62.png)

**核心提醒**：在进行任何 `rolling()` 操作之前，务必确保数据已经过清洗，处理了缺失值，否则前 `window-1` 个位置以及任何包含NaN的窗口的计算结果都将是NaN。

![](img/4f7d903acbedab3d4112633bcad43dec_64.png)

![](img/4f7d903acbedab3d4112633bcad43dec_66.png)

---

## 总结 📝

![](img/4f7d903acbedab3d4112633bcad43dec_68.png)

本节课中我们一起学习了时间序列分析的两个强大工具：

![](img/4f7d903acbedab3d4112633bcad43dec_70.png)

![](img/4f7d903acbedab3d4112633bcad43dec_72.png)

1.  **重采样**：用于改变数据的时间频率（如日变周、月）。通过 `resample()` 方法实现，并可配合 `label` 参数控制聚合结果的标签。
2.  **时间窗口操作**：用于在原有频率上计算移动统计量（如10日移动平均）。通过 `rolling()` 方法实现。**切记，先使用 `dropna()` 等方法处理数据缺失值**，是进行有效窗口计算的前提。

![](img/4f7d903acbedab3d4112633bcad43dec_74.png)

![](img/4f7d903acbedab3d4112633bcad43dec_76.png)

掌握这两种操作，你就能灵活地从不同时间维度分析和观察金融时间序列数据，为后续的量化策略构建打下坚实基础。