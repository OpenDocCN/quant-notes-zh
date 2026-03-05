# 量化交易教程：P42：获取因子指标数据 📊

在本节课中，我们将学习如何使用Python工具包获取金融因子指标数据。这是进行因子分析的第一步，我们将从指定日期范围内获取特定因子（如市盈率）在所有A股股票上的数据。

![](img/2bf55b9031025c6baa31dd7c56769ee0_1.png)

---

## 第一步：导入工具包与准备工作

![](img/2bf55b9031025c6baa31dd7c56769ee0_3.png)

上一节我们介绍了量化分析的基本框架，本节中我们来看看如何获取具体的因子数据。首先，我们已经导入了所有必要的工具包。

```python
# 导入必要的模块
# UTUS用于数据处理，PROTE用于绘图，其余模块用于分析
```

![](img/2bf55b9031025c6baa31dd7c56769ee0_5.png)

---

## 第二步：选择日期范围

![](img/2bf55b9031025c6baa31dd7c56769ee0_7.png)

与回测框架不同，当前模块没有内置的每日数据获取机制。因此，我们需要手动指定一个日期范围来查询数据。

![](img/2bf55b9031025c6baa31dd7c56769ee0_9.png)

以下是获取交易日期的步骤：

1.  定义一个函数 `get_trading_dates`，它接受开始日期和结束日期两个参数。
2.  函数返回指定时间段内的所有交易日列表。

![](img/2bf55b9031025c6baa31dd7c56769ee0_11.png)

```python
def get_trading_dates(start_date, end_date):
    """
    获取指定日期范围内的所有交易日。
    参数:
        start_date (str): 开始日期，格式为‘YYYY-MM-DD’。
        end_date (str): 结束日期，格式为‘YYYY-MM-DD’。
    返回:
        list: 交易日列表。
    """
    # 此处调用相关API获取交易日列表
    trading_dates = [...]  # 示例返回
    return trading_dates

![](img/2bf55b9031025c6baa31dd7c56769ee0_13.png)

# 示例：获取2019年全年的交易日
all_trading_dates = get_trading_dates('2019-01-01', '2020-01-01')
```

![](img/2bf55b9031025c6baa31dd7c56769ee0_15.png)

![](img/2bf55b9031025c6baa31dd7c56769ee0_17.png)

执行这段代码后，我们就获得了2019年所有交易日的列表，这是后续查询的基础。

---

![](img/2bf55b9031025c6baa31dd7c56769ee0_19.png)

## 第三步：获取股票池与因子数据

![](img/2bf55b9031025c6baa31dd7c56769ee0_21.png)

![](img/2bf55b9031025c6baa31dd7c56769ee0_23.png)

有了日期范围，接下来我们需要获取要分析的股票集合（股票池）以及具体的因子指标。

以下是获取因子指标数据的步骤：

![](img/2bf55b9031025c6baa31dd7c56769ee0_25.png)

![](img/2bf55b9031025c6baa31dd7c56769ee0_27.png)

1.  **确定目标因子**：例如，我们选择市盈率（P/E Ratio）作为分析因子。
2.  **构建股票池**：这里我们选择获取所有A股股票作为初始股票池。
3.  **编写查询语句**：使用工具包提供的`query`功能，编写SQL-like的语句来查询特定因子。
4.  **执行查询**：将查询语句和指定的日期传入`get_fundamentals`函数，获取数据。

![](img/2bf55b9031025c6baa31dd7c56769ee0_29.png)

```python
# 1. 获取所有A股股票代码作为股票池
stock_pool = get_all_securities(['stock']).index.tolist()

![](img/2bf55b9031025c6baa31dd7c56769ee0_31.png)

# 2. 构建查询语句，查询市盈率因子
query_statement = query(
    valuation.pe_ratio  # 市盈率因子
).filter(
    valuation.code.in_(stock_pool)  # 筛选条件：股票代码在股票池中
)

![](img/2bf55b9031025c6baa31dd7c56769ee0_33.png)

# 3. 执行查询，获取指定日期的因子数据
# 假设我们查询第一个交易日 ‘2019-01-02’
target_date = all_trading_dates[0]
factor_data = get_fundamentals(query_statement, target_date)

![](img/2bf55b9031025c6baa31dd7c56769ee0_35.png)

# 4. 查看数据形状和前几行
print(factor_data.shape)
print(factor_data.iloc[0, 0, :5])  # 查看前5只股票的市盈率数据
```

![](img/2bf55b9031025c6baa31dd7c56769ee0_37.png)

执行后，`factor_data` 是一个三维数据结构，通常我们只关心最后一个维度，它包含了各只股票在指定日期的因子值。

![](img/2bf55b9031025c6baa31dd7c56769ee0_39.png)

---

![](img/2bf55b9031025c6baa31dd7c56769ee0_41.png)

## 总结与回顾

![](img/2bf55b9031025c6baa31dd7c56769ee0_43.png)

![](img/2bf55b9031025c6baa31dd7c56769ee0_45.png)

本节课中我们一起学习了获取因子指标数据的完整流程：

![](img/2bf55b9031025c6baa31dd7c56769ee0_47.png)

1.  **明确目标**：我们首先需要确定分析的日期范围和目标因子。
2.  **获取基础信息**：通过 `get_trading_dates` 函数获取交易日列表，并通过 `get_all_securities` 构建股票池。
3.  **查询与获取**：使用 `query` 构建查询语句，并利用 `get_fundamentals` 函数传入日期和语句，最终获得具体的因子数据表。

![](img/2bf55b9031025c6baa31dd7c56769ee0_49.png)

![](img/2bf55b9031025c6baa31dd7c56769ee0_51.png)

目前，我们完成了**获取指定日期单日因子数据**的操作。在接下来的课程中，我们将在此基础上，学习如何循环获取一段时间内连续的因子数据，并进行初步的分析与可视化。