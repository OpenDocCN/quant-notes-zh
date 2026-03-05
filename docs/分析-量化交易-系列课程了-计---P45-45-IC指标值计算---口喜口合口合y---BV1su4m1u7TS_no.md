# Python金融分析+量化交易：P45：IC指标值计算

在本节课中，我们将学习如何计算IC指标值。IC指标用于衡量因子值与未来收益率之间的相关性，是量化选股中评估因子有效性的核心指标。我们将从获取股票价格数据开始，逐步完成数据格式转换，并最终计算出IC值。

## 获取收盘价数据

上一节我们介绍了因子的计算，本节中我们来看看如何获取计算IC值所需的股票收盘价数据。计算IC值需要将因子值与股票的实际收益率进行关联计算，因此首先需要获取股票池中所有股票在指定时间段内的每日收盘价。

以下是获取收盘价数据的步骤：

*   使用 `get_price` 函数。
*   参数 `security` 指定股票代码列表。
*   参数 `start_date` 和 `end_date` 分别指定起始日期和结束日期。

```python
# 假设股票池为 stock_list，起始日期为 ‘2019-01-01’，结束日期为 ‘2020-01-01’
price_data = get_price(security=stock_list, start_date='2019-01-01', end_date='2020-01-01')
```

![](img/5ec271474a1b9798f57621a63508763d_1.png)

`get_price` 函数返回的数据包含多种价格信息。为了后续计算方便，我们通常只提取收盘价，并将其整理成二维的 `DataFrame` 格式，其中行索引为日期，列名为股票代码。

```python
# 提取收盘价并整理数据格式
close_price = price_data['close']
close_price.index.name = 'date'
close_price.columns.name = 'code'
```

![](img/5ec271474a1b9798f57621a63508763d_3.png)

## 数据格式转换

现在我们已经有了因子数据和收盘价数据。为了使用量化分析库中的函数计算IC值，我们需要将数据转换成特定的格式。

这里需要使用一个专门的格式转换函数。该函数通常接受两个主要参数：处理好的因子数据和处理好的价格数据。

```python
# 假设 factor_data 是之前计算好的因子数据，close_price 是上一步获取的收盘价数据
from quant_utils import transform_data  # 假设转换函数来自 quant_utils 模块

formatted_data = transform_data(factor_data, close_price)
```

转换后的数据会包含日期、股票代码、因子值、以及根据因子值大小划分的组别等信息。组别（例如1到5）代表了该股票当天的因子值在所有股票中的百分位排名，组别数字越小，表示因子值越小。

## 计算IC指标值

在完成了数据准备和格式转换后，我们就可以计算最终的IC指标值了。IC值本质上是因子值与未来一期收益率之间的秩相关系数。

在量化分析库的 `performance` 模块中，通常有直接计算IC值的函数。我们只需将上一步转换好的 `formatted_data` 传入即可。

![](img/5ec271474a1b9798f57621a63508763d_5.png)

![](img/5ec271474a1b9798f57621a63508763d_7.png)

```python
from performance import calc_ic  # 假设计算IC的函数是 calc_ic

![](img/5ec271474a1b9798f57621a63508763d_9.png)

ic_series = calc_ic(formatted_data)
print(ic_series.head())
```

执行上述代码后，`ic_series` 就是一个时间序列，其中的每个值就代表了对应日期的IC指标值。通过观察IC值的序列，我们可以评估该因子在不同时间段内的预测能力。

![](img/5ec271474a1b9798f57621a63508763d_11.png)

## 总结

![](img/5ec271474a1b9798f57621a63508763d_13.png)

本节课中我们一起学习了IC指标值的完整计算流程。我们首先获取了股票的历史收盘价数据，然后将其与因子数据一同进行了必要的格式转换，最后调用专用函数计算出了代表因子预测能力的IC值序列。掌握IC值的计算是量化因子研究与评价的基础。