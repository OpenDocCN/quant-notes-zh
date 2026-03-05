# 从零开始量化：6：商品基本数据 📊

![](img/3b88a483b5c98be6e5450adee5cd8f0f_1.png)

![](img/3b88a483b5c98be6e5450adee5cd8f0f_3.png)

![](img/3b88a483b5c98be6e5450adee5cd8f0f_5.png)

在本节课中，我们将学习如何在MC（MultiCharts）中获取和使用商品的基本数据。这是编写指标和交易策略的基础，因为所有计算都依赖于这些基础信息。

![](img/3b88a483b5c98be6e5450adee5cd8f0f_7.png)

上一节我们介绍了函数、指标和信号的基本概念。本节中，我们来看看如何获取图表中商品的核心信息，例如商品名称、价格、成交量等。

![](img/3b88a483b5c98be6e5450adee5cd8f0f_9.png)

## 获取商品基础信息

![](img/3b88a483b5c98be6e5450adee5cd8f0f_11.png)

![](img/3b88a483b5c98be6e5450adee5cd8f0f_13.png)

要进行量化分析，首先需要获取图表中商品自带的基础数据。这些数据存储在软件的“报价管理器”（Quote Manager，简称QM）中。报价管理器包含了商品合约的基本信息，如交易所、合约名称、保证金、到期日等。当软件需要从服务器下载数据时，会依据这里的合约信息进行交互。

以下是获取商品基本数据的关键字和方法：

*   **商品名称**：使用 `Symbol`、`SymbolName` 或 `SymbolShortName` 关键字。它们通常返回相同的合约代码，例如 `SHFE.AG000`。这在策略中可用于限定交易品种。
    ```pinescript
    Print("Symbol: ", Symbol, " SymbolName: ", SymbolName, " SymbolShortName: ", SymbolShortName);
    ```
*   **商品类型**：使用 `SymbolType` 关键字。它返回一个数值代表商品类型，例如期货通常返回 `0`。
    ```pinescript
    Print("SymbolType: ", SymbolType);
    ```
*   **商品描述**：使用 `Description` 关键字。它返回在报价管理器中“描述”栏的内容，例如“白银”。
    ```pinescript
    Print("Description: ", Description);
    ```
*   **交易所名称**：使用 `GetExchangeName` 关键字获取当前商品的交易所名称，例如 `SHFE`。`ExchangeListed` 关键字也返回类似信息。
    ```pinescript
    Print("Exchange: ", GetExchangeName, " Listed: ", ExchangeListed);
    ```
*   **到期日**：使用 `ExpirationDate` 关键字。它返回脚本所在图表中商品的到期日，该日期在报价管理器中设定。

## 理解价格与价值计算

在量化交易中，准确计算合约的价值和波动至关重要。以下是几个核心概念及其关系：

*   **价格精度倒数**：`PriceScale` 返回价格精度的倒数。例如，价格精度为1（如白银），则 `PriceScale` 为1；价格精度为0.1（如股指期货IF），则 `PriceScale` 为10。
*   **价格精度**：`Point` 和 `Points` 代表一个价格精度单位。例如，IF的 `Point` 为0.1。注意，`Point` 可直接在计算中作为数值使用。
*   **整点价值**：`BigPointValue` 返回图表商品一个整点的价值（元）。例如，螺纹钢一个点价值10元，白银一个点价值15元。
*   **最小变动单位**：`MinMove` 返回价格变动的最小单位（整数）。例如，IF的最小价格变动是0.2点，但 `MinMove` 返回2。
*   **精度单位价值**：`PointValue` 返回一个价格精度单位对应的价值。其计算公式为：
    ```
    PointValue = BigPointValue / PriceScale
    ```
    这等价于 `BigPointValue` 乘以价格精度。

为了更直观地理解，我们可以同时输出这些值进行对比：

```pinescript
Print("PriceScale: ", PriceScale, "  PointValue: ", PointValue, "  BigPointValue: ", BigPointValue, "  MinMove: ", MinMove);
```

**重要提示**：商品的一跳（最小变动价值）需要通过 `MinMove * Point` 来计算，因为 `MinMove` 是整数倍的变动单位。

## 获取K线数据

![](img/3b88a483b5c98be6e5450adee5cd8f0f_15.png)

![](img/3b88a483b5c98be6e5450adee5cd8f0f_17.png)

K线代表一个特定时间周期内的行情信息，包含开盘价、最高价、最低价、收盘价、成交量等。

![](img/3b88a483b5c98be6e5450adee5cd8f0f_19.png)

![](img/3b88a483b5c98be6e5450adee5cd8f0f_21.png)

![](img/3b88a483b5c98be6e5450adee5cd8f0f_23.png)

*   **当前K线编号**：使用 `CurrentBar` 关键字。它返回从图表数据开始（不包含“参考栏”`MaxBarsBack`）计算到当前Bar的编号。例如，设置 `MaxBarsBack` 为10，则 `CurrentBar` 会从第11根K线开始编号为1。
    ```pinescript
    Print("CurrentBar Number: ", CurrentBar);
    ```

## 总结

![](img/3b88a483b5c98be6e5450adee5cd8f0f_25.png)

![](img/3b88a483b5c98be6e5450adee5cd8f0f_27.png)

![](img/3b88a483b5c98be6e5450adee5cd8f0f_29.png)

本节课我们一起学习了在MC中获取商品基本数据的方法。我们首先了解了如何获取商品名称、类型、交易所等基础信息。然后，重点剖析了 `PriceScale`、`Point`、`BigPointValue`、`MinMove` 和 `PointValue` 这几个关键概念，它们用于精确计算合约的价值、波动和仓位，是策略资金管理部分的基石。最后，我们介绍了获取当前K线编号的方法。掌握这些基础数据的获取与应用，是迈向编写有效量化策略的第一步。