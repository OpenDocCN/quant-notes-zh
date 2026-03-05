# 量化交易实战课程：P37：获取因子指标数据

![](img/409dd1aa598d7dc02d60e7dd8c70855c_0.png)

![](img/409dd1aa598d7dc02d60e7dd8c70855c_2.png)

## 概述
在本节课中，我们将学习如何使用Python量化工具包获取股票因子指标数据。我们将从指定日期范围开始，获取交易日列表，然后查询特定因子（如市盈率PE）在所有A股股票中的数值。这是进行后续因子分析的基础步骤。

![](img/409dd1aa598d7dc02d60e7dd8c70855c_4.png)

---

![](img/409dd1aa598d7dc02d60e7dd8c70855c_6.png)

## 导入工具包
我们已经完成了所有必要工具包的导入。接下来，我们将使用这些工具来完成因子分析任务。

在导入的`Alphalens`模块中，`utils`模块将帮助我们处理数据，`plotting`模块将帮助我们绘图。另外两个模块将在后续的分析中使用。

![](img/409dd1aa598d7dc02d60e7dd8c70855c_8.png)

因子分析模块的名称已经修改完毕。现在，我们开始编写代码。

![](img/409dd1aa598d7dc02d60e7dd8c70855c_10.png)

## 获取交易日数据
上一节我们介绍了工具包的导入。本节中，我们来看看如何获取特定时间段内的交易日数据。

与回测框架不同，回测框架中有`handlebar`函数可以每天执行操作。在当前模块中，没有自动按天获取数据的机制。因此，我们需要自己编写代码来获取数据。

第一步是选择日期数据。这意味着我们需要提取一个日期范围，例如从2019年1月1日到2020年1月1日。在后续查询中，需要传入这些日期才能获得相应的结果。

![](img/409dd1aa598d7dc02d60e7dd8c70855c_12.png)

以下是获取交易日数据的函数：

![](img/409dd1aa598d7dc02d60e7dd8c70855c_14.png)

![](img/409dd1aa598d7dc02d60e7dd8c70855c_16.png)

```python
def get_trading_dates(start_date, end_date):
    """
    获取指定日期范围内的所有交易日。
    参数:
        start_date (str): 开始日期，格式为'YYYY-MM-DD'。
        end_date (str): 结束日期，格式为'YYYY-MM-DD'。
    返回:
        pandas.DatetimeIndex: 交易日列表。
    """
    trading_dates = get_trading_dates(start_date, end_date)
    return trading_dates
```

![](img/409dd1aa598d7dc02d60e7dd8c70855c_18.png)

我们调用这个函数来获取2019年1月1日到2020年1月1日之间的所有交易日：

```python
dates = get_trading_dates('2019-01-01', '2020-01-01')
```

![](img/409dd1aa598d7dc02d60e7dd8c70855c_20.png)

![](img/409dd1aa598d7dc02d60e7dd8c70855c_22.png)

每完成一步，我们都应该检查结果以确保正确性。检查一下获取的日期数据，确认没有问题。这样，我们就得到了指定时间段内所有交易日的列表。

## 获取股票池
接下来，我们需要获取因子指标。这涉及到执行查询操作。

![](img/409dd1aa598d7dc02d60e7dd8c70855c_24.png)

首先，我们需要确定要查询的因子名称。这里我们以市盈率（PE）为例。然后，我们需要获取股票列表。在此，我们不设限制，直接获取所有A股股票。之后，我们将打印这些因子数据，并分析不同股票中该因子的表现。

![](img/409dd1aa598d7dc02d60e7dd8c70855c_26.png)

以下是获取所有A股股票代码的代码：

![](img/409dd1aa598d7dc02d60e7dd8c70855c_28.png)

```python
# 获取沪深300指数成分股作为股票池示例
stock_pool = get_index_stocks('000300.XSHG')
```

![](img/409dd1aa598d7dc02d60e7dd8c70855c_30.png)

这里，`stock_pool`就是我们的股票池，包含了我们当前要分析的所有股票。

## 构建因子查询
有了股票池之后，我们需要构建查询操作。

![](img/409dd1aa598d7dc02d60e7dd8c70855c_32.png)

查询基于`fundamentals`数据表，目的是获取指定因子（如PE）的各种数据。由于查询语句较长，我们可以参考帮助文档。

以下是查询市盈率（PE）因子数据的代码：

