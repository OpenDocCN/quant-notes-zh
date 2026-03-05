# VPY量化课程：第34课：RPC模块详解(3) - 客户端与实战演示

在本节课中，我们将深入讲解RPC模块的客户端核心机制，并学习如何搭建一个完整的RPC服务端与客户端进行通信。我们将重点解析`getattr`魔术方法在RPC客户端中的作用，理解请求发送与响应的完整流程，并通过一个实战Demo演示RPC的实际应用。

## 概述：RPC客户端核心机制

上一节我们介绍了RPC服务端的基本结构。本节中，我们来看看RPC客户端的核心——`RpcClient`类，特别是其`__getattr__`方法如何实现动态方法调用，以及整个请求-响应机制是如何工作的。

## `__getattr__`方法解析

`__getattr__`是Python的一个魔术方法。当尝试访问对象中不存在的属性时，Python解释器会自动调用此方法。

### Python基础：`__getattr__`的作用

以下是`__getattr__`方法的一个简单示例：

```python
class Person:
    def __init__(self):
        self.name = "小明"
        self.age = 20

    def __getattr__(self, item):
        # 当访问不存在的属性时，会进入此方法
        print(f"你试图访问的属性名是: {item}")
        # 可以返回一个默认值，例如返回年龄
        return self.age

p1 = Person()
print(p1.name)  # 输出: 小明
print(p1.sex)   # 输出: 你试图访问的属性名是: sex \n 20
print(p1.height) # 输出: 你试图访问的属性名是: height \n 20
```

在这个例子中，当我们访问`p1.sex`或`p1.height`这些类中未定义的属性时，程序不会报错，而是会执行`__getattr__`方法，并返回`self.age`的值。

### 在RpcClient中的应用

在`RpcClient`类中，`__getattr__`方法被用来拦截所有未在类中明确定义的方法调用（例如`subscribe`, `send_order`）。当这些方法被调用时，`__getattr__`会捕获方法名和参数，将它们组装成一个请求，并通过ZeroMQ发送到服务端。

其核心逻辑的简化表示如下：

```python
class RpcClient:
    def __getattr__(self, name):
        # 返回一个闭包函数，用于处理实际的方法调用
        def wrapper(*args, **kwargs):
            # 1. 组装请求数据
            req = [name, args, kwargs]
            # 2. 通过线程锁保证请求-响应配对，防止数据混乱
            with self._lock:
                # 3. 发送请求到服务端
                self._socket.send_pyobj(req)
                # 4. 等待服务端响应，设置超时时间（如30秒）
                if self._socket.poll(timeout=30000):
                    # 5. 接收响应
                    rep = self._socket.recv_pyobj()
                    # 6. 处理响应：成功则返回数据，失败则抛出异常
                    if rep[0]:
                        return rep[1]
                    else:
                        raise RpcException(rep[1])
                else:
                    # 超时处理
                    raise RpcException("RPC server no response")
        return wrapper
```

**关键点解析**：
*   **动态代理**：`__getattr__`让`RpcClient`成为了一个动态代理，客户端可以像调用本地方法一样调用远程服务。
*   **请求封装**：它将方法名(`name`)、位置参数(`args`)、关键字参数(`kwargs`)打包成一个列表进行传输。
*   **线程安全**：使用线程锁(`_lock`)确保在并发环境下，每个请求都能正确匹配到自己的响应，避免数据错乱。
*   **同步调用**：采用`poll`和`recv`进行同步等待，直到收到服务端响应或超时。

理解了`__getattr__`，就理解了RPC客户端实现透明远程调用的精髓。

## RpcGateway：客户端的封装

`RpcGateway`在`RpcClient`的基础上做了一层封装，使其更好地融入VN Trader的网关体系。

### 主要功能

以下是`RpcGateway`类的主要功能组件：

1.  **连接管理**：`connect`方法用于初始化`RpcClient`并连接到指定的RPC服务端地址。
2.  **合约映射**：`_simple_gateway_map`是一个字典，用于维护合约符号(`symbol`)与网关名称(`gateway_name`)的映射关系。这在多接口环境下，确保数据能正确路由到对应的网关。
3.  信息查询：提供了`query_contract`等方法，主动向服务端请求合约、账户等信息，并推送到本地的事件引擎中，模拟真实网关的初始化流程。
4.  标准操作：实现了`subscribe`（订阅行情）、`send_order`（发单）、`cancel_order`（撤单）等标准网关接口。这些调用最终都通过`self.client`（即`RpcClient`实例）转发给服务端。
5.  回调函数：`client.callback`方法用于处理服务端主动推送过来的数据（如行情、成交、委托等），并将其转换为VN Trader标准事件，放入事件引擎。

`RpcGateway`使得远程的RPC服务对上层应用（如策略、界面）来说，就像一个本地网关一样易用。

## 实战演示：搭建RPC服务端与客户端

![](img/15f01c81235ee894798cde187e412100_1.png)

理论讲解完毕，现在让我们通过一个完整的Demo来实践RPC通信。

![](img/15f01c81235ee894798cde187e412100_3.png)

### 第一步：创建RPC服务端

服务端的任务是启动交易接口（如CTP），并启动RPC服务以监听客户端请求。

