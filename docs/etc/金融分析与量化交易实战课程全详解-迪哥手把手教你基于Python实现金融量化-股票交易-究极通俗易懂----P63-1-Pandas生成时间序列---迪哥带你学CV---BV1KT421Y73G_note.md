# Python金融分析与量化交易实战课程：P63：Pandas生成时间序列 📅

![](img/3c617104f164302918027fbffcce3694_0.png)

![](img/3c617104f164302918027fbffcce3694_2.png)

![](img/3c617104f164302918027fbffcce3694_4.png)

![](img/3c617104f164302918027fbffcce3694_6.png)

![](img/3c617104f164302918027fbffcce3694_8.png)

在本节课中，我们将学习如何使用Pandas库来创建基本的时间序列数据。时间序列是金融数据分析的基础，掌握其生成方法是后续分析的第一步。

![](img/3c617104f164302918027fbffcce3694_10.png)

![](img/3c617104f164302918027fbffcce3694_12.png)

## 概述与准备工作

![](img/3c617104f164302918027fbffcce3694_14.png)

![](img/3c617104f164302918027fbffcce3694_16.png)

![](img/3c617104f164302918027fbffcce3694_18.png)

首先，我们需要导入必要的库。

![](img/3c617104f164302918027fbffcce3694_20.png)

![](img/3c617104f164302918027fbffcce3694_22.png)

![](img/3c617104f164302918027fbffcce3694_24.png)

```python
import pandas as pd
import numpy as np
```

![](img/3c617104f164302918027fbffcce3694_26.png)

![](img/3c617104f164302918027fbffcce3694_28.png)

时间序列数据主要分为三种类型：**时间戳**、**时间周期**和**时间间隔**。时间戳代表一个具体的时间点，例如“2017年7月19日10点11分35秒”。时间周期和时间间隔则分别表示一段持续的时间或两个时间点之间的差值。本节课我们将重点学习如何生成这些时间序列。

![](img/3c617104f164302918027fbffcce3694_30.png)

![](img/3c617104f164302918027fbffcce3694_32.png)

## 使用 `pd.date_range` 生成时间序列

![](img/3c617104f164302918027fbffcce3694_34.png)

![](img/3c617104f164302918027fbffcce3694_36.png)

Pandas提供了一个非常方便的函数 `pd.date_range` 来生成时间序列。它允许我们通过指定起始时间、周期数量和频率来创建一系列日期。

![](img/3c617104f164302918027fbffcce3694_38.png)

### 指定起始时间与周期数

![](img/3c617104f164302918027fbffcce3694_40.png)

![](img/3c617104f164302918027fbffcce3694_42.png)

以下代码展示了如何从2016年7月1日开始，生成10个按天递增的日期。

![](img/3c617104f164302918027fbffcce3694_44.png)

![](img/3c617104f164302918027fbffcce3694_46.png)

```python
dates = pd.date_range('2016-07-01', periods=10, freq='D')
print(dates)
```
**输出示例**：
```
DatetimeIndex(['2016-07-01', '2016-07-02', '2016-07-03', '2016-07-04',
               '2016-07-05', '2016-07-06', '2016-07-07', '2016-07-08',
               '2016-07-09', '2016-07-10'],
              dtype='datetime64[ns]', freq='D')
```
参数 `freq=‘D’` 表示频率为“天”。如果不指定 `freq` 参数，默认值也是 `‘D’`。

![](img/3c617104f164302918027fbffcce3694_48.png)

![](img/3c617104f164302918027fbffcce3694_50.png)

![](img/3c617104f164302918027fbffcce3694_52.png)

### 探索不同的频率

![](img/3c617104f164302918027fbffcce3694_54.png)

![](img/3c617104f164302918027fbffcce3694_56.png)

频率参数 `freq` 非常灵活，我们可以改变它以生成不同间隔的时间序列。

![](img/3c617104f164302918027fbffcce3694_58.png)

*   **按月生成**：将 `freq` 改为 `‘M’`，会生成每个月的最后一天。
    ```python
    dates_month = pd.date_range('2016-07-01', periods=5, freq='M')
    print(dates_month)
    ```
*   **按小时生成**：将 `freq` 改为 `‘H’`，会生成按小时递增的时间点。
    ```python
    dates_hour = pd.date_range('2016-07-01', periods=10, freq='H')
    print(dates_hour)
    ```
*   **指定倍数**：我们还可以在频率前加上数字，表示间隔的倍数。例如，`‘3D’` 表示每3天。
    ```python
    dates_3d = pd.date_range('2016-07-01', periods=4, freq='3D')
    print(dates_3d)
    ```

