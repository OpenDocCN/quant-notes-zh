# Python金融分析与量化交易实战教程：P43：06-6-IC指标值计算 📊

在本节课中，我们将要学习如何计算IC指标值。IC值是衡量因子预测能力的关键指标，通过计算因子值与未来收益率之间的相关性来评估因子的有效性。我们将从获取收盘价数据开始，逐步完成数据格式转换，并最终计算出IC值。

## 获取收盘价数据 💹

上一节我们介绍了因子的计算，本节中我们来看看如何获取计算IC值所需的实际收益率数据。计算收益率需要用到股票的收盘价。

以下是获取收盘价数据的步骤：

*   使用 `get_price` 函数。
*   指定股票池作为第一个参数。
*   指定起始日期 `start_date` 为 `2019-01-01`。
*   指定结束日期 `end_date` 为 `2020-01-01`。

```python
price = get_price(stock_pool, start_date='2019-01-01', end_date='2020-01-01')
```

执行后，`price` 变量中包含了多维的价格数据。为了后续计算方便，我们通常只提取收盘价，并将其转换为二维的 `DataFrame` 格式。

![](img/4d9fd3b572732e1658a1f49390ca6f00_1.png)

```python
# 提取收盘价并转换为二维DataFrame
close_price = price['close'].unstack()
# 设置索引和列名
close_price.index.name = 'date'
close_price.columns.name = 'code'
```

完成以上操作后，我们就得到了一个以日期为索引、股票代码为列名的收盘价数据表。

## 数据格式转换 🔄

![](img/4d9fd3b572732e1658a1f49390ca6f00_3.png)

现在我们已经有了因子数据和收盘价数据。为了使用量化分析库中的函数计算IC值，我们需要将数据转换成特定的格式。

以下是进行数据格式转换的步骤：

*   导入必要的工具库（如 `utils`）。
*   使用一个特定的转换函数（例如 `convert_to_forward_returns_data`）。
*   将处理好的因子数据作为第一个参数传入。
*   将处理好的收盘价数据作为第二个参数传入。

```python
from utils import convert_to_forward_returns_data

# 假设 factor_data 是之前处理好的因子DataFrame
formatted_data = convert_to_forward_returns_data(factor_data, close_price)
```

转换后的数据 `formatted_data` 会包含以下关键信息：
*   `date`: 日期。
*   `asset`: 股票代码。
*   `1D`, `5D`, `10D`: 分别对应未来1天、5天、10天的收益率。
*   `factor`: 因子值。
*   `factor_quantile`: 因子分组（默认分为5组，1代表因子值最小的组，5代表因子值最大的组）。

这个分组信息可用于后续的分层回测分析。其中，`1D` 收益率（即次日收益率）是我们当前计算IC值所需要用到的。

## 计算IC指标值 📈

在准备好格式正确的数据后，我们就可以计算IC指标值了。IC值本质上是计算因子值与未来一期收益率之间的秩相关系数。

以下是计算IC值的步骤：

![](img/4d9fd3b572732e1658a1f49390ca6f00_5.png)

![](img/4d9fd3b572732e1658a1f49390ca6f00_7.png)

*   从性能分析模块（如 `performance`）中导入计算IC的函数。
*   将上一步转换好的 `formatted_data` 作为参数传入该函数。
*   函数将返回一个包含IC值序列的结果。

![](img/4d9fd3b572732e1658a1f49390ca6f00_9.png)

```python
from performance import factor_information_coefficient

ic_series = factor_information_coefficient(formatted_data)
# 查看IC值序列的前几行
print(ic_series.head())
```

![](img/4d9fd3b572732e1658a1f49390ca6f00_11.png)

执行上述代码后，我们会得到一个以日期为索引的IC值序列。这个序列展示了我们的因子在不同时间点上对未来收益的预测能力。IC值越高（越接近1或-1），说明因子在该时间点的预测能力越强；IC值越接近0，则预测能力越弱。

## 总结 ✨

![](img/4d9fd3b572732e1658a1f49390ca6f00_13.png)

本节课中我们一起学习了IC指标值的完整计算流程。我们首先获取了股票的收盘价数据，然后将其与因子数据一同转换为量化分析库要求的特定格式，最后调用专用函数计算出了代表因子预测能力的IC值序列。掌握IC值的计算是量化因子评价的基础，为后续的因子筛选和策略构建提供了关键依据。