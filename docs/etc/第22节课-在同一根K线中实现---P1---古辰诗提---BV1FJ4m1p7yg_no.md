# 量化交易：22：在同一根K线中实现反手开仓

![](img/832160b84731723219220f2ef574375c_1.png)

![](img/832160b84731723219220f2ef574375c_3.png)

在本节课中，我们将学习如何修改回测引擎，以解决反手开仓时委托单时间戳不一致的问题，并探讨如何实现更精确的回测逻辑，使其更贴近实盘交易。

![](img/832160b84731723219220f2ef574375c_5.png)

![](img/832160b84731723219220f2ef574375c_7.png)

## 概述与问题回顾

上一节我们介绍了如何汇总回测数据。回顾VNPY的实现，它是通过统计每日开盘和收盘数据来计算盈利交易日等指标的。这体现了不同的设计思路，你可以根据自己的需求、习惯或关注的指标进行计算和模块改造。

在改造模块之前，你需要理解模块的内部逻辑。如果你从本系列课程开始，一步步跟随并写出了自己的回测引擎 `my_btesting`，那么理解VNPY中的许多组件将不再困难。核心在于将复杂逻辑拆解，再根据自己的思路重组。

例如，我们之前反复提到的一个问题：在成交记录中，开仓时间总是显示为 `09:01`，而反手操作的时间戳却不一致。

![](img/832160b84731723219220f2ef574375c_1.png)

![](img/832160b84731723219220f2ef574375c_9.png)

这个问题出现在成交记录部分。

![](img/832160b84731723219220f2ef574375c_11.png)

![](img/832160b84731723219220f2ef574375c_13.png)

![](img/832160b84731723219220f2ef574375c_15.png)

![](img/832160b84731723219220f2ef574375c_3.png)

开仓委托总是记录在 `09:01`。

![](img/832160b84731723219220f2ef574375c_5.png)

这种反手逻辑是不正确的。即使你将平仓和开仓（例如先 `sell` 后 `buy`，或先 `sell` 后 `short`）写在一起，也会出现问题。

![](img/832160b84731723219220f2ef574375c_7.png)

这与我们实际的成交逻辑不符。那么，如何解决这个问题呢？其实方法相对简单，但需要修改多处代码。

## 解决方案：委托单关联标识

本节中，我们来看看如何通过为委托单添加关联标识来解决反手时序问题。

首先，策略层需要修改。修改的核心逻辑是：为委托单添加一个标识，以明确其顺序依赖关系。一个简单的实现思路是：当一个委托单（例如平多单）发出后，它会有一个 `vt_orderid`。我们可以将这个 `id` 作为标记，添加到下一个关联的委托单（例如开空单 `short`）上。这意味着，只有在前一个委托单成交之后，才会尝试撮合后一个委托单。这样就建立了先后顺序。

当然，要实现一个完善的系统，你需要考虑更多边界情况。但基于这个逻辑，我们就可以开始改造代码了。

我们需要修改的地方包括：
1.  CTA策略模板 (`cta_template`)
2.  具体策略逻辑
3.  回测引擎 (`my_btesting`) 中的撮合逻辑

我们不直接修改VNPY底层代码，而是通过继承和重写的方式来实现。

### 第一步：修改CTA策略模板

我们从 `vm.py` 的CTA策略模板中创建一个新的模板文件。

```python
# 在 template.py 中
from vnpy_ctabacktester.engine import CtaTemplate
from typing import Optional

class MyCtaTemplate(CtaTemplate):
    def buy(self, price: float, volume: float, stop: bool = False, corr: Optional[str] = None) -> str:
        """买入开仓"""
        # 发送委托逻辑，返回 vt_orderid
        vt_orderid = self.send_order("buy", price, volume, stop, corr)
        return vt_orderid

    def sell(self, price: float, volume: float, stop: bool = False, corr: Optional[str] = None) -> str:
        """卖出平仓"""
        vt_orderid = self.send_order("sell", price, volume, stop, corr)
        return vt_orderid

    def short(self, price: float, volume: float, stop: bool = False, corr: Optional[str] = None) -> str:
        """卖出开仓"""
        vt_orderid = self.send_order("short", price, volume, stop, corr)
        return vt_orderid

    def cover(self, price: float, volume: float, stop: bool = False, corr: Optional[str] = None) -> str:
        """买入平仓"""
        vt_orderid = self.send_order("cover", price, volume, stop, corr)
        return vt_orderid

    def send_order(self, direction: str, price: float, volume: float, stop: bool, corr: Optional[str]) -> str:
        # 这里是具体的委托发送逻辑，会调用底层的 engine
        # 需要将 corr 参数传递下去
        # 返回生成的 vt_orderid
        pass
```
主要改动是：
*   在所有开平仓方法中增加了 `corr` (关联) 参数，类型为 `Optional[str]`，用于传递前置委托单的 `vt_orderid`。
*   方法返回值从列表改为单个字符串 `vt_orderid`。

