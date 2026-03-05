# Python金融量化交易：P56：统计分析所需数据准备 📊

在本节课中，我们将学习如何为统计分析准备数据。我们将基于股票价格的前两天和前一天的涨跌情况，来统计第三天涨跌的概率。这种方法虽然传统，但能帮助我们熟悉在Python中处理金融数据、进行分组统计的基本流程。

## 核心思路与数据准备

![](img/3bcc9acdec7f072af5a77e627aaf1f93_1.png)

![](img/3bcc9acdec7f072af5a77e627aaf1f93_3.png)

上一节我们介绍了量化交易的基本概念，本节中我们来看看如何通过统计分析历史数据来制定简单的交易策略。

![](img/3bcc9acdec7f072af5a77e627aaf1f93_5.png)

![](img/3bcc9acdec7f072af5a77e627aaf1f93_7.png)

核心思路是：分析历史数据中，**前一天**和**前两天**的涨跌情况（例如，都跌、都涨、一涨一跌等）与**当天**实际涨跌方向之间的关系。通过统计各种组合出现的频率，我们可以基于历史概率来预测未来。

![](img/3bcc9acdec7f072af5a77e627aaf1f93_9.png)

![](img/3bcc9acdec7f072af5a77e627aaf1f93_11.png)

首先，我们需要对原始数据进行处理，将连续的价格变化值转换为简单的“涨”（标记为1）和“跌”（标记为0）的二元状态。

![](img/3bcc9acdec7f072af5a77e627aaf1f93_13.png)

![](img/3bcc9acdec7f072af5a77e627aaf1f93_15.png)

![](img/3bcc9acdec7f072af5a77e627aaf1f93_17.png)

以下是实现这一转换的函数：

![](img/3bcc9acdec7f072af5a77e627aaf1f93_19.png)

![](img/3bcc9acdec7f072af5a77e627aaf1f93_21.png)

```python
def clip_ins(data, bins=[0]):
    global col_names
    col_names = []
    for col in feature_names:
        band_name = col + '_B'
        data[band_name] = np.digitize(data[col], bins=bins)
        col_names.append(band_name)
```

![](img/3bcc9acdec7f072af5a77e627aaf1f93_23.png)

![](img/3bcc9acdec7f072af5a77e627aaf1f93_25.png)

这个函数遍历我们之前创建的特征列（如`L1_R`， `L2_R`），使用`np.digitize`函数根据阈值列表`bins=[0]`将数据分类。大于0的值归类为1（涨），小于等于0的值归类为0（跌），并生成新的列（如`L1_R_B`， `L2_R_B`）。

调用函数并查看结果：
```python
clip_ins(data)
print(data[['L1_R_B', 'L2_R_B']].head())
```
输出结果中，`1`代表对应周期上涨，`0`代表下跌或不变。

![](img/3bcc9acdec7f072af5a77e627aaf1f93_27.png)

![](img/3bcc9acdec7f072af5a77e627aaf1f93_29.png)

## 整合实际走势数据

![](img/3bcc9acdec7f072af5a77e627aaf1f93_31.png)

![](img/3bcc9acdec7f072af5a77e627aaf1f93_33.png)

为了进行统计分析，我们还需要一列代表“当天”实际涨跌情况的数据。在我们的数据集中，`direction`列已经包含了这个信息（例如，1代表涨，-1代表跌，0代表平）。

![](img/3bcc9acdec7f072af5a77e627aaf1f93_35.png)

![](img/3bcc9acdec7f072af5a77e627aaf1f93_37.png)

因此，用于分析的数据列包括：
*   `L1_R_B`: 前一天的涨跌状态
*   `L2_R_B`: 前两天的涨跌状态
*   `direction`: 当天的实际走势

![](img/3bcc9acdec7f072af5a77e627aaf1f93_39.png)

## 执行分组统计

![](img/3bcc9acdec7f072af5a77e627aaf1f93_41.png)

![](img/3bcc9acdec7f072af5a77e627aaf1f93_43.png)

有了准备好的数据，接下来我们就可以进行分组统计，计算在各种历史情况下，当天出现涨、跌、平的概率。

![](img/3bcc9acdec7f072af5a77e627aaf1f93_45.png)

![](img/3bcc9acdec7f072af5a77e627aaf1f93_47.png)

以下是使用`pandas`的`groupby`方法进行统计的步骤：

![](img/3bcc9acdec7f072af5a77e627aaf1f93_49.png)

![](img/3bcc9acdec7f072af5a77e627aaf1f93_51.png)

```python
grouped = data.groupby(['L1_R_B', 'L2_R_B', 'direction'])
result = grouped.size()
print(result)
```

![](img/3bcc9acdec7f072af5a77e627aaf1f93_53.png)

![](img/3bcc9acdec7f072af5a77e627aaf1f93_55.png)

![](img/3bcc9acdec7f072af5a77e627aaf1f93_57.png)

这段代码会按照`L1_R_B`（前一天状态）、`L2_R_B`（前两天状态）和`direction`（当天实际方向）这三个维度对数据进行分组，然后计算每个唯一组合在历史数据中出现的次数（`size`）。

![](img/3bcc9acdec7f072af5a77e627aaf1f93_59.png)

![](img/3bcc9acdec7f072af5a77e627aaf1f93_61.png)

输出结果是一个多级索引的Series，清晰地展示了每一种历史情况组合下，后续涨、跌、平各出现了多少次。例如，`(0, 0, 1)` 表示“前两天跌，前一天跌，当天涨”这种情况在历史上发生的次数。

![](img/3bcc9acdec7f072af5a77e627aaf1f93_63.png)

![](img/3bcc9acdec7f072af5a77e627aaf1f93_65.png)

## 总结

![](img/3bcc9acdec7f072af5a77e627aaf1f93_67.png)

![](img/3bcc9acdec7f072af5a77e627aaf1f93_69.png)

本节课中我们一起学习了为统计分析准备数据的关键步骤：
1.  **数据转换**：将连续的价格变动指标通过阈值（如0）转换为代表“涨/跌”的二元状态（0或1）。
2.  **数据整合**：确保用于分析的数据包含历史状态（`L1_R_B`, `L2_R_B`）和当期实际结果（`direction`）。
3.  **分组统计**：利用`pandas.DataFrame.groupby()`方法，对历史状态和实际结果进行组合分组，并统计每种情况出现的频率。

![](img/3bcc9acdec7f072af5a77e627aaf1f93_71.png)

通过这个过程，我们得到了一个基于历史数据的概率统计表，这为后续构建基于统计概率的简单交易策略打下了基础。下一节，我们将学习如何解读这些统计结果，并据此制定买卖规则。