# Python金融量化分析：P30：28 时间对象生成 📅

在本节课中，我们将要学习如何使用Pandas库中的`pd.date_range`函数来生成一个时间范围序列。这是处理时间序列数据时，创建规则时间索引的常用方法。

![](img/71cec8b067822755b43a5dfcf7d06e2b_1.png)

![](img/71cec8b067822755b43a5dfcf7d06e2b_3.png)

上一节我们介绍了如何使用`pd.to_datetime`将字符串批量转换为时间对象。本节中我们来看看如何直接生成一个连续的时间范围。

![](img/71cec8b067822755b43a5dfcf7d06e2b_5.png)

## 生成时间范围：`pd.date_range`函数

![](img/71cec8b067822755b43a5dfcf7d06e2b_7.png)

`pd.date_range`函数的核心功能是生成一个等间隔的`DatetimeIndex`对象。它非常灵活，可以通过指定起始时间、结束时间或序列长度来创建时间范围。

![](img/71cec8b067822755b43a5dfcf7d06e2b_9.png)

以下是`pd.date_range`函数的主要参数：

![](img/71cec8b067822755b43a5dfcf7d06e2b_11.png)

*   **`start`**: 指定时间序列的**开始**时间。
*   **`end`**: 指定时间序列的**结束**时间。
*   **`periods`**: 指定要生成的**时间点数量**（长度）。
*   **`freq`**: 指定时间序列的**频率**。

![](img/71cec8b067822755b43a5dfcf7d06e2b_13.png)

**核心公式**：
```python
import pandas as pd
# 基本调用方式
pd.date_range(start, end, periods, freq)
```

![](img/71cec8b067822755b43a5dfcf7d06e2b_15.png)

## 参数详解与示例

![](img/71cec8b067822755b43a5dfcf7d06e2b_17.png)

![](img/71cec8b067822755b43a5dfcf7d06e2b_19.png)

### 1. 指定起始和结束时间
当指定`start`和`end`参数时，函数会在这个区间内，按照默认的每日频率（`freq=‘D’`）生成时间点。

![](img/71cec8b067822755b43a5dfcf7d06e2b_21.png)

```python
# 生成从2010年1月1日到2010年5月1日的每日时间序列
date_index = pd.date_range(start=‘2010-01-01’, end=‘2010-05-01’)
print(date_index)
```

### 2. 指定起始时间和长度
你可以不指定`end`，而是通过`periods`参数指定要生成多少个时间点。

```python
# 从2010年1月1日开始，生成60个连续的日期
date_index = pd.date_range(start=‘2010-01-01’, periods=60)
print(date_index)
```

![](img/71cec8b067822755b43a5dfcf7d06e2b_23.png)

### 3. 灵活的频率参数 `freq`
`freq`参数决定了时间序列的间隔，这是`pd.date_range`非常强大的一个特性。

![](img/71cec8b067822755b43a5dfcf7d06e2b_25.png)

![](img/71cec8b067822755b43a5dfcf7d06e2b_27.png)

以下是常用的频率代码：

*   **`‘D’`**: 天（默认值）
*   **`‘H’`**: 小时
*   **`‘W’**: 周（默认从周日开始，`‘W-MON’`表示从周一开始）
*   **`‘B’`**: 工作日（排除周末）
*   **`‘M’`**: 月（每月最后一天）
*   **`‘MS’`**: 月（每月第一天）
*   **`‘T’` 或 `‘min’`**: 分钟
*   **`‘S’`**: 秒
*   **`‘A’` 或 `‘Y’`**: 年
*   **`‘2H30min’`**: 复杂的频率，如“2小时30分钟”

![](img/71cec8b067822755b43a5dfcf7d06e2b_29.png)

![](img/71cec8b067822755b43a5dfcf7d06e2b_31.png)

```python
# 生成工作日的日期序列
business_days = pd.date_range(start=‘2023-10-30’, periods=5, freq=‘B’)
print(business_days)

![](img/71cec8b067822755b43a5dfcf7d06e2b_33.png)

# 生成每小时的序列
hourly = pd.date_range(start=‘2023-10-30 08:00’, periods=6, freq=‘H’)
print(hourly)
```

![](img/71cec8b067822755b43a5dfcf7d06e2b_35.png)

![](img/71cec8b067822755b43a5dfcf7d06e2b_37.png)

## 时间索引的转换

生成的`DatetimeIndex`可以方便地进行转换。

![](img/71cec8b067822755b43a5dfcf7d06e2b_39.png)

![](img/71cec8b067822755b43a5dfcf7d06e2b_41.png)

```python
# 转换为Python原生的datetime对象列表（注意：未来版本可能废弃此方法）
py_datetime_list = date_index.to_pydatetime()

# 更常见的操作是直接将其用作Pandas DataFrame或Series的索引
import numpy as np
data = np.random.randn(len(date_index))
time_series = pd.Series(data, index=date_index)
print(time_series.head())
```

![](img/71cec8b067822755b43a5dfcf7d06e2b_43.png)

本节课中我们一起学习了如何使用`pd.date_range`函数生成规则的时间范围序列。我们掌握了通过起始/结束时间或长度来控制序列范围，并了解了强大的`freq`参数如何定义时间间隔。结合上一节的字符串转换方法，你现在已经能够创建和处理基本的时间序列索引了。接下来，我们将介绍在拥有这些时间对象后，如何进行常见的时间序列操作。