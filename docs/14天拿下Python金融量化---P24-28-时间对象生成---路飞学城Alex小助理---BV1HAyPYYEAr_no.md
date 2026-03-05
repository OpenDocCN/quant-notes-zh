Python金融量化：P24：28 时间对象生成 📅

## 概述
在本节课中，我们将要学习如何使用Pandas库中的`pd.date_range`函数来生成一个时间范围序列。这是处理时间序列数据时，创建规则时间索引的常用方法。

![](img/c915a4aeb314da24626eecefeb6afdd3_1.png)

![](img/c915a4aeb314da24626eecefeb6afdd3_3.png)

上一节我们介绍了如何使用`pd.to_datetime`将字符串批量转换为时间对象。本节中我们来看看如何直接生成一个时间范围。

![](img/c915a4aeb314da24626eecefeb6afdd3_5.png)

## 生成时间范围：`pd.date_range`
`pd.date_range`函数用于生成一个等间隔的`DatetimeIndex`对象。它非常灵活，可以通过指定起始时间、结束时间、时间长度或频率来创建序列。

![](img/c915a4aeb314da24626eecefeb6afdd3_7.png)

### 核心参数
以下是`pd.date_range`函数的主要参数：

![](img/c915a4aeb314da24626eecefeb6afdd3_9.png)

*   **`start`**: 指定时间序列的起始点。
*   **`end`**: 指定时间序列的结束点。
*   **`periods`**: 指定要生成的时间点的数量（长度）。
*   **`freq`**: 指定时间序列的频率，例如“D”代表天，“H”代表小时。

![](img/c915a4aeb314da24626eecefeb6afdd3_11.png)

**注意**：你可以组合使用`start`/`end`与`periods`，但通常不需要同时指定`end`和`periods`。

![](img/c915a4aeb314da24626eecefeb6afdd3_13.png)

### 使用示例
以下是几种常见的使用方式：

![](img/c915a4aeb314da24626eecefeb6afdd3_15.png)

![](img/c915a4aeb314da24626eecefeb6afdd3_17.png)

**1. 指定起始和结束时间**
默认频率为“D”（每天）。
```python
import pandas as pd
date_index = pd.date_range(start='2010-01-01', end='2010-01-05')
print(date_index)
```
输出：
```
DatetimeIndex(['2010-01-01', '2010-01-02', '2010-01-03', '2010-01-04', '2010-01-05'], dtype='datetime64[ns]', freq='D')
```

![](img/c915a4aeb314da24626eecefeb6afdd3_19.png)

**2. 指定起始时间和长度**
使用`periods`参数。
```python
date_index = pd.date_range(start='2010-01-01', periods=5)
print(date_index)
```
输出结果与上例相同。

**3. 指定结束时间和长度**
```python
date_index = pd.date_range(end='2010-01-05', periods=5)
print(date_index)
```
输出：
```
DatetimeIndex(['2010-01-01', '2010-01-02', '2010-01-03', '2010-01-04', '2010-01-05'], dtype='datetime64[ns]', freq='D')
```

## 灵活的频率参数 `freq`
`freq`参数是`pd.date_range`的强大之处，它允许你生成不同间隔的时间序列。

![](img/c915a4aeb314da24626eecefeb6afdd3_21.png)

以下是常用的频率代码：

![](img/c915a4aeb314da24626eecefeb6afdd3_23.png)

*   `D`： 日历日
*   `B`： 工作日（排除周末）
*   `W`： 周（默认从周日开始，`W-MON`表示从周一开始）
*   `M`： 月末
*   `H`： 小时
*   `T`或`min`： 分钟
*   `S`： 秒
*   `A`或`Y`： 年末

![](img/c915a4aeb314da24626eecefeb6afdd3_25.png)

你还可以组合使用，例如`2H30min`表示每2小时30分钟。

![](img/c915a4aeb314da24626eecefeb6afdd3_27.png)

![](img/c915a4aeb314da24626eecefeb6afdd3_29.png)

**使用示例**
```python
# 生成工作日序列
business_days = pd.date_range(start='2023-10-30', periods=5, freq='B')
print(business_days)

![](img/c915a4aeb314da24626eecefeb6afdd3_31.png)

# 生成每小时序列
hourly = pd.date_range(start='2023-10-30 08:00', periods=3, freq='H')
print(hourly)

![](img/c915a4aeb314da24626eecefeb6afdd3_33.png)

![](img/c915a4aeb314da24626eecefeb6afdd3_35.png)

# 生成每2小时30分钟的序列
custom_freq = pd.date_range(start='2023-10-30', periods=3, freq='2H30min')
print(custom_freq)
```

## 时间索引的转换
生成的`DatetimeIndex`可以方便地进行转换。

![](img/c915a4aeb314da24626eecefeb6afdd3_37.png)

![](img/c915a4aeb314da24626eecefeb6afdd3_39.png)

**转换为Python标准`datetime`对象列表**
```python
date_list = date_index.to_pydatetime().tolist()
print(date_list)
```

**转换为字符串列表**
```python
str_list = date_index.strftime('%Y-%m-%d').tolist()
print(str_list)
```

![](img/c915a4aeb314da24626eecefeb6afdd3_41.png)

## 总结
本节课中我们一起学习了`pd.date_range`函数。我们了解到，它可以通过指定`start`、`end`、`periods`和`freq`等参数，灵活地生成各种规则的时间序列索引。这个功能极大地简化了时间序列数据框架的构建过程，是金融量化分析中准备时间维度数据的基础工具。