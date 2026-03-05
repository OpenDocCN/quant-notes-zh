# Python金融量化投资分析与股票交易：P28：时间对象生成 📅

在本节课中，我们将学习如何使用Pandas库中的`date_range`函数来生成一个时间范围序列。这是处理时间序列数据时，创建规则时间索引的常用方法。

![](img/1e35219a286c1425f89adebe41985ef7_1.png)

![](img/1e35219a286c1425f89adebe41985ef7_3.png)

上一节我们介绍了如何将批量字符串转换为`datetime`对象。本节中我们来看看如何直接生成一个时间范围。

![](img/1e35219a286c1425f89adebe41985ef7_5.png)

## 生成时间范围

![](img/1e35219a286c1425f89adebe41985ef7_7.png)

`date_range`函数用于生成一个等间隔的时间点序列。它有几个核心参数来控制序列的生成。

![](img/1e35219a286c1425f89adebe41985ef7_9.png)

以下是`date_range`函数的主要参数：

![](img/1e35219a286c1425f89adebe41985ef7_11.png)

*   **start**：指定时间序列的起始时间。
*   **end**：指定时间序列的结束时间。
*   **periods**：指定要生成的时间点的数量（长度）。
*   **freq**：指定时间序列的频率，例如“D”代表天，“H”代表小时。

![](img/1e35219a286c1425f89adebe41985ef7_13.png)

你可以通过指定`start`和`end`来生成一个时间范围，也可以指定`start`和`periods`（长度）来生成。

![](img/1e35219a286c1425f89adebe41985ef7_15.png)

```python
import pandas as pd

![](img/1e35219a286c1425f89adebe41985ef7_17.png)

# 指定起始和结束时间，默认频率为天（‘D‘）
date_range1 = pd.date_range(start='2010-01-01', end='2010-01-05')
print(date_range1)
# 输出：DatetimeIndex(['2010-01-01', '2010-01-02', '2010-01-03', '2010-01-04', '2010-01-05'], dtype='datetime64[ns]', freq='D')

![](img/1e35219a286c1425f89adebe41985ef7_19.png)

![](img/1e35219a286c1425f89adebe41985ef7_21.png)

# 指定起始时间和长度（periods）
date_range2 = pd.date_range(start='2010-01-01', periods=5)
print(date_range2)
# 输出：DatetimeIndex(['2010-01-01', '2010-01-02', '2010-01-03', '2010-01-04', '2010-01-05'], dtype='datetime64[ns]', freq='D')
```

## 设置时间频率

`freq`参数非常强大，它允许你以不同的时间间隔生成序列。

![](img/1e35219a286c1425f89adebe41985ef7_23.png)

以下是`freq`参数的一些常用取值：

*   **`‘D‘`**：日历日（默认值）。
*   **`‘H‘`**：小时。
*   **`‘W‘`**：周（默认从周日开始，`‘W-MON‘`表示从周一开始）。
*   **`‘B‘`**：工作日（排除周末）。
*   **`‘M‘`**：月末。
*   **`‘MS‘`**：月初。
*   **`‘T‘`** 或 **`‘min‘`**：分钟。
*   **`‘S‘`**：秒。
*   **`‘A‘`** 或 **`‘Y‘`**：年末。
*   **`‘AS‘`** 或 **`‘YS‘`**：年初。

![](img/1e35219a286c1425f89adebe41985ef7_25.png)

![](img/1e35219a286c1425f89adebe41985ef7_27.png)

你还可以组合使用，例如`‘2H30min‘`表示每2小时30分钟。

![](img/1e35219a286c1425f89adebe41985ef7_29.png)

```python
# 生成每小时的时间点
hourly_range = pd.date_range(start='2010-01-01', periods=3, freq='H')
print(hourly_range)
# 输出：DatetimeIndex(['2010-01-01 00:00:00', '2010-01-01 01:00:00', '2010-01-01 02:00:00'], dtype='datetime64[ns]', freq='H')

![](img/1e35219a286c1425f89adebe41985ef7_31.png)

![](img/1e35219a286c1425f89adebe41985ef7_33.png)

# 生成每周一的时间点
weekly_range = pd.date_range(start='2010-01-01', periods=3, freq='W-MON')
print(weekly_range)
# 输出：DatetimeIndex(['2010-01-04', '2010-01-11', '2010-01-18'], dtype='datetime64[ns]', freq='W-MON')

![](img/1e35219a286c1425f89adebe41985ef7_35.png)

# 生成每2小时30分钟的时间点
custom_range = pd.date_range(start='2010-01-01', periods=3, freq='2H30min')
print(custom_range)
# 输出：DatetimeIndex(['2010-01-01 00:00:00', '2010-01-01 02:30:00', '2010-01-01 05:00:00'], dtype='datetime64[ns]', freq='150T')
```

![](img/1e35219a286c1425f89adebe41985ef7_37.png)

## 时间索引的转换

![](img/1e35219a286c1425f89adebe41985ef7_39.png)

`date_range`生成的对象是一个`DatetimeIndex`。你可以方便地将其转换为Python原生的`datetime`对象列表或字符串列表。

![](img/1e35219a286c1425f89adebe41985ef7_41.png)

```python
# 转换为Python datetime对象列表
dt_list = date_range1.to_pydatetime()
print(dt_list)
# 输出：[datetime.datetime(2010, 1, 1, 0, 0), ...]

# 转换为字符串列表（指定格式）
str_list = date_range1.strftime('%Y-%m-%d').tolist()
print(str_list)
# 输出：['2010-01-01', '2010-01-02', ...]
```

![](img/1e35219a286c1425f89adebe41985ef7_43.png)

本节课中我们一起学习了如何使用Pandas的`date_range`函数生成时间范围序列。我们掌握了通过`start`、`end`、`periods`参数定义序列，以及使用强大的`freq`参数设置生成频率。这为我们后续构建和分析时间序列数据打下了坚实的基础。