# 量化交易Python入门之数据分析：3-4：Pandas时间与日期处理 📅

在本节课中，我们将学习Pandas库中处理时间和日期的核心功能。内容包括创建特定时间周期、转换字符串为日期时间格式、进行时间运算以及重采样等操作。这些技能对于处理金融时间序列数据至关重要。

---

![](img/7e8207fbbada1281bc2125c31ade7638_1.png)

## 创建特定时间周期

上一节我们介绍了Pandas的基础数据结构，本节中我们来看看如何创建和表示特定的时间周期。

![](img/7e8207fbbada1281bc2125c31ade7638_3.png)

我们可以使用 `pd.Period` 来创建代表特定月份或日期的周期对象。

![](img/7e8207fbbada1281bc2125c31ade7638_5.png)

**创建月份周期：**
```python
import pandas as pd
month_period = pd.Period('2024-01', freq='M')
```
这段代码创建了一个代表2024年1月的周期对象。参数 `freq='M'` 表示频率为“月”。

![](img/7e8207fbbada1281bc2125c31ade7638_7.png)

**创建日期周期：**
```python
day_period = pd.Period('2024-03-05', freq='D')
```
这段代码创建了一个代表2024年3月5日的周期对象。参数 `freq='D'` 表示频率为“日”。

---

![](img/7e8207fbbada1281bc2125c31ade7638_9.png)

![](img/7e8207fbbada1281bc2125c31ade7638_11.png)

## 生成日期时间索引

接下来，我们学习如何生成一个以日期时间为索引的Pandas Series。

我们可以使用 `pd.date_range` 生成一个日期范围，并将其作为Series的索引。

![](img/7e8207fbbada1281bc2125c31ade7638_13.png)

```python
import pandas as pd
date_index = pd.date_range('2024-02-01', periods=3, freq='D')
x = pd.Series(['A', 'B', 'C'], index=date_index)
```
执行后，`x` 是一个索引为 `DatetimeIndex` 类型的Series，包含三个日期（2024-02-01, 2024-02-02, 2024-02-03）和对应的值（‘A’, ‘B’, ‘C’）。

![](img/7e8207fbbada1281bc2125c31ade7638_15.png)

检查索引类型：
```python
print(type(x.index))
```
输出结果为 `<class 'pandas.core.indexes.datetimes.DatetimeIndex'>`。

![](img/7e8207fbbada1281bc2125c31ade7638_17.png)

---

## 字符串转换为日期时间格式

![](img/7e8207fbbada1281bc2125c31ade7638_19.png)

在实际数据分析中，日期数据常以字符串形式存储。我们需要将其转换为Pandas可识别的日期时间格式以便进行排序和计算。

![](img/7e8207fbbada1281bc2125c31ade7638_20.png)

![](img/7e8207fbbada1281bc2125c31ade7638_21.png)

假设我们有一个包含日期字符串的DataFrame：
```python
df = pd.DataFrame({'date': ['2024-01-01', '2024-01-02', '2024-01-03']})
print(df['date'].iloc[0])
print(type(df['date'].iloc[0]))
```
此时，`date` 列的数据类型是字符串（`str`）。

![](img/7e8207fbbada1281bc2125c31ade7638_22.png)

使用 `pd.to_datetime` 方法进行转换：
```python
df['date_dt'] = pd.to_datetime(df['date'])
print(df['date_dt'].iloc[0])
print(type(df['date_dt'].iloc[0]))
```
转换后，`date_dt` 列的数据类型变为 `Timestamp`。虽然显示可能相似，但 `Timestamp` 类型支持时间序列的排序和运算，而字符串则不支持。

---

![](img/7e8207fbbada1281bc2125c31ade7638_24.png)

## 时间运算与时间差

![](img/7e8207fbbada1281bc2125c31ade7638_26.png)

![](img/7e8207fbbada1281bc2125c31ade7638_28.png)

Pandas的 `Timestamp` 对象支持各种时间运算，这是处理时间序列数据的基础。

![](img/7e8207fbbada1281bc2125c31ade7638_30.png)

**计算两个日期之间的差值：**
```python
ts1 = pd.Timestamp('2025-09-04')
ts2 = pd.Timestamp('2025-10-04')
time_diff = ts2 - ts1
print(time_diff)
```
结果是一个 `Timedelta` 对象，表示负30天。

