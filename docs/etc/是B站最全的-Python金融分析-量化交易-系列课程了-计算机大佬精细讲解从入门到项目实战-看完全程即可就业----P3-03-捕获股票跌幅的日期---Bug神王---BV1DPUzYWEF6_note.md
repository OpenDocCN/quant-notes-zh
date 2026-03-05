# Python金融分析：03：捕获股票跌幅的日期 📉

![](img/c26b15f11b2cf31dfabd62145dc4684f_1.png)

![](img/c26b15f11b2cf31dfabd62145dc4684f_3.png)

在本节课中，我们将学习如何从股票数据中筛选出特定条件的交易日。具体来说，我们将编写代码来找出所有“开盘价较前一日收盘价跌幅超过2%”的日期。通过这个案例，你将掌握使用Pandas进行数据移位和条件筛选的核心技巧。

![](img/c26b15f11b2cf31dfabd62145dc4684f_5.png)

---

## 需求分析与伪代码

上一节我们介绍了如何获取和查看股票数据，本节中我们来看看如何实现一个具体的分析需求：找到股价开盘跌幅超过2%的日期。

首先，我们需要明确计算逻辑。跌幅的计算公式是：
**(今日开盘价 - 前一日收盘价) / 前一日收盘价**

![](img/c26b15f11b2cf31dfabd62145dc4684f_7.png)

我们的目标是找出计算结果小于 -0.02（即跌幅超过2%）的日期。因此，伪代码可以这样描述：

![](img/c26b15f11b2cf31dfabd62145dc4684f_9.png)

1.  计算 `(df[‘open’] - 前一日收盘价) / 前一日收盘价`。
2.  判断上述结果是否 **小于** `-0.02`。
3.  将判断结果为 `True` 的日期筛选出来。

---

## 核心概念：数据移位（Shift）

要实现“前一日收盘价”的计算，我们需要用到 `shift()` 方法。`shift(1)` 可以将整列数据向下移动一行，这样每一行的收盘价就变成了前一行的收盘价，即我们需要的“前一日收盘价”。

![](img/c26b15f11b2cf31dfabd62145dc4684f_11.png)

以下是演示代码：
```python
# 获取收盘价列
close_series = df[‘close’]
# 将收盘价下移一行，得到前一日收盘价
prev_close = close_series.shift(1)
```

![](img/c26b15f11b2cf31dfabd62145dc4684f_13.png)

![](img/c26b15f11b2cf31dfabd62145dc4684f_15.png)

---

![](img/c26b15f11b2cf31dfabd62145dc4684f_17.png)

## 代码实现与步骤拆解

理解了核心概念后，我们可以将伪代码转化为实际的Python代码。以下是分步实现过程：

![](img/c26b15f11b2cf31dfabd62145dc4684f_19.png)

![](img/c26b15f11b2cf31dfabd62145dc4684f_21.png)

首先，计算跌幅比率。
```python
# 计算（开盘价 - 前一日收盘价）/ 前一日收盘价
price_change_pct = (df[‘open’] - df[‘close’].shift(1)) / df[‘close’].shift(1)
```

接着，根据跌幅条件生成布尔序列。
```python
# 判断跌幅是否超过2%（即小于-0.02）
condition = price_change_pct < -0.02
```

![](img/c26b15f11b2cf31dfabd62145dc4684f_23.png)

![](img/c26b15f11b2cf31dfabd62145dc4684f_25.png)

最后，使用布尔索引筛选出符合条件的行，并提取其日期索引。
```python
# 使用布尔序列筛选原数据，并获取对应的日期索引
result_dates = df.loc[condition].index
```

![](img/c26b15f11b2cf31dfabd62145dc4684f_27.png)

当然，这些步骤可以合并为一行简洁的代码：
```python
result_dates = df.loc[(df[‘open’] - df[‘close’].shift(1)) / df[‘close’].shift(1) < -0.02].index
```

---

![](img/c26b15f11b2cf31dfabd62145dc4684f_29.png)

![](img/c26b15f11b2cf31dfabd62145dc4684f_31.png)

## 总结

![](img/c26b15f11b2cf31dfabd62145dc4684f_33.png)

本节课中我们一起学习了如何捕获股票特定跌幅的日期。我们首先分析了需求并写出了伪代码，然后引入了 `shift()` 函数来获取“前一日”的数据，最后通过布尔索引完成了条件筛选。这个流程是金融数据分析中的常见模式，熟练掌握后，你可以轻松应对各种基于时间序列的条件查询任务。