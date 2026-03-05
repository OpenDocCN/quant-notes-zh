# Python金融量化分析：P56：统计分析所需数据准备 📊

在本节课中，我们将学习如何为统计分析准备数据。具体来说，我们将基于股票价格的前两天和前一天的涨跌情况，来统计第三天实际涨跌的概率分布。这是一种基于历史频率的简单策略分析方法。

## 数据准备与特征构建

![](img/a70b23981ae931605f822a83245fa9ba_1.png)

![](img/a70b23981ae931605f822a83245fa9ba_3.png)

上一节我们介绍了统计分析的基本思路。本节中，我们来看看如何用Python代码实现数据准备。

![](img/a70b23981ae931605f822a83245fa9ba_5.png)

![](img/a70b23981ae931605f822a83245fa9ba_7.png)

首先，我们需要对原始数据进行处理，将连续的价格变化指标（如前一日收益率）转换为离散的涨跌标签（涨或跌）。

![](img/a70b23981ae931605f822a83245fa9ba_9.png)

![](img/a70b23981ae931605f822a83245fa9ba_11.png)

以下是实现这一功能的函数 `CLIPINS`：

![](img/a70b23981ae931605f822a83245fa9ba_13.png)

![](img/a70b23981ae931605f822a83245fa9ba_15.png)

```python
def CLIPINS(data, bins):
    global col_names
    col_names = []
    for col in feature_names:
        band_name = col + '_B'
        data[band_name] = np.digitize(data[col], bins=bins)
        col_names.append(band_name)
```

![](img/a70b23981ae931605f822a83245fa9ba_17.png)

![](img/a70b23981ae931605f822a83245fa9ba_19.png)

![](img/a70b23981ae931605f822a83245fa9ba_21.png)

这个函数遍历我们之前构建的特征列（如 `L1_Close`， `L2_Close`），并使用 `np.digitize` 函数根据阈值 `bins`（这里我们简单使用 `[0]`）将数据分为两类。大于0的标记为1（代表上涨），小于等于0的标记为0（代表下跌或持平）。新生成的列名会加上 `_B` 后缀。

![](img/a70b23981ae931605f822a83245fa9ba_23.png)

![](img/a70b23981ae931605f822a83245fa9ba_25.png)

接下来，我们调用这个函数来处理数据：

```python
CLIPINS(data, bins=[0])
```

![](img/a70b23981ae931605f822a83245fa9ba_27.png)

执行后，我们可以查看处理后的数据：

![](img/a70b23981ae931605f822a83245fa9ba_29.png)

![](img/a70b23981ae931605f822a83245fa9ba_31.png)

```python
print(data[col_names].head())
```

![](img/a70b23981ae931605f822a83245fa9ba_33.png)

![](img/a70b23981ae931605f822a83245fa9ba_35.png)

输出结果中，`1` 表示对应日期的指标为上涨，`0` 表示下跌。例如，第一行数据 `[1, 0]` 表示前一天上涨，前两天下跌。

![](img/a70b23981ae931605f822a83245fa9ba_37.png)

![](img/a70b23981ae931605f822a83245fa9ba_39.png)

## 分组统计概率

![](img/a70b23981ae931605f822a83245fa9ba_41.png)

数据准备好之后，我们需要统计在各种历史情况下，第三天实际涨跌的频次。

![](img/a70b23981ae931605f822a83245fa9ba_43.png)

![](img/a70b23981ae931605f822a83245fa9ba_45.png)

以下是使用 `groupby` 进行分组统计的代码：

![](img/a70b23981ae931605f822a83245fa9ba_47.png)

```python
grouped = data.groupby(col_names + [‘direction’])
result = grouped.size()
print(result)
```

![](img/a70b23981ae931605f822a83245fa9ba_49.png)

![](img/a70b23981ae931605f822a83245fa9ba_51.png)

![](img/a70b23981ae931605f822a83245fa9ba_53.png)

这段代码按照“前一天涨跌标签”、“前两天涨跌标签”以及“实际方向 (`direction`)”这三个维度对数据进行分组，并计算每个组合出现的次数。`direction` 列是原始数据中第三天的实际涨跌情况。

![](img/a70b23981ae931605f822a83245fa9ba_55.png)

![](img/a70b23981ae931605f822a83245fa9ba_57.png)

输出结果是一个多层索引的Series，清晰地展示了所有历史组合及其对应的出现次数。例如，索引 `(0, 0, 1.0)` 表示前两天跌、前一天跌、当天实际涨的情况在历史上出现了多少次。

![](img/a70b23981ae931605f822a83245fa9ba_59.png)

![](img/a70b23981ae931605f822a83245fa9ba_61.png)

## 课程总结

![](img/a70b23981ae931605f822a83245fa9ba_63.png)

![](img/a70b23981ae931605f822a83245fa9ba_65.png)

本节课中我们一起学习了为频率统计分析准备数据的关键步骤。

![](img/a70b23981ae931605f822a83245fa9ba_67.png)

![](img/a70b23981ae931605f822a83245fa9ba_69.png)

1.  **特征离散化**：我们将连续的价格变化指标通过阈值判断，转换为“涨/跌”的二元标签，为后续的频率统计打下基础。
2.  **分组统计**：利用Pandas的 `groupby` 方法，我们高效地统计了各种历史情况组合下，未来实际涨跌的频次。

![](img/a70b23981ae931605f822a83245fa9ba_71.png)

通过这两步，我们就得到了一个基于历史数据的概率分布表。在接下来的课程中，我们将基于这个统计结果来制定简单的交易策略并回测其效果。