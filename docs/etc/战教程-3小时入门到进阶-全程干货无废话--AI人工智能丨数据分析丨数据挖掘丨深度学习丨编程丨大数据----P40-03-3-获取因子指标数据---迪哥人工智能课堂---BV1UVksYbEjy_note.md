# Python金融分析与量化交易实战教程：P40：03-3-获取因子指标数据 📊

在本节课中，我们将学习如何使用量化分析工具包获取指定时间段内的因子指标数据。我们将从导入必要的模块开始，逐步完成日期选择、股票池构建、查询语句编写以及数据获取的全过程。

## 导入工具包

上一节我们介绍了课程的整体框架，本节中我们来看看如何获取因子数据。首先，我们需要导入分析所需的工具包。

```python
# 导入数据处理、绘图和分析模块
from alphalearn.utils import *
from alphalearn.visualization import *
from alphalearn.analysis import *
```

## 选择日期数据

与回测框架不同，当前模块没有自动按日执行的`handle_bar`函数。因此，我们需要手动获取多天的数据。第一步是选择我们想要分析的日期范围。

以下是获取交易日期的函数：

```python
def get_trading_date(start_date, end_date):
    """
    获取指定时间段内的所有交易日。
    参数:
        start_date (str): 开始日期，格式为'YYYY-MM-DD'。
        end_date (str): 结束日期，格式为'YYYY-MM-DD'。
    返回:
        trading_dates: 交易日期的序列。
    """
    trading_dates = ... # 调用相关API获取日期
    return trading_dates
```

![](img/81fc1bbd1a0e1bdc50320d02a6ef111c_1.png)

我们以2019年1月1日到2020年1月1日为例：

![](img/81fc1bbd1a0e1bdc50320d02a6ef111c_3.png)

![](img/81fc1bbd1a0e1bdc50320d02a6ef111c_5.png)

```python
# 获取指定时间段的交易日
trading_dates = get_trading_date('2019-01-01', '2020-01-01')
```

![](img/81fc1bbd1a0e1bdc50320d02a6ef111c_7.png)

## 获取因子指标数据

获取日期后，下一步是查询具体的因子指标。我们将以市盈率（P/E Ratio）为例，查询其在所有A股股票中的数值。

![](img/81fc1bbd1a0e1bdc50320d02a6ef111c_9.png)

以下是构建查询的步骤：

![](img/81fc1bbd1a0e1bdc50320d02a6ef111c_11.png)

![](img/81fc1bbd1a0e1bdc50320d02a6ef111c_13.png)

1.  **确定股票池**：首先，我们需要获取所有要分析的股票代码。

    ```python
    # 获取所有A股股票代码作为股票池
    stock_pool = get_all_a_stocks() # 例如：['000001.XSHE', '000002.XSHE', ...]
    ```

![](img/81fc1bbd1a0e1bdc50320d02a6ef111c_15.png)

2.  **编写查询语句**：使用工具包提供的`query`功能来构建数据查询。查询语句较长，我们可以从帮助文档中复制模板。

    ```python
    # 构建查询语句，查询市盈率指标，并筛选股票池中的股票
    q = query(fundamentals.eod_derivative_indicator.pe_ratio
             ).filter(fundamentals.stockcode.in_(stock_pool))
    ```

![](img/81fc1bbd1a0e1bdc50320d02a6ef111c_17.png)

3.  **执行查询**：最后，我们传入查询语句和具体的日期来获取数据。

    ```python
    # 执行查询，获取指定日期的因子数据
    # 这里我们先查询第一天的数据作为测试
    test_date = trading_dates[0]
    factor_data = get_fundamentals(q, test_date)
    ```

![](img/81fc1bbd1a0e1bdc50320d02a6ef111c_19.png)

![](img/81fc1bbd1a0e1bdc50320d02a6ef111c_21.png)

## 查看与分析数据

![](img/81fc1bbd1a0e1bdc50320d02a6ef111c_23.png)

执行查询后，我们获得了数据。需要注意的是，返回的数据结构可能是多维的。

![](img/81fc1bbd1a0e1bdc50320d02a6ef111c_25.png)

```python
# 查看数据的形状和内容
print(factor_data.shape)
# 例如输出：(1, 1, 3000)，前两维可能为占位，最后一维是股票数量

![](img/81fc1bbd1a0e1bdc50320d02a6ef111c_27.png)

# 查看前几条具体数据
print(factor_data.iloc[0, 0, :5])
# 输出格式可能为：股票代码 -> 市盈率数值
```

![](img/81fc1bbd1a0e1bdc50320d02a6ef111c_29.png)

目前，我们完成了获取**指定单日**因子数据的操作。数据显示了在特定日期（如2019年1月2日），股票池中每只股票对应的市盈率指标。

![](img/81fc1bbd1a0e1bdc50320d02a6ef111c_31.png)

## 总结

![](img/81fc1bbd1a0e1bdc50320d02a6ef111c_33.png)

![](img/81fc1bbd1a0e1bdc50320d02a6ef111c_35.png)

本节课中我们一起学习了获取因子指标数据的基本流程：

![](img/81fc1bbd1a0e1bdc50320d02a6ef111c_37.png)

1.  **导入工具包**：引入数据分析所需的模块。
2.  **选择日期**：定义需要分析的时间段并获取对应的交易日列表。
3.  **构建查询**：
    *   确定要分析的股票池。
    *   使用`query`语句指定目标因子（如市盈率）并设置过滤条件。
4.  **执行与查看**：传入日期执行查询，并初步查看返回数据的结构和内容。

![](img/81fc1bbd1a0e1bdc50320d02a6ef111c_39.png)

![](img/81fc1bbd1a0e1bdc50320d02a6ef111c_41.png)

我们已经掌握了获取单日因子数据的方法。接下来的任务是将此操作扩展到整个时间段，获取每一天的因子数据，为后续的因子分析和选股策略打下基础。