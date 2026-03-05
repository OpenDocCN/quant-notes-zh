# VNPY课程：第9课：CtaEngine讲解(3) 🚀

![](img/d9fe33a4ab2fc8532d73c6685f0999b1_1.png)

![](img/d9fe33a4ab2fc8532d73c6685f0999b1_3.png)

在本节课中，我们将继续深入讲解CtaEngine，重点分析委托订单（Order）的完整处理流程。我们将从策略发送委托开始，追踪其如何经过引擎处理，最终到达交易接口。理解这个过程对于掌握整个CTA策略的运行机制至关重要。

上一节我们介绍了策略的初始化和启动流程，本节中我们来看看策略如何发送交易委托，以及引擎如何处理这些委托。

## 委托发送流程 📤

在策略模板`CtaTemplate`中，当我们调用`buy`、`sell`、`cover`或`short`方法时，最终都会调用`self.cta_engine.send_order`方法。这个方法接收策略实例本身以及委托方向、价格、数量等信息。

![](img/d9fe33a4ab2fc8532d73c6685f0999b1_5.png)

![](img/d9fe33a4ab2fc8532d73c6685f0999b1_7.png)

以下是`send_order`方法的核心步骤：

![](img/d9fe33a4ab2fc8532d73c6685f0999b1_9.png)

![](img/d9fe33a4ab2fc8532d73c6685f0999b1_11.png)

![](img/d9fe33a4ab2fc8532d73c6685f0999b1_13.png)

1.  **参数检查与处理**：首先检查策略是否已启动（`self.trading == True`）。然后从主引擎获取合约信息，并使用`round_to`函数将委托价格调整至符合交易所最小变动价位的整数倍。
    ```python
    # 价格取整示例
    rounded_price = round_to(price, contract.pricetick)
    ```

2.  **判断委托类型**：根据`stop`参数判断是限价单还是停止单。目前国内期货CTA策略通常只使用限价单，停止单功能主要用于本地模拟。
    *   **限价单**：调用`self.send_limit_order`方法。
    *   **停止单**：调用`self.send_local_stop_order`方法。

![](img/d9fe33a4ab2fc8532d73c6685f0999b1_15.png)

![](img/d9fe33a4ab2fc8532d73c6685f0999b1_17.png)

## 本地停止单处理 🛑

`send_local_stop_order`方法并不立即将委托发送至交易所，而是先在引擎内部进行管理。

以下是其主要操作：

1.  **创建停止单对象**：生成一个唯一的`stop_orderid`，并创建`StopOrder`数据对象，记录委托的所有信息（如合约、方向、价格、数量、关联策略名等），并将其状态设为`WAITING`（等待中）。
2.  **存储停止单**：将此`StopOrder`对象存入`self.stop_orders`字典中，以便后续监控。
    ```python
    # 存储停止单
    self.stop_orders[stop_orderid] = stop_order
    ```
3.  **建立映射关系**：在`self.strategy_orderid_map`中建立策略名到其所有委托单ID（包括停止单ID）的映射，方便后续查询。
4.  **返回停止单ID**：将生成的`stop_orderid`以列表形式返回给策略。

## 停止单的监控与触发 🔄

停止单的触发依赖于行情数据（Tick）。在`CtaEngine`的`process_tick_event`方法中，每次收到新的Tick数据，都会调用`check_stop_order`方法来检查是否有停止单被触发。

以下是触发检查的逻辑：

1.  **遍历所有停止单**：检查`self.stop_orders`字典中每一个停止单。
2.  **条件判断**：判断当前Tick的合约是否与停止单合约一致，并检查价格是否达到触发条件。
    *   **多头停止单触发**：当`direction`为`多`，且当前Tick的`last_price`（最新价）**大于等于**停止单的触发价格`stop_price`时触发。
    *   **空头停止单触发**：当`direction`为`空`，且当前Tick的`last_price`**小于等于**停止单的触发价格`stop_price`时触发。
