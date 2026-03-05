# 从零开始量化：5：CtpGateway(3) - 委托与数据解析 🔧

在本节课中，我们将深入探讨CtpGateway中委托发送（Order）的核心流程，并学习如何解析和处理从交易所接收到的原始行情数据。理解这些底层交互是构建稳定量化交易系统的基础。

上一节我们介绍了CtpGateway的连接与查询功能，本节中我们来看看如何发送委托单并处理其反馈。

## 委托发送流程 📤

从策略层发出的委托请求，需要经过多层转换，最终才能被交易所服务器接收和处理。

### 委托数据类型

在CtpGateway底层，发送委托时接收的是 `OrderRequest` 数据类型。这是一个数据类（Data Class），用于封装委托所需的所有信息。

**核心字段包括：**
*   `symbol`: 合约代码
*   `exchange`: 交易所
*   `direction`: 方向（多/空）
*   `type`: 委托类型
*   `volume`: 数量
*   `price`: 价格
*   `offset`: 开平方向

需要注意的是，`type`（委托类型）在底层接口中主要支持 `LIMIT`（限价单）和 `MARKET`（市价单）。虽然VNPY的策略引擎层可能提供其他高级订单类型（如停止单`STOP`），但最终在发送到CTP接口时，都会转换为 `LIMIT` 单。

### 数据转换与发送

`OrderRequest` 的作用是向服务器发送请求。发送成功后，需要将其转换为 `OrderData` 类型，并放入事件引擎（Event Engine），以便策略或其他模块接收和处理。

`OrderData` 是存储在事件引擎中的委托数据表示。它继承自 `BaseData`，包含 `gateway_name` 和额外信息字段。其状态（`status`）会随着委托生命周期变化，例如从 `SUBMITTING`（提交中）变为 `ALLTRADED`（全部成交）。

以下是委托数据的核心转换流程：

1.  **策略层调用**：策略通过类似 `cta_engine.send_order()` 的方法发起委托。
2.  **转换为OrderRequest**：委托参数被组装成 `OrderRequest` 对象。
3.  **网关层处理**：在 `CtpGateway` 中，`OrderRequest` 的数据被转换为符合CTP C++接口要求的字典格式。这涉及一系列映射转换，例如将“开多”方向转换为数字“0”。
4.  **调用底层API**：转换后的字典通过 `td_api.SendOrder()` 方法发送给交易所服务器。
5.  **生成OrderData**：如果服务器接收成功，`OrderRequest` 会通过 `.create_order_data()` 方法生成一个状态为 `SUBMITTING` 的 `OrderData` 对象。
6.  **放入事件引擎**：生成的 `OrderData` 被放入事件引擎。策略可以通过 `on_order()` 回调函数接收到这个委托单，并获得一个唯一的 `vt_orderid`。

### 委托反馈处理

委托发送后，服务器会返回各种回调信息。

*   **委托状态更新**：通过 `on_rtn_order()` 方法处理。此方法将服务器返回的字典数据更新到对应的 `OrderData` 对象中（如状态变为部分成交`PARTTRADED`），然后推入事件引擎。
*   **委托失败**：通过 `on_rsp_order_insert()` 方法处理。它会创建一个状态为 `REJECTED`（拒单）的 `OrderData` 并推入事件引擎。
*   **撤单操作**：撤单请求使用 `CancelRequest` 数据类型，主要包含 `orderid`。其流程与下单类似，通过 `td_api.CancelOrder()` 发送。撤单结果也会通过 `on_rtn_order()` 或撤单失败回调 `on_rsp_order_action()` 来更新 `OrderData` 状态。

![](img/d958c6636d40d7d31dd48deb433dca0f_1.png)

## 行情数据解析 📊

![](img/d958c6636d40d7d31dd48deb433dca0f_3.png)

理解原始数据的格式对于正确合成K线、进行数据分析至关重要。

### 获取原始数据

鼓励初学者直接输出并保存原始的行情字典数据，以观察其真实结构。这能帮助理解代码为何要进行某些特定的处理。

![](img/d958c6636d40d7d31dd48deb433dca0f_5.png)

你可以在 `MdApi` 的 `on_rtn_depth_market_data()` 方法中，添加将原始 `data`（字典类型）保存到本地文件的逻辑。例如：

![](img/d958c6636d40d7d31dd48deb433dca0f_7.png)

```python
# 示例：在on_rtn_depth_market_data方法内添加数据记录
original_event = Event(type=EVENT_ORIGINAL_TICK, data=data)
self.gateway.event_engine.put(original_event)
```

然后，在事件引擎中处理这个事件，将 `data` 字典以文本形式写入文件。通过分析一天的数据，你可以直观看到tick的频率、字段含义以及不同交易所的差异。

![](img/d958c6636d40d7d31dd48deb433dca0f_9.png)

### 关键处理逻辑

![](img/d958c6636d40d7d31dd48deb433dca0f_11.png)

在 `on_rtn_depth_market_data()` 方法中，有几个重要的数据处理步骤：

1.  **合约校验**：根据收到的合约代码（`symbol`），从之前连接时获取的合约信息映射表（`symbol_contract_map`）中查询对应的 `ContractData`。如果合约不存在，则忽略此笔数据。
2.  **日期纠正**：针对大连商品交易所（DCE）的夜盘数据，其 `ActionDay` 字段在夜盘时段会指向下一个交易日。代码中对此进行了特殊处理，将其纠正为当前日期，以保证数据日期记录的连续性。
3.  **时间戳合成**：将 `ActionDay`（日期）、`UpdateTime`（时间）和 `UpdateMillisec`（毫秒）三个字段拼接成一个完整的时间戳字符串，并转换为Python的 `datetime` 对象，方便后续处理。

这些处理逻辑都是为了将交易所传来的、可能带有特殊规则或格式的原始数据，标准化为程序内部统一、易于处理的数据格式。

![](img/d958c6636d40d7d31dd48deb433dca0f_13.png)

本节课中我们一起学习了CtpGateway中委托发送的完整流程，从策略发起、数据转换、API调用到状态反馈。同时，我们也探讨了如何解析和处理原始的行情数据，并强调了查看原始数据对于理解代码和排查问题的重要性。掌握这些底层交互细节，是迈向高级量化交易开发的坚实一步。