### 第二步：修改策略逻辑

接下来，我们需要在具体的策略中使用新的模板和方法。

```python
# 在策略文件 avg_demo.py 中
from my_template import MyCtaTemplate  # 导入自定义模板

class AvgDemoStrategy(MyCtaTemplate):
    def on_bar(self, bar):
        # 策略逻辑判断...
        if 满足平多条件:
            # 平多单，获取其 vt_orderid
            vt_orderid_sell = self.sell(bar.close_price, abs(self.pos), stop=True)
            # 紧接着反手开空，并将平多单的id作为关联标识
            if vt_orderid_sell:
                self.short(bar.close_price, self.fixed_size, stop=True, corr=vt_orderid_sell)
        # 同理处理其他情况...
```
策略层的修改很简洁：在反手开仓时，将前一个平仓委托的 `vt_orderid` 通过 `corr` 参数传递给开仓委托。

### 第三步：修改回测引擎撮合逻辑

最后，也是最关键的一步，是在回测引擎 `my_btesting` 中处理这个关联标识。

![](img/832160b84731723219220f2ef574375c_17.png)

以下是需要修改的核心部分：

![](img/832160b84731723219220f2ef574375c_19.png)

![](img/832160b84731723219220f2ef574375c_21.png)

```python
# 在 my_btesting.py 的引擎类中
class MyBacktestingEngine:
    def __init__(self):
        self.corr_orders = {}  # 用于存储有关联关系的委托单，key为corr_id，value为委托单对象

    def send_limit_order(self, order_data, corr: Optional[str] = None):
        """发送限价单"""
        if not corr:
            # 如果没有关联，直接加入待撮合列表
            self.active_limit_orders[order_data.vt_orderid] = order_data
        else:
            # 有关联，先存储起来，等待前置订单成交
            self.corr_orders[corr] = order_data

    def send_stop_order(self, stop_order_data, corr: Optional[str] = None):
        """发送停止单（触价后转限价单）"""
        # 逻辑同 send_limit_order
        if not corr:
            # ... 直接处理
            pass
        else:
            self.corr_orders[corr] = stop_order_data

    def match_limit_order(self, bar):
        """撮合限价单"""
        # 原有的撮合逻辑...
        for order_id, order in list(self.active_limit_orders.items()):
            if self.check_match(order, bar):
                # 成交处理...
                self.on_order(order)

                # 关键：检查成交的订单是否是其他订单的前置订单
                if order_id in self.corr_orders:
                    # 取出被关联的订单
                    corr_order = self.corr_orders.pop(order_id)
                    # 将被关联的订单放入活跃列表，立即尝试撮合（因为用的是同一根Bar）
                    if isinstance(corr_order, OrderData):
                        self.active_limit_orders[corr_order.vt_orderid] = corr_order
                        # 递归调用，尝试撮合刚激活的订单
                        self.match_limit_order(bar)
                    elif isinstance(corr_order, StopOrder):
                        # 如果是停止单，则触发其转限价单的逻辑
                        self.process_stop_order(corr_order, bar)
        # ... 其他逻辑

    def on_order(self, order):
        """订单成交回调"""
        # 订单成交后的处理，例如更新持仓等
        # 同时，这里可以触发策略的 on_trade 方法
        pass
```
核心逻辑是：
1.  在 `send_limit_order` 和 `send_stop_order` 中，如果订单有 `corr` 标识，则不立即放入活跃订单池，而是存入 `self.corr_orders` 字典。
2.  在 `match_limit_order` 撮合过程中，每当有一个订单成交，就检查其 `order_id` 是否在 `self.corr_orders` 的键中。
3.  如果在，则取出被它关联的订单，并将其放入活跃订单池，然后**立即在同一根K线数据 (`bar`)** 中尝试撮合它。这样就保证了反手操作能在同一根K线内完成。

![](img/832160b84731723219220f2ef574375c_23.png)

![](img/832160b84731723219220f2ef574375c_24.png)

## 回测演示与结果对比

按照上述步骤修改代码后，我们进行回测。

以下是回测结果的对比要点：
*   **问题解决**：之前反手开仓记录为 `09:01`，而平仓记录为 `09:00` 的问题得到解决。现在反手开仓和平仓的时间戳可以保持一致（或在同一分钟內，取决于具体撮合价位）。
*   **结果差异**：即使时间戳只差一分钟，也可能导致后续一系列交易信号发生变化，最终使得回测结果（如总盈利）与修改前产生差异。本例中，盈利结果从 `2200` 变为 `2670`。
*   **重要性**：这个修改使得回测逻辑更加精确。许多实盘与回测结果不符的情况，根源就在于回测引擎中的逻辑瑕疵未能模拟出实盘订单的严格时序。理解并修正这些细节，是让回测贴近实盘的关键一步。

