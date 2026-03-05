# 从零开始量化：第10课：CtaEngine讲解(4) - 委托发送、撤销与反馈 🔄

![](img/29eac973bbb958b710be05dc9a782807_1.png)

![](img/29eac973bbb958b710be05dc9a782807_3.png)

![](img/29eac973bbb958b710be05dc9a782807_5.png)

![](img/29eac973bbb958b710be05dc9a782807_7.png)

![](img/29eac973bbb958b710be05dc9a782807_9.png)

![](img/29eac973bbb958b710be05dc9a782807_11.png)

![](img/29eac973bbb958b710be05dc9a782807_13.png)

![](img/29eac973bbb958b710be05dc9a782807_15.png)

![](img/29eac973bbb958b710be05dc9a782807_17.png)

![](img/29eac973bbb958b710be05dc9a782807_19.png)

在本节课中，我们将深入讲解CtaEngine中委托发送、撤销以及反馈的完整流程。我们将重点关注`send_order`、`cancel_order`和`process_order_event`等核心方法，理解委托单如何在策略、引擎和底层网关之间流转。

![](img/29eac973bbb958b710be05dc9a782807_21.png)

![](img/29eac973bbb958b710be05dc9a782807_23.png)

![](img/29eac973bbb958b710be05dc9a782807_25.png)

![](img/29eac973bbb958b710be05dc9a782807_27.png)

![](img/29eac973bbb958b710be05dc9a782807_29.png)

![](img/29eac973bbb958b710be05dc9a782807_31.png)

![](img/29eac973bbb958b710be05dc9a782807_33.png)

上一节我们介绍了委托发送的起点，本节中我们来看看委托的撤销与状态反馈是如何实现的。

![](img/29eac973bbb958b710be05dc9a782807_35.png)

![](img/29eac973bbb958b710be05dc9a782807_36.png)

![](img/29eac973bbb958b710be05dc9a782807_38.png)

![](img/29eac973bbb958b710be05dc9a782807_40.png)

![](img/29eac973bbb958b710be05dc9a782807_42.png)

![](img/29eac973bbb958b710be05dc9a782807_44.png)

![](img/29eac973bbb958b710be05dc9a782807_45.png)

## 委托撤销：cancel_order

![](img/29eac973bbb958b710be05dc9a782807_47.png)

![](img/29eac973bbb958b710be05dc9a782807_49.png)

![](img/29eac973bbb958b710be05dc9a782807_51.png)

![](img/29eac973bbb958b710be05dc9a782807_53.png)

![](img/29eac973bbb958b710be05dc9a782807_55.png)

当需要撤销一个委托时，会调用`cancel_order`方法。该方法首先判断委托单的类型。

![](img/29eac973bbb958b710be05dc9a782807_57.png)

![](img/29eac973bbb958b710be05dc9a782807_59.png)

![](img/29eac973bbb958b710be05dc9a782807_61.png)

![](img/29eac973bbb958b710be05dc9a782807_63.png)

![](img/29eac973bbb958b710be05dc9a782807_65.png)

![](img/29eac973bbb958b710be05dc9a782807_67.png)

![](img/29eac973bbb958b710be05dc9a782807_69.png)

```python
def cancel_order(self, vt_orderid: str):
    # 判断是否为停止单
    if vt_orderid.startswith("STOP"):
        self.cancel_local_stop_order(vt_orderid)
    else:
        self.cancel_server_order(vt_orderid)
```

![](img/29eac973bbb958b710be05dc9a782807_71.png)

![](img/29eac973bbb958b710be05dc9a782807_73.png)

![](img/29eac973bbb958b710be05dc9a782807_75.png)

![](img/29eac973bbb958b710be05dc9a782807_77.png)

![](img/29eac973bbb958b710be05dc9a782807_79.png)

### 撤销本地停止单

![](img/29eac973bbb958b710be05dc9a782807_81.png)

如果`vt_orderid`以`"STOP"`前缀开始，说明这是一个本地停止单。停止单保存在CtaEngine内部的`self.stop_orders`字典中，并未发送到服务器。

![](img/29eac973bbb958b710be05dc9a782807_83.png)

![](img/29eac973bbb958b710be05dc9a782807_85.png)

![](img/29eac973bbb958b710be05dc9a782807_87.png)

![](img/29eac973bbb958b710be05dc9a782807_89.png)

![](img/29eac973bbb958b710be05dc9a782807_91.png)

![](img/29eac973bbb958b710be05dc9a782807_93.png)

![](img/29eac973bbb958b710be05dc9a782807_95.png)

以下是撤销本地停止单的步骤：

![](img/29eac973bbb958b710be05dc9a782807_97.png)

1.  **从停止单字典中移除**：在`self.stop_orders`字典中找到对应的停止单对象并将其弹出（pop）。这意味着该停止单将不再参与后续`process_tick_event`中的`check_stop_order`检查，不会被触发。
2.  **从策略委托映射中移除**：每个策略都有一个列表（在`self.strategy_orderid_map`中），记录其发出的所有委托单号（包括停止单和限价单）。需要从这个列表中删除该停止单的`vt_orderid`。
3.  **更新停止单状态并回调策略**：将停止单的状态改为`CANCELLED`，然后调用策略的`on_stop_order`方法，通知策略该停止单已被撤销。

