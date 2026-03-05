# 量化交易入门：第26课：内建出场函数详解 🧮

![](img/111e33bcfdb411373ea5327f2d4b7a44_1.png)

![](img/111e33bcfdb411373ea5327f2d4b7a44_3.png)

![](img/111e33bcfdb411373ea5327f2d4b7a44_4.png)

在本节课中，我们将学习MultiCharts中一个重要的功能——内建出场函数。我们将从理解代码在图表上的执行机制开始，逐步深入到如何使用这些函数来简化我们的交易策略，并管理风险。

## 代码执行机制与变量更新 🔄

![](img/111e33bcfdb411373ea5327f2d4b7a44_6.png)

![](img/111e33bcfdb411373ea5327f2d4b7a44_8.png)

上一节我们介绍了策略的基本结构，本节中我们来看看代码在图表上的执行细节，特别是当启用特定设置时变量的更新方式。

首先，我们通过一个简单的演示来理解变量是如何更新的。定义两个变量：`VA20` 使用 `IntraBarPersist` 关键字声明，而 `VA21` 则使用普通方式声明。

![](img/111e33bcfdb411373ea5327f2d4b7a44_10.png)

![](img/111e33bcfdb411373ea5327f2d4b7a44_12.png)

```easylanguage
// 定义变量
var: VA20(0), VA21(0);
VA20 = VA20 + 1;
VA21 = VA21 + 1;
Print(“VA20: “, VA20, “ VA21: “, VA21);
```

当代码在图表上运行时，如果未启用 `IntraBarOrderGeneration`（图表属性 -> 属性 -> 常规），`VA20` 和 `VA21` 的行为是一致的，它们都只在每根K线结束时更新一次。

![](img/111e33bcfdb411373ea5327f2d4b7a44_14.png)

![](img/111e33bcfdb411373ea5327f2d4b7a44_16.png)

然而，当我们同时启用 `IntraBarOrderGeneration` 时，情况发生了变化。`IntraBarPersist` 定义的变量 `VA20` 会在每一个即时报价（Tick）到来时都更新并保存其值。而普通变量 `VA21` 虽然每次Tick都会执行 `VA21 = VA21 + 1` 这行代码，但其值不会被保存到下一Tick，因此看起来始终停留在上一根K线结束时的数值。

![](img/111e33bcfdb411373ea5327f2d4b7a44_18.png)

这个演示揭示了关键一点：**当 `IntraBarOrderGeneration` 打开时，策略主体内的代码（除了普通变量的值不被保存外）会在每个Tick都执行一遍**。这意味着，如果你的代码中包含开仓、平仓指令，它们也会在每个Tick被评估，可能导致重复发单。

![](img/111e33bcfdb411373ea5327f2d4b7a44_20.png)

![](img/111e33bcfdb411373ea5327f2d4b7a44_22.png)

因此，在使用此模式时，**必须使用 `MarketPosition` 等函数来控制仓位状态**，防止在已有仓位时重复开仓。一个常见的做法是定义一个函数来获取仓位状态：

![](img/111e33bcfdb411373ea5327f2d4b7a44_24.png)

![](img/111e33bcfdb411373ea5327f2d4b7a44_26.png)

![](img/111e33bcfdb411373ea5327f2d4b7a44_28.png)

```easylanguage
// 定义一个函数来获取带方向的持仓数量
// 正值表示多仓，负值表示空仓，0表示无仓
inputs: None;
variables: mp(0);
mp = MarketPosition * CurrentContracts;
mp;
```

![](img/111e33bcfdb411373ea5327f2d4b7a44_30.png)

![](img/111e33bcfdb411373ea5327f2d4b7a44_32.png)

## 内建出场函数（Set类指令）介绍 🎯

![](img/111e33bcfdb411373ea5327f2d4b7a44_34.png)

![](img/111e33bcfdb411373ea5327f2d4b7a44_36.png)

