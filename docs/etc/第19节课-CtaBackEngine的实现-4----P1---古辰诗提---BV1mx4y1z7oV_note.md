# 从零开始量化：第19课：CtaBackEngine的实现(4) - 限价单与停止单的撮合逻辑 🧮

![](img/2ee9004fe4028dcccb6dc3d5809e39ea_1.png)

![](img/2ee9004fe4028dcccb6dc3d5809e39ea_3.png)

![](img/2ee9004fe4028dcccb6dc3d5809e39ea_5.png)

![](img/2ee9004fe4028dcccb6dc3d5809e39ea_7.png)

![](img/2ee9004fe4028dcccb6dc3d5809e39ea_9.png)

![](img/2ee9004fe4028dcccb6dc3d5809e39ea_11.png)

![](img/2ee9004fe4028dcccb6dc3d5809e39ea_13.png)

在本节课中，我们将学习CtaBackEngine中撮合引擎的核心部分：限价单和停止单的撮合逻辑实现。我们将详细分析如何根据K线数据判断订单是否成交，并完成相应的状态更新、仓位计算和回调通知。

![](img/2ee9004fe4028dcccb6dc3d5809e39ea_15.png)

![](img/2ee9004fe4028dcccb6dc3d5809e39ea_17.png)

![](img/2ee9004fe4028dcccb6dc3d5809e39ea_19.png)

![](img/2ee9004fe4028dcccb6dc3d5809e39ea_21.png)

上一节我们介绍了`process_new_bar`方法，它是整个回测引擎的核心。本节中，我们来看看如何将合约的撮合逻辑拆分为限价单和停止单两个独立的方法，并逐一实现它们。

![](img/2ee9004fe4028dcccb6dc3d5809e39ea_23.png)

![](img/2ee9004fe4028dcccb6dc3d5809e39ea_25.png)

## 限价单撮合逻辑

![](img/2ee9004fe4028dcccb6dc3d5809e39ea_27.png)

首先，我们定义处理限价单的方法 `_cross_limit_order`。该方法接收一个Bar数据对象作为参数，用于与存储的限价单进行价格比对。

![](img/2ee9004fe4028dcccb6dc3d5809e39ea_29.png)

以下是限价单撮合的核心步骤：

![](img/2ee9004fe4028dcccb6dc3d5809e39ea_31.png)

![](img/2ee9004fe4028dcccb6dc3d5809e39ea_33.png)

![](img/2ee9004fe4028dcccb6dc3d5809e39ea_35.png)

1.  **检查订单列表**：首先检查是否存在待撮合的限价单。如果没有，则直接返回。
    ```python
    if not self.limit_orders:
        return
    ```

![](img/2ee9004fe4028dcccb6dc3d5809e39ea_37.png)

2.  **遍历订单**：遍历所有限价单。注意，为了避免在迭代过程中修改字典大小导致错误，需要将字典的键或值转换为列表。
    ```python
    for order in list(self.limit_orders.values()):
    ```

![](img/2ee9004fe4028dcccb6dc3d5809e39ea_39.png)

![](img/2ee9004fe4028dcccb6dc3d5809e39ea_41.png)

![](img/2ee9004fe4028dcccb6dc3d5809e39ea_43.png)

![](img/2ee9004fe4028dcccb6dc3d5809e39ea_45.png)

![](img/2ee9004fe4028dcccb6dc3d5809e39ea_47.png)

3.  **状态预处理**：在原始代码中，有一个步骤是将状态为“提交中”的订单更新为“未成交”。这模拟了订单被交易所接收的过程。
    ```python
    if order.status == Status.SUBMITTING:
        order.status = Status.NOTTRADED
        self.strategy.on_order(order)
    ```

![](img/2ee9004fe4028dcccb6dc3d5809e39ea_49.png)

![](img/2ee9004fe4028dcccb6dc3d5809e39ea_51.png)

