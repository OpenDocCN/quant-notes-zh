# Python金融分析与量化交易实战教程：P32：2-过滤筛选因子指标数据 📊

![](img/001e86401f256ec93185b262da9229cc_1.png)

## 概述
在本节课中，我们将学习如何对股票池进行过滤筛选，并获取我们所需的财务指标数据。这是构建量化交易策略中非常关键的一步，目的是剔除无效或不符合条件的股票，并提取用于后续分析的因子数据。

上一节我们介绍了量化交易的基本概念和策略框架，本节中我们来看看如何具体实现数据的预处理。

---

![](img/001e86401f256ec93185b262da9229cc_3.png)

## 过滤不想要的股票 🚫

在构建股票池时，并非所有股票都适合纳入我们的策略。我们需要过滤掉以下几类股票：
*   **停牌股票**：无法交易。
*   **ST/S*ST股票**：存在退市风险或其他特别处理情况。
*   **新上市股票**：上市时间过短，数据不充分，价格波动可能异常。

为了实现这个过滤过程，我们需要编写相应的函数。

![](img/001e86401f256ec93185b262da9229cc_5.png)

以下是过滤函数的实现思路：

![](img/001e86401f256ec93185b262da9229cc_7.png)

![](img/001e86401f256ec93185b262da9229cc_9.png)

1.  **判断是否停牌**：使用API判断股票是否全天停牌。
    ```python
    def filter_suspended(stock_list):
        filtered_list = []
        for stock in stock_list:
            if not api.is_suspended(stock):  # 假设的API调用，判断是否停牌
                filtered_list.append(stock)
        return filtered_list
    ```

![](img/001e86401f256ec93185b262da9229cc_11.png)

2.  **判断是否为ST/S*ST股**：使用API判断股票是否被标记为ST。
    ```python
    def filter_st(stock_list):
        filtered_list = []
        for stock in stock_list:
            if not api.is_st(stock):  # 假设的API调用，判断是否为ST股
                filtered_list.append(stock)
        return filtered_list
    ```

![](img/001e86401f256ec93185b262da9229cc_13.png)

3.  **判断是否为新股**：通过判断股票上市天数是否超过阈值（例如180天）来过滤新股。
    ```python
    def filter_new(stock_list, days=180):
        filtered_list = []
        for stock in stock_list:
            if api.days_from_listed(stock) > days:  # 假设的API调用，获取上市天数
                filtered_list.append(stock)
        return filtered_list
    ```

![](img/001e86401f256ec93185b262da9229cc_15.png)

在编写完这些辅助函数后，我们就可以对初始股票池进行层层过滤了。

![](img/001e86401f256ec93185b262da9229cc_17.png)

---

![](img/001e86401f256ec93185b262da9229cc_19.png)

## 执行过滤操作 ⚙️

![](img/001e86401f256ec93185b262da9229cc_21.png)

现在，我们将上述函数应用到我们的股票列表中。假设我们有一个包含所有候选股票代码的列表 `all_stocks`。

![](img/001e86401f256ec93185b262da9229cc_23.png)

以下是执行过滤的步骤：

![](img/001e86401f256ec93185b262da9229cc_25.png)

![](img/001e86401f256ec93185b262da9229cc_27.png)

```python
# 初始股票池
all_stocks = ['000001.XSHE', '600000.XSHG', ...] # 示例股票代码

![](img/001e86401f256ec93185b262da9229cc_29.png)

# 第一步：过滤停牌股票
filtered_stocks = filter_suspended(all_stocks)

# 第二步：过滤ST/S*ST股票
filtered_stocks = filter_st(filtered_stocks)

![](img/001e86401f256ec93185b262da9229cc_31.png)

# 第三步：过滤新上市股票（假设要求上市超过180天）
filtered_stocks = filter_new(filtered_stocks, days=180)

![](img/001e86401f256ec93185b262da9229cc_33.png)

![](img/001e86401f256ec93185b262da9229cc_35.png)

# 此时，filtered_stocks 中就是经过三重过滤后的、符合我们基本要求的股票池
```
通过以上步骤，我们得到了一个相对干净、可用于后续因子分析的股票池。

