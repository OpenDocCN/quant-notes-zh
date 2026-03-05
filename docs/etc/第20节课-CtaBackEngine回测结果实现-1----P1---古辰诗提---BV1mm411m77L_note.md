# 从零开始量化：第20课：CtaBackEngine回测结果实现(1)

在本节课中，我们将学习如何将回测引擎生成的成交记录保存为文件，并添加进度显示功能。同时，我们还将完善撤单逻辑，并开始构建用于计算回测统计结果的类。

上一节课我们已经完成了回测引擎最核心的撮合部分。现在，我们需要对这部分代码进行测试，以确保其功能正确。

## 保存成交记录

为了验证撮合逻辑是否正确，我们需要将回测引擎生成的成交记录保存下来，并与VN Trader的回测结果进行对比。如果结果完全一致，则说明我们的代码没有问题。

以下是保存成交记录的步骤：

![](img/6f3b9b432cd7c0de2517963c74d70aa6_1.png)

首先，在回测运行结束后，我们需要获取保存的成交数据。这些数据存储在 `self.trades` 字典中。

![](img/6f3b9b432cd7c0de2517963c74d70aa6_3.png)

```python
trades = self.trades
```

接下来，我们使用Pandas库将这些数据整理并保存为CSV文件。我们需要遍历所有成交记录，提取关键信息。

```python
import pandas as pd

# 定义要保存的列
columns = [‘vt_orderid‘, ‘symbol‘, ‘exchange‘, ‘direction‘, ‘offset‘, ‘price‘, ‘volume‘, ‘time‘]

# 创建一个空的DataFrame
df = pd.DataFrame(columns=columns)

# 遍历所有成交记录
for trade in list(trades.values()):
    trade_dict = {}
    for key in columns:
        # 获取对应字段的值，如果不存在则默认为0
        value = getattr(trade, key, 0)
        # 处理枚举类型（如exchange, direction, offset），获取其实际值
        if key in [‘exchange‘, ‘direction‘, ‘offset‘]:
            value = value.value
        trade_dict[key] = value
    # 将单条成交记录添加到DataFrame中
    df = df.append(trade_dict, ignore_index=True)

![](img/6f3b9b432cd7c0de2517963c74d70aa6_5.png)

# 保存为CSV文件，使用GBK编码以避免Excel打开乱码
df.to_csv(‘trade_data.csv‘, encoding=‘gbk‘)
```

![](img/6f3b9b432cd7c0de2517963c74d70aa6_7.png)

运行代码后，我们生成了成交记录文件。通过对比发现，我们的结果与VN Trader的回测结果在成交数量和细节上完全一致，这证明了我们的撮合逻辑是正确的。

![](img/6f3b9b432cd7c0de2517963c74d70aa6_9.png)

![](img/6f3b9b432cd7c0de2517963c74d70aa6_11.png)

在测试过程中，我们发现并修复了一个因笔误（多余的逗号导致布尔值被误判为元组）而引发的逻辑错误。这提醒我们，在编写代码时需要格外仔细。

## 添加回测进度显示

在长时间的回测过程中，一个可视化的进度条能让我们了解程序运行状态。我们将在回测引擎中添加一个简单的进度提示功能。

实现思路是：计算已处理K线数量占总K线数量的百分比，每完成10%就输出一次进度。

```python
# 在回测引擎的run_backtesting函数中添加
total_count = len(self.history_data)  # 总K线数量
processed_count = 0                   # 已处理K线计数器
last_percent = 0                      # 上一次输出的百分比

for bar in self.history_data:
    # ... 处理K线的逻辑 ...
    processed_count += 1

    # 计算当前进度百分比（整数，如10代表10%）
    current_percent = int((processed_count / total_count) * 10) * 10

    # 如果进度有更新，则输出
    if current_percent != last_percent:
        # 使用‘#‘号表示进度，例如‘########## 10%‘
        progress_bar = ‘#‘ * (current_percent // 10)
        self.write_log(f‘{progress_bar} {current_percent}%‘)
        last_percent = current_percent
```

添加此功能后，回测运行时会在控制台输出进度信息，使过程更加清晰。

## 完善撤单功能

![](img/6f3b9b432cd7c0de2517963c74d70aa6_13.png)

![](img/6f3b9b432cd7c0de2517963c74d70aa6_15.png)

![](img/6f3b9b432cd7c0de2517963c74d70aa6_17.png)

我们的策略中包含了撤单逻辑（`self.cancel_all`），因此回测引擎也必须支持撤单操作。撤单功能相对简单，核心是从委托字典中移除指定的委托。

![](img/6f3b9b432cd7c0de2517963c74d70aa6_19.png)

![](img/6f3b9b432cd7c0de2517963c74d70aa6_21.png)

![](img/6f3b9b432cd7c0de2517963c74d70aa6_23.png)

撤单分为撤销限价单和停止单，逻辑类似：

```python
def cancel_limit_order(self, strategy, vt_orderid: str):
    """撤销限价单"""
    if vt_orderid in self.limit_orders:
        order = self.limit_orders.pop(vt_orderid)
        order.status = Status.CANCELLED
        # 通知策略，委托已撤销
        strategy.on_order(order)
    else:
        self.write_log(f‘撤单失败，委托{vt_orderid}不存在‘)

def cancel_stop_order(self, strategy, vt_orderid: str):
    """撤销停止单"""
    if vt_orderid in self.stop_orders:
        order = self.stop_orders.pop(vt_orderid)
        order.status = Status.CANCELLED
        # 通知策略，委托已撤销
        strategy.on_order(order)
    else:
        self.write_log(f‘撤单失败，委托{vt_orderid}不存在‘)
```

