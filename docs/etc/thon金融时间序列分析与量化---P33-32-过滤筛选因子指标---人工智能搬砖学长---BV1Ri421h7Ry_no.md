# Python金融时间序列分析与量化交易实战教程：P33：32.过滤筛选因子指标数据 📊

![](img/c1736a0fcaffeefae6429ef495996b70_0.png)

![](img/c1736a0fcaffeefae6429ef495996b70_2.png)

![](img/c1736a0fcaffeefae6429ef495996b70_4.png)

![](img/c1736a0fcaffeefae6429ef495996b70_6.png)

![](img/c1736a0fcaffeefae6429ef495996b70_8.png)

在本节课中，我们将学习如何对股票池进行过滤和筛选，并获取我们所需的因子指标数据。这是构建量化交易策略中非常关键的一步，目的是剔除无效或不符合策略要求的股票，并提取有效的财务指标进行分析。

![](img/c1736a0fcaffeefae6429ef495996b70_10.png)

![](img/c1736a0fcaffeefae6429ef495996b70_12.png)

![](img/c1736a0fcaffeefae6429ef495996b70_14.png)

## 过滤不想要的股票 🚫

![](img/c1736a0fcaffeefae6429ef495996b70_16.png)

![](img/c1736a0fcaffeefae6429ef495996b70_18.png)

上一节我们介绍了量化策略的基本框架，本节中我们来看看如何对初始股票池进行清洗。在量化交易中，并非所有股票都适合纳入策略。我们需要过滤掉一些不符合条件的股票，例如停牌股、ST股和刚上市的新股。

![](img/c1736a0fcaffeefae6429ef495996b70_20.png)

![](img/c1736a0fcaffeefae6429ef495996b70_22.png)

![](img/c1736a0fcaffeefae6429ef495996b70_24.png)

![](img/c1736a0fcaffeefae6429ef495996b70_26.png)

以下是实现股票过滤的三个核心函数：

![](img/c1736a0fcaffeefae6429ef495996b70_28.png)

![](img/c1736a0fcaffeefae6429ef495996b70_30.png)

![](img/c1736a0fcaffeefae6429ef495996b70_32.png)

1.  **判断是否停牌**：使用API判断股票是否全天停牌，停牌的股票不纳入考虑。
    ```python
    def filter_suspended(stock_list):
        filtered_list = []
        for stock in stock_list:
            if not is_suspended(stock):  # 假设 is_suspended 是判断停牌的API
                filtered_list.append(stock)
        return filtered_list
    ```

![](img/c1736a0fcaffeefae6429ef495996b70_34.png)

![](img/c1736a0fcaffeefae6429ef495996b70_36.png)

![](img/c1736a0fcaffeefae6429ef495996b70_38.png)

![](img/c1736a0fcaffeefae6429ef495996b70_40.png)

2.  **判断是否为ST股**：识别带有ST（特别处理）标识的股票，这类股票通常风险较高。
    ```python
    def filter_st(stock_list):
        filtered_list = []
        for stock in stock_list:
            if not is_st(stock):  # 假设 is_st 是判断ST股的API
                filtered_list.append(stock)
        return filtered_list
    ```

![](img/c1736a0fcaffeefae6429ef495996b70_42.png)

![](img/c1736a0fcaffeefae6429ef495996b70_44.png)

![](img/c1736a0fcaffeefae6429ef495996b70_46.png)

3.  **判断是否为新股**：筛选掉上市时间过短的股票，例如上市天数少于180天，以保证数据有足够的长度进行分析。
    ```python
    def filter_new(stock_list):
        filtered_list = []
        for stock in stock_list:
            if days_from_listed(stock) > 180:  # 假设 days_from_listed 是获取上市天数的函数
                filtered_list.append(stock)
        return filtered_list
    ```

![](img/c1736a0fcaffeefae6429ef495996b70_48.png)

![](img/c1736a0fcaffeefae6429ef495996b70_50.png)

![](img/c1736a0fcaffeefae6429ef495996b70_52.png)

![](img/c1736a0fcaffeefae6429ef495996b70_54.png)

## 执行多级过滤 🔄

![](img/c1736a0fcaffeefae6429ef495996b70_56.png)

![](img/c1736a0fcaffeefae6429ef495996b70_58.png)

定义了过滤函数后，我们需要将它们串联起来，对初始股票池进行逐层筛选。

![](img/c1736a0fcaffeefae6429ef495996b70_60.png)

![](img/c1736a0fcaffeefae6429ef495996b70_62.png)

![](img/c1736a0fcaffeefae6429ef495996b70_64.png)

以下是执行过滤的步骤：

![](img/c1736a0fcaffeefae6429ef495996b70_66.png)

![](img/c1736a0fcaffeefae6429ef495996b70_68.png)

![](img/c1736a0fcaffeefae6429ef495996b70_70.png)

![](img/c1736a0fcaffeefae6429ef495996b70_72.png)

1.  首先，传入初始股票列表，过滤掉所有停牌的股票。
2.  接着，对上一步的结果再次过滤，移除所有ST股。
3.  最后，过滤掉上市时间不足180天的新股。

![](img/c1736a0fcaffeefae6429ef495996b70_74.png)

