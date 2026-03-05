# 量化交易课程：第33课：RPC模块详解(2) - 核心源码解析

在本节课中，我们将深入解析VNPY框架中RPC模块的核心源码，包括服务端（`rpc_server`）和客户端（`rpc_client`）的实现逻辑与工作机制。我们将重点关注其通信机制、心跳检测以及如何与VNPY主框架进行整合。

## 整体架构回顾

上一节我们介绍了RPC模块的基本概念。本节中，我们来看看其核心代码的具体实现。

RPC模块的核心思想是将交易系统的**数据接口层（Gateway）** 与**应用逻辑层（App）** 进行物理分离。服务端运行`MainEngine`并连接所有数据接口（Gateway），但不运行上层策略App。客户端则运行各种策略App，并通过RPC与服务端的`MainEngine`进行通信，从而间接调用底层接口的功能。

这种架构下，客户端通过RPC调用服务端`MainEngine`的方法（如`get_tick`、`send_order`），服务端执行后将结果返回。事件（如行情推送、订单状态更新）则由服务端主动推送给客户端。通信基于ZeroMQ，主要使用两种模式：
*   **REQ/REP（请求/回复）**：用于客户端主动调用方法。
*   **PUB/SUB（发布/订阅）**：用于服务端向客户端推送事件。

![](img/5c657f4ee4f2bdcf729e0e8194d7310d_1.png)

![](img/5c657f4ee4f2bdcf729e0e8194d7310d_3.png)

## 核心源码解析：RPC服务端 (`rpc_server`)

服务端的核心代码位于 `vnpy/rpc/rpc_server.py`。它负责接收客户端请求、执行对应方法并返回结果，同时主动推送事件。

### 初始化与基础设置

服务端初始化时，会创建必要的属性和ZeroMQ套接字。

```python
class RpcServer:
    def __init__(self):
        self._functions = {}  # 存储可调用方法的字典
        self._context = zmq.Context()  # ZMQ上下文
        self._socket_rep = self._context.socket(zmq.REP)  # 用于回复请求
        self._socket_pub = self._context.socket(zmq.PUB)  # 用于发布事件
        self._active = False  # 服务器活动状态
        self._lock = threading.Lock()  # 线程锁，保证数据安全
        self._heartbeat = 0  # 下一次心跳时间戳
```

*   `_functions` 字典是核心，用于注册所有可供远程调用的方法。
*   创建了REP和PUB两个套接字，分别处理请求和推送。
*   `_lock` 线程锁确保在并发访问时的数据一致性。
*   `_heartbeat` 用于实现心跳检测机制，防止连接因长时间无通信而断开。

### 心跳检测机制

心跳（Heartbeat）是维持长连接的重要机制。服务端会定期向客户端发送一个信号，表明连接依然存活。

```python
def _check_heartbeat(self):
    """检查并发送心跳"""
    now = time.time()
    if now >= self._heartbeat:
        self.publish(HEARTBEAT_TOPIC, now)  # 推送心跳主题和当前时间
        self._heartbeat = now + HEARTBEAT_INTERVAL  # 设置下一次心跳时间
```
常量 `HEARTBEAT_INTERVAL` 通常设置为10秒。服务端每隔10秒会向所有订阅的客户端发送一个包含当前时间戳的消息。

### 主事件循环 (`run` 方法)

`run` 方法是服务端的主线程，在一个循环中处理请求和心跳。

```python
def run(self):
    while self._active:
        # 等待客户端请求，超时时间为1秒
        n = self._socket_rep.poll(1000)

        if not n:
            # 1秒内没有收到请求，则检查并发送心跳
            self._check_heartbeat()
            continue

        # 收到请求，开始处理
        req = self._socket_rep.recv_pyobj()
        name, args, kwargs = req

        try:
            # 从注册的方法字典中获取对应函数
            func = self._functions[name]
            # 执行函数，并传入参数
            result = func(*args, **kwargs)
            # 回复成功和结果
            rep = [True, result]
        except Exception as e:
            # 如果执行出错，回复失败和错误信息
            rep = [False, traceback.format_exc()]

        # 将回复发送给客户端
        self._socket_rep.send_pyobj(rep)
```

1.  **轮询请求**：使用 `poll(1000)` 等待客户端请求，超时时间为1秒。这是一种“多路复用”的高效做法，避免线程空转。
2.  **处理心跳**：如果超时（即1秒内无请求），则调用 `_check_heartbeat` 发送心跳，然后继续轮询。
3.  **处理请求**：收到请求后，解析出方法名 `name`、位置参数 `args` 和关键字参数 `kwargs`。
4.  **执行与回复**：从 `_functions` 字典中找到对应方法并执行。将执行结果或异常信息打包后回复给客户端。

