# Pandas基础（二）：P1：列操作与统计函数 📊

![](img/96c0a92c4a3649d676adf370d0b7af69_0.png)

![](img/96c0a92c4a3649d676adf370d0b7af69_2.png)

在本节课中，我们将学习Pandas库中关于列操作和统计函数的知识。我们将通过一个包含比特币5分钟K线数据的DataFrame，演示如何对数据进行加减乘除、新增列、计算统计指标以及使用`shift`、`diff`、`cumsum`等常用函数。

![](img/96c0a92c4a3649d676adf370d0b7af69_4.png)

## 概述

我们将使用一个包含时间戳、开盘价、最高价、最低价、收盘价和成交量的DataFrame。通过一系列操作，你将学会如何灵活地处理和计算数据。

![](img/96c0a92c4a3649d676adf370d0b7af69_6.png)

## 列的基本运算

![](img/96c0a92c4a3649d676adf370d0b7af69_8.png)

上一节我们介绍了DataFrame的基本结构，本节中我们来看看如何对列进行基本的数学运算。

![](img/96c0a92c4a3649d676adf370d0b7af69_10.png)

我们可以直接对DataFrame中的某一列进行加减乘除操作。例如，将收盘价乘以100：

![](img/96c0a92c4a3649d676adf370d0b7af69_12.png)

```python
df['close'] * 100
```

![](img/96c0a92c4a3649d676adf370d0b7af69_14.png)

运行代码后，原本五位数的收盘价会变成七位数。

![](img/96c0a92c4a3649d676adf370d0b7af69_16.png)

## 新增与赋值列

除了运算，我们还可以在DataFrame中新增列并为其赋值。

![](img/96c0a92c4a3649d676adf370d0b7af69_18.png)

以下是新增列的操作步骤：
1.  新增一列名为 `timestamp_two`。
2.  将原有的 `timestamp` 列加上字符串“UTC”后赋值给新列。

![](img/96c0a92c4a3649d676adf370d0b7af69_20.png)

```python
df['timestamp_two'] = df['timestamp'] + 'UTC'
```

![](img/96c0a92c4a3649d676adf370d0b7af69_22.png)

但在执行时可能会遇到错误，因为时间戳格式不能直接与字符串相加。我们需要先将 `timestamp` 列转换为字符串格式：

![](img/96c0a92c4a3649d676adf370d0b7af69_24.png)

```python
df['timestamp'] = df['timestamp'].astype(str)
df['timestamp_two'] = df['timestamp'] + 'UTC'
```

![](img/96c0a92c4a3649d676adf370d0b7af69_26.png)

运行成功后，即可看到新增的列。

![](img/96c0a92c4a3649d676adf370d0b7af69_28.png)

我们还可以新增一列并赋予固定值。例如，新增一列“交易所”，所有值都设为“KX”：

![](img/96c0a92c4a3649d676adf370d0b7af69_30.png)

![](img/96c0a92c4a3649d676adf370d0b7af69_32.png)

```python
df['交易所'] = 'KX'
```

## 统计函数

![](img/96c0a92c4a3649d676adf370d0b7af69_34.png)

![](img/96c0a92c4a3649d676adf370d0b7af69_36.png)

掌握了列的基本操作后，我们来看看如何使用统计函数来计算数据的各种指标。

![](img/96c0a92c4a3649d676adf370d0b7af69_38.png)

统计函数可以帮助我们快速计算均值、最大值、标准差等。例如，计算收盘价的均值：

```python
df['close'].mean()
```

![](img/96c0a92c4a3649d676adf370d0b7af69_40.png)

结果为 `421.93`。

![](img/96c0a92c4a3649d676adf370d0b7af69_42.png)

我们也可以同时对多列进行操作。以下代码计算 `close` 和 `volume` 两列的均值：

![](img/96c0a92c4a3649d676adf370d0b7af69_44.png)

```python
df[['close', 'volume']].mean(axis=0)
```

![](img/96c0a92c4a3649d676adf370d0b7af69_46.png)

这将返回一个Series，分别显示两列的均值。需要注意的是，参数 `axis=0` 表示对每一列分别求均值（默认行为）。如果设置 `axis=1`，则会对每一行的这两列数据求均值。

![](img/96c0a92c4a3649d676adf370d0b7af69_48.png)

Pandas还提供了许多其他统计函数，例如：
*   `max()`: 取最大值
*   `min()`: 取最小值
*   `std()`: 取标准差
*   `count()`: 取非空值的数量

![](img/96c0a92c4a3649d676adf370d0b7af69_50.png)

