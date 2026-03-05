# Python金融量化投资分析与股票交易：P28：27 时间序列 📅

在本节课中，我们将要学习如何利用Pandas创建和操作时间序列。时间序列是以时间对象作为索引的Series或DataFrame，它在金融数据分析中至关重要，能够帮助我们轻松地进行时间维度的数据切片、重采样等操作。

![](img/a12f9c2184e3fe6e5ba35db2215ba547_1.png)

## 创建时间序列

上一节我们介绍了Pandas中生成时间对象的函数。本节中我们来看看如何用它们来构成一个时间序列。

我们可以使用`pd.date_range`函数生成一个`DatetimeIndex`，并将其作为Series或DataFrame的索引。以下是一个创建时间序列Series的示例：

```python
import pandas as pd
import numpy as np

# 创建一个长度为1000的数值序列
values = np.arange(1000)

![](img/a12f9c2184e3fe6e5ba35db2215ba547_3.png)

![](img/a12f9c2184e3fe6e5ba35db2215ba547_5.png)

# 创建一个从2017-01-01开始，长度为1000天的DatetimeIndex作为索引
date_index = pd.date_range(start='2017-01-01', periods=1000)

![](img/a12f9c2184e3fe6e5ba35db2215ba547_7.png)

# 创建以时间为索引的Series
sr = pd.Series(values, index=date_index)
print(sr.index)  # 输出：DatetimeIndex，而非字符串
```

![](img/a12f9c2184e3fe6e5ba35db2215ba547_9.png)

运行上述代码后，`sr`就成为了一个时间序列。它的索引类型是`DatetimeIndex`，这赋予了它许多强大的功能。

![](img/a12f9c2184e3fe6e5ba35db2215ba547_11.png)

## 时间序列的切片操作

成为时间序列后，一个直观的好处是我们可以使用不完整的日期字符串进行灵活的数据切片。

以下是几种常见的切片方式：

*   **按年切片**：`sr[‘2017’]` 会选取2017年全年的数据。
*   **按年月切片**：`sr[‘2017-03’]` 会选取2017年3月份的所有数据。
*   **按日期范围切片**：`sr[‘2017-12-25’:‘2018-02-01’]` 会选取从2017年12月25日到2018年2月1日（包含首尾）的数据。

这些操作使得按时间维度筛选数据变得异常简单。

## 时间序列的重采样（Resample）

`resample`函数是时间序列分析中的一个核心功能，它用于对数据按不同的时间频率进行重新采样和聚合。

例如，如果我们有一个按日记录的数据序列，想要查看每周的销售总额，就可以使用重采样。

以下是`resample`的用法示例：

```python
# 按周（‘W’）进行重采样，并计算每周的和
weekly_sum = sr.resample('W').sum()
print(weekly_sum.head())

# 按月（‘M’）进行重采样，并计算每月的平均值
monthly_mean = sr.resample('M').mean()
print(monthly_mean.head())
```

在代码中，`’W’`代表周频率，`’M’`代表月频率。`resample()`之后需要接一个聚合函数，如`.sum()`（求和）、`.mean()`（求平均）等。

![](img/a12f9c2184e3fe6e5ba35db2215ba547_13.png)

## 其他相关函数：truncate

`truncate`函数可以根据日期对时间序列进行截断。它接受`before`和`after`两个参数。

![](img/a12f9c2184e3fe6e5ba35db2215ba547_15.png)

以下是`truncate`函数的示例：

```python
# 截取2018年2月3日之后的数据
sr_truncated_after = sr.truncate(after='2018-02-03')

# 截取2018年2月3日之前的数据
sr_truncated_before = sr.truncate(before='2018-02-03')
```

不过，由于切片操作`sr[‘2018-02-03’:]`和`sr[:’2018-02-03’]`可以实现相同的效果，因此`truncate`函数在实际中使用得相对较少。

![](img/a12f9c2184e3fe6e5ba35db2215ba547_17.png)

---

![](img/a12f9c2184e3fe6e5ba35db2215ba547_19.png)

本节课中我们一起学习了Pandas时间序列的核心操作。我们掌握了如何创建以时间为索引的Series，并利用其特性进行灵活的日期切片。重点介绍了强大的`resample`函数，它能够方便地对数据按不同时间频率进行聚合分析。这些功能为后续的金融时间序列分析打下了坚实的基础。