```python
# 构建查询语句
q = query(
    fundamentals.eod_derivative_indicator.pe_ratio
).filter(
    fundamentals.eod_derivative_indicator.stockcode.in_(stock_pool)
)
```

![](img/409dd1aa598d7dc02d60e7dd8c70855c_34.png)

这段代码的意思是：查询`fundamentals`表中`eod_derivative_indicator`的`pe_ratio`字段，并且过滤出股票代码在我们`stock_pool`中的记录。

![](img/409dd1aa598d7dc02d60e7dd8c70855c_36.png)

![](img/409dd1aa598d7dc02d60e7dd8c70855c_38.png)

## 执行查询并获取数据
构建好查询语句后，我们需要执行查询以获取数据。

![](img/409dd1aa598d7dc02d60e7dd8c70855c_40.png)

我们使用`get_fundamentals`函数来执行查询。除了传入查询对象`q`，还需要指定查询的日期。我们之前已经定义了日期列表，这里我们先尝试查询第一天（索引为0的日期）的数据，以验证操作是否正确。

![](img/409dd1aa598d7dc02d60e7dd8c70855c_42.png)

以下是执行查询的代码：

![](img/409dd1aa598d7dc02d60e7dd8c70855c_44.png)

```python
# 执行查询，获取指定日期的因子数据
factor_data = get_fundamentals(q, date=dates[0])
```

执行后，我们获得了查询结果`factor_data`，它包含了指定日期所有股票的PE比率数据。可以忽略平台可能产生的一些警告信息。

![](img/409dd1aa598d7dc02d60e7dd8c70855c_46.png)

![](img/409dd1aa598d7dc02d60e7dd8c70855c_48.png)

## 查看与理解数据
获取数据后，我们来查看和理解数据的结构。

使用`.shape`属性查看数据的维度，并使用`.iloc`方法查看前几条数据。

![](img/409dd1aa598d7dc02d60e7dd8c70855c_50.png)

![](img/409dd1aa598d7dc02d60e7dd8c70855c_52.png)

```python
# 查看数据形状
print(factor_data.shape)

![](img/409dd1aa598d7dc02d60e7dd8c70855c_54.png)

# 查看前5条数据
print(factor_data.iloc[0, 0, :5])
```

![](img/409dd1aa598d7dc02d60e7dd8c70855c_56.png)

输出显示数据是三维的，但通常只有最后一个维度包含有效数据，前两个维度可以忽略。数据显示了股票代码及其对应的因子数值。

![](img/409dd1aa598d7dc02d60e7dd8c70855c_58.png)

需要注意的是，因为我们在查询时只传入了某一天（例如2019年1月2日）的日期，所以结果中只包含那一天的因子数据，没有日期列。这表示我们成功获取了特定日期下，股票池中所有股票的指定因子指标。

![](img/409dd1aa598d7dc02d60e7dd8c70855c_60.png)

## 当前进度总结
现在，我们已经完成了指定日期因子数据的获取。我们知道如何操作了。

![](img/409dd1aa598d7dc02d60e7dd8c70855c_62.png)

![](img/409dd1aa598d7dc02d60e7dd8c70855c_64.png)

目前我们实现的是获取**单日**的因子数据。接下来，我们的目标是将整个日期范围内的数据全部获取出来，为后续的时间序列分析做准备。

![](img/409dd1aa598d7dc02d60e7dd8c70855c_66.png)

---

![](img/409dd1aa598d7dc02d60e7dd8c70855c_68.png)

![](img/409dd1aa598d7dc02d60e7dd8c70855c_70.png)

## 总结
本节课中，我们一起学习了量化分析中获取因子指标数据的基本流程：
1.  **获取交易日列表**：使用`get_trading_dates`函数确定分析的时间范围。
2.  **构建股票池**：确定要分析的股票范围，例如沪深300成分股。
3.  **构建因子查询**：使用`query`和`filter`方法指定要获取的因子和股票范围。
4.  **执行查询**：使用`get_fundamentals`函数，传入查询语句和具体日期，获取该日的因子数据。
5.  **查看数据**：检查数据的维度和内容，确保获取成功。

![](img/409dd1aa598d7dc02d60e7dd8c70855c_72.png)

我们已经掌握了获取单日因子数据的方法。在下一节中，我们将学习如何循环获取整个时间序列的因子数据，并对其进行整理和分析。