**为日期时间添加具体时间：**
```python
ts_with_time = ts1 + pd.Timedelta(hours=8, minutes=10)
print(ts_with_time)
```
这将在日期上增加8小时10分钟。

![](img/7e8207fbbada1281bc2125c31ade7638_32.png)

**使用 `Timedelta` 进行时间偏移：**
```python
new_date = ts1 + pd.Timedelta(days=12, hours=3)
print(new_date)
```
这将在原日期（2025-09-04）基础上增加12天3小时，得到2025-09-16 03:00:00。

![](img/7e8207fbbada1281bc2125c31ade7638_34.png)

两个 `Timestamp` 相减得到的差值也是 `Timedelta` 类型。

---

## 生成规则日期序列

![](img/7e8207fbbada1281bc2125c31ade7638_36.png)

我们可以使用 `pd.date_range` 函数方便地生成一个规则的时间序列。

![](img/7e8207fbbada1281bc2125c31ade7638_38.png)

以下是生成一个双周频率日期序列的示例：
```python
date_series = pd.date_range(start='2025-10-01', periods=10, freq='2W-SUN')
print(date_series)
```
参数说明：
*   `start='2025-10-01'`： 序列的开始日期。
*   `periods=10`： 生成10个日期点。
*   `freq='2W-SUN'`： 频率为“每两周”（2W），且以星期日（SUN）作为每周的起点。

函数会自动从开始日期后找到第一个符合条件的星期日开始生成序列。

![](img/7e8207fbbada1281bc2125c31ade7638_40.png)

---

![](img/7e8207fbbada1281bc2125c31ade7638_42.png)

![](img/7e8207fbbada1281bc2125c31ade7638_44.png)

## 时间序列重采样

![](img/7e8207fbbada1281bc2125c31ade7638_46.png)

重采样是时间序列分析中一个强大且常用的功能，它可以将数据从一个频率转换到另一个频率（如从每小时到每天）。

首先，我们创建一个包含每小时数据的示例DataFrame：
```python
import numpy as np
import pandas as pd

![](img/7e8207fbbada1281bc2125c31ade7638_48.png)

# 生成2024年1月1日到1月7日之间每小时的日期时间索引
date_rng = pd.date_range(start='2024-01-01', end='2024-01-07', freq='h')
# 生成对应数量的随机数作为数据
data = np.random.randn(len(date_rng))
# 创建DataFrame
df_hourly = pd.DataFrame(data, index=date_rng, columns=['value'])
print(df_hourly.head())
```

![](img/7e8207fbbada1281bc2125c31ade7638_50.png)

现在，数据是每小时一条。如果我们想将其聚合为每日数据，可以使用 `resample` 方法。

![](img/7e8207fbbada1281bc2125c31ade7638_52.png)

**将每小时数据聚合为每日总和：**
```python
df_daily_sum = df_hourly.resample('D').sum()
print(df_daily_sum)
```
代码 `resample('D')` 指定按天（‘D’）进行重采样，然后 `.sum()` 将一天内的所有小时数据相加。

![](img/7e8207fbbada1281bc2125c31ade7638_54.png)

`resample` 支持多种聚合方式，以下是其他常用方法：
*   `.mean()`： 计算每日平均值。
*   `.max()`： 找出每日最大值。
*   `.min()`： 找出每日最小值。

![](img/7e8207fbbada1281bc2125c31ade7638_56.png)

在金融数据分析中，这个功能非常实用。例如，当我们拥有高频（如每分钟）的股票价格数据时，可以通过重采样轻松得到每日的开盘价（`.first`）、收盘价（`.last`）、最高价（`.max`）和最低价（`.min`）。

---

![](img/7e8207fbbada1281bc2125c31ade7638_58.png)

## 总结

![](img/7e8207fbbada1281bc2125c31ade7638_60.png)

本节课中我们一起学习了Pandas处理时间和日期的核心操作。我们掌握了如何创建时间周期对象、将字符串转换为可计算的日期时间格式、进行基本的时间运算以及生成规则的时间序列。最后，我们重点介绍了**重采样**这一强大功能，它能够将时间序列数据在不同时间频率之间进行转换和聚合，是金融时间序列分析中不可或缺的工具。掌握这些知识，将为后续进行更复杂的量化分析打下坚实基础。