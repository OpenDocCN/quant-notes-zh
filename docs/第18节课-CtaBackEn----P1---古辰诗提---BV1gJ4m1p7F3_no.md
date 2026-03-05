# 从零开始量化：18：CtaBackEngine的实现(3) - P1

![](img/76c98ce8b57bf7063a2f7fc339c14548_1.png)

在本节课中，我们将继续构建CtaBackEngine，重点实现委托的发送与触发逻辑。我们将学习如何规范价格、创建并存储委托数据，以及如何模拟实盘中的委托发送与回调过程。

上一节我们完成了引擎的初始化工作，本节中我们来看看如何启动策略并处理委托发送。

## 启动策略

初始化完成后，下一步是启动策略。启动策略的核心是调用策略的 `on_start` 方法，并将引擎的 `trading` 状态设置为 `True`，以允许委托向下层发送。

以下是启动策略的步骤：

1.  调用策略的 `on_start` 方法。
2.  将引擎的 `trading` 属性设置为 `True`。

```python
# 启动策略
self.strategy.on_start()
self.trading = True
```

当 `trading` 为 `True` 时，策略中通过 `send_order` 方法发出的委托才会被引擎处理并尝试发送。

## 执行回测循环

启动策略后，真正的回测过程开始。我们需要遍历历史数据，对每一根K线调用策略的 `on_bar` 方法。

以下是执行回测循环的步骤：

1.  遍历历史数据中的每一根K线。
2.  更新引擎的当前时间。
3.  调用策略的 `on_bar` 方法。

```python
# 执行回测循环
for bar in self.history_data:
    self.datetime = bar.datetime.replace(tzinfo=None)  # 移除时区信息
    self.strategy.on_bar(bar)
```

在 `on_bar` 方法中，策略会根据其逻辑判断是否发出交易委托。

## 发送委托

当策略调用 `send_order` 方法时，引擎需要处理该请求。`send_order` 方法根据参数决定发送限价单还是停止单。

以下是发送委托的流程：

1.  规范委托价格。
2.  根据 `stop` 参数判断委托类型。
3.  创建对应的委托数据对象。
4.  将委托数据保存到引擎中等待触发。
5.  回调策略的 `on_order` 或 `on_stop_order` 方法。
6.  返回委托ID。

### 规范价格

首先，我们需要对策略传入的价格进行规范化，使其符合合约的最小变动价位。

```python
from vnpy.trader.utility import round_to

price = round_to(price, self.price_tick)
```

### 创建限价单

如果发送的是限价单，我们需要创建一个 `OrderData` 对象。

以下是创建限价单的步骤：

1.  生成唯一的委托ID。
2.  填充 `OrderData` 对象的各项字段。
3.  将委托保存到 `limit_orders` 字典中。
4.  回调策略的 `on_order` 方法。

```python
def send_limit_order(
    self,
    direction: Direction,
    offset: Offset,
    price: float,
    volume: float
) -> List[str]:
    """发送限价单"""
    # 生成委托ID
    self.limit_order_count += 1
    orderid: str = f"{self.limit_order_prefix}.{self.limit_order_count}"

    # 创建委托数据
    order: OrderData = OrderData(
        gateway_name=self.gateway_name,
        symbol=self.vt_symbol,
        exchange=self.exchange,
        orderid=orderid,
        direction=direction,
        offset=offset,
        price=price,
        volume=volume,
        status=Status.SUBMITTING,
        datetime=self.datetime
    )

    # 保存委托
    self.limit_orders[order.vt_orderid] = order

    # 回调策略
    self.strategy.on_order(order)

    # 返回委托ID列表
    return [order.vt_orderid]
```

### 创建停止单

如果发送的是停止单，流程与限价单类似，但创建的是 `StopOrderData` 对象。

以下是创建停止单的步骤：

1.  生成唯一的停止单ID。
2.  填充 `StopOrderData` 对象的各项字段。
3.  将停止单保存到 `stop_orders` 字典中。
4.  回调策略的 `on_stop_order` 方法。

![](img/76c98ce8b57bf7063a2f7fc339c14548_3.png)

![](img/76c98ce8b57bf7063a2f7fc339c14548_5.png)

```python
def send_stop_order(
    self,
    direction: Direction,
    offset: Offset,
    price: float,
    volume: float
) -> List[str]:
    """发送停止单"""
    # 生成停止单ID
    self.stop_order_count += 1
    stop_orderid: str = f"{self.stop_order_prefix}.{self.stop_order_count}"

    # 创建停止单数据
    stop_order: StopOrderData = StopOrderData(
        gateway_name=self.gateway_name,
        symbol=self.vt_symbol,
        exchange=self.exchange,
        stop_orderid=stop_orderid,
        direction=direction,
        offset=offset,
        price=price,
        volume=volume,
        strategy_name=self.strategy.strategy_name,
        status=StopOrderStatus.WAITING,
        datetime=self.datetime,
        vt_orderids=[]
    )

    # 保存停止单
    self.stop_orders[stop_order.stop_orderid] = stop_order

    # 回调策略
    self.strategy.on_stop_order(stop_order)

    # 返回停止单ID列表
    return [stop_order.stop_orderid]
```

## 处理新K线与委托触发

委托发送后，保存在引擎中等待触发。触发检查发生在每一根新K线到来时，在策略的 `on_bar` 方法被调用之前。

我们需要实现一个 `process_new_bar` 方法，在新K线到来时检查是否有委托可以被触发。

以下是处理新K线与委托触发的逻辑：

1.  在新K线到来时，首先更新引擎的当前时间。
2.  调用 `process_new_bar` 方法检查并触发符合条件的委托。
3.  再调用策略的 `on_bar` 方法。

```python
def run_backtesting(self):
    """执行回测"""
    # 启动策略
    self.strategy.on_start()
    self.trading = True

    # 遍历历史数据
    for bar in self.history_data:
        # 更新当前时间并移除时区信息
        self.datetime = bar.datetime.replace(tzinfo=None)
        bar.datetime = self.datetime

        # 处理新K线，检查委托触发
        self.process_new_bar(bar)

        # 调用策略逻辑
        self.strategy.on_bar(bar)

def process_new_bar(self, bar: BarData):
    """处理新K线，检查委托触发"""
    # 委托触发逻辑将在下一节实现
    pass
```

将触发检查放在 `on_bar` 之前，是为了避免“未来函数”问题，确保回测逻辑与实盘交易逻辑一致。在实盘中，委托的成交可能发生在下一根K线生成之前的Tick数据流中。

![](img/76c98ce8b57bf7063a2f7fc339c14548_7.png)

本节课中我们一起学习了CtaBackEngine中委托发送与触发检查的核心流程。我们实现了策略的启动、回测循环的执行、限价单与停止单的创建与存储，并确立了在新K线到来时优先检查委托触发的逻辑顺序。下一节我们将深入实现 `process_new_bar` 方法，完成委托的触发与成交处理。