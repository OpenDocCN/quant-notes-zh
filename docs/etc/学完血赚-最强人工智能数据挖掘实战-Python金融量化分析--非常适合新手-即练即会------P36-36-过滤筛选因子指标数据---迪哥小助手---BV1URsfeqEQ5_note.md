# 人工智能数据挖掘实战：P36：过滤筛选因子指标数据 📊

![](img/f73f53e00847e60a3c882adece8850d5_0.png)

![](img/f73f53e00847e60a3c882adece8850d5_2.png)

![](img/f73f53e00847e60a3c882adece8850d5_3.png)

![](img/f73f53e00847e60a3c882adece8850d5_5.png)

![](img/f73f53e00847e60a3c882adece8850d5_6.png)

在本节课中，我们将学习如何对股票池进行过滤筛选，并获取所需的财务指标数据。这是构建量化交易策略中非常关键的一步，目的是剔除无效或不符合策略要求的股票，并提取用于分析的因子数据。

![](img/f73f53e00847e60a3c882adece8850d5_7.png)

![](img/f73f53e00847e60a3c882adece8850d5_9.png)

![](img/f73f53e00847e60a3c882adece8850d5_10.png)

上一节我们介绍了如何获取初始的股票列表，本节中我们来看看如何对这些股票进行清洗和筛选。

![](img/f73f53e00847e60a3c882adece8850d5_11.png)

![](img/f73f53e00847e60a3c882adece8850d5_13.png)

## 过滤不想要的股票 🚫

![](img/f73f53e00847e60a3c882adece8850d5_14.png)

![](img/f73f53e00847e60a3c882adece8850d5_15.png)

![](img/f73f53e00847e60a3c882adece8850d5_16.png)

在量化分析中，并非所有股票都适合纳入策略。我们需要过滤掉以下几类股票：
*   **停牌股票**：无法交易。
*   **ST股**：存在退市风险或其他特殊状况。
*   **新股**：上市时间过短，数据可能不稳定。

![](img/f73f53e00847e60a3c882adece8850d5_18.png)

![](img/f73f53e00847e60a3c882adece8850d5_19.png)

![](img/f73f53e00847e60a3c882adece8850d5_20.png)

![](img/f73f53e00847e60a3c882adece8850d5_21.png)

![](img/f73f53e00847e60a3c882adece8850d5_22.png)

以下是实现过滤功能的函数：

![](img/f73f53e00847e60a3c882adece8850d5_24.png)

![](img/f73f53e00847e60a3c882adece8850d5_25.png)

![](img/f73f53e00847e60a3c882adece8850d5_26.png)

```python
def filter_suspended(stock_list):
    """
    过滤掉全年停牌的股票。
    """
    filtered_list = []
    for stock in stock_list:
        if not is_suspended(stock):  # 假设 is_suspended 是判断停牌的API
            filtered_list.append(stock)
    return filtered_list

![](img/f73f53e00847e60a3c882adece8850d5_28.png)

![](img/f73f53e00847e60a3c882adece8850d5_29.png)

![](img/f73f53e00847e60a3c882adece8850d5_30.png)

def filter_st(stock_list):
    """
    过滤掉ST股。
    """
    filtered_list = []
    for stock in stock_list:
        if not is_st_stock(stock):  # 假设 is_st_stock 是判断ST股的API
            filtered_list.append(stock)
    return filtered_list

![](img/f73f53e00847e60a3c882adece8850d5_32.png)

![](img/f73f53e00847e60a3c882adece8850d5_33.png)

![](img/f73f53e00847e60a3c882adece8850d5_34.png)

def filter_new(stock_list, min_days=180):
    """
    过滤掉上市时间过短的新股。
    """
    filtered_list = []
    for stock in stock_list:
        if get_list_days(stock) > min_days:  # 假设 get_list_days 是获取上市天数的API
            filtered_list.append(stock)
    return filtered_list
```

![](img/f73f53e00847e60a3c882adece8850d5_36.png)

![](img/f73f53e00847e60a3c882adece8850d5_37.png)

![](img/f73f53e00847e60a3c882adece8850d5_38.png)

![](img/f73f53e00847e60a3c882adece8850d5_39.png)

![](img/f73f53e00847e60a3c882adece8850d5_40.png)

现在，我们可以使用这些函数对初始股票池进行逐步过滤。

![](img/f73f53e00847e60a3c882adece8850d5_42.png)

![](img/f73f53e00847e60a3c882adece8850d5_44.png)

```python
# 假设 all_stocks 是初始股票列表
filtered_stocks = filter_suspended(all_stocks)
filtered_stocks = filter_st(filtered_stocks)
filtered_stocks = filter_new(filtered_stocks)
```

![](img/f73f53e00847e60a3c882adece8850d5_46.png)

![](img/f73f53e00847e60a3c882adece8850d5_47.png)

![](img/f73f53e00847e60a3c882adece8850d5_48.png)

![](img/f73f53e00847e60a3c882adece8850d5_50.png)

![](img/f73f53e00847e60a3c882adece8850d5_52.png)

