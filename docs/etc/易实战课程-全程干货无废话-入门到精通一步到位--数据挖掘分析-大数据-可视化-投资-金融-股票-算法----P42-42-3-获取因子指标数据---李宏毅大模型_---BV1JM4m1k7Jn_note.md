# Python金融分析与量化交易实战课程：P42：获取因子指标数据 📊

在本节课中，我们将学习如何使用 `alphalens` 库获取因子指标数据。我们将从导入必要的工具包开始，逐步完成选择日期、获取股票池、构建查询语句以及执行数据获取的完整流程。

![](img/1c98ff7f26ebbb653052062b714626da_1.png)

![](img/1c98ff7f26ebbb653052062b714626da_3.png)

---

## 导入工具包

首先，我们需要导入后续分析将要用到的所有工具包。`alphalens` 库提供了数据处理、绘图和分析的模块。

![](img/1c98ff7f26ebbb653052062b714626da_5.png)

以下是导入的模块及其作用：
*   `utils`：用于数据处理。
*   `plotting`：用于绘图。
*   `performance` 和 `tears`：用于后续的因子绩效分析。

```python
# 示例代码结构
from alphalens import utils, plotting, performance, tears
```

---

## 选择日期数据

![](img/1c98ff7f26ebbb653052062b714626da_7.png)

上一节我们介绍了所需的工具包，本节中我们来看看如何获取分析所需的时间范围数据。与回测框架不同，`alphalens` 的分析通常需要我们先指定一个日期范围，然后一次性获取该时间段内的所有数据。

![](img/1c98ff7f26ebbb653052062b714626da_9.png)

我们需要定义一个函数来获取指定时间段内的所有交易日日期。

![](img/1c98ff7f26ebbb653052062b714626da_11.png)

```python
def get_trading_date(start_date, end_date):
    """
    获取指定起始日期和结束日期之间的所有交易日。
    参数:
        start_date (str): 起始日期，格式如 '2019-01-01'
        end_date (str): 结束日期，格式如 '2020-01-01'
    返回:
        trading_dates: 交易日列表
    """
    # 此处应调用相应API获取交易日列表，例如使用`pandas.bdate_range`
    trading_dates = ... # 获取日期的具体代码
    return trading_dates
```

执行此函数后，我们就获得了从 `2019-01-01` 到 `2020-01-01` 的所有交易日列表，为后续查询数据做好准备。

---

![](img/1c98ff7f26ebbb653052062b714626da_13.png)

## 获取股票池

有了日期范围，接下来我们需要确定分析哪些股票。在本例中，我们选择获取A股市场所有股票作为我们的股票池。

![](img/1c98ff7f26ebbb653052062b714626da_15.png)

以下是获取股票池的步骤：
1.  调用API获取全市场股票列表。
2.  将股票列表存储在变量中，供后续查询使用。

![](img/1c98ff7f26ebbb653052062b714626da_17.png)

```python
# 获取全市场股票代码，例如沪深300成分股或全部A股
stock_pool = get_all_securities() # 假设的获取函数
```

变量 `stock_pool` 现在包含了我们要进行因子分析的所有股票。

![](img/1c98ff7f26ebbb653052062b714626da_19.png)

---

![](img/1c98ff7f26ebbb653052062b714626da_21.png)

![](img/1c98ff7f26ebbb653052062b714626da_23.png)

## 构建因子查询语句

现在，我们需要查询具体的因子数据。我们以市盈率（P/E）因子为例，其代码可能为 `pe_ratio`。

![](img/1c98ff7f26ebbb653052062b714626da_25.png)

查询需要构建一个复杂的查询语句（Query），用于指定要获取的因子字段，并过滤出我们股票池中的股票。

![](img/1c98ff7f26ebbb653052062b714626da_27.png)

```python
# 构建查询语句
query = (
    query(fundamentals.eod_derivative_indicator.pe_ratio) # 查询PE比率因子
    .filter(fundamentals.stockcode.in_(stock_pool)) # 过滤股票池中的股票
)
```

![](img/1c98ff7f26ebbb653052062b714626da_29.png)

这段代码的意思是：从基本面数据中查询 `pe_ratio` 这个指标，并且只保留那些股票代码在我们 `stock_pool` 列表里的数据。

![](img/1c98ff7f26ebbb653052062b714626da_31.png)

---

![](img/1c98ff7f26ebbb653052062b714626da_33.png)

## 执行查询并获取数据

![](img/1c98ff7f26ebbb653052062b714626da_35.png)

查询语句构建好后，我们需要执行它来获取具体日期的因子数据。

以下是执行查询的步骤：
1.  调用数据获取函数 `get_fundamentals`。
2.  传入我们构建好的查询语句 `query` 和具体的查询日期 `date`。
3.  函数将返回一个包含股票代码和对应因子值的数据结构。

![](img/1c98ff7f26ebbb653052062b714626da_37.png)

```python
# 假设我们查询第一个交易日的数据
target_date = trading_dates[0]
factor_data = get_fundamentals(query, target_date)

![](img/1c98ff7f26ebbb653052062b714626da_39.png)

# 查看获取到的前几条数据
print(factor_data.iloc[0, 0, :5]) # 注意数据可能是三维结构，我们通常关心最后一个维度
```

![](img/1c98ff7f26ebbb653052062b714626da_41.png)

执行后，我们将得到在 `target_date` 这一天，股票池中所有股票的市盈率数据。数据通常以类似 `(股票代码， 因子值)` 的格式呈现。

![](img/1c98ff7f26ebbb653052062b714626da_43.png)

---

![](img/1c98ff7f26ebbb653052062b714626da_45.png)

![](img/1c98ff7f26ebbb653052062b714626da_47.png)

## 总结

![](img/1c98ff7f26ebbb653052062b714626da_49.png)

本节课中我们一起学习了使用 `alphalens` 获取因子指标数据的完整流程：
1.  **导入工具包**：引入了数据处理、绘图和分析所需的模块。
2.  **选择日期数据**：定义了函数来获取特定时间段的交易日列表。
3.  **获取股票池**：确定了本次分析所要覆盖的股票范围。
4.  **构建查询语句**：编写了用于获取特定因子（如市盈率）并过滤股票的查询。
5.  **执行查询获取数据**：通过API调用，成功获取了指定日期下股票池内各股票的因子值。

![](img/1c98ff7f26ebbb653052062b714626da_51.png)

通过以上步骤，我们已经掌握了获取原始因子数据的方法，这是进行后续因子有效性分析和构建量化策略的基础。