![](img/2ee9004fe4028dcccb6dc3d5809e39ea_53.png)

4.  **判断成交条件**：这是撮合逻辑的关键。我们需要根据订单方向和价格，判断在当前K线周期内是否满足成交条件。
    *   **多单成交条件**：订单方向为多，且订单价格大于等于当前K线的最低价。
        `order.direction == Direction.LONG and order.price >= bar.low_price`
    *   **空单成交条件**：订单方向为空，且订单价格小于等于当前K线的最高价。
        `order.direction == Direction.SHORT and order.price <= bar.high_price`

![](img/2ee9004fe4028dcccb6dc3d5809e39ea_55.png)

![](img/2ee9004fe4028dcccb6dc3d5809e39ea_57.png)

![](img/2ee9004fe4028dcccb6dc3d5809e39ea_59.png)

![](img/2ee9004fe4028dcccb6dc3d5809e39ea_61.png)

![](img/2ee9004fe4028dcccb6dc3d5809e39ea_63.png)

![](img/2ee9004fe4028dcccb6dc3d5809e39ea_65.png)

![](img/2ee9004fe4028dcccb6dc3d5809e39ea_67.png)

![](img/2ee9004fe4028dcccb6dc3d5809e39ea_69.png)

5.  **确定成交价**：如果满足成交条件，则需要确定成交价格。逻辑是取订单价格和当前K线开盘价的较小值（对多单）或较大值（对空单），以模拟可能的最优成交价。
    *   **多单成交价**：`trade_price = min(bar.open_price, order.price)`
    *   **空单成交价**：`trade_price = max(bar.open_price, order.price)`

![](img/2ee9004fe4028dcccb6dc3d5809e39ea_71.png)

![](img/2ee9004fe4028dcccb6dc3d5809e39ea_73.png)

![](img/2ee9004fe4028dcccb6dc3d5809e39ea_75.png)

![](img/2ee9004fe4028dcccb6dc3d5809e39ea_77.png)

6.  **更新订单状态**：将订单状态标记为“全部成交”，并记录成交数量。
    ```python
    order.status = Status.ALLTRADED
    order.traded = order.volume
    ```

![](img/2ee9004fe4028dcccb6dc3d5809e39ea_79.png)

![](img/2ee9004fe4028dcccb6dc3d5809e39ea_81.png)

![](img/2ee9004fe4028dcccb6dc3d5809e39ea_82.png)

![](img/2ee9004fe4028dcccb6dc3d5809e39ea_84.png)

![](img/2ee9004fe4028dcccb6dc3d5809e39ea_86.png)

![](img/2ee9004fe4028dcccb6dc3d5809e39ea_88.png)

![](img/2ee9004fe4028dcccb6dc3d5809e39ea_90.png)

7.  **回调策略**：调用策略的`on_order`方法，通知策略订单状态已更新。
    ```python
    self.strategy.on_order(order)
    ```

![](img/2ee9004fe4028dcccb6dc3d5809e39ea_92.png)

![](img/2ee9004fe4028dcccb6dc3d5809e39ea_94.png)

![](img/2ee9004fe4028dcccb6dc3d5809e39ea_96.png)

![](img/2ee9004fe4028dcccb6dc3d5809e39ea_98.png)

![](img/2ee9004fe4028dcccb6dc3d5809e39ea_100.png)

8.  **生成成交记录**：创建TradeData对象，记录本次成交的详细信息。
    ```python
    trade = TradeData(
        gateway_name=self.gateway_name,
        symbol=self.symbol,
        exchange=self.exchange,
        orderid=order.orderid,
        tradeid=f"TRADE_{self.trade_count}",
        direction=order.direction,
        offset=order.offset,
        price=trade_price,
        volume=order.volume,
        datetime=self.datetime
    )
    self.trade_count += 1
    ```

![](img/2ee9004fe4028dcccb6dc3d5809e39ea_102.png)

