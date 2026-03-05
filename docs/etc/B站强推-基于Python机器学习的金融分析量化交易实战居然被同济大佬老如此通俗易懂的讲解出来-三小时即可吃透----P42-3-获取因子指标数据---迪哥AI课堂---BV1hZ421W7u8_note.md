# 金融量化分析：P42：3-获取因子指标数据 📊

在本节课程中，我们将学习如何使用Python工具包获取金融因子指标数据。我们将从导入必要的模块开始，逐步完成选择日期范围、构建股票池、编写查询语句以及执行数据获取的完整流程。

![](img/02bf84e837c54ac3fb3dd7e0ffbb2ebf_1.png)

![](img/02bf84e837c54ac3fb3dd7e0ffbb2ebf_3.png)

## 导入工具包

上一节我们介绍了课程的整体框架，本节中我们来看看具体的数据获取操作。首先，我们需要导入分析所需的工具包。

```python
from alphalearns import *
```

![](img/02bf84e837c54ac3fb3dd7e0ffbb2ebf_5.png)

以下是导入的具体模块及其作用：

*   **UTUS**：用于处理数据。
*   **PROTE**：用于绘制图表。
*   其他模块：用于后续的因子分析。

## 选择日期范围

与回测框架不同，当前模块没有自动按日执行的函数。因此，我们需要手动获取多天的数据。第一步是确定我们要分析的时间段。

![](img/02bf84e837c54ac3fb3dd7e0ffbb2ebf_7.png)

我们定义一个函数来获取指定时间段内的所有交易日。

```python
def get_trading_date(start_date, end_date):
    """
    获取指定时间段内的交易日列表。
    参数:
        start_date (str): 开始日期，格式为'YYYY-MM-DD'。
        end_date (str): 结束日期，格式为'YYYY-MM-DD'。
    返回:
        trading_dates: 交易日列表。
    """
    trading_dates = ... # 调用相关API获取日期列表
    return trading_dates
```

![](img/02bf84e837c54ac3fb3dd7e0ffbb2ebf_9.png)

![](img/02bf84e837c54ac3fb3dd7e0ffbb2ebf_11.png)

例如，获取2019年全年的交易日：
```python
trading_dates = get_trading_date('2019-01-01', '2020-01-01')
```

这样，我们就得到了`trading_dates`，它包含了所需时间段内所有的交易日。

## 构建股票池

有了日期范围，接下来我们需要确定要分析哪些股票。这里我们选择以沪深300指数成分股作为我们的股票池。

![](img/02bf84e837c54ac3fb3dd7e0ffbb2ebf_13.png)

```python
# 获取沪深300成分股代码列表
stock_pool = get_index_stocks('000300.XSHG')
```

变量`stock_pool`现在包含了我们要分析的所有股票代码。

![](img/02bf84e837c54ac3fb3dd7e0ffbb2ebf_15.png)

## 查询因子指标数据

核心步骤是查询具体的因子数据。我们以市盈率（P/E）因子为例。

![](img/02bf84e837c54ac3fb3dd7e0ffbb2ebf_17.png)

首先，我们需要构建一个查询（query）语句。这通常涉及指定要查询的因子字段和过滤条件。

![](img/02bf84e837c54ac3fb3dd7e0ffbb2ebf_19.png)

```python
# 定义查询语句
q = query(fundamentals.eod_derivative_indicator.pe_ratio
         ).filter(fundamentals.stockcode.in_(stock_pool))
```

![](img/02bf84e837c54ac3fb3dd7e0ffbb2ebf_21.png)

这段代码的意思是：查询`fundamentals`表中的`pe_ratio`（市盈率）数据，并且只筛选出股票代码在我们`stock_pool`中的记录。

## 执行查询并获取数据

![](img/02bf84e837c54ac3fb3dd7e0ffbb2ebf_23.png)

![](img/02bf84e837c54ac3fb3dd7e0ffbb2ebf_25.png)

查询语句构建好后，我们需要传入具体的查询日期来执行它，并获取数据。

```python
# 指定查询日期（例如，时间段内的第一天）
query_date = trading_dates[0]

![](img/02bf84e837c54ac3fb3dd7e0ffbb2ebf_27.png)

# 执行查询
factor_data = get_fundamentals(q, query_date)
```

![](img/02bf84e837c54ac3fb3dd7e0ffbb2ebf_29.png)

执行后，`factor_data`变量中存储了在`query_date`那一天，所有`stock_pool`中股票的市盈率数据。

![](img/02bf84e837c54ac3fb3dd7e0ffbb2ebf_31.png)

## 查看与理解数据

![](img/02bf84e837c54ac3fb3dd7e0ffbb2ebf_33.png)

获取数据后，我们查看一下数据的结构。

```python
# 查看前5条数据
print(factor_data.iloc[0, 0, :5])
```

![](img/02bf84e837c54ac3fb3dd7e0ffbb2ebf_35.png)

输出结果可能类似于：
```
stock
000001.XSHE    12.5
000002.XSHE    20.3
000008.XSHE    15.7
...
Name: pe_ratio, dtype: float64
```

![](img/02bf84e837c54ac3fb3dd7e0ffbb2ebf_37.png)

这表示我们成功获取了指定日期下，各股票对应的因子值。需要注意的是，`get_fundamentals`返回的数据通常是三维结构，但有效数据通常集中在最后一个维度。

![](img/02bf84e837c54ac3fb3dd7e0ffbb2ebf_39.png)

## 当前进展与后续任务

![](img/02bf84e837c54ac3fb3dd7e0ffbb2ebf_41.png)

至此，我们已经完成了**获取指定日期单日因子数据**的操作。

![](img/02bf84e837c54ac3fb3dd7e0ffbb2ebf_43.png)

![](img/02bf84e837c54ac3fb3dd7e0ffbb2ebf_45.png)

然而，我们的目标是分析一段时间内的因子表现。因此，接下来我们需要循环`trading_dates`中的每一个日期，重复执行查询，从而获得一个时间序列上的完整因子数据面板（Panel Data）。这将是我们下一阶段工作的基础。

![](img/02bf84e837c54ac3fb3dd7e0ffbb2ebf_47.png)

---

![](img/02bf84e837c54ac3fb3dd7e0ffbb2ebf_49.png)

**本节课总结**：我们一起学习了金融因子分析中获取数据的关键步骤。我们从导入工具包开始，逐步完成了**选择日期范围**、**构建股票池**、**编写因子查询语句**以及**执行查询获取单日数据**。这个过程是进行任何量化因子分析的前提。在接下来的课程中，我们将在此基础上，扩展为获取多期时间序列数据并进行深入分析。