## 其他常用函数

![](img/96c0a92c4a3649d676adf370d0b7af69_52.png)

接下来，我们介绍一些在数据处理中非常有用的函数，它们能帮助我们进行数据偏移、差分和累积计算。

![](img/96c0a92c4a3649d676adf370d0b7af69_54.png)

### Shift函数

![](img/96c0a92c4a3649d676adf370d0b7af69_56.png)

`shift` 函数的作用是将列数据向上或向下偏移若干行。

![](img/96c0a92c4a3649d676adf370d0b7af69_58.png)

我们通过一个例子来理解。新增一列“下周期收盘价”，其值等于收盘价列向上偏移一行（`shift(-1)`）：

```python
df['下周期收盘价'] = df['close'].shift(-1)
```

观察结果可以发现，“下周期收盘价”列的每一行值，都等于原始 `close` 列的下一行值。同理，`shift(1)` 会使数据向下偏移一行。

![](img/96c0a92c4a3649d676adf370d0b7af69_60.png)

若要删除这一列，可以使用 `drop` 方法：

![](img/96c0a92c4a3649d676adf370d0b7af69_62.png)

```python
df = df.drop(columns=['下周期收盘价'])
```

![](img/96c0a92c4a3649d676adf370d0b7af69_64.png)

或者使用 `del` 语句：

![](img/96c0a92c4a3649d676adf370d0b7af69_66.png)

```python
del df['下周期收盘价']
```

### Diff函数

![](img/96c0a92c4a3649d676adf370d0b7af69_68.png)

`diff` 函数用于计算当前行与另一行（默认是上一行）的差值。

![](img/96c0a92c4a3649d676adf370d0b7af69_70.png)

新增一列“涨跌”，计算收盘价与上一周期收盘价的差值：

```python
df['涨跌'] = df['close'].diff(1)
```

![](img/96c0a92c4a3649d676adf370d0b7af69_72.png)

结果列的值等于当前行的 `close` 减去上一行的 `close`。参数为 `-1` 时，则计算上一行减当前行。

### Pct_change函数

![](img/96c0a92c4a3649d676adf370d0b7af69_74.png)

`pct_change` 函数与 `diff` 类似，但它计算的是变化百分比，即涨跌幅。

![](img/96c0a92c4a3649d676adf370d0b7af69_76.png)

![](img/96c0a92c4a3649d676adf370d0b7af69_77.png)

```python
df['涨跌幅'] = df['close'].pct_change(1)
```

![](img/96c0a92c4a3649d676adf370d0b7af69_79.png)

### 累积类函数

![](img/96c0a92c4a3649d676adf370d0b7af69_81.png)

累积类函数用于计算累计值。

`cumsum` 函数计算累积和。例如，计算累计成交量：

![](img/96c0a92c4a3649d676adf370d0b7af69_83.png)

```python
df['累计成交量'] = df['volume'].cumsum()
```

![](img/96c0a92c4a3649d676adf370d0b7af69_85.png)

`cumprod` 函数计算累积乘积。例如，假设初始投资为1元，计算资金曲线（净值）：

```python
df['资金曲线'] = (1 + df['涨跌幅']).cumprod()
```

![](img/96c0a92c4a3649d676adf370d0b7af69_87.png)

### Rank与Value_counts函数

![](img/96c0a92c4a3649d676adf370d0b7af69_89.png)

`rank` 函数用于为数据排序并返回排名。例如，对收盘价进行排名：

![](img/96c0a92c4a3649d676adf370d0b7af69_91.png)

```python
df['收盘价排名'] = df['close'].rank(ascending=False) # ascending=False为降序
```

![](img/96c0a92c4a3649d676adf370d0b7af69_93.png)

`value_counts` 函数用于统计某一列中每个值出现的次数，返回一个Series：

![](img/96c0a92c4a3649d676adf370d0b7af69_95.png)

```python
df['close'].value_counts()
```

## 总结

![](img/96c0a92c4a3649d676adf370d0b7af69_97.png)

本节课中我们一起学习了Pandas的列操作与统计函数。我们掌握了如何对列进行数学运算、新增列、使用`mean`、`max`等统计函数，以及`shift`、`diff`、`pct_change`、`cumsum`、`rank`、`value_counts`等实用方法。这些是进行数据分析和处理的基础技能。

![](img/96c0a92c4a3649d676adf370d0b7af69_99.png)

想要了解更多Pandas支持的函数，可以查阅其[官方文档](https://pandas.pydata.org/docs/)。