理解了代码的执行机制后，我们正式进入本节课的核心：内建出场函数。这些函数均以 `Set` 开头，用于设置自动化的止损止盈条件，它们最大的优点是**可以在每个Tick进行实时评估和触发**，无需等待K线收盘。

![](img/111e33bcfdb411373ea5327f2d4b7a44_38.png)

![](img/111e33bcfdb411373ea5327f2d4b7a44_40.png)

以下是主要的Set类出场函数及其简要说明：

![](img/111e33bcfdb411373ea5327f2d4b7a44_42.png)

*   **`SetExitOnClose`**
    *   **功能**：在当日收盘时平仓。
    *   **注意**：此函数涉及未来数据，**仅建议在回测中使用**，实盘交易中应避免。

*   **`SetStopLoss`**
    *   **功能**：设置固定金额的止损。当亏损达到指定金额时，触发平仓。
    *   **公式**：`SetStopLoss(止损金额)`

*   **`SetProfitTarget`**
    *   **功能**：设置固定金额的止盈。当盈利达到指定金额时，触发平仓。
    *   **公式**：`SetProfitTarget(止盈金额)`

*   **`SetBreakEven`**
    *   **功能**：设置保本止损。当仓位盈利达到指定金额后，如果利润回撤至零（即保本点），则触发平仓。
    *   **公式**：`SetBreakEven(触发保本的盈利金额)`

*   **`SetDollarTrailing`**
    *   **功能**：设置金额跟踪止盈。当仓位盈利达到指定金额后，从最高点回撤指定的金额时，触发平仓。
    *   **公式**：`SetDollarTrailing(触发跟踪的盈利金额， 回撤金额)`

*   **`SetPercentTrailing`**
    *   **功能**：设置百分比跟踪止盈。当仓位盈利达到指定金额后，从最高点回撤指定的百分比时，触发平仓。
    *   **公式**：`SetPercentTrailing(触发跟踪的盈利金额， 回撤百分比)`

**使用Set类函数的前置条件**：
在使用 `SetStopLoss`, `SetProfitTarget`, `SetDollarTrailing`, `SetPercentTrailing` 之前，**必须**先使用以下两个函数之一来声明止损止盈的计算方式：
*   `SetStopPosition`：所有仓位合并计算（例如，分批进场算总账）。
*   `SetStopContract` 或 `SetStopShare`：每个仓位单独计算。

**关于参数中的“PT”**：
在某些函数的参数中，你会看到“PT”，例如 `SetProfitTarget(50 pt)`。这里的 **PT代表“点数”或“跳数”**，而不是金额。它指的是价格的最小变动单位。计算跳数通常涉及 `MinMove`（最小变动值）和 `BigPointValue`（每点价值）等函数。

## Set类函数的优缺点与价格计算 ⚖️

现在我们已经了解了有哪些Set类函数，本节中我们来看看它们的优缺点，以及在实际计算触发价时需要考虑的因素。

**优点**：
1.  **即时性**：由于在每个Tick评估，能更快地响应价格变动，及时止损或止盈，避免因等待K线收盘而扩大亏损或回吐过多利润。
2.  **简化代码**：无需编写复杂的条件判断逻辑来计算止损止盈价并手动发平仓单，极大减少了代码量。

**缺点**：
1.  **可能过早触发**：在震荡行情中，价格可能瞬间触及止损线后又迅速回转，导致被“洗”出场，错过后续行情。
2.  **依赖设置**：需要正确理解并设置 `SetStopPosition` 或 `SetStopContract`。

![](img/111e33bcfdb411373ea5327f2d4b7a44_44.png)

**价格计算公式与滑点/手续费**：
当你在策略属性中设置了滑点（Slippage）和手续费（Commission）后，Set类函数在计算触发价格时会自动将这些成本考虑进去。

![](img/111e33bcfdb411373ea5327f2d4b7a44_46.png)

![](img/111e33bcfdb411373ea5327f2d4b7a44_48.png)

