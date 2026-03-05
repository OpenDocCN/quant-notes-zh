# CTP网关开发：第4课：CtpGateway(2) - P1

![](img/c970a6594a5d6ffaa192c526420c9f33_1.png)

在本节课中，我们将深入解析`CtpGateway`类的核心结构与工作流程。我们将重点关注其继承关系、连接登录机制、数据查询逻辑以及底层API的调用方式，帮助你理解一个交易网关是如何构建和运作的。

![](img/c970a6594a5d6ffaa192c526420c9f33_3.png)

上一节我们介绍了`CtpGateway`的基本框架，本节中我们来看看其具体的实现细节。

## 继承与接口规范

`CtpGateway`类继承自`BaseGateway`。`BaseGateway`本身又继承自`ABC`（抽象基类）。这样设计是为了规定所有网关必须实现的统一接口。

`BaseGateway`位于`vnpy`包的`gateway`模块中，它定义了交易接口的标准方法。任何具体的网关实现（如CTP、飞马等）都必须继承它，并重写其中被`@abstractmethod`装饰的方法。

以下是必须实现的核心方法：
*   `connect()`: 连接服务器
*   `close()`: 关闭连接
*   `subscribe()`: 订阅行情
*   `send_order()`: 发送委托
*   `cancel_order()`: 撤销委托

![](img/c970a6594a5d6ffaa192c526420c9f33_5.png)

`BaseGateway`并非纯粹的抽象类，它还实现了一个关键功能：`push_event()`。这个方法负责将数据（如Tick、Trade）推送到全局的事件引擎（`main_engine.event_engine`）中，供策略或其他模块消费。

## 类属性与初始化

`CtpGateway`定义了一些类属性，如默认名称和交易所映射。

![](img/c970a6594a5d6ffaa192c526420c9f33_7.png)

```python
default_name = "CTP"
default_setting = {...}  # 连接所需的参数字典
exchanges = [Exchange.SHFE, Exchange.INE, ...]  # 支持的交易所列表
```

![](img/c970a6594a5d6ffaa192c526420c9f33_9.png)

在`__init__`构造函数中，主要工作是创建两个核心对象：
*   `self.md_api`: 行情接口实例，继承自`MdApi`。
*   `self.td_api`: 交易接口实例，继承自`TdApi`。

`CtpGateway`本身像一个外壳或管理器，具体的行情订阅和交易操作都委托给这两个底层API对象执行。构造函数中`TdApi`类名加了引号，是因为在代码顺序上`TdApi`类定义在`CtpGateway`之后，这是一种解决前向引用的常见做法。

## 连接与登录流程

连接服务器并非一步完成，它模拟了网页登录的过程：先建立连接，再验证身份。

1.  **连接 (`connect`)**: 调用`md_api.connect()`和`td_api.connect()`。此步骤会与CTP前置服务器建立网络连接。连接成功后，底层API会通过回调函数`on_front_connected`通知网关。
2.  **登录 (`login`)**: 收到连接成功的回调后，网关再调用`md_api.login()`和`td_api.login()`，传入用户名、密码等参数进行认证。

![](img/c970a6594a5d6ffaa192c526420c9f33_11.png)

如果连接失败，底层会回调`on_front_disconnected`并返回错误原因。例如，常见的错误码`4097`（十六进制`0x1001`）代表“网络读失败”。

![](img/c970a6594a5d6ffaa192c526420c9f33_13.png)

## 数据订阅与查询

连接并登录成功后，需要主动订阅行情才能接收数据。

*   `subscribe()`: 调用`md_api.subscribe()`，向服务器发送订阅特定合约行情的请求。
*   `close()`: 调用`md_api.exit()`和`td_api.exit()`，退出API并释放资源。

除了被动的行情推送，网关还需要主动查询账户信息。这是通过`query_account()`（查询资金）和`query_position()`（查询持仓）方法实现的。查询到的数据会被包装成`AccountData`或`PositionData`对象，然后推送到事件引擎。

服务器不会持续主动推送账户和持仓变动，因此需要定时轮询。这个功能由`_query()`方法实现。

## 定时查询机制

`_query()`方法利用事件引擎每秒触发的`EVENT_TIMER`事件来工作。

以下是其工作逻辑：
1.  内部维护一个计数器`self._query_count`和一个函数列表`self._query_functions`（包含`query_account`和`query_position`）。
2.  每次收到定时器事件，计数器加1。当计数器达到2（即间隔约2秒）时，才执行一次查询。
3.  从`_query_functions`列表头部取出一个函数执行（如`query_account`）。
4.  执行完毕后，将该函数重新放回列表尾部。
5.  这样便实现了每2秒交替查询一次资金和持仓。

```python
def _query(self, event: Event):
    self._query_count += 1
    if self._query_count < 2:
        return

    self._query_count = 0
    func = self._query_functions.pop(0)
    func()
    self._query_functions.append(func)
    self.update_date()
```

![](img/c970a6594a5d6ffaa192c526420c9f33_15.png)

本节课中我们一起学习了`CtpGateway`的核心结构。我们明白了它如何通过继承`BaseGateway`来规范接口，如何通过封装`MdApi`和`TdApi`来调用底层功能，以及连接、登录、订阅和定时查询的完整流程。下一节课，我们将深入探讨网关如何处理接收到的行情（Tick）、委托（Order）和成交（Trade）等数据。