### 方法注册与事件推送

服务端需要将`MainEngine`的方法注册到 `_functions` 字典中。

```python
def register(self, func):
    """注册一个可远程调用的函数"""
    self._functions[func.__name__] = func
```
例如，`main_engine.get_tick` 方法被注册后，客户端就可以通过RPC调用它。

![](img/5c657f4ee4f2bdcf729e0e8194d7310d_5.png)

对于事件推送（如行情、订单更新），服务端使用 `publish` 方法：

![](img/5c657f4ee4f2bdcf729e0e8194d7310d_7.png)

```python
def publish(self, topic, data):
    """发布主题和数据到所有订阅的客户端"""
    with self._lock:
        self._socket_pub.send_pyobj([topic, data])
```
在VNPY的RPC引擎包装中，几乎所有事件（除了定时器事件）都会通过此方法推送给客户端，其中 `topic` 通常为空字符串，`data` 是整个事件对象。

![](img/5c657f4ee4f2bdcf729e0e8194d7310d_9.png)

![](img/5c657f4ee4f2bdcf729e0e8194d7310d_11.png)

![](img/5c657f4ee4f2bdcf729e0e8194d7310d_13.png)

![](img/5c657f4ee4f2bdcf729e0e8194d7310d_15.png)

## 核心源码解析：RPC客户端 (`rpc_client`)

![](img/5c657f4ee4f2bdcf729e0e8194d7310d_17.png)

客户端的核心代码位于 `vnpy/rpc/rpc_client.py`。它负责向服务端发送请求，并接收服务端的回复和事件推送。

### 初始化与连接

客户端初始化时创建ZMQ上下文和套接字，并设置连接保活参数。

```python
class RpcClient:
    def __init__(self):
        self._context = zmq.Context()
        self._socket_req = self._context.socket(zmq.REQ)  # 用于发送请求
        self._socket_sub = self._context.socket(zmq.SUB)  # 用于订阅事件
        # 设置TCP保活参数，维持长连接
        for socket in [self._socket_req, self._socket_sub]:
            socket.setsockopt(zmq.TCP_KEEPALIVE, 1)
            socket.setsockopt(zmq.TCP_KEEPALIVE_IDLE, 30)
        self._active = False
        self._lock = threading.Lock()
        self._last_received_ping = None  # 记录最后一次收到心跳的时间
        self._cache = {}  # 本地缓存，用于属性访问代理
```

`TCP_KEEPALIVE` 相关设置是为了在网络层保持TCP连接的活性，与应用层的心跳互为补充。

### 动态属性访问 (`__getattr__`)

这是客户端的一个关键魔法方法，它使得远程调用像调用本地方法一样简单。

```python
def __getattr__(self, name):
    """动态获取属性。如果本地缓存没有，则创建一个可调用对象来发起RPC请求。"""
    if name in self._cache:
        return self._cache[name]

    def dorpc(*args, **kwargs):
        with self._lock:
            # 打包请求：方法名、位置参数、关键字参数
            req = [name, args, kwargs]
            self._socket_req.send_pyobj(req)
            # 等待并接收回复
            rep = self._socket_req.recv_pyobj()
            success, value = rep
            if success:
                return value  # 调用成功，返回结果
            else:
                raise RemoteException(value)  # 调用失败，抛出远程异常
    # 将生成的可调用函数存入缓存
    self._cache[name] = dorpc
    return dorpc
```

当客户端代码尝试调用一个不存在于本地的方法时（例如 `client.get_tick(...)`），`__getattr__` 会被触发。
1.  它首先检查缓存 `_cache`。
2.  如果不存在，它会动态创建一个名为 `dorpc` 的函数。这个函数的工作是将方法名和参数打包，通过REQ套接字发送给服务端，然后等待并解析回复。
3.  最后，将这个 `dorpc` 函数存入缓存并返回。这样，同一种方法的后续调用会直接使用缓存中的函数，提高了效率。

### 主事件循环 (`run` 方法)

客户端的主线程负责监听服务端推送的事件和心跳。

```python
def run(self):
    while self._active:
        # 等待服务端消息，超时时间30秒
        n = self._socket_sub.poll(HEARTBEAT_TIMEOUT * 1000)

        if not n:
            # 30秒内未收到任何消息（包括心跳），认为连接断开
            self._on_disconnected()
            continue

        # 收到消息，进行解析
        topic, data = self._socket_sub.recv_pyobj(flags=zmq.NOBLOCK)

        if topic == HEARTBEAT_TOPIC:
            # 收到心跳，更新最后心跳时间
            self._last_received_ping = data
        else:
            # 收到其他事件，调用回调函数处理
            self.callback(topic, data)
```

