Python金融分析与量化交易：P22：Series缺失值处理 🐍

![](img/ce87c3edcb777b31e03a44bbf17f94a1_1.png)

在本节课中，我们将要学习如何处理Pandas Series数据结构中的缺失值。缺失值是数据分析中常见的问题，掌握其处理方法对于后续的数据清洗、运算和可视化至关重要。

上一节我们介绍了Series的基本操作，本节中我们来看看如何处理其中的缺失数据。

在Series中，缺失数据通常以`NaN`（Not a Number）值的形式出现。虽然有时可以忽略这些缺失值，但在进行数学运算或生成图表时，它们可能会带来问题或影响视觉效果。因此，我们需要掌握处理缺失数据的方法。

![](img/ce87c3edcb777b31e03a44bbf17f94a1_3.png)

处理缺失数据主要有两种思路：删除缺失值或填充缺失值。

以下是删除缺失值的方法：

首先，我们需要判断哪些数据是缺失的。可以使用`isnull()`函数来检查。

![](img/ce87c3edcb777b31e03a44bbf17f94a1_5.png)

```python
SR.isnull()
```

![](img/ce87c3edcb777b31e03a44bbf17f94a1_7.png)

该函数返回一个布尔型Series，其中`True`表示该位置是缺失值（`NaN`），`False`表示不是。

与之对应的还有一个`notnull()`函数，其逻辑与`isnull()`相反。

![](img/ce87c3edcb777b31e03a44bbf17f94a1_9.png)

```python
SR.notnull()
```

利用布尔索引的特性，我们可以结合`notnull()`来过滤掉缺失值。

![](img/ce87c3edcb777b31e03a44bbf17f94a1_11.png)

```python
SR[SR.notnull()]
```

![](img/ce87c3edcb777b31e03a44bbf17f94a1_13.png)

此外，Pandas提供了一个更直接的方法`dropna()`来删除所有包含缺失值的行。

![](img/ce87c3edcb777b31e03a44bbf17f94a1_15.png)

```python
SR.dropna()
```

以上是删除缺失值的方式。接下来，我们看看第二种方法：填充缺失值。

有时，我们不想删除数据，而是希望用某个值来替换缺失值。这时可以使用`fillna()`函数。

例如，我们可以将所有`NaN`值填充为0。

```python
SR.fillna(0)
```

![](img/ce87c3edcb777b31e03a44bbf17f94a1_17.png)

需要注意的是，无论是`dropna()`还是`fillna()`，默认都不会修改原始的Series对象，而是返回一个新的处理后的对象。如果你想保存处理结果，需要将其赋值给一个新变量。

![](img/ce87c3edcb777b31e03a44bbf17f94a1_19.png)

```python
SR_filled = SR.fillna(0)
```

![](img/ce87c3edcb777b31e03a44bbf17f94a1_21.png)

在实际应用中，填充值的选择取决于业务逻辑。除了填充固定值（如0），更常见的是用统计值（如平均值）进行填充，这有助于保持数据的整体趋势。

例如，我们可以用整个Series的平均值来填充缺失值。`mean()`函数在计算时会自动忽略`NaN`值，非常方便。

```python
mean_value = SR.mean()
SR.fillna(mean_value)
```

![](img/ce87c3edcb777b31e03a44bbf17f94a1_23.png)

本节课中我们一起学习了处理Series缺失值的两种核心方法：删除（使用`dropna()`）和填充（使用`fillna()`）。理解并熟练运用这些方法，是进行数据清洗和准备的关键步骤，为后续的金融分析与量化建模打下坚实基础。