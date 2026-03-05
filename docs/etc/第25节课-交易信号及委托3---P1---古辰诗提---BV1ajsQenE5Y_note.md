# 量化交易入门：第25课：交易信号及委托详解（三）

在本节课中，我们将继续深入学习交易信号与委托相关的关键字，重点探讨如何实现分批进场、指定平仓以及一些高级的订单管理功能。通过具体的代码示例和图表演示，我们将理解这些概念的实际应用。

上一节我们介绍了基本的交易信号语法，本节中我们来看看如何实现更复杂的仓位管理。

---

![](img/fca4ec54640df03d6c339ff3f4dea5cb_1.png)

![](img/fca4ec54640df03d6c339ff3f4dea5cb_3.png)

## 分批进场与仓位管理

![](img/fca4ec54640df03d6c339ff3f4dea5cb_5.png)

![](img/fca4ec54640df03d6c339ff3f4dea5cb_7.png)

![](img/fca4ec54640df03d6c339ff3f4dea5cb_9.png)

![](img/fca4ec54640df03d6c339ff3f4dea5cb_11.png)

在策略中，有时我们希望分批建立仓位，而不是一次性全仓买入。这需要使用到仓位状态判断和循环控制逻辑。

![](img/fca4ec54640df03d6c339ff3f4dea5cb_13.png)

![](img/fca4ec54640df03d6c339ff3f4dea5cb_15.png)

![](img/fca4ec54640df03d6c339ff3f4dea5cb_17.png)

以下是实现分批进场的一个基础代码框架：

![](img/fca4ec54640df03d6c339ff3f4dea5cb_19.png)

![](img/fca4ec54640df03d6c339ff3f4dea5cb_21.png)

![](img/fca4ec54640df03d6c339ff3f4dea5cb_23.png)

![](img/fca4ec54640df03d6c339ff3f4dea5cb_25.png)

```easyLanguage
Variables:
    control(0); // 控制开关

![](img/fca4ec54640df03d6c339ff3f4dea5cb_27.png)

![](img/fca4ec54640df03d6c339ff3f4dea5cb_29.png)

![](img/fca4ec54640df03d6c339ff3f4dea5cb_31.png)

If fastMA crosses over slowMA and currentShares < 10 and control = 0 Then
    Buy next bar at market;

![](img/fca4ec54640df03d6c339ff3f4dea5cb_33.png)

![](img/fca4ec54640df03d6c339ff3f4dea5cb_35.png)

![](img/fca4ec54640df03d6c339ff3f4dea5cb_37.png)

![](img/fca4ec54640df03d6c339ff3f4dea5cb_39.png)

If currentShares = 10 and control = 0 Then
    control = 1; // 仓位达到10手后，关闭买入开关

![](img/fca4ec54640df03d6c339ff3f4dea5cb_41.png)

![](img/fca4ec54640df03d6c339ff3f4dea5cb_43.png)

If close < slowMA and control = 1 Then
    Sell next bar at market;
```

![](img/fca4ec54640df03d6c339ff3f4dea5cb_45.png)

![](img/fca4ec54640df03d6c339ff3f4dea5cb_47.png)

![](img/fca4ec54640df03d6c339ff3f4dea5cb_49.png)

**关键点解析**：
*   `currentShares`：获取当前持仓数量（无正负号）。
*   `control`：作为一个状态开关，用于控制买入行为的启停。
*   代码逻辑：当快线上穿慢线且持仓不足10手时，持续买入；持仓达到10手后，停止买入并准备在条件满足时卖出。

![](img/fca4ec54640df03d6c339ff3f4dea5cb_51.png)

![](img/fca4ec54640df03d6c339ff3f4dea5cb_53.png)

![](img/fca4ec54640df03d6c339ff3f4dea5cb_55.png)

![](img/fca4ec54640df03d6c339ff3f4dea5cb_57.png)

然而，仅靠代码逻辑可能无法实现预期的分批效果，还需要在信号属性中进行设置。

![](img/fca4ec54640df03d6c339ff3f4dea5cb_59.png)

![](img/fca4ec54640df03d6c339ff3f4dea5cb_61.png)

**属性设置步骤**：
1.  在信号属性窗口中，找到“属性”或“设置”选项。
2.  寻找名为“最多允许多少笔与目前仓位同向的进场”或类似的参数。
3.  将该参数设置为期望的最大分批次数（例如10）。
4.  此设置允许策略在同一方向上多次发出进场信号，直到达到设定的次数上限。

---

## 指定平仓与 `from entry` 关键字

当存在多笔不同时间或价格的进场时，平仓时可能需要指定平掉哪一笔仓位。这时需要使用 `from entry` 关键字。

**基本语法**：
```easyLanguage
Sell from entry("EntryName") next bar at market;
```
这条指令的意思是：平掉所有标签为 `"EntryName"` 的进场仓位。

为了实现给每笔进场赋予独特的名称，我们可以结合变量和字符串函数。

以下是动态生成进场标签并指定平仓的示例：

```easyLanguage
Variables:
    entryCount(0); // 进场计数
    entryName(""); // 进场名称

If 进场条件 Then Begin
    entryCount = entryCount + 1;
    entryName = "MyEntry" + NumToStr(entryCount, 0); // 生成唯一名称，如 MyEntry1, MyEntry2
    Buy entryName next bar at market;
End;

If 平仓条件 Then
    Sell from entry("MyEntry2") next bar at market; // 仅平掉名为 "MyEntry2" 的仓位
```

