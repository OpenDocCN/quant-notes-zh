# Python金融量化与股票分析：P28：时间对象生成 📅

在本节课中，我们将学习如何使用Pandas库中的`date_range`函数来生成一个时间范围序列。这是处理时间序列数据，尤其是金融时间序列数据时非常基础且重要的技能。

![](img/219f7f0d0189d428f0e25bad4661c566_1.png)

上一节我们介绍了如何将批量字符串转换为时间对象。本节中我们来看看如何直接生成一个连续的时间序列。

![](img/219f7f0d0189d428f0e25bad4661c566_3.png)

![](img/219f7f0d0189d428f0e25bad4661c566_5.png)

## 使用`date_range`函数

![](img/219f7f0d0189d428f0e25bad4661c566_7.png)

`date_range`函数可以方便地生成一个等间隔的时间索引（DatetimeIndex）。它主要有以下几个参数：

*   **`start`**: 指定时间序列的起始点。
*   **`end`**: 指定时间序列的结束点。
*   **`periods`**: 指定要生成的时间点的数量（长度）。
*   **`freq`**: 指定时间序列的频率，例如“D”代表天，“H”代表小时。

![](img/219f7f0d0189d428f0e25bad4661c566_9.png)

以下是`date_range`函数的基本用法示例：

![](img/219f7f0d0189d428f0e25bad4661c566_11.png)

![](img/219f7f0d0189d428f0e25bad4661c566_13.png)

```python
import pandas as pd

![](img/219f7f0d0189d428f0e25bad4661c566_15.png)

# 方法一：指定起始和结束时间，默认频率为天（‘D‘）
date_range1 = pd.date_range(start='2010-01-01', end='2010-01-05')
print(date_range1)
# 输出：DatetimeIndex(['2010-01-01', '2010-01-02', '2010-01-03', '2010-01-04', '2010-01-05'], dtype='datetime64[ns]', freq='D')

![](img/219f7f0d0189d428f0e25bad4661c566_17.png)

# 方法二：指定起始时间和长度（periods）
date_range2 = pd.date_range(start='2010-01-01', periods=5)
print(date_range2)
# 输出：DatetimeIndex(['2010-01-01', '2010-01-02', '2010-01-03', '2010-01-04', '2010-01-05'], dtype='datetime64[ns]', freq='D')
```

![](img/219f7f0d0189d428f0e25bad4661c566_19.png)

## 理解频率参数 `freq`

`freq`参数非常强大，它允许你生成不同间隔的时间序列。以下是常用的频率代码：

*   `D`： 日历日
*   `B`： 工作日（排除周末）
*   `H`： 小时
*   `T` 或 `min`： 分钟
*   `S`： 秒
*   `W`： 周（默认从周日开始，`W-MON`表示从周一开始）
*   `M`： 月末
*   `MS`： 月初
*   `A` 或 `Y`： 年末
*   `AS` 或 `YS`： 年初

![](img/219f7f0d0189d428f0e25bad4661c566_21.png)

你还可以组合这些代码来定义更复杂的频率，例如“2H30min”表示每2小时30分钟。

以下是不同频率的生成示例：

![](img/219f7f0d0189d428f0e25bad4661c566_23.png)

![](img/219f7f0d0189d428f0e25bad4661c566_25.png)

```python
# 生成工作日序列
business_days = pd.date_range(start='2024-01-01', periods=5, freq='B')
print(business_days)

![](img/219f7f0d0189d428f0e25bad4661c566_27.png)

# 生成每小时序列
hourly = pd.date_range(start='2024-01-01', periods=5, freq='H')
print(hourly)

![](img/219f7f0d0189d428f0e25bad4661c566_29.png)

![](img/219f7f0d0189d428f0e25bad4661c566_31.png)

# 生成每周一序列
weekly_monday = pd.date_range(start='2024-01-01', periods=5, freq='W-MON')
print(weekly_monday)

![](img/219f7f0d0189d428f0e25bad4661c566_33.png)

# 生成复杂的频率序列：每2小时30分钟
complex_freq = pd.date_range(start='2024-01-01', periods=5, freq='2H30min')
print(complex_freq)
```

![](img/219f7f0d0189d428f0e25bad4661c566_35.png)

## 时间序列的转换

![](img/219f7f0d0189d428f0e25bad4661c566_37.png)

`date_range`生成的对象是Pandas的`DatetimeIndex`。你可以方便地将其转换为Python原生的`datetime`对象列表或字符串列表，以便在其他场景中使用。

![](img/219f7f0d0189d428f0e25bad4661c566_39.png)

```python
# 转换为Python datetime对象列表
py_datetime_list = date_range1.to_pydatetime().tolist()
print(py_datetime_list)

# 转换为字符串列表（指定格式）
str_list = date_range1.strftime('%Y-%m-%d').tolist()
print(str_list)
```

![](img/219f7f0d0189d428f0e25bad4661c566_41.png)

本节课中我们一起学习了如何使用Pandas的`date_range`函数生成时间范围序列。我们掌握了通过指定起止时间或起始时间加长度来生成序列，并了解了强大的`freq`参数如何控制生成序列的频率。这个功能极大地简化了时间序列数据的准备工作，是后续进行数据分析和处理的重要基础。