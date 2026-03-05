# Python金融分析+量化交易：P42：获取因子指标数据 📊

## 概述
在本节课中，我们将学习如何使用量化交易平台获取特定时间段内所有股票的因子指标数据。我们将从获取交易日列表开始，然后构建查询语句，最终获取并查看指定因子（如市盈率PE）的数据。

---

## 导入工具包
首先，我们已经将所有必要的工具包导入完毕。这些工具将帮助我们完成因子分析任务。

```python
# 导入所需模块
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
# 其他用于分析的模块...
```

在导入的模块中，`UT`用于数据处理，`POLOATING`用于绘图，其余模块将在后续分析中使用。现在，我们开始编写代码。

---

## 选择日期数据
上一节我们介绍了工具包的导入。本节中，我们来看看如何获取分析所需的日期数据。

与回测框架不同，当前模块没有每日自动执行的`handlebar`函数。因此，我们需要手动获取多天的数据。第一步是选择一个日期范围。

![](img/9a468a9b03bfe0459081ef883db0f41e_1.png)

以下是获取交易日列表的步骤：

1.  定义一个函数来获取指定时间段内的所有交易日。
2.  函数接收两个参数：起始日期和结束日期。
3.  函数返回该时间段内的所有交易日列表。

![](img/9a468a9b03bfe0459081ef883db0f41e_3.png)

```python
def get_trading_dates(start_date, end_date):
    """
    获取指定时间段内的交易日列表。
    参数:
        start_date (str): 起始日期，格式为'YYYY-MM-DD'。
        end_date (str): 结束日期，格式为'YYYY-MM-DD'。
    返回:
        list: 交易日期的列表。
    """
    # 调用平台API获取交易日列表
    trading_dates = get_trade_days(start_date, end_date)
    return trading_dates
```

我们来执行一下，检查是否正确获取了从2019年1月1日到2020年1月1日的交易日。

```python
# 定义日期范围
start = '2019-01-01'
end = '2020-01-01'
# 获取交易日列表
dates_list = get_trading_dates(start, end)
# 打印查看
print(dates_list[:5]) # 查看前5个交易日
```

这样，我们就得到了所需时间段内的所有交易日列表。

---

![](img/9a468a9b03bfe0459081ef883db0f41e_5.png)

![](img/9a468a9b03bfe0459081ef883db0f41e_7.png)

## 获取股票池
在获取因子数据之前，我们需要确定要对哪些股票进行分析。这里，我们选择获取A股市场所有股票作为我们的股票池。

![](img/9a468a9b03bfe0459081ef883db0f41e_9.png)

以下是获取股票池的代码：

```python
# 获取所有A股股票代码
stock_pool = get_all_securities(['stock']).index.tolist()
# 指定一个变量名
my_stock_pool = stock_pool
```

![](img/9a468a9b03bfe0459081ef883db0f41e_11.png)

变量`my_stock_pool`现在包含了我们要分析的所有股票代码。

---

## 构建因子查询语句
有了日期和股票池，接下来我们需要构建查询语句来获取具体的因子数据。我们以市盈率（PE）因子为例。

![](img/9a468a9b03bfe0459081ef883db0f41e_13.png)

构建查询语句需要用到`query`函数，并设置过滤条件，确保只查询我们股票池中的股票。

![](img/9a468a9b03bfe0459081ef883db0f41e_15.png)

![](img/9a468a9b03bfe0459081ef883db0f41e_17.png)

以下是构建查询语句的步骤：

1.  从`fundamentals`表中查询`valuation`（估值）数据。
2.  使用`filter`方法过滤出股票代码在`my_stock_pool`中的记录。
3.  选择我们需要的字段，这里选择`pe_ratio`（市盈率）。

由于查询语句较长，我们可以参考平台的帮助文档来编写。

![](img/9a468a9b03bfe0459081ef883db0f41e_19.png)

```python
# 构建查询语句
q = query(
    valuation.pe_ratio # 选择市盈率字段
).filter(
    valuation.code.in_(my_stock_pool) # 过滤条件：股票代码在股票池中
)
```

![](img/9a468a9b03bfe0459081ef883db0f41e_21.png)

变量`q`现在保存了我们构建好的查询语句。

![](img/9a468a9b03bfe0459081ef883db0f41e_23.png)

---

![](img/9a468a9b03bfe0459081ef883db0f41e_25.png)

## 执行查询并获取数据
查询语句构建好后，我们需要执行它来获取数据。执行查询时需要指定具体的查询日期。

以下是执行查询的步骤：

![](img/9a468a9b03bfe0459081ef883db0f41e_27.png)

1.  使用`get_fundamentals`函数执行查询。
2.  传入查询语句`q`和指定的日期。
3.  我们先尝试查询第一个交易日（`dates_list[0]`）的数据，以验证操作是否正确。

![](img/9a468a9b03bfe0459081ef883db0f41e_29.png)

```python
# 执行查询，获取第一天的数据
target_date = dates_list[0]
factor_data = get_fundamentals(q, target_date)
```

执行后，`factor_data`变量中存储了指定日期所有股票的市盈率数据。平台可能会返回一些警告信息，这通常与平台本身有关，不影响我们的数据获取，可以忽略。

![](img/9a468a9b03bfe0459081ef883db0f41e_31.png)

---

## 查看与分析数据
现在，我们已经成功获取了因子数据。让我们查看一下数据的结构和内容。

![](img/9a468a9b03bfe0459081ef883db0f41e_33.png)

获取的数据是一个三维数据结构，但通常只有最后一个维度存放着有效数据。我们可以使用`.iloc`和`.shape`属性来查看。

![](img/9a468a9b03bfe0459081ef883db0f41e_35.png)

```python
# 查看数据的维度
print(factor_data.shape)
# 查看前5条数据的具体内容
print(factor_data.iloc[0, 0, :5])
```

![](img/9a468a9b03bfe0459081ef883db0f41e_37.png)

输出结果将显示股票代码及其对应的市盈率数值。请注意，因为我们只查询了一天，所以数据中不包含日期列，日期信息由我们传入的`target_date`参数决定。

![](img/9a468a9b03bfe0459081ef883db0f41e_39.png)

---

![](img/9a468a9b03bfe0459081ef883db0f41e_41.png)

## 总结
本节课中，我们一起学习了如何获取因子指标数据。

![](img/9a468a9b03bfe0459081ef883db0f41e_43.png)

我们首先学习了如何获取指定时间段的交易日列表。接着，我们构建了包含所有A股股票的股票池。然后，我们详细讲解了如何构建一个针对特定因子（如PE）的查询语句，并通过过滤条件限定股票范围。最后，我们执行了查询，获取了数据，并学会了如何查看和分析获取到的三维数据。

![](img/9a468a9b03bfe0459081ef883db0f41e_45.png)

通过以上步骤，我们已经掌握了从量化平台获取基础因子数据的方法，这是进行后续因子分析和策略构建的重要基础。