![](img/fca4ec54640df03d6c339ff3f4dea5cb_63.png)

**代码逻辑**：
1.  每次满足进场条件时，`entryCount` 计数器加1。
2.  使用 `NumToStr` 函数将计数器转换为字符串，并与固定前缀拼接，生成唯一的进场标签（如 `MyEntry1`）。
3.  平仓时，在 `from entry` 后指定具体的标签名，即可实现精准平仓。

![](img/fca4ec54640df03d6c339ff3f4dea5cb_65.png)

![](img/fca4ec54640df03d6c339ff3f4dea5cb_67.png)

![](img/fca4ec54640df03d6c339ff3f4dea5cb_69.png)

---

![](img/fca4ec54640df03d6c339ff3f4dea5cb_71.png)

## 订单生成模式：`intrabar order generation`

![](img/fca4ec54640df03d6c339ff3f4dea5cb_73.png)

![](img/fca4ec54640df03d6c339ff3f4dea5cb_75.png)

![](img/fca4ec54640df03d6c339ff3f4dea5cb_77.png)

默认情况下，`next bar at market` 等指令会在下一根K线的开盘价成交。启用 `intrabar order generation` 功能后，系统会尝试在发出信号的那根K线内部（根据Tick数据或K线内推逻辑）寻找成交机会。

![](img/fca4ec54640df03d6c339ff3f4dea5cb_79.png)

![](img/fca4ec54640df03d6c339ff3f4dea5cb_81.png)

**启用方法**：
在策略代码开头或初始化部分设置：
```easyLanguage
intrabar order generation = true;
```

**作用与注意事项**：
*   **作用**：可以让成交标记（箭头）出现在K线内部，而不是严格在下一根K线的开盘位置。这对于依赖精确入场点的策略（如某些限价单、止损单）回测更贴近实际。
*   **注意事项**：在回测中，如果未使用精细的Tick数据，MC会基于K线的OHLC（开高低收）价格对K线内部走势进行假设（例如，假设价格先探最低点再回升）。因此，对于止损/止盈幅度很小的策略，回测结果可能与实盘有差异。要进行更精确的回测，需启用“使用精细资料”选项。

---

![](img/fca4ec54640df03d6c339ff3f4dea5cb_83.png)

## 高级订单管理：`place market order`

![](img/fca4ec54640df03d6c339ff3f4dea5cb_85.png)

`place market order` 是一个用于同步图表策略持仓与实际账户持仓的函数。它直接向经纪商（交易所）发送市价单，但**不会在图表上产生新的交易信号箭头**。

![](img/fca4ec54640df03d6c339ff3f4dea5cb_87.png)

![](img/fca4ec54640df03d6c339ff3f4dea5cb_89.png)

![](img/fca4ec54640df03d6c339ff3f4dea5cb_91.png)

**典型应用场景**：
当图表策略信号因使用限价单等原因未能实际成交（异步模式下，图表仍显示该信号），导致图表持仓与真实账户持仓不一致时，可以使用此函数补单，使两者一致。

![](img/fca4ec54640df03d6c339ff3f4dea5cb_93.png)

**函数语法**：
```easyLanguage
place market order(isBuy, isEntry, shares);
```
*   `isBuy`：`True` 为买入，`False` 为卖出。
*   `isEntry`：`True` 为开仓，`False` 为平仓。
*   `shares`：交易手数。

![](img/fca4ec54640df03d6c339ff3f4dea5cb_95.png)

![](img/fca4ec54640df03d6c339ff3f4dea5cb_97.png)

![](img/fca4ec54640df03d6c339ff3f4dea5cb_99.png)

**示例逻辑**：
假设图表显示持仓应为2手多头，但实际账户持仓为0手。
```easyLanguage
If currentShares = 2 and marketPositionAtBroker = 0 Then
    place market order(true, true, 2); // 发出买入开仓2手的市价单，以补齐仓位
```
**注意**：`marketPositionAtBroker` 用于获取经纪商处的实际持仓。在使用此函数时，需仔细考虑多策略共用账户等复杂情况。

![](img/fca4ec54640df03d6c339ff3f4dea5cb_101.png)

![](img/fca4ec54640df03d6c339ff3f4dea5cb_103.png)

---

![](img/fca4ec54640df03d6c339ff3f4dea5cb_105.png)

## 其他关键字回顾

*   **`total`**：在平仓语句中，指定平掉**总共**多少手，而不是针对每一笔进场平多少手。例如，`Sell total 3 shares next bar at market` 会从所有进场仓位中总计平掉3手。
*   **`this bar on close`**：在当前K线收盘时成交。不建议在开仓时使用，可能导致信号闪烁。
*   **`limit`, `stop`**：限价单和止损单。理解其逻辑（`limit` 订单在优于或等于指定价格时成交，`stop` 订单在差于或等于指定价格时成交）对策略设计和风险控制至关重要。

---

![](img/fca4ec54640df03d6c339ff3f4dea5cb_107.png)

本节课中我们一起学习了如何实现分批进场与精细化的仓位管理，掌握了使用 `from entry` 关键字指定平仓的方法，了解了 `intrabar order generation` 对订单成交位置的影响，并认识了用于同步仓位的 `place market order` 函数。这些工具能帮助你构建更复杂、更接近实盘交易逻辑的量化策略。理解并妥善运用这些概念，是策略从简单走向成熟的关键一步。