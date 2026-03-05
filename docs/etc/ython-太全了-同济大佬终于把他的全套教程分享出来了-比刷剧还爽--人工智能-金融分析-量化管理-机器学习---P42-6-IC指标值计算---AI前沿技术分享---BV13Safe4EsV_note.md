# 量化投资入门：P42：IC指标值计算 📊

在本节课中，我们将学习如何计算量化投资中的一个核心指标——信息系数。信息系数用于衡量我们构建的选股因子与股票未来收益率之间的相关性，是评估因子有效性的关键步骤。

上一节我们介绍了如何构建和计算选股因子，本节中我们来看看如何计算该因子的IC值，以评估其预测能力。

## 获取股票收盘价数据

首先，我们需要获取计算收益率所需的股票历史收盘价数据。这可以通过`get_price`函数实现。

以下是获取收盘价数据的步骤：

1.  指定股票池：即我们需要计算哪些股票的数据。
2.  设定时间范围：确定计算收益率的起始和结束日期。
3.  提取收盘价：从返回的多维价格数据中，专门提取出收盘价序列。

```python
# 假设 stock_pool 是之前定义好的股票代码列表
start_date = ‘2019-01-01’
end_date = ‘2020-01-01’

![](img/b2e452fa1468965d84b983bebd1c12b0_1.png)

# 使用 get_price 函数获取价格数据
price_data = get_price(
    securities=stock_pool,
    start_date=start_date,
    end_date=end_date,
    fields=[‘close’]  # 指定获取收盘价
)

# 通常返回的数据是三维的（时间，股票，字段），我们将其转换为二维DataFrame（时间，股票）
close_price = price_data[‘close’]  # 提取收盘价字段
close_price.index.name = ‘date’    # 设置索引名称为日期
close_price.columns.name = ‘code’  # 设置列名称为股票代码
```

执行上述代码后，我们得到一个二维的`DataFrame`，其索引是日期，列是各只股票代码，对应的值就是每日的收盘价。

![](img/b2e452fa1468965d84b983bebd1c12b0_3.png)

## 数据格式转换

为了使用后续的IC计算函数，我们需要将因子数据和价格数据转换成特定的格式。这个转换过程通常有现成的工具函数，但参数较多。

以下是数据格式转换的关键点：

*   输入数据：处理好的因子数据`factor_data`和收盘价数据`close_price`。
*   转换函数：通常来自量化库（如`alphalens`）的工具集（`utils`）。
*   输出数据：一个包含因子值、未来收益率以及因子分组信息的结构化数据。

```python
# 导入格式转换工具（此处为示例，具体函数名需参考所用库的文档）
from alphalens import utils

# 进行数据格式转换
formatted_data = utils.get_clean_factor_and_forward_returns(
    factor=factor_data,      # 之前计算好的因子值
    prices=close_price,      # 获取的收盘价数据
    periods=(1, 5, 10)       # 计算未来1期、5期、10期的收益率
)
```

转换后得到的数据`formatted_data`是一个多列的`DataFrame`，其中包含：
*   `factor`: 因子原始值。
*   `1D`、`5D`、`10D`: 对应未来1天、5天、10天的股票收益率。
*   `factor_quantile`: 因子分组，系统自动将因子值从小到大分为5组（例如1到5），数字越大代表因子值越大。

## 计算信息系数

数据准备就绪后，我们就可以计算斯皮尔曼秩相关系数，即IC值。这衡量了因子排名与未来收益率排名之间的相关性。

以下是计算IC值的步骤：

![](img/b2e452fa1468965d84b983bebd1c12b0_5.png)

![](img/b2e452fa1468965d84b983bebd1c12b0_7.png)

1.  调用性能分析模块中的IC计算函数。
2.  传入格式化后的数据。
3.  函数将返回一个包含每日IC值等统计量的`DataFrame`。

![](img/b2e452fa1468965d84b983bebd1c12b0_9.png)

```python
# 导入性能分析模块
from alphalens import performance

# 计算信息系数（IC）
ic_data = performance.factor_information_coefficient(formatted_data)

![](img/b2e452fa1468965d84b983bebd1c12b0_11.png)

# 查看IC值序列
print(ic_data[‘IC’].head())
```

输出的`ic_data[‘IC’]`序列就是我们需要的每日IC指标值。正值通常表示因子与未来收益率正相关，因子值越大，预期收益越高；负值则相反。

![](img/b2e452fa1468965d84b983bebd1c12b0_13.png)

本节课中我们一起学习了IC指标的计算全流程：从获取基础价格数据、进行必要的格式转换，到最终调用函数计算因子与收益率之间的秩相关系数。理解并计算IC值是检验量化因子有效性的第一步，为后续的因子组合与策略构建打下基础。