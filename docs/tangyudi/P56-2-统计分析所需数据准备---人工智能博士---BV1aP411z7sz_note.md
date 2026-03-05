# 量化交易入门：P56：2-统计分析所需数据准备 📊

在本节课中，我们将学习如何为统计分析准备数据。我们将基于股票价格的前两天和前一天的变化情况，来统计第三天涨跌的概率。这种方法虽然传统，但能帮助我们熟悉在Python中处理和分析数据的基本流程。

## 数据准备思路

上一节我们介绍了数据分析的基本概念，本节中我们来看看如何具体实现统计分析所需的数据准备。核心思路是：基于历史数据，统计在“前两天”和“前一天”的特定涨跌组合下，第三天出现“涨”、“跌”或“不变”的频率。

![](img/5c53e4c811dfbffca539f2c111b868f7_1.png)

![](img/5c53e4c811dfbffca539f2c111b868f7_3.png)

## 创建涨跌判断函数

![](img/5c53e4c811dfbffca539f2c111b868f7_5.png)

首先，我们需要一个函数来判断每个交易日的价格变化是涨（标记为1）还是跌（标记为0）。以下是实现该功能的函数：

![](img/5c53e4c811dfbffca539f2c111b868f7_7.png)

![](img/5c53e4c811dfbffca539f2c111b868f7_9.png)

```python
def create_binary_signal(data, base=0):
    global col_names
    col_names = []
    for col in [‘lag1‘, ‘lag2‘]:
        new_col_name = col + ‘_binary‘
        data[new_col_name] = np.where(data[col] > base, 1, 0)
        col_names.append(new_col_name)
```

![](img/5c53e4c811dfbffca539f2c111b868f7_11.png)

![](img/5c53e4c811dfbffca539f2c111b868f7_13.png)

![](img/5c53e4c811dfbffca539f2c111b868f7_15.png)

这个函数遍历代表“前一天”（`lag1`）和“前两天”（`lag2`）的列。对于每一列，它创建一个新的列名（在原列名后加“_binary”），并使用`np.where`函数进行判断：如果值大于`base`（默认为0），则标记为1（涨），否则标记为0（跌）。结果会存储在新的DataFrame列中。

![](img/5c53e4c811dfbffca539f2c111b868f7_17.png)

![](img/5c53e4c811dfbffca539f2c111b868f7_19.png)

## 应用函数并查看结果

![](img/5c53e4c811dfbffca539f2c111b868f7_21.png)

执行上述函数后，我们可以查看处理后的数据。以下是查看数据前几行的代码：

![](img/5c53e4c811dfbffca539f2c111b868f7_23.png)

```python
create_binary_signal(data)
print(data[[‘lag1_binary‘, ‘lag2_binary‘]].head())
```

执行后，数据中会新增两列，例如：
- `lag1_binary = 1` 表示前一天上涨。
- `lag2_binary = 0` 表示前两天下跌。

![](img/5c53e4c811dfbffca539f2c111b868f7_25.png)

这样，我们就将连续的价格变化数据转换成了离散的涨跌信号，便于后续的统计分析。

![](img/5c53e4c811dfbffca539f2c111b868f7_27.png)

## 整合实际涨跌方向

![](img/5c53e4c811dfbffca539f2c111b868f7_29.png)

![](img/5c53e4c811dfbffca539f2c111b868f7_31.png)

为了进行统计，我们还需要第三天的实际涨跌情况。原始数据中通常有一列名为`direction`，表示价格的实际变动方向（例如：1代表涨，-1代表跌，0代表不变）。我们需要将这列数据整合进来。

![](img/5c53e4c811dfbffca539f2c111b868f7_33.png)

现在，我们的数据中包含了：
1.  `lag1_binary`: 前一天的涨跌信号。
2.  `lag2_binary`: 前两天的涨跌信号。
3.  `direction`: 当天的实际涨跌方向。

![](img/5c53e4c811dfbffca539f2c111b868f7_35.png)

接下来，我们将基于这三个字段进行分组统计。

![](img/5c53e4c811dfbffca539f2c111b868f7_37.png)

![](img/5c53e4c811dfbffca539f2c111b868f7_39.png)

## 使用GroupBy进行分组统计

在Pandas中，`groupby`方法是进行分组统计的强大工具。我们将使用它来统计在各种“前两天+前一天”的信号组合下，第三天实际出现各种方向的次数。

![](img/5c53e4c811dfbffca539f2c111b868f7_41.png)

![](img/5c53e4c811dfbffca539f2c111b868f7_43.png)

以下是执行分组统计的代码：

![](img/5c53e4c811dfbffca539f2c111b868f7_45.png)

![](img/5c53e4c811dfbffca539f2c111b868f7_47.png)

```python
grouped = data.groupby([‘lag1_binary‘, ‘lag2_binary‘, ‘direction‘])
result = grouped.size()
print(result)
```

![](img/5c53e4c811dfbffca539f2c111b868f7_49.png)

![](img/5c53e4c811dfbffca539f2c111b868f7_51.png)

这段代码的含义是：按照`lag1_binary`（前一天信号）、`lag2_binary`（前两天信号）和`direction`（实际方向）这三个列进行分组，然后计算每个唯一组合在数据中出现的次数（`size`）。

![](img/5c53e4c811dfbffca539f2c111b868f7_53.png)

![](img/5c53e4c811dfbffca539f2c111b868f7_55.png)

执行后，我们会得到一个类似如下的结果（示例）：
- `(0, 0, -1): 120` 表示前两天跌、前一天跌、当天实际也跌的情况出现了120次。
- `(0, 1, 1): 85` 表示前两天涨、前一天跌、当天实际涨的情况出现了85次。

![](img/5c53e4c811dfbffca539f2c111b868f7_57.png)

这个结果清晰地展示了历史数据中各种情况发生的频次，为后续计算概率奠定了基础。

![](img/5c53e4c811dfbffca539f2c111b868f7_59.png)

## 总结

![](img/5c53e4c811dfbffca539f2c111b868f7_61.png)

本节课中我们一起学习了为统计分析准备数据的关键步骤。我们首先创建了一个函数，将连续的价格变化转换为二进制的涨跌信号。然后，我们整合了实际的涨跌方向数据。最后，利用Pandas的`groupby`方法，我们成功地统计了在各种历史信号组合下，后续涨跌情况出现的频率。这些处理好的数据是进行概率分析和制定简单交易策略的基础。