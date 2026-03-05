# 量化投资基础：P33：2-过滤筛选因子指标数据 📊

![](img/ca9f1415d6f63afa544b8dc248fe08f9_1.png)

![](img/ca9f1415d6f63afa544b8dc248fe08f9_3.png)

在本节课中，我们将学习如何对股票池进行初步筛选，并获取我们所需的财务因子数据。这是构建量化策略中非常关键的一步，目的是剔除无效或不符合策略逻辑的股票，并提取有效的分析指标。

![](img/ca9f1415d6f63afa544b8dc248fe08f9_5.png)

![](img/ca9f1415d6f63afa544b8dc248fe08f9_7.png)

上一节我们介绍了量化策略的基本框架，本节中我们来看看如何具体实现股票数据的过滤与因子查询。

![](img/ca9f1415d6f63afa544b8dc248fe08f9_9.png)

## 过滤不想要的股票 🚫

![](img/ca9f1415d6f63afa544b8dc248fe08f9_11.png)

![](img/ca9f1415d6f63afa544b8dc248fe08f9_13.png)

![](img/ca9f1415d6f63afa544b8dc248fe08f9_15.png)

在构建股票池时，我们首先需要剔除一些不符合交易条件的股票，例如停牌股、ST股和刚上市的新股。以下是实现这一功能的步骤。

![](img/ca9f1415d6f63afa544b8dc248fe08f9_17.png)

![](img/ca9f1415d6f63afa544b8dc248fe08f9_19.png)

我们将编写一个过滤函数，其核心逻辑是遍历传入的股票列表，并使用特定的API判断每只股票是否满足剔除条件。不满足条件的股票将被保留。

![](img/ca9f1415d6f63afa544b8dc248fe08f9_21.png)

![](img/ca9f1415d6f63afa544b8dc248fe08f9_23.png)

![](img/ca9f1415d6f63afa544b8dc248fe08f9_25.png)

以下是具体的过滤条件与实现方法：

![](img/ca9f1415d6f63afa544b8dc248fe08f9_27.png)

![](img/ca9f1415d6f63afa544b8dc248fe08f9_29.png)

![](img/ca9f1415d6f63afa544b8dc248fe08f9_31.png)

1.  **判断股票是否停牌**：使用 `is_suspended` 函数判断股票是否全天停牌。如果是，则剔除。
    ```python
    if is_suspended(stock):
        # 剔除该股票
    ```

![](img/ca9f1415d6f63afa544b8dc248fe08f9_33.png)

2.  **判断股票是否为ST股**：使用 `is_st_stock` 函数判断股票是否为ST或*ST股。如果是，则剔除。
    ```python
    if is_st_stock(stock):
        # 剔除该股票
    ```

![](img/ca9f1415d6f63afa544b8dc248fe08f9_35.png)

![](img/ca9f1415d6f63afa544b8dc248fe08f9_37.png)

![](img/ca9f1415d6f63afa544b8dc248fe08f9_39.png)

![](img/ca9f1415d6f63afa544b8dc248fe08f9_41.png)

3.  **判断股票是否为新股**：通过计算股票上市天数来判断。例如，我们可能希望剔除上市天数少于180天的股票。
    ```python
    if days_from_listed(stock) < 180:
        # 剔除该股票
    ```

![](img/ca9f1415d6f63afa544b8dc248fe08f9_43.png)

通过以上三个步骤的连续过滤，我们可以得到一个相对“干净”的、可用于后续分析的股票池。

![](img/ca9f1415d6f63afa544b8dc248fe08f9_45.png)

## 查询所需的因子指标 🔍

![](img/ca9f1415d6f63afa544b8dc248fe08f9_47.png)

![](img/ca9f1415d6f63afa544b8dc248fe08f9_49.png)

![](img/ca9f1415d6f63afa544b8dc248fe08f9_51.png)

过滤掉不想要的股票后，下一步是从剩余的股票中查询我们策略所需的财务指标。本节我们将以市盈率（PE）和市值（Market Cap）为例。

![](img/ca9f1415d6f63afa544b8dc248fe08f9_53.png)

![](img/ca9f1415d6f63afa544b8dc248fe08f9_55.png)

我们使用 `get_fundamentals` 函数来查询财务数据。该函数需要传入一个查询语句（query），指定要查询的指标和股票范围。

![](img/ca9f1415d6f63afa544b8dc248fe08f9_57.png)

![](img/ca9f1415d6f63afa544b8dc248fe08f9_59.png)

以下是查询因子指标的具体操作：

![](img/ca9f1415d6f63afa544b8dc248fe08f9_61.png)

![](img/ca9f1415d6f63afa544b8dc248fe08f9_63.png)

![](img/ca9f1415d6f63afa544b8dc248fe08f9_65.png)

1.  **构建查询语句**：在query中指定需要查询的指标，例如 `fundamentals.eod_derivative_indicator.pe_ratio`（市盈率）和 `fundamentals.eod_derivative_indicator.market_cap`（市值）。
    ```python
    query = query(fundamentals.eod_derivative_indicator.pe_ratio,
                  fundamentals.eod_derivative_indicator.market_cap)
    ```

![](img/ca9f1415d6f63afa544b8dc248fe08f9_67.png)

![](img/ca9f1415d6f63afa544b8dc248fe08f9_69.png)

2.  **指定股票范围**：在query中使用 `filter` 条件，将查询范围限定在我们过滤后的股票池 `filtered_stocks` 中。
    ```python
    query = query.filter(fundamentals.stockcode.in_(filtered_stocks))
    ```

![](img/ca9f1415d6f63afa544b8dc248fe08f9_71.png)

![](img/ca9f1415d6f63afa544b8dc248fe08f9_73.png)

3.  **执行查询与数据整理**：执行查询后，返回的数据格式通常是指标为行、股票为列。为了便于分析，我们通常需要将其转置，使每一行代表一只股票。同时，可以剔除其中的空值（NaN）。
    ```python
    factor_data = get_fundamentals(query).T.dropna()
    ```

![](img/ca9f1415d6f63afa544b8dc248fe08f9_75.png)

完成这一步后，我们就得到了一个包含目标股票及其对应市盈率和市值的数据表，为后续的因子分析和选股做好了准备。

![](img/ca9f1415d6f63afa544b8dc248fe08f9_77.png)

![](img/ca9f1415d6f63afa544b8dc248fe08f9_79.png)

![](img/ca9f1415d6f63afa544b8dc248fe08f9_81.png)

本节课中我们一起学习了量化策略中数据预处理的两个核心步骤：股票池过滤与因子数据查询。我们首先通过编写过滤函数剔除了停牌股、ST股和新股，然后使用 `get_fundamentals` 函数查询了所需的财务指标（如市盈率和市值），并对数据进行了转置和清理。这些步骤是构建有效量化模型的基础，确保了后续分析所使用数据的质量与相关性。