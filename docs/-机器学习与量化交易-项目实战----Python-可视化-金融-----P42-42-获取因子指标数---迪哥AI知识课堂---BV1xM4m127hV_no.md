# 机器学习与量化交易：P42：获取因子指标数据 📊

![](img/b2294e8ddb10b4ef5471e3dff2bb0a27_1.png)

在本节课中，我们将学习如何使用量化分析工具包，获取特定时间段内股票的因子指标数据。这是进行因子分析、构建量化模型的基础步骤。

![](img/b2294e8ddb10b4ef5471e3dff2bb0a27_3.png)

---

上一节我们介绍了分析所需工具包的导入。本节中，我们来看看如何获取并处理因子数据。

![](img/b2294e8ddb10b4ef5471e3dff2bb0a27_5.png)

## 第一步：选择日期范围 📅

在回测框架中，通常有`handle_bar`函数来逐日处理数据。但在当前的分析模块中，我们需要手动获取多日数据。因此，第一步是选择我们想要分析的日期范围。

以下是获取交易日期的函数：

```python
def get_trading_date(start_date, end_date):
    """
    获取指定时间段内的所有交易日。
    参数:
        start_date (str): 起始日期，格式如‘2019-01-01’。
        end_date (str): 结束日期，格式如‘2020-01-01’。
    返回:
        trading_dates: 交易日列表。
    """
    trading_dates = get_trading_dates(start_date, end_date)
    return trading_dates
```

![](img/b2294e8ddb10b4ef5471e3dff2bb0a27_7.png)

执行此函数后，我们就获得了从`2019-01-01`到`2020-01-01`之间的所有交易日列表。

![](img/b2294e8ddb10b4ef5471e3dff2bb0a27_9.png)

---

![](img/b2294e8ddb10b4ef5471e3dff2bb0a27_11.png)

## 第二步：获取因子指标数据 📈

有了日期范围后，接下来我们需要获取具体的因子指标数据。这涉及到指定要查询的因子和股票池。

以下是操作步骤：

![](img/b2294e8ddb10b4ef5471e3dff2bb0a27_13.png)

1.  **指定目标因子**：我们以`pe_ratio`（市盈率）为例。
2.  **构建股票池**：这里我们选择获取全部A股股票。
3.  **编写查询语句**：使用工具包提供的API，构造数据查询请求。
4.  **执行查询**：传入查询语句和指定日期，获取数据。

![](img/b2294e8ddb10b4ef5471e3dff2bb0a27_15.png)

以下是实现代码：

```python
# 1. 指定目标因子
factor_name = ‘pe_ratio’

# 2. 构建股票池（获取全部A股）
stock_pool = get_all_securities(types=[‘stock’], date=None).index.tolist()

![](img/b2294e8ddb10b4ef5471e3dff2bb0a27_17.png)

![](img/b2294e8ddb10b4ef5471e3dff2bb0a27_19.png)

# 3. 编写查询语句
q = query(fundamentals.eod_derivative_indicator.pe_ratio
         ).filter(fundamentals.eod_derivative_indicator.stockcode.in_(stock_pool))

![](img/b2294e8ddb10b4ef5471e3dff2bb0a27_21.png)

# 4. 执行查询（以第一个交易日为例）
target_date = trading_dates[0]  # 例如 ‘2019-01-02’
factor_data = get_fundamentals(q, target_date)
```

执行后，`factor_data`变量中存储了指定日期所有股票的市盈率数据。

![](img/b2294e8ddb10b4ef5471e3dff2bb0a27_23.png)

![](img/b2294e8ddb10b4ef5471e3dff2bb0a27_25.png)

---

![](img/b2294e8ddb10b4ef5471e3dff2bb0a27_27.png)

## 第三步：查看与理解数据 🔍

![](img/b2294e8ddb10b4ef5471e3dff2bb0a27_29.png)

获取数据后，我们需要查看其结构以理解如何后续使用。

![](img/b2294e8ddb10b4ef5471e3dff2bb0a27_31.png)

以下是查看数据的方法：

![](img/b2294e8ddb10b4ef5471e3dff2bb0a27_33.png)

```python
# 查看前5条数据
print(factor_data.iloc[0, 0, :5])
```

输出结果类似：
```
stock
000001.XSHE    10.5
000002.XSHE    15.2
000004.XSHE    22.1
...
```
这表示在查询日期（如2019-01-02），股票`000001.XSHE`的市盈率为10.5。

![](img/b2294e8ddb10b4ef5471e3dff2bb0a27_35.png)

**注意**：`get_fundamentals`返回的数据是一个三维结构，但通常只有最后一个维度（股票代码和因子值）包含有效数据，前两个维度可以忽略。

![](img/b2294e8ddb10b4ef5471e3dff2bb0a27_37.png)

---

![](img/b2294e8ddb10b4ef5471e3dff2bb0a27_39.png)

![](img/b2294e8ddb10b4ef5471e3dff2bb0a27_41.png)

## 总结 ✨

![](img/b2294e8ddb10b4ef5471e3dff2bb0a27_43.png)

本节课中我们一起学习了获取因子指标数据的完整流程：

![](img/b2294e8ddb10b4ef5471e3dff2bb0a27_45.png)

![](img/b2294e8ddb10b4ef5471e3dff2bb0a27_47.png)

1.  **选择日期范围**：使用`get_trading_date`函数确定分析的时间段。
2.  **获取因子数据**：通过指定因子名称、构建股票池、编写查询语句并执行，获取特定日期的因子值。
3.  **理解数据结构**：查看获取的数据，理解其三维结构，并明确有效数据所在的位置。

![](img/b2294e8ddb10b4ef5471e3dff2bb0a27_49.png)

目前，我们完成了**获取指定日期数据**的操作。接下来，我们将学习如何循环获取整个时间段内每一天的数据，为后续的因子分析做准备。