例如，你设置止盈目标为盈利100元，滑点为10元/笔，手续费为10元/笔。对于一手螺纹钢（10吨/手），Set类函数计算的实际目标价格偏移将是：
`(100元利润 + 10元滑点 + 10元手续费) / 10吨 = 12元/吨`。
这意味着，它会确保在扣除所有交易成本后，你的净盈利达到100元时才触发止盈。

## 策略示例与回测验证 💻

![](img/111e33bcfdb411373ea5327f2d4b7a44_50.png)

理论需要结合实践。下面我们构建一个简单的“高低点突破”策略，并在回测中观察其表现，特别是滑点和手续费的影响。

策略逻辑：
*   **开多仓**：收盘价创20周期新高（排除当前K线）。
*   **平多仓**：持有多仓时，收盘价创10周期新低。
*   **开空仓**：收盘价创20周期新低（排除当前K线）。
*   **平空仓**：持有空仓时，收盘价创10周期新高。

```easylanguage
// 策略示例：高低点突破
vars: mp(0);
mp = MarketPosition * CurrentContracts;

// 开多条件：收盘价高于前19根K线的最高收盘价
if Close > Highest(Close[1], 19) and mp = 0 then
    Buy next bar at open;

// 平多条件：持有多仓且收盘价低于前9根K线的最低收盘价
if mp > 0 and Close < Lowest(Close[1], 9) then
    Sell next bar at open;

![](img/111e33bcfdb411373ea5327f2d4b7a44_52.png)

// 开空条件：收盘价低于前19根K线的最低收盘价
if Close < Lowest(Close[1], 19) and mp = 0 then
    SellShort next bar at open;

![](img/111e33bcfdb411373ea5327f2d4b7a44_54.png)

![](img/111e33bcfdb411373ea5327f2d4b7a44_56.png)

![](img/111e33bcfdb411373ea5327f2d4b7a44_58.png)

// 平空条件：持有空仓且收盘价高于前9根K线的最高收盘价
if mp < 0 and Close > Highest(Close[1], 9) then
    BuyToCover next bar at open;
```

![](img/111e33bcfdb411373ea5327f2d4b7a44_60.png)

**回测观察**：
1.  在策略属性的“信号设置”中，可以设置滑点和手续费模型。
2.  回测报告中的“已付滑点”和“已付手续费”会清晰地展示交易成本对绩效的影响。
3.  注意：使用 `next bar at open` 等市价单指令，回测中会计入滑点；而使用 `next bar at close limit` 等限价单指令，在回测中通常默认没有滑点（除非特别设置）。

![](img/111e33bcfdb411373ea5327f2d4b7a44_62.png)

![](img/111e33bcfdb411373ea5327f2d4b7a44_64.png)

![](img/111e33bcfdb411373ea5327f2d4b7a44_66.png)

![](img/111e33bcfdb411373ea5327f2d4b7a44_68.png)

## 总结 📝

本节课中我们一起学习了MultiCharts中内建出场函数（Set类指令）的核心知识。我们从理解 `IntraBarOrderGeneration` 模式下代码的执行特性开始，明白了实时风控的必要性。然后，我们系统地介绍了各种Set类函数的用途、语法和前置条件，并分析了其优缺点。最后，我们通过一个策略示例和回测，了解了交易成本（滑点、手续费）如何影响策略绩效，以及Set类函数如何自动处理这些成本。

![](img/111e33bcfdb411373ea5327f2d4b7a44_70.png)

![](img/111e33bcfdb411373ea5327f2d4b7a44_72.png)

![](img/111e33bcfdb411373ea5327f2d4b7a44_74.png)

关键要点：
1.  启用 `IntraBarOrderGeneration` 后，需用 `MarketPosition` 严格管控开仓逻辑。
2.  Set类出场函数能实现实时、自动的止损止盈，简化代码。
3.  使用 `SetStopLoss`、`SetProfitTarget` 等函数前，必须先调用 `SetStopPosition` 或 `SetStopContract` 来定义计算范围。
4.  回测时，务必设置合理的滑点和手续费，以更接近真实交易环境。Set类函数会自动在计算中考虑这些成本。