```python
# server_demo.py
from time import sleep
from vnpy.event import EventEngine
from vnpy.trader.engine import MainEngine
from vnpy.trader.gateway import CtpGateway
from vnpy.rpc_service import RpcEngine
from vnpy.trader.utility import load_json
from vnpy.trader.object import SubscribeRequest
from vnpy.trader.constant import Exchange

def main():
    # 1. 创建事件引擎和主引擎
    event_engine = EventEngine()
    main_engine = MainEngine(event_engine)
    
    # 2. 添加CTP网关
    ctp_gateway = main_engine.add_gateway(CtpGateway)
    # 加载CTP连接配置
    ctp_setting = load_json("connect_ctp.json")
    ctp_gateway.connect(ctp_setting)
    sleep(5)  # 等待连接
    
    # 3. 添加并启动RPC引擎
    rpc_engine = main_engine.add_app(RpcEngine)
    # 注册RPC日志事件
    main_engine.write_log("RPC服务启动中...")
    rpc_engine.start()
    sleep(2)
    main_engine.write_log("RPC服务启动成功")
    
    # 4. 订阅一个合约作为测试
    req = SubscribeRequest(
        symbol="rb2310", 
        exchange=Exchange.SHFE
    )
    main_engine.subscribe(req, ctp_gateway.gateway_name)
    
    # 5. 保持主线程运行
    while True:
        sleep(1)

if __name__ == "__main__":
    main()
```

### 第二步：创建RPC客户端

客户端的任务是连接RPC服务端，并通过RPC网关接收行情、发送交易指令。

```python
# client_demo.py
from time import sleep
from vnpy.event import EventEngine
from vnpy.trader.engine import MainEngine
from vnpy.rpc_service import RpcGateway
from vnpy.trader.object import SubscribeRequest
from vnpy.trader.constant import Exchange

def process_tick_event(event):
    """处理行情事件"""
    tick = event.data
    print(f"收到Tick: {tick.symbol}, 最新价: {tick.last_price}")

def main():
    # 1. 创建事件引擎和主引擎
    event_engine = EventEngine()
    main_engine = MainEngine(event_engine)
    
    # 2. 注册行情事件处理函数
    from vnpy.event import EVENT_TICK
    event_engine.register(EVENT_TICK, process_tick_event)
    
    # 3. 添加并连接RPC网关
    rpc_gateway = main_engine.add_gateway(RpcGateway)
    # 使用默认的RPC服务器地址（通常是本机）
    rpc_gateway.connect(rpc_gateway.default_setting)
    sleep(5)  # 等待连接
    main_engine.write_log("RPC网关连接成功")
    
    # 4. 通过RPC网关订阅行情
    req = SubscribeRequest(
        symbol="al2310", 
        exchange=Exchange.SHFE
    )
    main_engine.subscribe(req, rpc_gateway.gateway_name)
    
    # 5. 保持主线程运行，等待数据
    while True:
        sleep(1)

if __name__ == "__main__":
    main()
```

### 运行步骤

![](img/15f01c81235ee894798cde187e412100_5.png)

1.  确保您的`connect_ctp.json`配置正确，并先运行`server_demo.py`。看到“RPC服务启动成功”且CTP连接正常的日志。
2.  再运行`client_demo.py`。看到“RPC网关连接成功”的日志。
3.  观察客户端控制台，应该会开始打印来自服务端的铝(AL2310)合约的Tick行情数据。

![](img/15f01c81235ee894798cde187e412100_7.png)

![](img/15f01c81235ee894798cde187e412100_9.png)

![](img/15f01c81235ee894798cde187e412100_11.png)

这个Demo清晰地展示了数据流：客户端发起订阅请求 -> 通过RPC传递给服务端 -> 服务端通过CTP接口向交易所订阅 -> 行情数据返回给服务端 -> 服务端通过RPC推送给客户端 -> 客户端在控制台打印。

![](img/15f01c81235ee894798cde187e412100_13.png)

## 信号处理小贴士

在服务端代码中，我们经常会看到以下代码片段：

![](img/15f01c81235ee894798cde187e412100_15.png)

![](img/15f01c81235ee894798cde187e412100_17.png)

![](img/15f01c81235ee894798cde187e412100_19.png)

![](img/15f01c81235ee894798cde187e412100_21.png)

![](img/15f01c81235ee894798cde187e412100_23.png)

```python
import signal
signal.signal(signal.SIGINT, signal.SIG_DFL)
```

![](img/15f01c81235ee894798cde187e412100_25.png)

![](img/15f01c81235ee894798cde187e412100_27.png)

它的作用是捕获键盘中断信号（`Ctrl+C`），并按照系统默认行为来处理（即终止程序）。如果没有这行代码，在`Ctrl+C`终止某些循环时可能会打印出冗长的异常堆栈信息。加上这行代码可以使程序更干净地退出。

## 总结

![](img/15f01c81235ee894798cde187e412100_29.png)

本节课中我们一起学习了RPC模块的客户端核心。我们深入剖析了`__getattr__`魔术方法如何实现远程方法的透明调用，理解了`RpcClient`中请求发送、响应接收及线程安全的完整机制。随后，我们介绍了`RpcGateway`如何对客户端进行封装，以适配VN Trader的网关框架。最后，通过一个从零搭建的实战Demo，我们完整演示了RPC服务端与客户端的通信流程，将理论付诸实践。掌握RPC模块，是构建分布式量化交易系统的重要一步。