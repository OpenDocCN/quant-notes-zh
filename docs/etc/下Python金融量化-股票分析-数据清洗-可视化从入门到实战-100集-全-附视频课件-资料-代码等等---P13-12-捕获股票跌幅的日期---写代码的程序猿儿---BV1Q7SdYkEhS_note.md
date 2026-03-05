# Python金融量化实战：P13：12 捕获股票跌幅的日期 📉

![](img/64816ef8fd1b7bd6b1c4a03f8b1a4b21_1.png)

![](img/64816ef8fd1b7bd6b1c4a03f8b1a4b21_3.png)

在本节课中，我们将学习如何通过编程找出某支股票所有“开盘价较前一日收盘价跌幅超过2%”的日期。这是一个典型的数据筛选问题，我们将使用Pandas库中的`shift`方法来实现。

![](img/64816ef8fd1b7bd6b1c4a03f8b1a4b21_5.png)

上一节我们介绍了如何计算股票价格的涨跌幅，本节中我们来看看如何基于特定条件筛选日期。

## 需求分析与伪代码

需求是：找出股票所有“开盘价比前日收盘价跌幅超过2%”的日期。

![](img/64816ef8fd1b7bd6b1c4a03f8b1a4b21_7.png)

以下是实现此需求的核心逻辑步骤：
1.  计算跌幅：`(当日开盘价 - 前日收盘价) / 前日收盘价`。
2.  判断条件：计算结果是否小于 `-0.02`（因为跌幅超过2%意味着比值更小，例如-0.03代表跌幅3%）。
3.  筛选数据：将满足条件的布尔序列作为索引，从原始数据中筛选出对应的行。
4.  提取日期：从筛选出的行数据中提取日期索引。

![](img/64816ef8fd1b7bd6b1c4a03f8b1a4b21_9.png)

## 代码实现与分步讲解

首先，我们需要获取“前日收盘价”。在时间序列数据中，“前日”意味着数据需要向后（未来方向）移动一位。Pandas的`shift()`方法可以轻松实现这个操作。

```python
# 获取‘close’列，并使其整体下移一位，得到‘前日收盘价’序列
pre_close = df[‘close’].shift(1)
```

接下来，我们计算跌幅并设置筛选条件。

![](img/64816ef8fd1b7bd6b1c4a03f8b1a4b21_11.png)

```python
# 计算跌幅：(当日开盘 - 前日收盘) / 前日收盘
# 判断跌幅是否超过2% (即小于 -0.02)
condition = (df[‘open’] - pre_close) / pre_close < -0.02
```

![](img/64816ef8fd1b7bd6b1c4a03f8b1a4b21_13.png)

![](img/64816ef8fd1b7bd6b1c4a03f8b1a4b21_15.png)

现在，我们得到了一个布尔序列`condition`，其中`True`值对应的行就是满足跌幅条件的日期。我们可以用这个序列来筛选原始数据。

![](img/64816ef8fd1b7bd6b1c4a03f8b1a4b21_17.png)

```python
# 使用布尔索引筛选出满足条件的行数据
filtered_df = df.loc[condition]
```

![](img/64816ef8fd1b7bd6b1c4a03f8b1a4b21_19.png)

最后，我们只需要这些行的日期。

![](img/64816ef8fd1b7bd6b1c4a03f8b1a4b21_21.png)

```python
# 从筛选后的DataFrame中提取日期索引
result_dates = filtered_df.index
```

![](img/64816ef8fd1b7bd6b1c4a03f8b1a4b21_23.png)

以上步骤可以合并为一行简洁的代码：

![](img/64816ef8fd1b7bd6b1c4a03f8b1a4b21_25.png)

![](img/64816ef8fd1b7bd6b1c4a03f8b1a4b21_27.png)

```python
result_dates = df.loc[(df[‘open’] - df[‘close’].shift(1)) / df[‘close’].shift(1) < -0.02].index
```

## 核心概念回顾

![](img/64816ef8fd1b7bd6b1c4a03f8b1a4b21_29.png)

![](img/64816ef8fd1b7bd6b1c4a03f8b1a4b21_31.png)

本节课中我们一起学习了如何使用`shift()`方法进行数据位移，以及如何利用布尔索引进行条件筛选。关键在于理解“前日收盘价”是通过将收盘价序列下移一位来获得的，这使得我们可以将当日开盘价与正确的前日收盘价对齐并进行计算。

![](img/64816ef8fd1b7bd6b1c4a03f8b1a4b21_33.png)

通过这个案例，我们掌握了从时间序列数据中提取符合特定波动条件日期的完整流程。