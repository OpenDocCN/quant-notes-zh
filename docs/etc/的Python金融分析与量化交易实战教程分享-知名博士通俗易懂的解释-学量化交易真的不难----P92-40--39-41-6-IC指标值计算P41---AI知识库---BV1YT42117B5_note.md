# 量化交易完全可自学教程：P92：40. IC指标值计算

## 概述
在本节课中，我们将学习如何计算IC指标值。IC指标，即信息系数，用于衡量因子值与未来收益率之间的相关性。我们将通过获取股票价格数据、处理因子数据、进行数据格式转换，最终计算出IC值。

---

## 获取收盘价数据
上一节我们介绍了因子数据的处理，本节中我们来看看如何获取计算收益率所需的收盘价数据。

计算IC值需要用到因子值和股票的实际收益率。因此，我们需要获取股票池中所有股票在指定时间段内的每日收盘价。

以下是获取收盘价数据的步骤：

1.  使用 `get_price` 函数获取价格数据。
2.  指定股票池、起始日期和结束日期。
3.  从返回的多维数据中提取收盘价，并将其转换为二维的 `DataFrame`。

![](img/859c0f5b4010b1b821b8ca5317b3aaac_1.png)

```python
# 获取收盘价数据
price = get_price(
    securities=stock_pool,  # 股票池
    start_date='2019-01-01',  # 起始日期
    end_date='2020-01-01',  # 结束日期
    fields=['close']  # 指定获取收盘价
)

# 提取收盘价数据，并转换为二维DataFrame
close_price = price['close']
```

执行后，我们得到一个 `DataFrame`，其索引是日期，列名是股票代码，值为每日收盘价。

为了后续处理方便，我们可以为这个 `DataFrame` 指定更清晰的索引和列名。

```python
# 指定索引名称和列名
close_price.index.name = 'date'
close_price.columns.name = 'code'
```

至此，收盘价数据准备完毕。

![](img/859c0f5b4010b1b821b8ca5317b3aaac_3.png)

---

## 数据格式转换
有了因子数据和收盘价数据后，我们需要将它们转换为计算IC值所需的特定格式。

在量化库中，通常提供了一个专门的函数来完成这种格式转换。这个函数需要两个核心参数：处理好的因子数据和价格数据。

以下是进行数据格式转换的代码：

```python
# 导入必要的工具模块
from quant_utils import transform_data

# 进行数据格式转换
factors_data = transform_data(
    factor_data=processed_factor_data,  # 处理好的因子数据
    price_data=close_price  # 收盘价数据
)
```

转换后的数据 `factors_data` 是一个结构化的对象，它包含以下关键信息：
*   `date`: 日期。
*   `code`: 股票代码。
*   `factor`: 因子值。
*   `1D`/`5D`/`10D`: 未来1期、5期、10期的收益率（例如，`1D` 代表 `(今日收盘价 - 昨日收盘价) / 昨日收盘价`）。
*   `factor_quantile`: 因子分组。系统默认将因子值从小到大分为5组（1到5），数字越大代表因子值越大。这用于后续分析不同因子水平的股票表现。

这个步骤将原始数据整合为一种标准格式，为计算相关系数做好了准备。

---

## 计算IC值
数据格式转换完成后，我们就可以计算IC指标值了。

IC值本质上是因子值与未来一期收益率之间的相关系数。量化库的 `performance` 模块中通常提供了计算该系数的函数。

以下是计算IC值的代码：

![](img/859c0f5b4010b1b821b8ca5317b3aaac_5.png)

```python
# 从性能分析模块导入计算IC的函数
from performance import factor_information_coefficient

![](img/859c0f5b4010b1b821b8ca5317b3aaac_7.png)

![](img/859c0f5b4010b1b821b8ca5317b3aaac_9.png)

# 计算IC值
ic_result = factor_information_coefficient(factors_data)

# 查看前几行的IC值
print(ic_result.head())
```

执行上述代码后，`ic_result` 中会包含一列名为 `IC` 的数据，这就是我们最终计算出的每日IC指标值。正值通常表示因子与未来收益率正相关，负值则表示负相关。

![](img/859c0f5b4010b1b821b8ca5317b3aaac_11.png)

---

## 总结
本节课中我们一起学习了IC指标值的完整计算流程：
1.  **获取数据**：使用 `get_price` 函数获取指定股票池和时间的收盘价。
2.  **格式转换**：使用专用函数将因子数据和价格数据转换为计算所需的统一格式。
3.  **计算IC**：调用 `factor_information_coefficient` 函数，计算因子值与未来收益率之间的相关系数，即IC值。

![](img/859c0f5b4010b1b821b8ca5317b3aaac_13.png)

通过这三个步骤，我们可以量化评估一个选股因子的预测能力，这是构建量化策略的重要一环。