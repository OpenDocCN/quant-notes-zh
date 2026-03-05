# Python金融量化投资分析与股票交易：P29：28 时间对象生成 📅

在本节课中，我们将学习如何使用Pandas库中的 `pd.date_range` 函数来生成一个时间范围序列。这对于创建时间索引、填充数据或进行时间序列分析至关重要。

![](img/43535718fa43247904bfd3cea8367806_1.png)

上一节我们介绍了如何将字符串批量转换为时间对象。本节中我们来看看如何直接生成一个连续的时间序列。

![](img/43535718fa43247904bfd3cea8367806_3.png)

## 生成时间范围序列

![](img/43535718fa43247904bfd3cea8367806_5.png)

`pd.date_range` 函数是生成时间范围的核心工具。它允许你指定开始时间、结束时间、序列长度以及频率，从而灵活地创建所需的时间点。

![](img/43535718fa43247904bfd3cea8367806_7.png)

以下是 `pd.date_range` 函数的主要参数：

![](img/43535718fa43247904bfd3cea8367806_9.png)

*   **`start`**: 指定时间序列的开始日期。
*   **`end`**: 指定时间序列的结束日期。
*   **`periods`**: 指定要生成的时间点数量（序列长度）。
*   **`freq`**: 指定时间序列的频率（例如，每天‘D’、每小时‘H’）。

![](img/43535718fa43247904bfd3cea8367806_11.png)

你可以通过指定 `start` 和 `end` 来生成一个日期范围，也可以指定 `start` 和 `periods`（长度）来生成。

![](img/43535718fa43247904bfd3cea8367806_13.png)

```python
import pandas as pd

![](img/43535718fa43247904bfd3cea8367806_15.png)

# 方式一：指定开始和结束日期
date_range_1 = pd.date_range(start='2010-01-01', end='2010-01-05')
print(date_range_1)
# 输出：DatetimeIndex(['2010-01-01', '2010-01-02', '2010-01-03', '2010-01-04', '2010-01-05'], dtype='datetime64[ns]', freq='D')

![](img/43535718fa43247904bfd3cea8367806_17.png)

# 方式二：指定开始日期和序列长度
date_range_2 = pd.date_range(start='2010-01-01', periods=5)
print(date_range_2)
# 输出：DatetimeIndex(['2010-01-01', '2010-01-02', '2010-01-03', '2010-01-04', '2010-01-05'], dtype='datetime64[ns]', freq='D')
```

![](img/43535718fa43247904bfd3cea8367806_19.png)

## 设置时间频率

`freq` 参数非常强大，它决定了时间点之间的间隔。默认频率是‘D’（天）。

以下是常用的频率代码示例：

![](img/43535718fa43247904bfd3cea8367806_21.png)

*   **`‘D’`**: 日历日（默认）。
*   **`‘H’`**: 小时。
*   **`‘W’`**: 周，默认从周日开始（‘W-SUN’）。可以使用 `‘W-MON’` 指定从周一开始。
*   **`‘B’`**: 工作日（周一至周五），跳过周末。
*   **`‘M’`**: 月末。
*   **`‘MS’`**: 月初。
*   **`‘T’` 或 `‘min’`**: 分钟。
*   **`‘S’`**: 秒。
*   **`‘A’` 或 `‘Y’`**: 年末。
*   **`‘AS’` 或 `‘YS’`**: 年初。

![](img/43535718fa43247904bfd3cea8367806_23.png)

你还可以组合使用，例如 `‘2H30min’` 表示每2小时30分钟。

![](img/43535718fa43247904bfd3cea8367806_25.png)

```python
# 生成每小时的时间点
hourly_range = pd.date_range(start='2010-01-01', periods=3, freq='H')
print(hourly_range)
# 输出：DatetimeIndex(['2010-01-01 00:00:00', '2010-01-01 01:00:00', '2010-01-01 02:00:00'], dtype='datetime64[ns]', freq='H')

![](img/43535718fa43247904bfd3cea8367806_27.png)

![](img/43535718fa43247904bfd3cea8367806_29.png)

# 生成每周一的时间点
weekly_monday_range = pd.date_range(start='2010-01-01', periods=4, freq='W-MON')
print(weekly_monday_range)
# 输出：DatetimeIndex(['2010-01-04', '2010-01-11', '2010-01-18', '2010-01-25'], dtype='datetime64[ns]', freq='W-MON')
```

![](img/43535718fa43247904bfd3cea8367806_31.png)

## 时间序列的转换与应用

![](img/43535718fa43247904bfd3cea8367806_33.png)

`pd.date_range` 生成的是一个 `DatetimeIndex` 对象。这个对象功能强大，可以直接用于构建Pandas的Series或DataFrame的索引，也可以方便地进行转换。

![](img/43535718fa43247904bfd3cea8367806_35.png)

```python
# 创建一个以时间序列为索引的Series
time_index = pd.date_range(start='2023-01-01', periods=5, freq='D')
data_series = pd.Series([1, 2, 3, 4, 5], index=time_index)
print(data_series)
```

![](img/43535718fa43247904bfd3cea8367806_37.png)

虽然 `DatetimeIndex` 本身不是Python原生的 `datetime` 对象，但可以轻松转换。例如，可以将其转换为字符串列表以便于展示或存储。

![](img/43535718fa43247904bfd3cea8367806_39.png)

```python
# 将DatetimeIndex转换为字符串列表
str_dates = time_index.strftime('%Y-%m-%d').tolist()
print(str_dates)
# 输出：['2023-01-01', '2023-01-02', '2023-01-03', '2023-01-04', '2023-01-05']
```

本节课中我们一起学习了如何使用 `pd.date_range` 函数生成时间范围序列。我们掌握了通过指定起止日期或序列长度来创建序列，并了解了如何通过 `freq` 参数灵活控制时间频率（如天、小时、周、工作日等）。这个功能极大地简化了时间序列数据的准备工作，是金融量化分析中处理时间数据的基础工具。

![](img/43535718fa43247904bfd3cea8367806_41.png)

接下来，我们将介绍在拥有了这些时间对象之后，如何进行更高级的时间序列操作和分析。