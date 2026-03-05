# Python金融分析与量化交易实战：P56：统计分析所需数据准备 📊

在本节课中，我们将学习如何为统计分析准备数据。具体来说，我们将基于股票价格的前两天和前一天的涨跌情况，来统计第三天涨跌的概率。这是一种基础的统计分析方法，虽然传统，但能帮助我们熟悉在Python中处理金融数据、进行分组统计的流程。

上一节我们介绍了数据的基本处理，本节中我们来看看如何将原始价格数据转换为可用于统计分析的分类数据。

![](img/5f643c4dc9bb5b3df8ae6dcfd5f02248_1.png)

## 理解统计需求

![](img/5f643c4dc9bb5b3df8ae6dcfd5f02248_3.png)

![](img/5f643c4dc9bb5b3df8ae6dcfd5f02248_5.png)

统计分析的目标是基于历史数据计算概率。我们有两个指标：`L1`（前一天的价格变化）和`L2`（前两天的价格变化）。我们需要判断这些指标是“涨”还是“跌”，然后统计在不同历史组合（如“前两天跌，前一天跌”）下，当天实际涨跌的频次。

![](img/5f643c4dc9bb5b3df8ae6dcfd5f02248_7.png)

![](img/5f643c4dc9bb5b3df8ae6dcfd5f02248_9.png)

以下是核心思路：
1.  将连续的价格变化数值（如 `L1`， `L2`）转换为二分类标签（例如，1代表“涨”，0代表“跌”）。
2.  结合当天的实际方向（`direction`），统计所有历史组合出现的次数。
3.  基于统计结果，可以计算在特定历史情况下，未来涨跌的概率。

![](img/5f643c4dc9bb5b3df8ae6dcfd5f02248_11.png)

![](img/5f643c4dc9bb5b3df8ae6dcfd5f02248_13.png)

## 数据转换：创建分类标签

![](img/5f643c4dc9bb5b3df8ae6dcfd5f02248_15.png)

![](img/5f643c4dc9bb5b3df8ae6dcfd5f02248_17.png)

首先，我们需要一个函数来将价格变化指标转换为分类标签。我们将以0为分界点，大于0的标记为1（涨），小于等于0的标记为0（跌）。

![](img/5f643c4dc9bb5b3df8ae6dcfd5f02248_19.png)

![](img/5f643c4dc9bb5b3df8ae6dcfd5f02248_21.png)

以下是实现此功能的函数代码：

![](img/5f643c4dc9bb5b3df8ae6dcfd5f02248_23.png)

![](img/5f643c4dc9bb5b3df8ae6dcfd5f02248_25.png)

```python
def clip_ins(data, bins=[0]):
    global col_names
    col_names = []
    for col in [l1_name, l2_name]:
        band_name = col + '_B'
        data[band_name] = np.digitize(data[col], bins=bins)
        col_names.append(band_name)
```

**代码解释**：
*   `np.digitize(data[col], bins=[0])`：此函数将数据根据`bins`参数指定的边界进行分箱。这里`bins=[0]`表示以0为唯一分界。返回值是索引，大于0的值返回1，小于等于0的值返回0。
*   `global col_names`：声明`col_names`为全局变量，用于存储新创建的列名（`L1_B`, `L2_B`），方便后续使用。
*   循环遍历`L1`和`L2`列，为每列创建对应的`_B`后缀新列，存储分类结果。

![](img/5f643c4dc9bb5b3df8ae6dcfd5f02248_27.png)

执行这个函数，将分类标签添加到数据中：

![](img/5f643c4dc9bb5b3df8ae6dcfd5f02248_29.png)

![](img/5f643c4dc9bb5b3df8ae6dcfd5f02248_31.png)

```python
clip_ins(data)
```

![](img/5f643c4dc9bb5b3df8ae6dcfd5f02248_33.png)

查看处理后的数据前几行，确认转换结果：

![](img/5f643c4dc9bb5b3df8ae6dcfd5f02248_35.png)

![](img/5f643c4dc9bb5b3df8ae6dcfd5f02248_37.png)

```python
data[[l1_name+'_B', l2_name+'_B']].head()
```

![](img/5f643c4dc9bb5b3df8ae6dcfd5f02248_39.png)

![](img/5f643c4dc9bb5b3df8ae6dcfd5f02248_41.png)

输出结果中，`1`代表对应时期价格上涨，`0`代表价格下跌或不变。例如，第一行数据`[1, 0]`表示前一天涨了，前两天跌了。

![](img/5f643c4dc9bb5b3df8ae6dcfd5f02248_43.png)

## 分组统计频次

![](img/5f643c4dc9bb5b3df8ae6dcfd5f02248_45.png)

![](img/5f643c4dc9bb5b3df8ae6dcfd5f02248_47.png)

数据准备完成后，下一步是进行分组统计。我们需要统计在前一天(`L1_B`)和前两天(`L2_B`)的每一种涨跌组合下，当天实际行情(`direction`)的分布情况。

![](img/5f643c4dc9bb5b3df8ae6dcfd5f02248_49.png)

![](img/5f643c4dc9bb5b3df8ae6dcfd5f02248_51.png)

Pandas库的`groupby`函数非常适合完成这个任务。

![](img/5f643c4dc9bb5b3df8ae6dcfd5f02248_53.png)

![](img/5f643c4dc9bb5b3df8ae6dcfd5f02248_55.png)

以下是使用`groupby`进行统计的代码：

![](img/5f643c4dc9bb5b3df8ae6dcfd5f02248_57.png)

![](img/5f643c4dc9bb5b3df8ae6dcfd5f02248_59.png)

```python
grouped = data.groupby([l1_name+'_B', l2_name+'_B', 'direction'])
result = grouped.size()
print(result)
```

![](img/5f643c4dc9bb5b3df8ae6dcfd5f02248_61.png)

![](img/5f643c4dc9bb5b3df8ae6dcfd5f02248_63.png)

**代码解释**：
*   `data.groupby([...])`：根据括号内列表指定的列进行分组。这里我们按`L1_B`（前一天状态）、`L2_B`（前两天状态）和`direction`（当天实际方向）这三个维度进行分组。
*   `grouped.size()`：计算每个唯一分组中的行数，即每种历史组合下，对应实际行情出现的**频次**。

![](img/5f643c4dc9bb5b3df8ae6dcfd5f02248_65.png)

![](img/5f643c4dc9bb5b3df8ae6dcfd5f02248_67.png)

执行上述代码后，会输出一个序列（Series），索引是`(L1_B状态, L2_B状态, direction状态)`的三元组，值是该组合在历史数据中出现的次数。

![](img/5f643c4dc9bb5b3df8ae6dcfd5f02248_69.png)

例如，输出中的 `(0, 0, -1.0): 15` 表示在历史中，出现“前两天跌(`L2_B=0`)、前一天跌(`L1_B=0`)、当天实际也跌(`direction=-1`)”这种情况共有15次。

![](img/5f643c4dc9bb5b3df8ae6dcfd5f02248_71.png)

本节课中我们一起学习了为简单的统计分析准备数据的关键步骤。我们首先通过`np.digitize`函数将连续的价格变化数据转换为“涨/跌”二分类标签。然后，利用Pandas强大的`groupby`功能，统计了不同历史行情组合下，后续实际涨跌的频次。这些频次数据是计算条件概率的基础，为后续构建基于历史统计的交易策略提供了数据支持。