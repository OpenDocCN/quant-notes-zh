# Python金融分析与量化交易实战教程：P30：06-6-IC指标值计算 📊

在本节课中，我们将学习如何计算IC（信息系数）指标值。IC值是衡量因子（如我们之前计算的移动平均线差值）与股票未来收益率之间相关性强弱的关键指标，是量化选股策略评估的重要环节。

上一节我们介绍了如何计算并处理因子数据，本节中我们来看看如何获取股票价格数据，并最终计算出IC值。

## 数据准备：获取股票收盘价

首先，我们需要获取股票池中所有股票在指定时间段内的每日收盘价数据。这可以通过`get_price`函数实现。

以下是获取收盘价数据的步骤：

1.  指定股票代码列表（即我们的股票池）。
2.  设定起始日期和结束日期。
3.  调用`get_price`函数获取数据，并从中提取收盘价。

```python
# 假设 stock_pool 是之前定义好的股票池列表
start_date = ‘2019-01-01’
end_date = ‘2020-01-01’

![](img/bcecad142ed62a6435aa1b8f49b35f96_1.png)

# 获取价格数据
price_data = get_price(security=stock_pool, start_date=start_date, end_date=end_date)

# 提取收盘价，并转换为二维DataFrame格式
close_price = price_data[‘close’].unstack()
close_price.index.name = ‘date’
close_price.columns.name = ‘code’
```

执行上述代码后，`close_price`将是一个`DataFrame`，其行索引为日期，列索引为股票代码，值为对应的每日收盘价。

![](img/bcecad142ed62a6435aa1b8f49b35f96_3.png)

## 数据格式转换

为了进行后续的IC值计算，我们需要将因子数据和价格数据转换为分析库要求的特定格式。这通常涉及一个较长的格式转换函数。

以下是格式转换的关键步骤：

1.  导入必要的工具库（如`utils`）。
2.  使用专用函数将处理好的因子数据`factor_data`和收盘价数据`close_price`进行合并与转换。

```python
# 假设 factor_data 是上一节处理好的因子值DataFrame
# 假设 close_price 是上一步获取的收盘价DataFrame

# 调用格式转换函数（函数名可能为 format_data 或类似）
formatted_data = utils.format_data(factor_data=factor_data, price_data=close_price)
```

转换后的`formatted_data`将包含日期、股票代码、因子值、因子分组以及不同持有期（如1日、5日、10日）对应的未来收益率（如`1D`代表下一日的收益率）等信息。其中，因子分组是将所有股票的因子值从小到大排序后，按百分位划分成若干组（例如5组），用于观察不同因子水平的股票表现。

## 计算IC值

数据准备就绪后，我们就可以计算IC值了。IC值本质上是因子值与未来某一期收益率之间的**秩相关系数**（Rank Correlation）。我们主要关注因子值与下一期收益率（`1D`）的相关性。

以下是计算IC值的步骤：

![](img/bcecad142ed62a6435aa1b8f49b35f96_5.png)

![](img/bcecad142ed62a6435aa1b8f49b35f96_7.png)

1.  从性能分析库（如`performance`）中导入计算IC的函数。
2.  将格式化后的数据`formatted_data`传入该函数。
3.  函数将返回一个包含每日IC值等信息的`DataFrame`。

![](img/bcecad142ed62a6435aa1b8f49b35f96_9.png)

```python
# 导入计算IC的函数
from performance import calculate_ic

# 计算IC值
ic_result = calculate_ic(formatted_data)

![](img/bcecad142ed62a6435aa1b8f49b35f96_11.png)

# 查看IC值序列
print(ic_result[‘ic’].head())
```

执行后，`ic_result[‘ic’]`就是一列时间序列数据，代表了我们的因子在每个交易日对未来一日收益率的预测能力。IC值越接近1或-1，表示因子与收益率的单调关系越强；越接近0，则预测能力越弱。

![](img/bcecad142ed62a6435aa1b8f49b35f96_13.png)

本节课中我们一起学习了IC指标值的完整计算流程：从获取基础价格数据，到进行必要的数据格式转换，最后调用专用函数计算出关键的IC值序列。这个值是评估因子有效性的基石，在后续的课程中，我们将基于IC值对因子进行更深入的分析与评价。