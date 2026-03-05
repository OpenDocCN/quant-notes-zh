# Python金融量化分析：P42：获取因子指标数据 📊

![](img/ccab8c18cc5758f22514d5aa0fd1abd5_1.png)

在本节课中，我们将学习如何使用量化分析工具包，获取指定时间段内所有股票的特定因子指标数据。我们将从导入工具包开始，逐步完成日期选择、股票池构建、查询语句编写以及数据获取的全过程。

## 导入工具包

上一节我们介绍了课程的整体框架，本节中我们来看看具体实现所需的核心工具。首先，我们需要导入必要的分析模块。

![](img/ccab8c18cc5758f22514d5aa0fd1abd5_3.png)

```python
# 导入数据处理、绘图和分析模块
from alphalearns import UTUS, PROTE, 其他分析模块1, 其他分析模块2
```

*   **UTUS**：用于后续的数据处理。
*   **PROTE**：用于后续的绘图。
*   **其他分析模块**：用于后续的因子分析。

## 选择日期范围

![](img/ccab8c18cc5758f22514d5aa0fd1abd5_5.png)

与回测框架不同，当前模块没有自动按日执行的机制。因此，我们需要手动获取并处理多日数据。第一步是确定我们要分析的时间段。

以下是获取交易日期的步骤：

![](img/ccab8c18cc5758f22514d5aa0fd1abd5_7.png)

1.  定义一个函数 `get_trading_date`，它接受开始日期和结束日期两个参数。
2.  函数内部使用工具包功能，获取该时间段内所有的交易日列表。
3.  返回这个交易日列表。

![](img/ccab8c18cc5758f22514d5aa0fd1abd5_9.png)

```python
def get_trading_date(start_date, end_date):
    """
    获取指定时间段内的所有交易日。
    参数:
        start_date (str): 开始日期，格式如 '2019-01-01'
        end_date (str): 结束日期，格式如 '2020-01-01'
    返回:
        trading_dates (list): 交易日列表
    """
    # 调用相关API获取交易日列表
    trading_dates = ... # 具体获取逻辑
    return trading_dates

# 示例：获取2019年全年的交易日
trading_dates = get_trading_date('2019-01-01', '2020-01-01')
```

执行此步骤后，我们就得到了一个包含所有目标交易日的列表 `trading_dates`，为后续按日查询数据做好准备。

![](img/ccab8c18cc5758f22514d5aa0fd1abd5_11.png)

## 构建股票池

![](img/ccab8c18cc5758f22514d5aa0fd1abd5_13.png)

有了日期范围，接下来我们需要确定分析哪些股票。在本例中，我们选择获取A股市场所有的股票代码，构建一个完整的股票池。

![](img/ccab8c18cc5758f22514d5aa0fd1abd5_15.png)

```python
# 获取所有A股股票代码，构建股票池
stock_pool = get_all_A_shares() # 假设的获取函数
```

变量 `stock_pool` 现在包含了我们要分析的所有股票代码。

![](img/ccab8c18cc5758f22514d5aa0fd1abd5_17.png)

## 编写查询语句

![](img/ccab8c18cc5758f22514d5aa0fd1abd5_19.png)

![](img/ccab8c18cc5758f22514d5aa0fd1abd5_21.png)

现在，我们要查询具体的因子指标。我们以 `p_retro` 因子为例。我们需要编写一个查询语句，指定要获取的因子字段，并过滤出属于我们股票池的股票。

查询语句通常较长，我们可以参考工具的API文档来构建。核心是使用 `query` 和 `filter` 方法。

![](img/ccab8c18cc5758f22514d5aa0fd1abd5_23.png)

```python
# 构建查询语句
q = query(
    # 指定要查询的因子字段，例如 p_retro
    factor_field_1,
    factor_field_2,
    # ... 其他字段
).filter(
    # 过滤条件：股票代码在指定的股票池中
    factor_table.code.in_(stock_pool)
)
```

![](img/ccab8c18cc5758f22514d5aa0fd1abd5_25.png)

这段代码创建了一个查询对象 `q`，它定义了我们要获取 `p_retro` 等因子数据，并且只针对 `stock_pool` 中的股票。

![](img/ccab8c18cc5758f22514d5aa0fd1abd5_27.png)

## 执行查询并获取数据

![](img/ccab8c18cc5758f22514d5aa0fd1abd5_29.png)

查询语句构建好后，我们需要执行它来获取数据。这里的关键是，我们需要为查询指定一个具体的日期。

![](img/ccab8c18cc5758f22514d5aa0fd1abd5_31.png)

以下是执行查询的步骤：

![](img/ccab8c18cc5758f22514d5aa0fd1abd5_33.png)

1.  从之前获取的交易日列表 `trading_dates` 中选取一个日期（例如第一天）。
2.  调用数据获取函数（如 `get_fundamentals`），传入查询对象 `q` 和指定的日期。
3.  函数返回该日期所有目标股票的因子数据。

![](img/ccab8c18cc5758f22514d5aa0fd1abd5_35.png)

```python
# 获取第一个交易日的数据进行测试
test_date = trading_dates[0]
factor_data = get_fundamentals(q, date=test_date)

![](img/ccab8c18cc5758f22514d5aa0fd1abd5_37.png)

# 查看获取到的数据的前几行
print(factor_data.iloc[0, 0, :5]) # 注意数据可能是三维结构，我们通常关注最后一个维度
```

执行后，`factor_data` 变量中存储了 `test_date` 那一天，股票池中所有股票的 `p_retro` 因子值。数据可能呈现多维结构，但有效数据通常集中在最后一个维度。

![](img/ccab8c18cc5758f22514d5aa0fd1abd5_39.png)

![](img/ccab8c18cc5758f22514d5aa0fd1abd5_41.png)

## 总结与展望

![](img/ccab8c18cc5758f22514d5aa0fd1abd5_43.png)

本节课中我们一起学习了获取因子指标数据的完整流程：

![](img/ccab8c18cc5758f22514d5aa0fd1abd5_45.png)

![](img/ccab8c18cc5758f22514d5aa0fd1abd5_47.png)

1.  **选择日期范围**：定义函数获取特定时间段内的交易日列表。
2.  **构建股票池**：确定需要分析的目标股票集合。
3.  **编写查询语句**：使用 `query` 和 `filter` 构建获取特定因子的查询条件。
4.  **执行查询**：针对单个日期，执行查询并获取因子数据。

![](img/ccab8c18cc5758f22514d5aa0fd1abd5_49.png)

目前，我们成功获取了**单日**的因子数据。下一节，我们将在此基础上，编写循环逻辑，遍历整个 `trading_dates` 列表，从而获取**多日、全时间段**的因子数据，为后续的因子分析和策略构建打下基础。