![](img/001e86401f256ec93185b262da9229cc_37.png)

---

![](img/001e86401f256ec93185b262da9229cc_39.png)

## 查询所需的因子指标 🔍

过滤掉不想要的股票后，下一步是获取这些股票的具体财务指标数据。在本例的策略中，我们关注**市净率（PB）**和**市值（Market Cap）**这两个因子。

![](img/001e86401f256ec93185b262da9229cc_41.png)

我们使用 `get_fundamentals` API来查询数据。这个函数通常接受一个查询语句（query），用于指定要获取的指标和过滤条件。

以下是查询指标数据的代码：

![](img/001e86401f256ec93185b262da9229cc_43.png)

```python
# 构建查询语句，获取市净率(pb_ratio)和市值(market_cap)
query = query(
    fundamentals.eod_derivative_indicator.pb_ratio,  # 市净率
    fundamentals.eod_derivative_indicator.market_cap  # 总市值
).filter(
    fundamentals.income_statement.stockcode.in_(filtered_stocks) # 只查询我们过滤后的股票
)

![](img/001e86401f256ec93185b262da9229cc_45.png)

# 执行查询
fundamental_data = get_fundamentals(query)

![](img/001e86401f256ec93185b262da9229cc_47.png)

![](img/001e86401f256ec93185b262da9229cc_49.png)

# 查看数据形状
print(fundamental_data.shape)
```
查询返回的数据 `fundamental_data` 通常是一个 `DataFrame`，其索引（行）可能是时间或指标，列是股票代码。但这不一定符合我们后续计算的需求。

---

![](img/001e86401f256ec93185b262da9229cc_51.png)

## 数据转换与整理 📐

![](img/001e86401f256ec93185b262da9229cc_53.png)

直接从API获取的数据格式可能不适合进行向量化计算。通常，我们需要将其转换为更规整的格式，例如，行代表股票，列代表指标。

![](img/001e86401f256ec93185b262da9229cc_55.png)

此外，数据中可能存在缺失值（NaN），我们需要对其进行处理。

![](img/001e86401f256ec93185b262da9229cc_57.png)

以下是数据整理的关键步骤：

![](img/001e86401f256ec93185b262da9229cc_59.png)

1.  **转置数据**：将数据转换为以股票为行、指标为列的格式。
2.  **处理缺失值**：一个简单的方法是直接删除包含缺失值的行。

![](img/001e86401f256ec93185b262da9229cc_61.png)

```python
# 1. 转置数据，使每行代表一只股票，每列代表一个指标
# 假设 fundamental_data 需要转置才能得到股票在行的格式
df = fundamental_data.T  # 转置操作

# 2. 处理缺失值：删除任何包含NaN的行
df_cleaned = df.dropna()

![](img/001e86401f256ec93185b262da9229cc_63.png)

# 此时，df_cleaned 就是一个干净、规整的数据框，可以直接用于后续的因子分析和选股
```
经过转置和清理，我们得到了一个结构清晰的数据集，其中包含了经过筛选的股票的市净率和市值信息。

![](img/001e86401f256ec93185b262da9229cc_65.png)

---

## 总结 🎯

![](img/001e86401f256ec93185b262da9229cc_67.png)

本节课中我们一起学习了量化交易策略中数据预处理的核心环节：

![](img/001e86401f256ec93185b262da9229cc_69.png)

1.  **股票过滤**：我们编写了三个函数，分别用于过滤停牌股、ST/S*ST股和新上市股，确保股票池的质量。
2.  **指标查询**：使用 `get_fundamentals` API，通过构建查询语句，获取了目标股票的关键财务指标（市净率和市值）。
3.  **数据整理**：对查询结果进行转置和缺失值处理，将其转换为适合后续数学建模和数据分析的规整格式。

![](img/001e86401f256ec93185b262da9229cc_71.png)

至此，我们已经完成了从原始股票列表到干净因子数据集的准备工作。下一节，我们将基于这些处理好的数据，开始构建具体的选股和排序逻辑。