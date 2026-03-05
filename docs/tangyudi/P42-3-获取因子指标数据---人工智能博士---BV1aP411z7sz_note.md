# 因子分析：P42-3：获取因子指标数据 📊

在本节课中，我们将学习如何使用量化分析工具包，获取指定时间段内所有股票的特定因子指标数据。我们将从导入工具包开始，逐步完成日期选择、股票池构建、数据查询等步骤。

## 导入工具包

首先，我们需要导入必要的工具包。`alpharies` 中的 `utils` 模块用于数据处理，`plotting` 模块用于绘图，其他模块将用于后续的分析任务。

```python
import alpharies.utils as utils
import alpharies.plotting as plotting
# 其他用于分析的模块
```

## 选择日期范围

上一节我们介绍了工具包的导入。本节中，我们来看看如何获取分析所需的时间段数据。与回测框架不同，当前模块没有内置的每日执行功能。因此，我们需要手动指定并获取一个日期序列。

以下是获取交易日期的步骤：

1.  定义函数 `get_trading_dates`，它接受开始日期和结束日期两个参数。
2.  函数内部调用相关方法，返回该时间段内所有的交易日列表。

```python
def get_trading_dates(start_date, end_date):
    """
    获取指定时间段内的所有交易日。
    参数:
        start_date (str): 开始日期，格式为'YYYY-MM-DD'。
        end_date (str): 结束日期，格式为'YYYY-MM-DD'。
    返回:
        list: 交易日期的列表。
    """
    # 此处调用工具包方法获取日期列表，示例中使用固定值说明
    trading_dates = ['2019-01-02', '2019-01-03', ...] # 实际应由API返回
    return trading_dates

# 指定我们需要分析的日期范围
start = '2019-01-01'
end = '2020-01-01'
date_list = get_trading_dates(start, end)
print(date_list[:5]) # 检查前5个日期
```

![](img/f35df35844b8f7fff01a587e322ce502_1.png)

运行上述代码后，我们就获得了从2019年1月1日到2020年1月1日之间所有的交易日列表。

![](img/f35df35844b8f7fff01a587e322ce502_3.png)

## 构建股票池

![](img/f35df35844b8f7fff01a587e322ce502_5.png)

获取日期后，下一步是确定我们要分析哪些股票。为了简化，我们将获取所有A股股票作为我们的股票池。

![](img/f35df35844b8f7fff01a587e322ce502_7.png)

以下是构建股票池的步骤：

1.  调用API获取所有A股股票的代码列表。
2.  将列表存储在变量中，供后续查询使用。

```python
# 获取所有A股股票代码
stock_pool = get_all_a_stocks() # 假设的API函数
print(f"股票池中共有 {len(stock_pool)} 只股票")
print(stock_pool[:10]) # 查看前10只股票代码
```

![](img/f35df35844b8f7fff01a587e322ce502_9.png)

![](img/f35df35844b8f7fff01a587e322ce502_11.png)

## 查询因子指标数据

![](img/f35df35844b8f7fff01a587e322ce502_13.png)

现在，我们有了日期和股票列表。接下来，我们将学习如何查询具体的因子指标数据。本节以市盈率（PE Ratio）因子为例。

以下是查询单日因子数据的步骤：

![](img/f35df35844b8f7fff01a587e322ce502_15.png)

1.  使用 `query` 语句指定要查询的因子（例如 `fundamentals.eod_derivative_indicator.pe_ratio`）。
2.  通过 `filter` 条件限制只查询我们股票池中的股票。
3.  执行查询，并传入具体的查询日期。

![](img/f35df35844b8f7fff01a587e322ce502_17.png)

```python
# 1. 构建查询语句
q = query(
    fundamentals.eod_derivative_indicator.pe_ratio # 查询PE比率因子
).filter(
    fundamentals.eod_derivative_indicator.code.in_(stock_pool) # 过滤股票池
)

# 2. 执行查询（以日期列表中的第一天为例）
single_date = date_list[0]
factor_data = get_fundamentals(q, date=single_date)

# 3. 查看查询结果
print(factor_data.iloc[0, 0, :5]) # 查看前5只股票的PE数据
```

执行代码后，我们便得到了指定日期（2019年1月2日）所有A股股票的市盈率数据。数据是一个多维结构，我们通常只关心最后一个维度的具体数值。

## 总结

本节课中我们一起学习了因子分析数据准备的核心流程。

1.  **选择日期**：我们定义了 `get_trading_dates` 函数，用于获取特定时间段的交易日序列。
2.  **构建股票池**：我们通过API获取了全市场A股代码，构建了分析范围。
3.  **查询数据**：我们使用 `query()` 和 `get_fundamentals()` 方法，成功查询到了指定日期、指定股票池的单一因子（PE比率）数据。

![](img/f35df35844b8f7fff01a587e322ce502_19.png)

目前，我们仅获取了单日的因子数据。在接下来的课程中，我们将扩展此流程，循环获取整个时间段内每一天的因子数据，为后续的因子有效性分析和策略构建打下基础。