# Python金融分析量化交易：P56：统计分析所需数据准备 📊

在本节课中，我们将学习如何为统计分析策略准备数据。我们将基于股票价格的前两天和前一天的涨跌情况，通过历史数据统计来预测第三天的走势概率。这是一种基于频率统计的传统方法，虽然简单，但能帮助我们熟悉在Python中处理金融数据和进行分组统计的操作。

上一节我们介绍了如何计算技术指标，本节中我们来看看如何将这些指标转换为可用于统计分析的形式。

![](img/278e13551ae90d6817fe02feb69044d1_1.png)

## 数据转换与标记

![](img/278e13551ae90d6817fe02feb69044d1_3.png)

![](img/278e13551ae90d6817fe02feb69044d1_5.png)

首先，我们需要将连续的价格变化指标转换为离散的涨跌标记。具体来说，对于“前一天”和“前两天”的指标，我们判断其值是大于0（涨）还是小于等于0（跌或平）。

![](img/278e13551ae90d6817fe02feb69044d1_7.png)

![](img/278e13551ae90d6817fe02feb69044d1_9.png)

以下是实现该功能的函数：

![](img/278e13551ae90d6817fe02feb69044d1_11.png)

![](img/278e13551ae90d6817fe02feb69044d1_13.png)

```python
def create_binary_data(data):
    global col_names
    col_names = []
    for col in data.columns:
        if col in [‘lag1‘, ‘lag2‘]:
            bin_name = col + ‘_binary‘
            data[bin_name] = np.where(data[col] > 0, 1, 0)
            col_names.append(bin_name)
```

![](img/278e13551ae90d6817fe02feb69044d1_15.png)

![](img/278e13551ae90d6817fe02feb69044d1_17.png)

这个函数遍历数据中的特定列（`lag1`代表前一天，`lag2`代表前两天），为每一列创建一个新的二进制列。新列的值根据原列是否大于0来决定：大于0标记为1（涨），否则标记为0（跌或平）。

![](img/278e13551ae90d6817fe02feb69044d1_19.png)

![](img/278e13551ae90d6817fe02feb69044d1_21.png)

执行该函数后，我们的数据集中会增加两列：`lag1_binary`和`lag2_binary`。

![](img/278e13551ae90d6817fe02feb69044d1_23.png)

![](img/278e13551ae90d6817fe02feb69044d1_25.png)

## 查看转换结果

数据转换完成后，我们可以查看一下结果。

![](img/278e13551ae90d6817fe02feb69044d1_27.png)

以下是查看数据前几行的代码：

![](img/278e13551ae90d6817fe02feb69044d1_29.png)

![](img/278e13551ae90d6817fe02feb69044d1_31.png)

```python
data[[‘lag1_binary‘, ‘lag2_binary‘]].head()
```

![](img/278e13551ae90d6817fe02feb69044d1_33.png)

输出结果中，`1`代表对应时期股价上涨，`0`代表下跌或持平。例如，第一行数据`lag1_binary=1, lag2_binary=0`表示前一天涨了，前两天跌了。

![](img/278e13551ae90d6817fe02feb69044d1_35.png)

![](img/278e13551ae90d6817fe02feb69044d1_37.png)

## 整合实际走势数据

![](img/278e13551ae90d6817fe02feb69044d1_39.png)

![](img/278e13551ae90d6817fe02feb69044d1_41.png)

为了进行统计，我们还需要一列代表“当天”实际走势的数据。假设原始数据中已有一列名为`direction`，其值1代表涨，0代表跌或平。

![](img/278e13551ae90d6817fe02feb69044d1_43.png)

现在，我们拥有了进行分析所需的所有数据列：
*   `lag1_binary`: 前一天涨跌标记
*   `lag2_binary`: 前两天涨跌标记
*   `direction`: 当天实际走势

![](img/278e13551ae90d6817fe02feb69044d1_45.png)

![](img/278e13551ae90d6817fe02feb69044d1_47.png)

## 执行分组统计

![](img/278e13551ae90d6817fe02feb69044d1_49.png)

![](img/278e13551ae90d6817fe02feb69044d1_51.png)

接下来，我们需要统计在各种历史情况组合下，当天实际涨跌的频率。这可以通过`pandas`的`groupby`方法轻松实现。

![](img/278e13551ae90d6817fe02feb69044d1_53.png)

![](img/278e13551ae90d6817fe02feb69044d1_55.png)

以下是进行分组统计的代码：

![](img/278e13551ae90d6817fe02feb69044d1_57.png)

![](img/278e13551ae90d6817fe02feb69044d1_59.png)

```python
grouped = data.groupby([‘lag1_binary‘, ‘lag2_binary‘, ‘direction‘])
result = grouped.size()
print(result)
```

![](img/278e13551ae90d6817fe02feb69044d1_61.png)

![](img/278e13551ae90d6817fe02feb69044d1_63.png)

这段代码按照`lag1_binary`（前一天）、`lag2_binary`（前两天）和`direction`（当天实际）这三个列进行分组，然后计算每个唯一组合在数据中出现的次数（`size`）。

![](img/278e13551ae90d6817fe02feb69044d1_65.png)

![](img/278e13551ae90d6817fe02feb69044d1_67.png)

输出结果是一个序列（Series），它清晰地展示了所有可能的历史组合所对应的当天涨跌次数。例如，结果`(0, 0, 1): 150`表示在历史数据中，前两天跌、前一天跌、而当天涨的情况出现了150次。

![](img/278e13551ae90d6817fe02feb69044d1_69.png)

---

![](img/278e13551ae90d6817fe02feb69044d1_71.png)

本节课中我们一起学习了为统计分析准备数据的关键步骤。我们首先将连续的技术指标转换为二进制的涨跌标记，然后整合当天的实际走势数据，最后利用`groupby`方法统计了各种历史情况组合下未来走势的频率。这些数据是构建基于历史频率的简单交易策略的基础。下一节，我们将利用这些统计结果来制定具体的交易决策规则。