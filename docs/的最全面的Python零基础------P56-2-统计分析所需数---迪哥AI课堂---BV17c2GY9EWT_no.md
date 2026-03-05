# Python金融分析量化交易：P56：2-统计分析所需数据准备 📊

## 概述
在本节课中，我们将学习如何为统计分析准备数据。我们将基于历史股票数据，通过判断“前一天”和“前两天”的涨跌情况，来统计“当天”实际涨跌的频率，从而探索一种简单的统计交易策略。核心在于使用Python的Pandas库进行数据转换和分组统计。

---

![](img/fff2e70d820604dc94a0d045431bbfce_1.png)

## 数据准备与思路解析
上一节我们介绍了如何计算技术指标。本节中，我们来看看如何基于这些指标进行统计分析。

![](img/fff2e70d820604dc94a0d045431bbfce_3.png)

![](img/fff2e70d820604dc94a0d045431bbfce_5.png)

我们的目标是：统计在“前两天”和“前一天”的特定涨跌组合下，“当天”股价上涨或下跌的概率。例如，当“前两天”和“前一天”都下跌时，“当天”上涨的可能性有多大？

![](img/fff2e70d820604dc94a0d045431bbfce_7.png)

这本质上是一个基于历史数据的频率统计。

![](img/fff2e70d820604dc94a0d045431bbfce_9.png)

![](img/fff2e70d820604dc94a0d045431bbfce_11.png)

![](img/fff2e70d820604dc94a0d045431bbfce_13.png)

---

![](img/fff2e70d820604dc94a0d045431bbfce_15.png)

![](img/fff2e70d820604dc94a0d045431bbfce_17.png)

## 第一步：创建涨跌判断列
首先，我们需要将连续的指标数值（如涨跌幅）转换为离散的“涨”或“跌”标签。以下是实现步骤：

![](img/fff2e70d820604dc94a0d045431bbfce_19.png)

![](img/fff2e70d820604dc94a0d045431bbfce_21.png)

我们将编写一个函数 `create_bins`，其核心逻辑是判断每个指标值是否大于0。

![](img/fff2e70d820604dc94a0d045431bbfce_23.png)

![](img/fff2e70d820604dc94a0d045431bbfce_25.png)

```python
def create_bins(data):
    global col_names
    col_names = []
    for col in data.columns:
        if col.startswith('L'):
            new_col_name = col + '_B'
            data[new_col_name] = np.where(data[col] > 0, 1, 0)
            col_names.append(new_col_name)
```

**代码解释**：
*   `if col.startswith('L')`：只处理以‘L’开头的列（即我们之前创建的滞后特征列，如`L1_Close`代表前一天的收盘价）。
*   `np.where(condition, x, y)`：NumPy的条件赋值函数。如果`data[col] > 0`为真，则赋值为1（代表“涨”），否则赋值为0（代表“跌”）。
*   新列名会在原列名后加上“_B”后缀。

![](img/fff2e70d820604dc94a0d045431bbfce_27.png)

执行这个函数：
```python
create_bins(data)
```

![](img/fff2e70d820604dc94a0d045431bbfce_29.png)

![](img/fff2e70d820604dc94a0d045431bbfce_31.png)

查看处理后的数据：
```python
print(data[['L1_Close_B', 'L2_Close_B']].head())
```
输出结果中，`1`表示该日相对于更早一天上涨，`0`表示下跌。

![](img/fff2e70d820604dc94a0d045431bbfce_33.png)

---

![](img/fff2e70d820604dc94a0d045431bbfce_35.png)

![](img/fff2e70d820604dc94a0d045431bbfce_37.png)

## 第二步：整合实际走势数据
为了进行统计，我们还需要“当天”的实际涨跌情况。原始数据中通常包含一个代表价格变动方向的列，例如`direction`（其中1可能代表上涨，-1代表下跌，0代表持平）。

![](img/fff2e70d820604dc94a0d045431bbfce_39.png)

![](img/fff2e70d820604dc94a0d045431bbfce_41.png)

我们需要将这个实际走势列与刚创建的涨跌判断列结合起来。

![](img/fff2e70d820604dc94a0d045431bbfce_43.png)

---

![](img/fff2e70d820604dc94a0d045431bbfce_45.png)

![](img/fff2e70d820604dc94a0d045431bbfce_47.png)

## 第三步：使用GroupBy进行分组统计
现在，我们有了“前两天状态(`L2_Close_B`)”、“前一天状态(`L1_Close_B`)”和“当天实际走势(`direction`)”。接下来，我们对这三列进行分组，统计每种组合出现的次数。

![](img/fff2e70d820604dc94a0d045431bbfce_49.png)

![](img/fff2e70d820604dc94a0d045431bbfce_51.png)

以下是具体操作：

![](img/fff2e70d820604dc94a0d045431bbfce_53.png)

![](img/fff2e70d820604dc94a0d045431bbfce_55.png)

```python
grouped = data[['L2_Close_B', 'L1_Close_B', 'direction']].groupby(['L2_Close_B', 'L1_Close_B', 'direction'])
result = grouped.size()
print(result)
```

![](img/fff2e70d820604dc94a0d045431bbfce_57.png)

![](img/fff2e70d820604dc94a0d045431bbfce_59.png)

**代码解释**：
1.  `data[['L2_Close_B', 'L1_Close_B', 'direction']]`：选取我们需要进行分组统计的三列数据。
2.  `.groupby(['L2_Close_B', 'L1_Close_B', 'direction'])`：按照这三个列的组合进行分组。Pandas会找出所有唯一的组合。
3.  `.size()`：计算每个分组中的数据行数，即每种情况在历史中出现的频率。

![](img/fff2e70d820604dc94a0d045431bbfce_61.png)

![](img/fff2e70d820604dc94a0d045431bbfce_63.png)

执行后，你会得到一个类似以下的多级索引Series：
```
L2_Close_B  L1_Close_B  direction
0           0            -1             xx
                             0             yy
                             1             zz
            1            -1             aa
...
```
这个结果清晰地展示了在各种历史情况下，后续涨、跌、平分别发生了多少次。

![](img/fff2e70d820604dc94a0d045431bbfce_65.png)

![](img/fff2e70d820604dc94a0d045431bbfce_67.png)

---

![](img/fff2e70d820604dc94a0d045431bbfce_69.png)

## 总结
本节课中我们一起学习了为统计分析准备数据的关键步骤：
1.  **数据离散化**：使用`np.where`函数将连续的指标值转换为代表“涨/跌”的二元标签（1和0）。
2.  **数据整合**：将代表历史状态的列与代表实际结果的列组合在一起。
3.  **频率统计**：利用Pandas的`groupby().size()`方法，高效统计各种历史状态组合下，不同结果出现的频次。

![](img/fff2e70d820604dc94a0d045431bbfce_71.png)

这些步骤为我们后续计算条件概率、评估简单统计策略的有效性打下了坚实的数据基础。下一节，我们将基于这些统计结果，尝试构建并回测一个简单的交易策略。