1.  **轮询消息**：使用 `poll` 等待消息，超时时间较长（30秒）。
2.  **连接健康检查**：如果30秒内未收到任何消息（无论是心跳还是事件），则判定连接可能已断开，触发 `_on_disconnected` 警告。
3.  **处理心跳**：如果收到心跳消息，更新 `_last_received_ping` 时间戳。
4.  **处理事件**：如果收到其他事件，则调用 `callback` 函数进行处理。注意，在基础的 `RpcClient` 类中，`callback` 需要由使用者（如 `RpcGateway`）来具体实现，通常是将事件放入客户端的 `EventEngine`。

## RPC引擎包装：`RpcEngine` 与 `RpcGateway`

理解了核心的 `rpc_server` 和 `rpc_client` 后，我们再看它们在VNPY框架中的“包装器”。

### `RpcEngine` (服务端包装)

`RpcEngine` 位于 `vnpy/rpc/rpc_service.py`，它继承自 `MainEngine`，内部包含一个 `RpcServer` 实例。

以下是它的主要职责：
*   **注册方法**：在 `_init_server` 中，将 `MainEngine` 的核心方法（如 `get_tick`, `send_order`, `subscribe` 等）注册到 `RpcServer._functions` 字典中。
*   **处理事件**：在 `register_event` 和 `process_event` 中，它监听 `MainEngine` 产生的几乎所有事件（除了定时器事件），并通过 `RpcServer.publish()` 推送给所有连接的客户端。
*   **生命周期管理**：提供 `start()`、`stop()` 等方法，方便在VNPY应用中启动和停止RPC服务。

```python
# 在 RpcEngine 中注册事件处理的示例
def register_event(self):
    # 注册通用事件处理函数，接收所有事件
    self.main_engine.register_general_handler(self.process_event)

def process_event(self, event):
    # 过滤掉定时器事件，其他事件全部通过RPC推送
    if event.type != EVENT_TIMER:
        self.server.publish("", event)  # topic为空字符串
```

### `RpcGateway` (客户端包装)

`RpcGateway` 位于 `vnpy/rpc/rpc_gateway.py`，它继承自 `BaseGateway`，内部包含一个 `RpcClient` 实例。

以下是它的主要职责：
*   **设置回调**：将 `RpcClient` 的 `callback` 方法指向自身的 `process_event` 方法，从而将收到的事件放入客户端的 `EventEngine`。
*   **实现网关接口**：实现 `BaseGateway` 要求的 `connect`, `subscribe`, `send_order` 等方法。这些方法的实现通常是直接调用 `RpcClient` 的对应远程方法（通过 `__getattr__` 机制）。
*   **数据标签**：在 `process_event` 中，为接收到的事件数据添加 `gateway_name` 属性，确保在客户端的多网关环境下能正确识别数据来源。

```python
# 在 RpcGateway 中设置回调和处理事件的示例
def __init__(self, main_engine, gateway_name):
    super().__init__(main_engine, gateway_name)
    self.client = RpcClient()
    # 将客户端的回调指向自己的处理方法
    self.client.callback = self.process_event

def process_event(self, topic, data):
    # 为事件数据添加本网关的名称标签
    if not hasattr(data, "gateway_name"):
        data.gateway_name = self.gateway_name
    # 将事件放入主引擎的事件引擎中
    self.main_engine.put_event(data)
```

## 总结

本节课中我们一起深入学习了VNPY RPC模块的核心源码。

*   **架构核心**是分离的数据接口层与应用逻辑层，通过 `rpc_server` 和 `rpc_client` 进行通信。
*   **服务端 (`rpc_server`)** 的核心是维护一个可调用方法字典，并通过轮询处理请求和定时发送心跳。`RpcEngine` 对其进行了包装，集成了VNPY的 `MainEngine` 和事件推送逻辑。
*   **客户端 (`rpc_client`)** 的核心是利用 `__getattr__` 实现透明的远程方法调用，并通过轮询接收事件和心跳。`RpcGateway` 对其进行了包装，使其能够作为标准的Gateway接入客户端的 `MainEngine`，并正确处理收到的事件。

![](img/5c657f4ee4f2bdcf729e0e8194d7310d_19.png)

通过这两节课的分析，你应该对VNPY如何实现分布式部署有了清晰的认识。理解这套机制后，你不仅可以更好地使用它，还能根据自身需求对其进行修改或扩展。