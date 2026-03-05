# 量化金融分析师.AQF：P17：实盘交易模拟_基于优矿平台的面向对象策略 05.优矿框架_其他重要操作 📊

![](img/3c63b4373cdd3a9190354e760ecac767_0.png)

![](img/3c63b4373cdd3a9190354e760ecac767_1.png)

![](img/3c63b4373cdd3a9190354e760ecac767_3.png)

在本节课中，我们将学习优矿平台中除核心API外的一些重要操作。这些内容包括强大的因子库调用、信号框架以及如何在构建投资组合时直接应用因子进行筛选。掌握这些内容，将帮助你更灵活地获取和处理数据，为构建复杂策略打下基础。

![](img/3c63b4373cdd3a9190354e760ecac767_5.png)

![](img/3c63b4373cdd3a9190354e760ecac767_7.png)

![](img/3c63b4373cdd3a9190354e760ecac767_9.png)

---

## 强大的因子库 📈

上一节我们介绍了优矿策略的核心API。本节中，我们来看看优矿平台提供的一个强大功能：因子库。

优矿平台内置了丰富的因子数据，这是本课程选择在此平台教学的重要原因之一。免费版提供数百个因子，专业版则更多。这些因子数据已经过预处理，用户可以直接调用，无需自行寻找和处理，这极大地简化了多因子策略的开发流程。

以下是访问因子数据的主要位置和方式：

*   **位置**：在平台左侧导航栏的“研究数据” -> “沪深股票” -> “因子”目录下。
*   **主要接口**：有两个常用接口，`DataAPI.MktStockFactorsOneDayGet` 和带 `Pro` 后缀的专业版接口。对于免费用户，若在社区代码中看到 `ProGet`，通常删除 `Pro` 后即可使用免费接口。
*   **核心参数**：
    *   `tradeDate`：指定获取因子数据的交易日。
    *   `secID` 与 `ticker`：均代表股票代码。`secID` 通常包含交易所后缀（如 `.XSHG`），与 `universe` 中的格式一致，因此更常用。
    *   `field`：指定需要获取的因子名称，例如 `PE`（市盈率）、`LCAP`（对数市值）等。
    *   `pandas`：设置为 `1` 时，返回 `pandas DataFrame` 格式的数据，便于后续分析。

一个典型的调用示例如下：
```python
# 获取指定交易日、指定股票池的市盈率因子数据
factor_data = DataAPI.MktStockFactorsOneDayGet(tradeDate=“2017-09-13”， secID=universe， field=[“PE”， “ticker”]， pandas=1)
```
此代码会返回一个 `DataFrame`，包含股票代码及其对应的市盈率。关于如何具体应用这些因子数据，我们将在后续的策略实例中详细讲解。

![](img/3c63b4373cdd3a9190354e760ecac767_11.png)

---

## 获取因子数据的多种方法 🔍

了解了因子库的存在后，我们来看看在优矿策略中获取因子数据的几种主要方法。每种方法各有特点，适用于不同的场景。

以下是三种常用的因子数据获取方法：

1.  **Signal框架（因子注册法）**：这是优矿特有的信号框架，用于在策略初始化时注册因子，然后在交易逻辑中调用。其优点是集成度高，但用法稍复杂。
    *   **步骤**：在 `initialize` 函数中注册因子，在 `handle_data` 函数中通过 `context.signal_result` 获取数据。
    *   **示例代码结构**：
        ```python
        def initialize(context):
            # 注册市盈率因子
            pe_factor = Signal(‘pe_ratio’)
            context.signal_generator = SignalGenerator(pe_factor)
        def handle_data(context, data):
            # 获取注册的因子数据
            factor_values = context.signal_result
        ```

2.  **`context.get_history` 方法**：这是我们熟悉的方法，除了获取价格、成交量，也能获取一些常用因子。
    *   **特点**：获取的是**盘前数据**，自动避免了未来函数问题。
    *   **示例**：`context.get_history(symbol=universe, time_range=10, attribute=‘PE’)` 可以获取股票池过去10天的市盈率数据。

3.  **`DataAPI` 直接调用法**：即上一节介绍的方法，直接从研究数据接口获取。
    *   **注意**：此接口返回的是**当日**因子值。在实盘或回测中，为了规避未来数据，应使用 `context.previous_date` 作为交易日参数。
    *   **示例**：
        ```python
        trade_date = context.previous_date # 使用前一个交易日
        factor_data = DataAPI.MktStockFactorsOneDayGet(tradeDate=trade_date, secID=universe, field=[‘PE’, ‘ticker’], pandas=1)
        ```

对于初学者，建议先掌握后两种方法。Signal框架在看到社区代码时能理解即可。

---

## 在组合构建中应用因子筛选 🎯

在定义了如何获取因子之后，一个常见的需求是直接根据因子条件来筛选股票，构建初始的投资组合。优矿提供了便捷的类和方法来实现这一点。

我们可以在定义 `universe`（股票池）时，就加入因子筛选条件。这主要用到 `Factor` 类和 `StockScreener` 或 `DynamicUniverse` 的 `apply_filter` 方法。

以下是基于因子筛选股票的核心方法：

*   **`StockScreener` 类**：顾名思义，用于筛选股票。
    *   **示例**：选取市盈率最大的50只股票作为股票池。
        ```python
        universe = StockScreener(Factor.PE.nlargest(50))
        ```
*   **`DynamicUniverse` 的 `apply_filter` 方法**：在现有动态指数成分股（如沪深300）基础上进行二次筛选。
    *   **示例**：从沪深300成分股中，筛选出市值最小的50只股票。
        ```python
        universe = DynamicUniverse(‘HS300’).apply_filter(Factor.LCAP.nsmallest(50))
        ```
*   **`Factor` 类的常用筛选条件**：
    *   `nlargest(N)`：选取因子值最大的N只股票。
    *   `nsmallest(N)`：选取因子值最小的N只股票。
    *   `value_range(lower, upper)`：选取因子值在特定范围内的股票。
    *   `percentile_range(lower, upper)`：选取因子值在特定百分比区间的股票。

通过这种方式，策略的 `universe` 不再是固定的全市场或指数，而是动态的、基于因子表现的股票组合。例如，每月末选取市值最小的50只股票等权投资，就构成了一个简单的单因子策略。

---

## 总结 📝

本节课中我们一起学习了优矿平台的几项重要操作。

![](img/3c63b4373cdd3a9190354e760ecac767_13.png)

首先，我们介绍了优矿强大的**因子库**，它提供了大量预处理好的因子数据，可通过 `DataAPI` 直接调用，是构建多因子策略的利器。其次，我们梳理了获取因子数据的**三种方法**：Signal注册框架、`context.get_history` 接口以及 `DataAPI` 直接调用，你需要理解它们的特点和适用场景。最后，我们学习了如何在构建投资组合的源头——定义 `universe` 时，就使用 `Factor` 类进行**因子筛选**，这能高效地实现基于规则的选股逻辑。

![](img/3c63b4373cdd3a9190354e760ecac767_15.png)

至此，优矿平台的基础概念、核心API以及其他重要操作都已介绍完毕。从下一节课开始，我们将运用所学知识，在优矿上真正动手编写面向对象的量化交易策略。