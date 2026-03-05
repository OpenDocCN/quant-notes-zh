# Python金融分析与量化交易实战：P68：统计分析所需数据准备 📊

在本节课中，我们将学习如何为统计分析准备数据。具体来说，我们将基于股票价格的前两天和前一天的涨跌情况，来统计第三天实际涨跌的概率分布。这是一种基础的统计分析方法，旨在帮助大家熟悉在Python中处理金融数据、进行分组统计的流程。

![](img/8bed6821c051cd4559a528a5e88ff183_1.png)

上一节我们介绍了如何计算技术指标，本节中我们来看看如何基于这些指标进行统计分析。

![](img/8bed6821c051cd4559a528a5e88ff183_3.png)

![](img/8bed6821c051cd4559a528a5e88ff183_5.png)

![](img/8bed6821c051cd4559a528a5e88ff183_7.png)

## 数据准备与分类

![](img/8bed6821c051cd4559a528a5e88ff183_9.png)

![](img/8bed6821c051cd4559a528a5e88ff183_11.png)

![](img/8bed6821c051cd4559a528a5e88ff183_13.png)

首先，我们需要对数据进行分类。我们有两个指标：`LE`（前一天涨跌幅）和`L2`（前两天涨跌幅）。我们的目标是判断这些值是大于0（涨）还是小于0（跌），并将结果用0和1表示。

![](img/8bed6821c051cd4559a528a5e88ff183_15.png)

![](img/8bed6821c051cd4559a528a5e88ff183_17.png)

![](img/8bed6821c051cd4559a528a5e88ff183_19.png)

以下是实现这一步骤的代码：

![](img/8bed6821c051cd4559a528a5e88ff183_21.png)

![](img/8bed6821c051cd4559a528a5e88ff183_23.png)

![](img/8bed6821c051cd4559a528a5e88ff183_25.png)

```python
def create_binary_data(data, bs=0):
    global col_names
    col_names = []
    for col in data.columns:
        if col in [‘LE‘, ‘L2‘]:
            new_col_name = col + ‘_GB‘
            data[new_col_name] = (data[col] > bs).astype(int)
            col_names.append(new_col_name)
```

执行该函数后，数据框中会新增两列：`LE_GB`和`L2_GB`。值为1表示对应指标大于0（上涨），值为0表示小于或等于0（下跌或持平）。

![](img/8bed6821c051cd4559a528a5e88ff183_27.png)

![](img/8bed6821c051cd4559a528a5e88ff183_29.png)

## 整合实际走势数据

![](img/8bed6821c051cd4559a528a5e88ff183_31.png)

![](img/8bed6821c051cd4559a528a5e88ff183_33.png)

为了进行统计，我们还需要一列代表股票实际的当日走势，即`direction`列。它表示价格的实际变化方向。

![](img/8bed6821c051cd4559a528a5e88ff183_35.png)

![](img/8bed6821c051cd4559a528a5e88ff183_37.png)

现在，我们的数据包含了：
*   `LE_GB`: 前一日涨跌分类。
*   `L2_GB`: 前两日涨跌分类。
*   `direction`: 当日实际走势。

![](img/8bed6821c051cd4559a528a5e88ff183_39.png)

![](img/8bed6821c051cd4559a528a5e88ff183_41.png)

## 进行分组统计

![](img/8bed6821c051cd4559a528a5e88ff183_43.png)

![](img/8bed6821c051cd4559a528a5e88ff183_45.png)

接下来，我们需要统计在不同历史情况下（即`LE_GB`和`L2_GB`的各种组合下），实际`direction`的分布情况。这可以通过`pandas`的`groupby`方法轻松实现。

![](img/8bed6821c051cd4559a528a5e88ff183_47.png)

![](img/8bed6821c051cd4559a528a5e88ff183_49.png)

![](img/8bed6821c051cd4559a528a5e88ff183_51.png)

以下是进行分组统计的代码：

![](img/8bed6821c051cd4559a528a5e88ff183_53.png)

![](img/8bed6821c051cd4559a528a5e88ff183_55.png)

![](img/8bed6821c051cd4559a528a5e88ff183_57.png)

```python
grouped = data.groupby([‘LE_GB‘, ‘L2_GB‘, ‘direction‘])
result = grouped.size()
print(result)
```

![](img/8bed6821c051cd4559a528a5e88ff183_59.png)

![](img/8bed6821c051cd4559a528a5e88ff183_61.png)

![](img/8bed6821c051cd4559a528a5e88ff183_63.png)

执行这段代码后，我们将得到一个统计结果。它展示了所有可能的历史情况组合（如前一日跌、前两日跌），以及在这些情况下，实际出现上涨、下跌或持平的次数。

![](img/8bed6821c051cd4559a528a5e88ff183_65.png)

![](img/8bed6821c051cd4559a528a5e88ff183_67.png)

例如，输出结果中的一行 `(0, 0, 1)` 可能表示：当前一日下跌(`LE_GB=0`)、前两日也下跌(`L2_GB=0`)时，实际第三日上涨(`direction=1`)的历史出现次数。

![](img/8bed6821c051cd4559a528a5e88ff183_69.png)

![](img/8bed6821c051cd4559a528a5e88ff183_71.png)

**本节课中我们一起学习了**如何为基于历史频率的统计分析准备数据。我们掌握了将连续指标转换为二分类（涨/跌）的方法，并利用`groupby`对不同的历史情况进行分组计数，为后续的概率计算奠定了基础。这是一种理解市场历史规律的基础方法。