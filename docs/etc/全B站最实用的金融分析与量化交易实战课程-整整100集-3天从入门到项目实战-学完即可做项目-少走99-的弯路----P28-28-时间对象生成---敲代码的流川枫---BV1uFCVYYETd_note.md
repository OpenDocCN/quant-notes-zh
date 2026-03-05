# 量化交易实战课程：P28：时间对象生成 📅

## 概述
在本节课中，我们将学习如何使用Pandas库中的`pd.date_range`函数来生成一个时间范围序列。这是处理时间序列数据时，创建规则时间索引的常用方法。

![](img/d9ebe0598da345a2676f1f2bce2d82c1_1.png)

![](img/d9ebe0598da345a2676f1f2bce2d82c1_3.png)

上一节我们介绍了如何使用`pd.to_datetime`将字符串批量转换为时间对象。本节中我们来看看如何直接生成一个时间范围。

![](img/d9ebe0598da345a2676f1f2bce2d82c1_5.png)

![](img/d9ebe0598da345a2676f1f2bce2d82c1_7.png)

## 使用 pd.date_range 生成时间范围
`pd.date_range`函数的核心功能是生成一个等间隔的`DatetimeIndex`对象。它非常灵活，可以通过多种方式指定时间范围。

![](img/d9ebe0598da345a2676f1f2bce2d82c1_9.png)

以下是该函数的主要参数：
*   **`start`**: 指定时间序列的起始点。
*   **`end`**: 指定时间序列的结束点。
*   **`periods`**: 指定要生成的时间点的数量（长度）。
*   **`freq`**: 指定时间序列的频率（例如，天‘D’、小时‘H’、月‘M’）。

![](img/d9ebe0598da345a2676f1f2bce2d82c1_11.png)

### 指定起始和结束时间
你可以通过`start`和`end`参数来定义一个明确的时间范围。默认情况下，频率`freq`为‘D’（天）。

![](img/d9ebe0598da345a2676f1f2bce2d82c1_13.png)

![](img/d9ebe0598da345a2676f1f2bce2d82c1_15.png)

```python
import pandas as pd
# 生成从2010-01-01到2010-01-05的每日时间序列
date_range1 = pd.date_range(start='2010-01-01', end='2010-01-05')
print(date_range1)
# 输出：DatetimeIndex(['2010-01-01', '2010-01-02', '2010-01-03', '2010-01-04', '2010-01-05'], dtype='datetime64[ns]', freq='D')
```

![](img/d9ebe0598da345a2676f1f2bce2d82c1_17.png)

### 指定起始时间和长度
如果你知道起始点和需要的时间点数量，可以使用`start`和`periods`参数。

![](img/d9ebe0598da345a2676f1f2bce2d82c1_19.png)

```python
# 从2010-01-01开始，生成60个连续的日期
date_range2 = pd.date_range(start='2010-01-01', periods=60)
print(date_range2[:5]) # 打印前5个
# 输出：DatetimeIndex(['2010-01-01', '2010-01-02', '2010-01-03', '2010-01-04', '2010-01-05'], dtype='datetime64[ns]', freq='D')
```

## 灵活的频率参数 (freq)
`freq`参数是`pd.date_range`的强大之处，它允许你生成不同间隔的时间序列。

![](img/d9ebe0598da345a2676f1f2bce2d82c1_21.png)

以下是常用的频率代码：
*   **`‘D’`**: 日历日（默认）
*   **`‘H’`**: 小时
*   **`‘W’`**: 周（默认从周日开始，`‘W-MON’`表示从周一开始）
*   **`‘B’`**: 工作日（排除周末）
*   **`‘MS’`**: 每月第一天
*   **`‘M’`**: 每月最后一天
*   **`‘T’`或`‘min’`**: 分钟
*   **`‘S’`**: 秒
*   **`‘A’`或`‘Y’`**: 年
*   **`‘Q’`**: 季度

![](img/d9ebe0598da345a2676f1f2bce2d82c1_23.png)

![](img/d9ebe0598da345a2676f1f2bce2d82c1_25.png)

你甚至可以组合使用，例如`‘2H30min’`表示每2小时30分钟。

![](img/d9ebe0598da345a2676f1f2bce2d82c1_27.png)

![](img/d9ebe0598da345a2676f1f2bce2d82c1_29.png)

```python
# 生成工作日的日期序列
business_days = pd.date_range(start='2023-10-30', periods=5, freq='B')
print(business_days)
# 输出可能为：DatetimeIndex(['2023-10-30', '2023-10-31', '2023-11-01', '2023-11-02', '2023-11-03'], dtype='datetime64[ns]', freq='B')

![](img/d9ebe0598da345a2676f1f2bce2d82c1_31.png)

# 生成每2小时的时间序列
hourly_range = pd.date_range(start='2023-10-30 08:00', periods=3, freq='2H')
print(hourly_range)
# 输出：DatetimeIndex(['2023-10-30 08:00:00', '2023-10-30 10:00:00', '2023-10-30 12:00:00'], dtype='datetime64[ns]', freq='2H')
```

![](img/d9ebe0598da345a2676f1f2bce2d82c1_33.png)

![](img/d9ebe0598da345a2676f1f2bce2d82c1_35.png)

## 时间索引的转换与应用
`pd.date_range`生成的是一个`DatetimeIndex`对象。它可以方便地用作Pandas `DataFrame`或`Series`的索引，从而进行高效的时间序列操作。

![](img/d9ebe0598da345a2676f1f2bce2d82c1_37.png)

```python
# 创建一个以时间序列为索引的简单数据
time_index = pd.date_range(start='2023-01-01', periods=5, freq='D')
data = pd.Series([10, 20, 15, 30, 25], index=time_index)
print(data)
# 输出：
# 2023-01-01    10
# 2023-01-02    20
# 2023-01-03    15
# 2023-01-04    30
# 2023-01-05    25
# Freq: D, dtype: int64
```

![](img/d9ebe0598da345a2676f1f2bce2d82c1_39.png)

这个`DatetimeIndex`可以转换为Python标准的`datetime`对象列表（虽然某些方法可能已弃用，但通常有替代方案），也可以方便地进行格式化输出。

![](img/d9ebe0598da345a2676f1f2bce2d82c1_41.png)

## 总结
本节课中我们一起学习了Pandas库中生成时间范围的核心工具`pd.date_range`。我们掌握了如何通过指定起始点、结束点或长度来创建时间序列，并深入了解了强大的`freq`参数，它让我们能够以天、小时、工作日、月等多种频率生成数据。生成的`DatetimeIndex`是构建和分析时间序列数据（如股票价格、经济指标等）的基石，为后续的数据处理和分析提供了规整的时间维度。