![](img/c1736a0fcaffeefae6429ef495996b70_76.png)

![](img/c1736a0fcaffeefae6429ef495996b70_78.png)

![](img/c1736a0fcaffeefae6429ef495996b70_80.png)

经过这三步操作，我们就得到了一个相对干净、符合基本交易条件的股票池。

![](img/c1736a0fcaffeefae6429ef495996b70_82.png)

![](img/c1736a0fcaffeefae6429ef495996b70_84.png)

![](img/c1736a0fcaffeefae6429ef495996b70_86.png)

![](img/c1736a0fcaffeefae6429ef495996b70_88.png)

## 查询所需的因子指标 📈

![](img/c1736a0fcaffeefae6429ef495996b70_90.png)

![](img/c1736a0fcaffeefae6429ef495996b70_92.png)

![](img/c1736a0fcaffeefae6429ef495996b70_94.png)

在获得过滤后的股票池后，下一步是获取我们策略分析所需的财务指标。本节课的策略示例需要用到市净率（PB）和市值（Market Cap）这两个因子。

![](img/c1736a0fcaffeefae6429ef495996b70_96.png)

![](img/c1736a0fcaffeefae6429ef495996b70_98.png)

![](img/c1736a0fcaffeefae6429ef495996b70_100.png)

以下是查询指标数据的代码实现：

![](img/c1736a0fcaffeefae6429ef495996b70_102.png)

![](img/c1736a0fcaffeefae6429ef495996b70_104.png)

![](img/c1736a0fcaffeefae6429ef495996b70_106.png)

![](img/c1736a0fcaffeefae6429ef495996b70_108.png)

```python
# 假设 filtered_stocks 是经过上述过滤后的股票代码列表
# 使用 get_fundamentals API 查询指标
query = query(
    fundamentals.eod_derivative_indicator.pb_ratio,  # 市净率
    fundamentals.eod_derivative_indicator.market_cap  # 总市值
).filter(
    fundamentals.income_statement.stockcode.in_(filtered_stocks)  # 限定查询范围为我们过滤后的股票
)

![](img/c1736a0fcaffeefae6429ef495996b70_110.png)

![](img/c1736a0fcaffeefae6429ef495996b70_112.png)

# 执行查询
factor_data = get_fundamentals(query)

![](img/c1736a0fcaffeefae6429ef495996b70_114.png)

![](img/c1736a0fcaffeefae6429ef495996b70_116.png)

![](img/c1736a0fcaffeefae6429ef495996b70_118.png)

![](img/c1736a0fcaffeefae6429ef495996b70_120.png)

# 对查询结果进行转置，使每一行代表一只股票，每一列代表一个指标
# 同时可以处理可能存在的空值
factor_data = factor_data.T.dropna()
```

![](img/c1736a0fcaffeefae6429ef495996b70_122.png)

![](img/c1736a0fcaffeefae6429ef495996b70_124.png)

![](img/c1736a0fcaffeefae6429ef495996b70_126.png)

![](img/c1736a0fcaffeefae6429ef495996b70_128.png)

这段代码的核心是构建一个查询语句，指定需要获取的指标（`pb_ratio` 和 `market_cap`），并通过 `filter` 方法将查询范围限定在我们过滤后的股票池中。查询返回的数据格式可能需要进行转置（`.T`）和空值处理（`.dropna()`），以方便后续的数据分析和建模。

![](img/c1736a0fcaffeefae6429ef495996b70_130.png)

![](img/c1736a0fcaffeefae6429ef495996b70_132.png)

## 总结 📝

![](img/c1736a0fcaffeefae6429ef495996b70_134.png)

![](img/c1736a0fcaffeefae6429ef495996b70_136.png)

![](img/c1736a0fcaffeefae6429ef495996b70_138.png)

![](img/c1736a0fcaffeefae6429ef495996b70_140.png)

本节课中我们一起学习了量化策略中数据准备阶段的两个关键操作。

![](img/c1736a0fcaffeefae6429ef495996b70_142.png)

![](img/c1736a0fcaffeefae6429ef495996b70_144.png)

![](img/c1736a0fcaffeefae6429ef495996b70_146.png)

![](img/c1736a0fcaffeefae6429ef495996b70_148.png)

首先，我们编写了三个过滤函数，用于从股票池中剔除**停牌股**、**ST股**和**新股**，确保策略分析的样本质量。

![](img/c1736a0fcaffeefae6429ef495996b70_150.png)

![](img/c1736a0fcaffeefae6429ef495996b70_152.png)

![](img/c1736a0fcaffeefae6429ef495996b70_154.png)

其次，我们使用 `get_fundamentals` 接口，通过构建查询语句，获取了过滤后股票池的**市净率**和**市值**这两个关键因子指标数据，并对数据格式进行了初步整理。

![](img/c1736a0fcaffeefae6429ef495996b70_156.png)

![](img/c1736a0fcaffeefae6429ef495996b70_158.png)

![](img/c1736a0fcaffeefae6429ef495996b70_160.png)

通过本课的学习，你已经掌握了清洗股票池和获取特定因子数据的基本方法，这是构建任何量化交易策略的坚实基础。在接下来的课程中，我们将利用这些处理好的数据，进行更深入的因子分析和策略构建。