![](img/3c617104f164302918027fbffcce3694_60.png)

### 指定起始与结束时间

![](img/3c617104f164302918027fbffcce3694_62.png)

![](img/3c617104f164302918027fbffcce3694_64.png)

除了指定起始点和周期数，我们也可以直接指定起始和结束时间，让Pandas自动按频率填充中间的日期。

![](img/3c617104f164302918027fbffcce3694_66.png)

![](img/3c617104f164302918027fbffcce3694_68.png)

![](img/3c617104f164302918027fbffcce3694_70.png)

```python
dates_range = pd.date_range(start='2016-07-01', end='2016-07-10', freq='D')
print(dates_range)
```

![](img/3c617104f164302918027fbffcce3694_72.png)

![](img/3c617104f164302918027fbffcce3694_74.png)

![](img/3c617104f164302918027fbffcce3694_76.png)

### 日期格式的多样性

![](img/3c617104f164302918027fbffcce3694_78.png)

在指定日期时，Pandas支持多种字符串格式，非常人性化。

![](img/3c617104f164302918027fbffcce3694_80.png)

![](img/3c617104f164302918027fbffcce3694_82.png)

![](img/3c617104f164302918027fbffcce3694_84.png)

```python
# 多种等效的日期书写方式
print(pd.date_range('2016-07-01', periods=3))
print(pd.date_range('Jul 1, 2016', periods=3))
print(pd.date_range('7/1/2016', periods=3))  # 月/日/年
print(pd.date_range('1/7/2016', periods=3))  # 日/月/年
```

![](img/3c617104f164302918027fbffcce3694_86.png)

![](img/3c617104f164302918027fbffcce3694_88.png)

![](img/3c617104f164302918027fbffcce3694_90.png)

## 创建带时间索引的Series数据

![](img/3c617104f164302918027fbffcce3694_92.png)

![](img/3c617104f164302918027fbffcce3694_94.png)

上一节我们介绍了如何生成纯日期索引，本节我们来看看如何将其与数据结合。我们可以将生成的时间序列作为Pandas Series的索引，从而创建出带有时间标签的数据。

![](img/3c617104f164302918027fbffcce3694_96.png)

![](img/3c617104f164302918027fbffcce3694_98.png)

![](img/3c617104f164302918027fbffcce3694_100.png)

```python
# 生成一个时间索引
time_index = pd.date_range('2016-01-01', periods=20, freq='D')
# 创建随机数据，并指定时间索引
time_series = pd.Series(np.random.randn(20), index=time_index)
print(time_series.head())
```

![](img/3c617104f164302918027fbffcce3694_102.png)

![](img/3c617104f164302918027fbffcce3694_104.png)

![](img/3c617104f164302918027fbffcce3694_106.png)

将时间作为索引的最大好处是，我们可以方便地通过日期来选取数据。

![](img/3c617104f164302918027fbffcce3694_108.png)

![](img/3c617104f164302918027fbffcce3694_110.png)

```python
# 通过具体日期获取数据
print(time_series['2016-01-05'])
# 通过日期切片获取一个区间的数据
print(time_series['2016-01-05':'2016-01-10'])
```

![](img/3c617104f164302918027fbffcce3694_112.png)

![](img/3c617104f164302918027fbffcce3694_114.png)

![](img/3c617104f164302918027fbffcce3694_116.png)

## 数据的过滤与选择

![](img/3c617104f164302918027fbffcce3694_118.png)

![](img/3c617104f164302918027fbffcce3694_120.png)

![](img/3c617104f164302918027fbffcce3694_122.png)

基于时间索引，我们可以轻松地对数据进行过滤。

![](img/3c617104f164302918027fbffcce3694_124.png)

![](img/3c617104f164302918027fbffcce3694_126.png)

![](img/3c617104f164302918027fbffcce3694_128.png)

```python
# 过滤出2016年1月10日之后的数据
filtered_after = time_series[time_series.index > pd.Timestamp('2016-01-10')]
print(filtered_after.head())

![](img/3c617104f164302918027fbffcce3694_130.png)

![](img/3c617104f164302918027fbffcce3694_132.png)

# 过滤出2016年1月10日之前的数据
filtered_before = time_series[time_series.index < pd.Timestamp('2016-01-10')]
print(filtered_before.tail())
```

![](img/3c617104f164302918027fbffcce3694_134.png)

![](img/3c617104f164302918027fbffcce3694_136.png)

![](img/3c617104f164302918027fbffcce3694_138.png)

## 时间戳与周期

![](img/3c617104f164302918027fbffcce3694_140.png)