![](img/2ee9004fe4028dcccb6dc3d5809e39ea_104.png)

![](img/2ee9004fe4028dcccb6dc3d5809e39ea_106.png)

![](img/2ee9004fe4028dcccb6dc3d5809e39ea_108.png)

![](img/2ee9004fe4028dcccb6dc3d5809e39ea_110.png)

![](img/2ee9004fe4028dcccb6dc3d5809e39ea_112.png)

9.  **更新策略仓位**：根据成交方向，更新策略的持仓数量。多单成交增加持仓，空单成交减少持仓。
    ```python
    if long_cross:
        pos_change = order.volume
    elif short_cross:
        pos_change = -order.volume
    self.strategy.pos += pos_change
    ```

![](img/2ee9004fe4028dcccb6dc3d5809e39ea_114.png)

![](img/2ee9004fe4028dcccb6dc3d5809e39ea_116.png)

10. **保存成交并回调**：将成交记录存入字典，并调用策略的`on_trade`方法。
    ```python
    self.trades[trade.vt_tradeid] = trade
    self.strategy.on_trade(trade)
    ```

11. **移除已成交订单**：从`limit_orders`字典中移除已完全成交的订单。
    ```python
    self.limit_orders.pop(order.vt_orderid)
    ```

![](img/2ee9004fe4028dcccb6dc3d5809e39ea_118.png)

![](img/2ee9004fe4028dcccb6dc3d5809e39ea_120.png)

![](img/2ee9004fe4028dcccb6dc3d5809e39ea_122.png)

## 停止单撮合逻辑

![](img/2ee9004fe4028dcccb6dc3d5809e39ea_124.png)

![](img/2ee9004fe4028dcccb6dc3d5809e39ea_126.png)

![](img/2ee9004fe4028dcccb6dc3d5809e39ea_128.png)

![](img/2ee9004fe4028dcccb6dc3d5809e39ea_130.png)

接下来，我们定义处理停止单的方法 `_cross_stop_order`。停止单的逻辑与限价单相反，它需要等待市场价格达到某个阈值后才触发。

![](img/2ee9004fe4028dcccb6dc3d5809e39ea_132.png)

![](img/2ee9004fe4028dcccb6dc3d5809e39ea_134.png)

以下是停止单撮合的核心步骤：

![](img/2ee9004fe4028dcccb6dc3d5809e39ea_136.png)

![](img/2ee9004fe4028dcccb6dc3d5809e39ea_138.png)

![](img/2ee9004fe4028dcccb6dc3d5809e39ea_140.png)

1.  **检查订单列表**：同样，首先检查是否存在待触发的停止单。
    ```python
    if not self.stop_orders:
        return
    ```

![](img/2ee9004fe4028dcccb6dc3d5809e39ea_142.png)

![](img/2ee9004fe4028dcccb6dc3d5809e39ea_144.png)

![](img/2ee9004fe4028dcccb6dc3d5809e39ea_146.png)

![](img/2ee9004fe4028dcccb6dc3d5809e39ea_148.png)

2.  **遍历订单**：遍历所有停止单。
    ```python
    for stop_order in list(self.stop_orders.values()):
    ```

![](img/2ee9004fe4028dcccb6dc3d5809e39ea_150.png)

![](img/2ee9004fe4028dcccb6dc3d5809e39ea_152.png)

![](img/2ee9004fe4028dcccb6dc3d5809e39ea_154.png)

![](img/2ee9004fe4028dcccb6dc3d5809e39ea_155.png)

![](img/2ee9004fe4028dcccb6dc3d5809e39ea_157.png)

3.  **判断触发条件**：停止单的触发逻辑与限价单成交逻辑相反。
    *   **多方向触发条件**（如开多仓、平空仓）：订单方向为多，且当前K线的最高价大于等于停止单价格。
        `stop_order.direction == Direction.LONG and bar.high_price >= stop_order.price`
    *   **空方向触发条件**（如开空仓、平多仓）：订单方向为空，且当前K线的最低价小于等于停止单价格。
        `stop_order.direction == Direction.SHORT and bar.low_price <= stop_order.price`

