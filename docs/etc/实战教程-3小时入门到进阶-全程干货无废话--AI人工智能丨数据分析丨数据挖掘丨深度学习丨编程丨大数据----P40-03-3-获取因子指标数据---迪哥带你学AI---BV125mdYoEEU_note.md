# Python金融分析与量化交易实战教程：P40：03-3-获取因子指标数据 📊

在本节课中，我们将学习如何使用`alphalens`库获取因子指标数据。我们将从导入必要的工具包开始，逐步完成选择日期范围、构建股票池、编写查询语句以及执行数据获取的完整流程。

## 导入工具包

上一节我们介绍了课程的整体框架，本节中我们来看看如何获取因子数据。首先，我们需要导入分析所需的工具包。

```python
# 导入数据处理、绘图和分析所需的模块
from alphalens.utils import get_clean_factor_and_forward_returns
from alphalens.tears import create_full_tear_sheet
from alphalens.performance import factor_information_coefficient
from alphalens.plotting import plot_ic_ts
```

## 选择日期数据

与回测框架不同，`alphalens`的因子分析模块没有内置的每日数据获取机制。因此，我们需要手动指定一个日期范围来获取数据。

以下是获取交易日期的步骤：

1.  定义一个函数 `get_trading_date`，它接受开始日期和结束日期两个参数。
2.  函数内部调用相关API，返回指定时间段内的所有交易日列表。
3.  将返回的交易日列表赋值给变量 `trading_dates`。

![](img/ddb29580d100c40adaf28fca4c8e5caa_1.png)

```python
# 获取指定日期范围内的交易日数据
def get_trading_date(start_date, end_date):
    # 此处应调用具体的API来获取交易日列表，例如使用`pandas.bdate_range`
    # trading_dates = pd.bdate_range(start=start_date, end=end_date)
    # 为示例清晰，此处使用伪代码
    pass

![](img/ddb29580d100c40adaf28fca4c8e5caa_3.png)

![](img/ddb29580d100c40adaf28fca4c8e5caa_5.png)

# 指定日期范围：2019年1月1日 至 2020年1月1日
start_date = '2019-01-01'
end_date = '2020-01-01'
trading_dates = get_trading_date(start_date, end_date)
```

![](img/ddb29580d100c40adaf28fca4c8e5caa_7.png)

## 获取因子指标数据

在确定了交易日范围后，下一步是获取具体的因子指标数据。我们将以市盈率（P/E Ratio）为例进行查询。

![](img/ddb29580d100c40adaf28fca4c8e5caa_9.png)

![](img/ddb29580d100c40adaf28fca4c8e5caa_11.png)

以下是获取因子数据的步骤：

![](img/ddb29580d100c40adaf28fca4c8e5caa_13.png)

1.  **定义股票池**：获取所有A股股票的代码列表。
2.  **构建查询语句**：使用`query`函数，指定要查询的因子（如`valuation.pe_ratio`），并通过`filter`条件将股票范围限定在我们的股票池内。
3.  **执行查询**：调用数据获取函数（如`get_fundamentals`），传入查询语句和具体的查询日期，以获取该日所有股票的因子数据。

![](img/ddb29580d100c40adaf28fca4c8e5caa_15.png)

```python
# 1. 定义股票池：获取所有A股股票代码
stock_pool = get_all_securities(['stock']).index.tolist() # 示例函数

![](img/ddb29580d100c40adaf28fca4c8e5caa_17.png)

# 2. 构建查询语句
q = query(
    valuation.pe_ratio # 查询市盈率因子
).filter(
    valuation.pe_ratio != None # 过滤掉无数据的股票
).filter(
    valuation.code.in_(stock_pool) # 限定股票范围在预定义的股票池内
)

![](img/ddb29580d100c40adaf28fca4c8e5caa_19.png)

![](img/ddb29580d100c40adaf28fca4c8e5caa_21.png)

# 3. 执行查询（以第一个交易日为例）
query_date = trading_dates[0] # 获取日期列表中的第一个日期
factor_data = get_fundamentals(q, date=query_date)
```

![](img/ddb29580d100c40adaf28fca4c8e5caa_23.png)

## 查看与理解数据

![](img/ddb29580d100c40adaf28fca4c8e5caa_25.png)

数据获取完成后，我们需要查看其结构以理解返回结果。

![](img/ddb29580d100c40adaf28fca4c8e5caa_27.png)

执行 `factor_data.iloc[0, 0, :5]` 可以查看前5条数据。返回的数据通常是一个三维结构，但有效数据主要集中在最后一个维度。每条数据包含了股票代码及其对应的因子值。

![](img/ddb29580d100c40adaf28fca4c8e5caa_29.png)

```python
# 查看获取到的因子数据结构
print(factor_data.shape) # 查看数据形状
print(factor_data.iloc[0, 0, :5]) # 查看具体的前5条数据样本
```

![](img/ddb29580d100c40adaf28fca4c8e5caa_31.png)

## 当前进度与后续任务

![](img/ddb29580d100c40adaf28fca4c8e5caa_33.png)

![](img/ddb29580d100c40adaf28fca4c8e5caa_35.png)

至此，我们已经完成了**获取指定日期单日因子数据**的任务。

![](img/ddb29580d100c40adaf28fca4c8e5caa_37.png)

现在我们要做的是将这一过程扩展到整个日期范围 `trading_dates`，即循环获取每一天的因子数据，为后续的因子分析（如计算信息系数IC、绘制分析图表等）准备完整的数据集。

![](img/ddb29580d100c40adaf28fca4c8e5caa_39.png)

![](img/ddb29580d100c40adaf28fca4c8e5caa_41.png)

本节课中我们一起学习了使用`alphalens`进行因子分析的第一步：获取因子指标数据。我们掌握了如何选择日期范围、构建股票池、编写查询语句以及执行单日数据查询。在接下来的课程中，我们将在此基础上，学习如何处理多期数据并进行深入的因子有效性分析。