而 `cancel_all` 函数则是遍历所有委托并逐一撤销：

```python
def cancel_all(self, strategy):
    """撤销所有活动委托"""
    # 撤销所有限价单
    for vt_orderid in list(self.limit_orders.keys()):
        self.cancel_limit_order(strategy, vt_orderid)
    # 撤销所有停止单
    for vt_orderid in list(self.stop_orders.keys()):
        self.cancel_stop_order(strategy, vt_orderid)
```

完成撤单功能后，再次运行回测，我们的成交记录与VN Trader的结果依然保持一致，说明撤单逻辑也已正确实现。

## 关于回测与实盘的差异分析

观察成交记录时，我们发现一个现象：平仓和反手开仓的时间有时会相差一分钟（例如09:00平仓，09:01开仓）。这与策略逻辑（立即反手）似乎不符。

![](img/6f3b9b432cd7c0de2517963c74d70aa6_25.png)

**原因在于回测引擎的机制**：
1.  在K线A（例如14:59）的 `on_bar` 回调中，策略发出平仓委托。
2.  该委托在下一根K线B（09:00）的开盘时被撮合成交，触发 `on_trade` 回调。
3.  在 `on_trade` 回调中，策略才发出反手开仓的委托。
4.  这个开仓委托需要等到再下一根K线C（09:01）的开盘时才能被撮合。

![](img/6f3b9b432cd7c0de2517963c74d70aa6_27.png)

![](img/6f3b9b432cd7c0de2517963c74d70aa6_29.png)

因此，在回测中，反手操作天然会有一根K线的延迟。这是由事件驱动的回测框架结构决定的。在实盘交易中，如果条件满足，平仓和开仓的委托可以几乎同时发出并等待成交，延迟会小得多。

如果想在回测中模拟“同时发出平仓和开仓委托”的效果，就需要修改策略接口和引擎的委托管理逻辑，使其支持以“目标持仓”为导向的指令，这涉及到更复杂的框架设计。目前，我们首先需要理解并遵循VN Trader现有的逻辑。

## 构建统计结果计算类

成交记录正确后，下一步是计算回测的绩效指标。我们创建一个名为 `MyCalculateSt` 的类来负责这项工作。

首先，我们定义这个类需要计算和存储哪些指标：

```python
class MyCalculateSt:
    def __init__(self):
        # 资金相关
        self.capital_init = 1000000  # 初始资金
        self.net_profit = 0.0        # 净利润（不含浮动盈亏）
        self.floating_profit = 0.0   # 浮动盈亏

        # 盈亏比相关
        self.win_loss_rate = 0.0     # 盈亏比
        self.total_win = 0.0         # 总盈利
        self.total_loss = 0.0        # 总亏损

        # 胜率相关
        self.win_rate = 0.0          # 胜率
        self.win_count = 0           # 盈利交易次数
        self.total_trade_count = 0   # 总交易次数

        # 其他参数（可通过参数设置）
        self.size = 10               # 合约乘数
        self.commission_rate = 0.0001 # 手续费率
        self.slippage = 1            # 滑点（跳）
```

我们需要一个方法来处理每一笔成交，并更新这些统计值。核心是计算每笔交易的盈亏、手续费，并分类统计。

```python
    def add_trade(self, trade: TradeData):
        """处理一笔成交，更新统计数据"""
        # 1. 计算该笔成交的成交额
        turnover = trade.volume * trade.price * self.size

        # 2. 计算并累加手续费（此处按双边计算）
        commission = turnover * self.commission_rate
        self.total_commission += commission

        # 3. 计算盈亏（需要在平仓时与开仓价配对计算，此处为简化逻辑示意）
        # ... 实际实现需要维护持仓和开仓均价 ...

        # 4. 更新交易次数
        self.total_trade_count += 1
```

这个类只是一个起点。完整的绩效统计还需要计算夏普比率、最大回撤、年化收益等复杂指标，并且需要正确处理持仓和浮动盈亏的计算。我们可以在此基础上根据需求逐步扩展。

## 总结

本节课中，我们一起完成了以下工作：
1.  **验证了撮合逻辑**：通过将成交记录保存为文件并与VN Trader对比，确认了我们编写的回测引擎核心功能正确无误。
2.  **增强了用户体验**：为回测过程添加了简单的进度显示功能。
3.  **完善了引擎功能**：实现了撤销委托的逻辑，使回测引擎支持更完整的策略操作。
4.  **分析了机制差异**：理解了回测中反手操作存在延迟的原因，明确了回测与实盘的一个关键区别。
5.  **开启了绩效统计**：创建了用于计算回测统计结果的类框架，为后续生成完整的回测报告打下了基础。

![](img/6f3b9b432cd7c0de2517963c74d70aa6_31.png)

至此，我们的回测引擎已经能够正确模拟策略的委托、成交和撤单行为。下一节课，我们将深入实现绩效统计类的具体逻辑，计算出策略的盈亏比、胜率、最大回撤等关键指标。