![](img/29eac973bbb958b710be05dc9a782807_99.png)

![](img/29eac973bbb958b710be05dc9a782807_101.png)

![](img/29eac973bbb958b710be05dc9a782807_103.png)

![](img/29eac973bbb958b710be05dc9a782807_105.png)

![](img/29eac973bbb958b710be05dc9a782807_107.png)

**需要注意**：如果停止单已经触发并转换成了限价单，那么它已经从`self.stop_orders`中被移除。此时撤销操作将在后续的`cancel_server_order`中处理。

![](img/29eac973bbb958b710be05dc9a782807_109.png)

![](img/29eac973bbb958b710be05dc9a782807_111.png)

![](img/29eac973bbb958b710be05dc9a782807_113.png)

![](img/29eac973bbb958b710be05dc9a782807_115.png)

![](img/29eac973bbb958b710be05dc9a782807_117.png)

![](img/29eac973bbb958b710be05dc9a782807_119.png)

### 撤销服务器委托单

![](img/29eac973bbb958b710be05dc9a782807_121.png)

![](img/29eac973bbb958b710be05dc9a782807_123.png)

![](img/29eac973bbb958b710be05dc9a782807_125.png)

![](img/29eac973bbb958b710be05dc9a782807_127.png)

对于已经发送到底层服务器的限价单（包括由停止单转换而来的限价单），调用`cancel_server_order`。

```python
def cancel_server_order(self, vt_orderid: str):
    # 调用主引擎的cancel_order方法，最终转发至CTP网关
    self.main_engine.cancel_order(vt_orderid, self.gateway_name)
```

这个过程是向底层CTP网关发送一个撤单请求。如果撤单失败，底层会通过`on_order`回调返回一个错误信息。

在`cancel_order`方法中，有一个关键细节：**使用`.copy()`**。

```python
def cancel_all(self, strategy_name: str):
    orderid_list = self.strategy_orderid_map[strategy_name].copy()
    for vt_orderid in orderid_list:
        self.cancel_order(vt_orderid)
```

![](img/29eac973bbb958b710be05dc9a782807_129.png)

![](img/29eac973bbb958b710be05dc9a782807_131.png)

![](img/29eac973bbb958b710be05dc9a782807_133.png)

![](img/29eac973bbb958b710be05dc9a782807_135.png)

**为什么需要copy？**
因为`self.strategy_orderid_map[strategy_name]`是一个列表（可变对象）。在遍历这个列表并执行撤单操作的过程中，列表内容可能会发生变化（例如，某个委托单成交后被自动移除）。如果直接遍历原列表，可能会引发运行时错误或产生不可预知的行为。使用`.copy()`创建列表的副本进行遍历，可以避免这种问题。

![](img/29eac973bbb958b710be05dc9a782807_137.png)

![](img/29eac973bbb958b710be05dc9a782807_139.png)

## 委托反馈：process_order_event

![](img/29eac973bbb958b710be05dc9a782807_141.png)

![](img/29eac973bbb958b710be05dc9a782807_143.png)

![](img/29eac973bbb958b710be05dc9a782807_145.png)

![](img/29eac973bbb958b710be05dc9a782807_147.png)

![](img/29eac973bbb958b710be05dc9a782807_149.png)

![](img/29eac973bbb958b710be05dc9a782807_151.png)

委托单的状态更新（如成交、拒单、撤单成功）通过`process_order_event`方法处理。这些事件来源于底层网关（如CTP）的`on_order`回调。

![](img/29eac973bbb958b710be05dc9a782807_153.png)

![](img/29eac973bbb958b710be05dc9a782807_155.png)

![](img/29eac973bbb958b710be05dc9a782807_157.png)

![](img/29eac973bbb958b710be05dc9a782807_159.png)

![](img/29eac973bbb958b710be05dc9a782807_161.png)

![](img/29eac973bbb958b710be05dc9a782807_163.png)

![](img/29eac973bbb958b710be05dc9a782807_165.png)

以下是该方法的核心流程：

![](img/29eac973bbb958b710be05dc9a782807_167.png)

![](img/29eac973bbb958b710be05dc9a782807_169.png)

![](img/29eac973bbb958b710be05dc9a782807_171.png)

![](img/29eac973bbb958b710be05dc9a782807_173.png)

![](img/29eac973bbb958b710be05dc9a782807_175.png)

![](img/29eac973bbb958b710be05dc9a782807_177.png)

![](img/29eac973bbb958b710be05dc9a782807_179.png)

1.  **数据转换**：通过转换器（Converter）处理原始的`order_data`。
2.  **查找对应策略**：利用`self.orderid_strategy_map`，通过`vt_orderid`快速找到发出该委托的策略。
3.  **更新活跃委托列表**：从`self.strategy_orderid_map[strategy]`列表中找到该`vt_orderid`。如果委托状态已不再活跃（即不是`SUBMITTING`, `NOTTRADED`, `PARTTRADED`），则将其从列表中删除。
4.  **回调策略**：调用策略的`on_order`方法，将更新后的委托信息传递给策略。