经过以上步骤，`filtered_stocks` 中剩下的就是经过初步筛选、相对“干净”的股票池。

![](img/f73f53e00847e60a3c882adece8850d5_54.png)

## 查询所需的财务指标 📈

![](img/f73f53e00847e60a3c882adece8850d5_56.png)

![](img/f73f53e00847e60a3c882adece8850d5_57.png)

![](img/f73f53e00847e60a3c882adece8850d5_58.png)

![](img/f73f53e00847e60a3c882adece8850d5_59.png)

![](img/f73f53e00847e60a3c882adece8850d5_60.png)

过滤掉不合适的股票后，下一步是获取我们策略分析所需的财务指标。例如，我们可能需要市盈率（PE Ratio）和市值（Market Cap）这两个因子。

![](img/f73f53e00847e60a3c882adece8850d5_61.png)

![](img/f73f53e00847e60a3c882adece8850d5_62.png)

![](img/f73f53e00847e60a3c882adece8850d5_64.png)

以下是查询指标数据的代码：

![](img/f73f53e00847e60a3c882adece8850d5_65.png)

![](img/f73f53e00847e60a3c882adece8850d5_66.png)

![](img/f73f53e00847e60a3c882adece8850d5_68.png)

```python
# 使用 get_fundamentals API 查询指标
query_object = query(
    fundamentals.eod_derivative_indicator.pe_ratio,  # 市盈率
    fundamentals.eod_derivative_indicator.market_cap   # 总市值
).filter(
    fundamentals.income_statement.stockcode.in_(filtered_stocks)  # 只查询过滤后的股票
)

![](img/f73f53e00847e60a3c882adece8850d5_69.png)

![](img/f73f53e00847e60a3c882adece8850d5_70.png)

# 执行查询，获取数据
factor_data = get_fundamentals(query_object)
```

![](img/f73f53e00847e60a3c882adece8850d5_72.png)

![](img/f73f53e00847e60a3c882adece8850d5_73.png)

![](img/f73f53e00847e60a3c882adece8850d5_74.png)

![](img/f73f53e00847e60a3c882adece8850d5_75.png)

![](img/f73f53e00847e60a3c882adece8850d5_76.png)

查询返回的数据 `factor_data` 通常是一个 `DataFrame`，其结构可能如下（示意）：

![](img/f73f53e00847e60a3c882adece8850d5_78.png)

![](img/f73f53e00847e60a3c882adece8850d5_79.png)

![](img/f73f53e00847e60a3c882adece8850d5_81.png)

| 股票代码 | pe_ratio | market_cap |
| :--- | :--- | :--- |
| 000001 | 10.5 | 1.2e11 |
| 000002 | 15.3 | 2.5e11 |
| ... | ... | ... |

![](img/f73f53e00847e60a3c882adece8850d5_83.png)

![](img/f73f53e00847e60a3c882adece8850d5_84.png)

然而，有时API返回的数据可能是**转置**的格式（指标为行，股票为列），这不符合我们的分析习惯。此外，数据中可能存在缺失值（NaN）。

![](img/f73f53e00847e60a3c882adece8850d5_86.png)

![](img/f73f53e00847e60a3c882adece8850d5_87.png)

![](img/f73f53e00847e60a3c882adece8850d5_88.png)

![](img/f73f53e00847e60a3c882adece8850d5_89.png)

因此，我们需要对数据进行后处理：

![](img/f73f53e00847e60a3c882adece8850d5_91.png)

![](img/f73f53e00847e60a3c882adece8850d5_92.png)

![](img/f73f53e00847e60a3c882adece8850d5_93.png)

```python
# 1. 转置数据（如果需要）
if factor_data.shape[0] < factor_data.shape[1]:  # 简单判断是否需要转置
    factor_data = factor_data.T

![](img/f73f53e00847e60a3c882adece8850d5_95.png)

![](img/f73f53e00847e60a3c882adece8850d5_96.png)

![](img/f73f53e00847e60a3c882adece8850d5_98.png)

# 2. 处理缺失值：这里选择直接删除包含缺失值的行
factor_data_clean = factor_data.dropna()

![](img/f73f53e00847e60a3c882adece8850d5_99.png)

![](img/f73f53e00847e60a3c882adece8850d5_100.png)

![](img/f73f53e00847e60a3c882adece8850d5_101.png)

# 现在 factor_data_clean 就是一份干净、格式规整的因子数据
```

![](img/f73f53e00847e60a3c882adece8850d5_103.png)

![](img/f73f53e00847e60a3c882adece8850d5_104.png)

![](img/f73f53e00847e60a3c882adece8850d5_105.png)

![](img/f73f53e00847e60a3c882adece8850d5_106.png)

本节课中我们一起学习了量化分析中数据准备的核心步骤。我们首先定义了三个过滤函数，用于剔除**停牌股**、**ST股**和**新股**，从而得到一个高质量的股票池。接着，我们使用 `get_fundamentals` 接口查询了目标股票的**市盈率**和**市值**指标，并对获取的数据进行了**转置**和**缺失值处理**，最终得到了可用于后续因子分析和策略构建的干净数据集。