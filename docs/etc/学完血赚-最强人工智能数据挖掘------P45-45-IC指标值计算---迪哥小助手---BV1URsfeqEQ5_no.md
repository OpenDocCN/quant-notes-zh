# 金融量化分析：P45：IC指标值计算

在本节课中，我们将学习如何计算IC指标值。IC值用于衡量因子（如技术指标）与股票未来收益率之间的相关性，是量化选股中评估因子有效性的核心指标。我们将通过获取股票价格数据、计算收益率、转换数据格式，最终计算出IC值。

## 数据准备：获取收盘价

上一节我们介绍了因子的计算，本节中我们来看看如何获取计算IC值所需的实际收益率数据。计算收益率需要股票的收盘价数据。

以下是获取收盘价数据的步骤：

*   使用 `get_price` 函数。
*   指定股票池、起始日期和结束日期。
*   从返回的多维数据中提取收盘价，并整理为二维DataFrame格式。

![](img/eabf3f06fb702e769dbd30a79883ee28_1.png)

```python
# 获取股票池的收盘价数据
price = get_price(stock_pool, start_date='2019-01-01', end_date='2020-01-01')
# 提取收盘价并整理格式
close_price = price['close'].unstack()
close_price.index.name = 'date'
close_price.columns.name = 'code'
```

执行上述代码后，我们得到了一份清晰的收盘价数据，索引为日期，列名为股票代码。

![](img/eabf3f06fb702e769dbd30a79883ee28_3.png)

## 数据转换：为IC计算做准备

有了因子数据和收盘价数据后，我们需要将它们转换为计算IC值所需的特定格式。这里会使用一个专门的格式转换函数。

以下是数据转换的关键步骤：

*   调用格式转换函数 `convert_to_forward_returns_columns`。
*   传入处理好的因子数据和收盘价数据。
*   函数会自动计算未来不同周期（如1天、5天、10天）的收益率，并对因子值进行分档处理。

```python
# 将因子数据和价格数据转换为IC计算所需的格式
factor_data = al.utils.get_clean_factor_and_forward_returns(factor=processed_factor_data,
                                                             prices=close_price,
                                                             periods=(1,))
```

转换后的数据包含了日期、股票代码、未来1期收益率、原始因子值以及因子分档信息。其中，因子分档将因子值从小到大划分为5个区间（例如1到5），数字越大代表因子值越大。

## 计算IC指标值

现在，我们已经准备好了格式规整的数据，可以开始计算IC指标值了。IC值通常指斯皮尔曼秩相关系数，用于评估因子值与未来收益率排序的相关性。

![](img/eabf3f06fb702e769dbd30a79883ee28_5.png)

![](img/eabf3f06fb702e769dbd30a79883ee28_6.png)

以下是计算IC值的步骤：

![](img/eabf3f06fb702e769dbd30a79883ee28_8.png)

*   从 `al.performance` 模块中调用 `factor_information_coefficient` 函数。
*   将上一步转换得到的 `factor_data` 作为参数传入。
*   函数将返回一个包含每日IC值的数据序列。

```python
# 计算IC指标值
ic_series = al.performance.factor_information_coefficient(factor_data)
# 查看前几日的IC值
print(ic_series.head())
```

![](img/eabf3f06fb702e769dbd30a79883ee28_10.png)

执行代码后，我们便得到了每一天的IC值。这个值介于-1到1之间，正值表示因子与未来收益率正相关，负值表示负相关，其绝对值大小代表了相关性的强弱。

![](img/eabf3f06fb702e769dbd30a79883ee28_12.png)

本节课中我们一起学习了IC指标值的完整计算流程。我们首先获取了收盘价数据并计算收益率，然后通过专用函数转换数据格式，最后计算出了评估因子预测能力的IC值。理解并计算IC值是量化因子研究中的基础且关键的一步。