![](img/29eac973bbb958b710be05dc9a782807_181.png)

![](img/29eac973bbb958b710be05dc9a782807_183.png)

![](img/29eac973bbb958b710be05dc9a782807_185.png)

![](img/29eac973bbb958b710be05dc9a782807_187.png)

**重要说明**：从CTP等底层网关返回的委托单类型（`order.type`）只可能是`LIMIT`（限价）或其他交易所支持的类型，**绝不可能是`STOP`**。因为停止单是CtaEngine内部的概念，只在引擎层面管理，不会下发给服务器。因此，代码中判断`order.type == STOP`的分支，在期货交易中实际上不会被执行，可以忽略。

![](img/29eac973bbb958b710be05dc9a782807_189.png)

![](img/29eac973bbb958b710be05dc9a782807_191.png)

![](img/29eac973bbb958b710be05dc9a782807_193.png)

![](img/29eac973bbb958b710be05dc9a782807_195.png)

![](img/29eac973bbb958b710be05dc9a782807_197.png)

![](img/29eac973bbb958b710be05dc9a782807_199.png)

![](img/29eac973bbb958b710be05dc9a782807_201.png)

## 核心流程梳理与总结

![](img/29eac973bbb958b710be05dc9a782807_203.png)

![](img/29eac973bbb958b710be05dc9a782807_205.png)

![](img/29eac973bbb958b710be05dc9a782807_207.png)

![](img/29eac973bbb958b710be05dc9a782807_209.png)

![](img/29eac973bbb958b710be05dc9a782807_211.png)

![](img/29eac973bbb958b710be05dc9a782807_213.png)

让我们将发送、撤销和反馈的整个流程串联起来：

![](img/29eac973bbb958b710be05dc9a782807_215.png)

![](img/29eac973bbb958b710be05dc9a782807_217.png)

![](img/29eac973bbb958b710be05dc9a782807_219.png)

![](img/29eac973bbb958b710be05dc9a782807_221.png)

1.  **策略发出委托**：策略调用`buy`/`sell`/`short`/`cover`方法。
2.  **CtaEngine处理**：
    *   **停止单**：被放入`self.stop_orders`字典，等待行情触发。
    *   **限价单**：被转换为标准格式，通过`main_engine`->`gateway`->`TdApi`发送至交易所。
    *   **记录映射关系**：在`self.orderid_strategy_map`和`self.strategy_orderid_map`中分别记录委托与策略的对应关系。
3.  **停止单触发**：行情触发停止单条件，CtaEngine将其从`self.stop_orders`移除，并**调用策略的`on_stop_order`**。同时，生成一个限价单并发送至服务器。
4.  **服务器回报**：交易所返回委托状态变化（成交、撤单等）。
5.  **CtaEngine处理回报**：`process_order_event`接收回报，更新内部映射关系，并**调用策略的`on_order`**。
6.  **策略撤销委托**：策略调用`cancel_order`。
    *   若是停止单，CtaEngine内部撤销并回调`on_stop_order`。
    *   若是服务器限价单，转发撤单请求至交易所。

![](img/29eac973bbb958b710be05dc9a782807_223.png)

![](img/29eac973bbb958b710be05dc9a782807_225.png)

![](img/29eac973bbb958b710be05dc9a782807_227.png)

![](img/29eac973bbb958b710be05dc9a782807_229.png)

![](img/29eac973bbb958b710be05dc9a782807_231.png)

![](img/29eac973bbb958b710be05dc9a782807_233.png)

![](img/29eac973bbb958b710be05dc9a782807_235.png)

**关键点记忆**：
*   **`on_stop_order`**：仅在**停止单**被触发、撤销或拒单时，由CtaEngine调用。
*   **`on_order`**：在**任何发送到服务器的委托单**（包括由停止单转换来的限价单）状态发生变化时，由CtaEngine调用。
*   **数据一致性**：`self.stop_orders`、`self.orderid_strategy_map`、`self.strategy_orderid_map`这几个数据结构共同维护着委托单的生命周期和归属关系，确保引擎能正确地将委托事件路由到对应的策略。

![](img/29eac973bbb958b710be05dc9a782807_237.png)

![](img/29eac973bbb958b710be05dc9a782807_239.png)

![](img/29eac973bbb958b710be05dc9a782807_241.png)

![](img/29eac973bbb958b710be05dc9a782807_243.png)

![](img/29eac973bbb958b710be05dc9a782807_245.png)

![](img/29eac973bbb958b710be05dc9a782807_247.png)

![](img/29eac973bbb958b710be05dc9a782807_249.png)

本节课中我们一起学习了CtaEngine中委托管理的后半部分——撤销与反馈。我们明确了停止单和服务器限价单在撤销流程上的区别，理解了委托状态如何从交易所反馈至策略层，并掌握了`on_order`与`on_stop_order`两个关键回调的触发时机。理解这些流程对于编写能正确处理委托、管理仓位的策略至关重要。