![](img/3c617104f164302918027fbffcce3694_142.png)

Pandas中，`pd.Timestamp` 用于表示具体的时间点（时间戳），而 `pd.Period` 用于表示一个时间段（周期）。

![](img/3c617104f164302918027fbffcce3694_144.png)

![](img/3c617104f164302918027fbffcce3694_146.png)

![](img/3c617104f164302918027fbffcce3694_148.png)

```python
# 创建一个时间戳
ts = pd.Timestamp('2016-01-01 08:00:00')
print(ts)

![](img/3c617104f164302918027fbffcce3694_150.png)

![](img/3c617104f164302918027fbffcce3694_152.png)

# 创建一个时间周期（2016年1月）
pr = pd.Period('2016-01', freq='M')
print(pr)
```

![](img/3c617104f164302918027fbffcce3694_154.png)

![](img/3c617104f164302918027fbffcce3694_156.png)

两者在数据切片时行为略有不同：周期索引包含起始边界，而时间戳索引是精确匹配。

![](img/3c617104f164302918027fbffcce3694_158.png)

![](img/3c617104f164302918027fbffcce3694_160.png)

![](img/3c617104f164302918027fbffcce3694_162.png)

## 时间的偏移与计算

![](img/3c617104f164302918027fbffcce3694_164.png)

![](img/3c617104f164302918027fbffcce3694_166.png)

我们可以对时间进行加减运算，这在实际分析中非常有用，比如计算“三天后”或“一月前”的日期。

![](img/3c617104f164302918027fbffcce3694_168.png)

![](img/3c617104f164302918027fbffcce3694_170.png)

![](img/3c617104f164302918027fbffcce3694_172.png)

```python
# 时间偏移计算
from pandas.tseries.offsets import Day, Hour

![](img/3c617104f164302918027fbffcce3694_174.png)

![](img/3c617104f164302918027fbffcce3694_176.png)

![](img/3c617104f164302918027fbffcce3694_178.png)

ts = pd.Timestamp('2016-01-01')
print(ts + Day(1))  # 加一天
print(ts + 3 * Day(1))  # 加三天
print(ts + Hour(12))  # 加12小时
```

![](img/3c617104f164302918027fbffcce3694_180.png)

![](img/3c617104f164302918027fbffcce3694_182.png)

![](img/3c617104f164302918027fbffcce3694_184.png)

## 频率参数速查表

![](img/3c617104f164302918027fbffcce3694_186.png)

![](img/3c617104f164302918027fbffcce3694_188.png)

以下是 `freq` 参数的一些常用选项：

![](img/3c617104f164302918027fbffcce3694_190.png)

![](img/3c617104f164302918027fbffcce3694_192.png)

![](img/3c617104f164302918027fbffcce3694_194.png)

| 别名 | 描述 |
| :--- | :--- |
| `D` | 日历日 |
| `B` | 工作日 |
| `H` | 小时 |
| `T` 或 `min` | 分钟 |
| `S` | 秒 |
| `M` | 月末 |
| `MS` | 月初 |
| `Q` | 季末 |
| `QS` | 季初 |
| `A` 或 `Y` | 年末 |
| `AS` 或 `YS` | 年初 |
| `W` | 每周 |
| `W-MON` | 指定星期几（如周一） |

![](img/3c617104f164302918027fbffcce3694_196.png)

![](img/3c617104f164302918027fbffcce3694_198.png)

你可以组合数字使用，例如 `‘2H30min’` 表示每2小时30分钟。

![](img/3c617104f164302918027fbffcce3694_200.png)

![](img/3c617104f164302918027fbffcce3694_202.png)

![](img/3c617104f164302918027fbffcce3694_204.png)

## 总结

![](img/3c617104f164302918027fbffcce3694_206.png)

![](img/3c617104f164302918027fbffcce3694_208.png)

本节课我们一起学习了使用Pandas生成时间序列的核心方法。我们掌握了：
1.  使用 **`pd.date_range()`** 函数，通过指定起始点、周期和频率来生成日期范围。
2.  如何创建**带有时间索引的Series**，并利用时间索引进行高效的数据选取和切片。
3.  对时间序列数据进行**过滤**。
4.  了解了**时间戳**与**时间周期**的区别。
5.  学习了如何进行**时间的偏移计算**。

![](img/3c617104f164302918027fbffcce3694_210.png)

![](img/3c617104f164302918027fbffcce3694_212.png)

![](img/3c617104f164302918027fbffcce3694_214.png)

这些是处理金融时间序列数据最基础也是最重要的技能。在后续课程中，我们将基于这些时间序列进行更深入的分析和可视化。