3.  **转换为限价单**：一旦停止单被触发，引擎会立即将其转换为一个限价单。为了确保快速成交，这个限价单的价格通常设置为涨停价（对于买单）或跌停价（对于卖单）。
4.  **清理与回调**：触发后，该停止单会从`self.stop_orders`字典中移除，其状态更新为`TRIGGERED`（已触发）。同时，引擎会调用策略的`on_stop_order`方法进行通知。

## 限价单的发送 📡

无论是直接发送的限价单，还是由停止单转换而来的限价单，最终都会通过`send_limit_order`方法发送至交易接口。

以下是其核心流程：

1.  **创建委托请求**：将委托信息封装成`OrderRequest`对象。
2.  **委托转换**：通过`converter`（转换器）将`OrderRequest`转换为一个或多个适合底层交易接口的委托请求列表。这一步处理了诸如锁仓、净仓等逻辑。
    ```python
    # 委托转换示例
    req_list = self.converter.convert_order_request(req, lock, net)
    ```
3.  **发送至主引擎**：遍历转换后的委托请求列表，逐个调用`self.main_engine.send_order`方法，将委托发送至对应的交易接口（如CTP）。
4.  **记录映射关系**：交易接口会返回唯一的`vt_orderid`。引擎会建立`vt_orderid`与策略名的双向映射关系（`self.orderid_strategy_map`和`self.strategy_orderid_map`），以便后续成交回报能准确路由到对应的策略。

## 线程池的应用示例 💡

在之前的初始化部分，我们提到了使用`sleep(60)`等待策略初始化完成的方法较为笨拙。一个更优雅的解决方案是使用线程池的`add_done_callback`功能。

以下是使用`concurrent.futures`线程池的改进示例：

```python
from concurrent.futures import ThreadPoolExecutor, Future
from time import sleep
from random import randint

def init_task(strategy_name):
    """模拟初始化一个策略"""
    cost_time = randint(1, 5)  # 模拟耗时1-5秒
    sleep(cost_time)
    print(f"策略 {strategy_name} 初始化完成，耗时 {cost_time} 秒")
    return cost_time  # 返回耗时

def all_done_callback(future):
    """所有任务完成后的回调函数"""
    result = future.result()
    print(f"所有策略初始化完毕，最后一项耗时 {result} 秒")
    # 这里可以执行启动交易等后续操作

# 假设有3个策略需要初始化
strategy_list = ["策略A", "策略B", "策略C"]
init_counter = 0
total_strategies = len(strategy_list)

def single_done_callback(future):
    """单个任务完成后的回调"""
    global init_counter
    init_counter += 1
    result = future.result()
    print(f"回调通知：又完成一个，当前完成 {init_counter}/{total_strategies}，耗时 {result} 秒")
    if init_counter == total_strategies:
        print("所有策略初始化完成，可以启动交易！")

# 创建线程池（最大工作线程数为1，模拟原代码）
with ThreadPoolExecutor(max_workers=1) as executor:
    for s_name in strategy_list:
        # 提交任务，并添加回调函数
        future = executor.submit(init_task, s_name)
        future.add_done_callback(single_done_callback)
```
通过这种方式，我们可以精确地知道所有策略何时初始化完成，而无需固定等待一个可能过长或过短的时间。

## 总结 📝

本节课中我们一起学习了CtaEngine中委托订单的处理全流程。

1.  **委托起源**：策略通过模板方法发起委托。
2.  **路径分叉**：引擎根据委托类型（限价/停止）选择不同的处理路径。
3.  **停止单管理**：停止单在引擎内部被创建和存储，由行情监控触发，触发后转为限价单。
4.  **限价单发送**：所有限价单（包括转换来的）都经过转换器处理，最终通过主引擎发送至底层交易接口。
5.  **映射关系**：引擎维护着委托单ID与策略之间的映射，这是成交回报能够正确回调到对应策略的关键。

![](img/d9fe33a4ab2fc8532d73c6685f0999b1_19.png)

整个流程体现了VN.PY框架清晰的分层设计：策略层负责逻辑，引擎层负责调度、风控和委托管理，网关层负责与交易所通信。理解这一流程，将帮助你在开发策略时更好地把握委托状态，并能在出现问题时进行有效排查。