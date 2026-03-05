# Python金融分析与量化交易实战教程：P45：6-IC指标值计算 📊

在本节课中，我们将要学习如何计算IC指标值。IC值是衡量选股因子有效性的核心指标，它通过计算因子值与股票未来收益率之间的相关性来评估因子的预测能力。我们将从获取数据开始，逐步完成格式转换，并最终计算出IC值。

## 数据准备：获取收盘价

上一节我们介绍了如何计算因子值，本节中我们来看看如何获取计算IC值所需的收益率数据。计算IC值需要将因子值与股票的实际收益率进行相关性计算。因此，我们需要知道股票每日的实际收盘价格，以便计算收益率。

以下是获取收盘价数据的步骤：

1.  使用 `get_price` 函数获取指定股票池在特定时间段内的价格数据。
2.  从返回的多维价格数据中，提取出我们需要的“收盘价”数据。
3.  对提取出的收盘价数据进行整理，设置合适的索引和列名，使其成为一个规整的二维 `DataFrame`。

```python
# 获取股票池的收盘价数据
price = get_price(
    stock_list,  # 股票池列表
    start_date='2019-01-01',  # 起始日期
    end_date='2020-01-01'  # 结束日期
)

# 提取收盘价数据，并整理格式
close_price = price['close']  # 假设返回数据中'close'键对应收盘价
close_price.index.name = 'date'
close_price.columns.name = 'code'
```

![](img/170e8b509cafc3159caf3e847bedffb0_1.png)

通过以上步骤，我们得到了一个以日期为索引、股票代码为列名的收盘价数据表。

## 数据格式转换

![](img/170e8b509cafc3159caf3e847bedffb0_3.png)

在计算IC值之前，我们需要将因子数据和价格数据转换成量化分析库所要求的特定格式。这个转换过程通常由一个工具函数完成。

以下是进行数据格式转换的步骤：

1.  导入必要的工具函数（通常来自 `utils` 模块）。
2.  调用该函数，并传入我们准备好的因子数据 `factor_data` 和收盘价数据 `close_price`。
3.  函数会返回一个结构化的数据对象，其中包含了按日期和股票代码对齐的因子值、未来不同周期的收益率（如1日、5日、10日收益率），以及因子值的分组信息。

```python
from utils import convert_to_analysis_format  # 假设的格式转换函数

# 进行数据格式转换
analysis_data = convert_to_analysis_format(factor_data, close_price)
```

转换后的数据中，`factor` 列是原始的因子值，`1D`、`5D`、`10D` 等列分别代表未来1日、5日、10日的收益率。`factor_quantile` 列则是将因子值从小到大排序后，按分位数划分的等级（例如分为5组，1代表因子值最小的20%，5代表因子值最大的20%）。我们将主要使用 `1D`（一期）收益率进行计算。

## 计算IC指标值

现在，我们已经拥有了格式规整的因子值和未来一期收益率数据，可以开始计算斯皮尔曼秩相关系数，即IC值。

以下是计算IC值的步骤：

![](img/170e8b509cafc3159caf3e847bedffb0_5.png)

1.  从量化分析库的 `performance` 模块中，找到计算因子IC值的函数（通常名为 `factor_information_coefficient` 或类似名称）。
2.  调用该函数，并传入我们上一步得到的 `analysis_data`。
3.  函数会返回一个包含每日IC值的结果。

![](img/170e8b509cafc3159caf3e847bedffb0_7.png)

![](img/170e8b509cafc3159caf3e847bedffb0_9.png)

```python
from performance import factor_information_coefficient  # 假设的IC计算函数

# 计算IC值
ic_result = factor_information_coefficient(analysis_data)
```

计算完成后，`ic_result` 中会有一列数据，它代表了每一个交易日，因子值与股票下一交易日收益率之间的秩相关系数。这个值就是我们需要分析的IC指标序列。

![](img/170e8b509cafc3159caf3e847bedffb0_11.png)

## 总结

![](img/170e8b509cafc3159caf3e847bedffb0_13.png)

本节课中我们一起学习了IC指标值的完整计算流程。我们首先获取了计算收益率所需的收盘价数据，然后利用工具函数将因子数据和价格数据转换为标准格式，最后调用专门的函数计算出了每日的IC值。这个IC值序列是后续评估因子有效性、进行因子筛选和构建多因子模型的重要基础。