## 扩展思考：让回测完全贴合实盘

解决了反手时序问题后，我们可以进一步思考如何让回测与实盘启动状态完全一致。

例如，一个ATR策略或三均线策略，其进场和出场信号强烈依赖于启动时的状态。如果你在实盘中途启动策略，策略引擎加载的历史数据有限，计算出的指标和信号点会与从历史起点开始完整回测的结果完全不同，导致“踏错节奏”。

一个解决方案是：在策略模板中增加状态缓存。

```python
class MyCtaTemplate(CtaTemplate):
    def __init__(self, cta_engine, strategy_name, vt_symbol, setting):
        super().__init__(cta_engine, strategy_name, vt_symbol, setting)
        self.pos_history = []  # 缓存历史持仓状态
        self.entry_price_history = []  # 缓存历史入场价格

    def on_bar(self, bar):
        # 1. 在策略逻辑前，先用完整历史数据更新缓存
        # 2. 开仓前，检查 self.pos_history 和 self.pos
        # 3. 只有当缓存中的历史持仓为0，且当前计算信号为开仓时，才执行开仓
        # 这样可以模拟“从历史起点开始一直运行”的状态
        pass
```
你需要根据策略的具体逻辑来设计状态缓存和同步机制。这没有统一答案，但思路是：**在回测中模拟策略从合约上市第一天起就开始运行的全部状态**，并在“实盘启动点”进行状态同步，从而保证后续信号的一致性。

## 知识延伸：多进程穷举回测

在掌握了回测引擎的核心后，实现参数穷举回测就变得非常简单。这本质上是一个**计算密集型**任务，适合使用多进程并行处理。

以下是使用Python标准库 `concurrent.futures` 进行多进程回测的框架：

```python
import os
from concurrent.futures import ProcessPoolExecutor, as_completed
from my_btesting import MyBacktestingEngine  # 你的回测引擎
from avg_demo import AvgDemoStrategy  # 你的策略

def run_backtest(params):
    """单个回测任务"""
    # params 是一个字典，包含策略参数
    setting = params
    engine = MyBacktestingEngine()
    engine.add_strategy(AvgDemoStrategy, setting)
    engine.load_data(...)
    engine.run_backtesting()
    result = engine.calculate_result()
    return result  # 返回回测结果

def main():
    # 1. 生成所有需要测试的参数组合列表
    all_param_settings = [
        {'fast_period': 5, 'slow_period': 20},
        {'fast_period': 10, 'slow_period': 30},
        # ... 更多参数组合
    ]

    results = []
    # 2. 创建进程池，最大进程数设为CPU核心数
    max_workers = os.cpu_count()
    with ProcessPoolExecutor(max_workers=max_workers) as executor:
        # 3. 提交所有任务
        future_to_params = {executor.submit(run_backtest, params): params for params in all_param_settings}

        # 4. 异步获取结果
        for future in as_completed(future_to_params):
            params = future_to_params[future]
            try:
                result = future.result()
                results.append((params, result))
                print(f"参数 {params} 回测完成，结果: {result}")
            except Exception as exc:
                print(f"参数 {params} 回测产生异常: {exc}")

    # 5. 所有任务完成后，分析结果，找出最优参数
    best_result = max(results, key=lambda x: x[1]['total_return'])
    print(f"最优参数: {best_result[0]}, 最佳收益: {best_result[1]}")
```
这个框架清晰地将参数生成、任务分配、并行执行和结果收集分离。你只需要将核心的回测逻辑封装进 `run_backtest` 函数即可。

## 总结

本节课中，我们一起学习了如何通过修改回测引擎来解决反手开仓的时序问题。我们分三步走：修改策略模板以支持委托单关联、在策略逻辑中传递关联标识、最后在回测引擎的撮合逻辑中实现依赖订单的延迟激活与即时撮合。

这个过程不仅解决了一个具体问题，更重要的是展示了理解并改造回测引擎内部逻辑的方法。当你深入掌握了这些原理，再看VNPY或其他量化框架的源码，就会发现它们并没有想象中那么复杂。你获得了根据自身需求定制回测逻辑的能力，这是让回测结果真正可信、让策略得以优化的基础。

![](img/832160b84731723219220f2ef574375c_26.png)

此外，我们还探讨了让回测完全贴合实盘的思路，并介绍了利用多进程进行参数穷举回测的框架。希望你能将这些知识融会贯通，构建出更强大、更精准的量化交易系统。