![](img/2ee9004fe4028dcccb6dc3d5809e39ea_159.png)

![](img/2ee9004fe4028dcccb6dc3d5809e39ea_161.png)

![](img/2ee9004fe4028dcccb6dc3d5809e39ea_163.png)

![](img/2ee9004fe4028dcccb6dc3d5809e39ea_165.png)

4.  **确定触发价**：触发后，需要确定一个“虚拟”的成交价。逻辑是取停止单价格和当前K线开盘价的较大值（对多方向）或较小值（对空方向）。
    *   **多方向触发价**：`trade_price = max(bar.open_price, stop_order.price)`
    *   **空方向触发价**：`trade_price = min(bar.open_price, stop_order.price)`

5.  **模拟限价单流程**：停止单触发后，在实盘中会转换为一个限价单发送至交易所。在回测中，我们模拟这一过程：
    *   **更新停止单状态**：将停止单状态改为“已触发”。
        `stop_order.status = StopOrderStatus.TRIGGERED`
    *   **创建虚拟限价单**：生成一个对应的OrderData对象，模拟发送到交易所的限价单。
    *   **回调策略**：先调用`on_stop_order`通知停止单已触发，再调用`on_order`模拟限价单被交易所接收。
    *   **生成成交记录**：假设该虚拟限价单立即全部成交，创建对应的TradeData。
    *   **更新仓位**：根据成交方向更新策略持仓。
    *   **保存并回调**：保存成交记录，调用`on_trade`方法。
    *   **移除停止单**：从`stop_orders`字典中移除已触发的停止单。

![](img/2ee9004fe4028dcccb6dc3d5809e39ea_167.png)

## 核心概念总结

![](img/2ee9004fe4028dcccb6dc3d5809e39ea_169.png)

![](img/2ee9004fe4028dcccb6dc3d5809e39ea_171.png)

![](img/2ee9004fe4028dcccb6dc3d5809e39ea_173.png)

![](img/2ee9004fe4028dcccb6dc3d5809e39ea_175.png)

![](img/2ee9004fe4028dcccb6dc3d5809e39ea_177.png)

![](img/2ee9004fe4028dcccb6dc3d5809e39ea_179.png)

本节课中我们一起学习了CtaBackEngine撮合引擎的核心实现。关键点在于理解两种订单的成交/触发逻辑：

![](img/2ee9004fe4028dcccb6dc3d5809e39ea_181.png)

![](img/2ee9004fe4028dcccb6dc3d5809e39ea_183.png)

![](img/2ee9004fe4028dcccb6dc3d5809e39ea_185.png)

![](img/2ee9004fe4028dcccb6dc3d5809e39ea_187.png)

![](img/2ee9004fe4028dcccb6dc3d5809e39ea_189.png)

*   **限价单**：希望以指定或更优价格成交。**买**单成交价 <= 订单价，**卖**单成交价 >= 订单价。
*   **停止单**：等待市场达到指定价格后触发，通常用于止损或趋势跟进。其触发逻辑与限价单的成交逻辑**相反**。

![](img/2ee9004fe4028dcccb6dc3d5809e39ea_191.png)

![](img/2ee9004fe4028dcccb6dc3d5809e39ea_193.png)

![](img/2ee9004fe4028dcccb6dc3d5809e39ea_195.png)

![](img/2ee9004fe4028dcccb6dc3d5809e39ea_197.png)

![](img/2ee9004fe4028dcccb6dc3d5809e39ea_199.png)

整个流程严格模拟了实盘中订单从发出、接收、成交到回调策略的完整生命周期，确保了回测系统与实盘交易在逻辑上的一致性。通过实现这些细节，我们构建的回测引擎才能更真实地反映策略可能产生的交易结果。