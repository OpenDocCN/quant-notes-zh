# 从零开始量化：第32课：RPC模块讲解(1) - 网络通信基础与ZMQ入门 🚀

在本节课中，我们将要学习远程过程调用（RPC）的基础知识。为了理解RPC，我们必须先了解网络通信的核心——套接字（Socket）。我们将从最基础的Socket编程开始，逐步过渡到更高级、更便捷的ZeroMQ（ZMQ）库，并最终实现一个简单的RPC原型。课程内容将尽可能简单直白，确保初学者能够跟上。

## 概述：什么是网络通信？ 💻

网络通信的本质是计算机之间的数据交换。你可以把它想象成电流的传输，用“0”和“1”来表示信息。计算机通信之所以强大，关键在于其速度——接近光速的传输能力。

要实现通信，必然涉及两个角色：**服务器**和**客户端**。例如，你玩网络游戏时，你的操作（如攻击）并不是直接发送到对手的电脑，而是先发送到游戏服务器，再由服务器转发给对手。微信聊天、浏览网页都是类似的原理。一个服务器可以同时连接多个客户端。

## 第一部分：使用原生Socket进行通信

上一节我们介绍了网络通信的基本概念，本节中我们来看看如何使用Python的原生`socket`模块建立一个简单的服务器与客户端对话程序。

### 服务端代码解析

以下是创建一个TCP服务端的基本步骤：

```python
from socket import *

# 1. 创建TCP套接字
tcp_server = socket(AF_INET, SOCK_STREAM)
# 2. 设置端口复用，方便调试
tcp_server.setsockopt(SOL_SOCKET, SO_REUSEADDR, 1)

# 3. 绑定IP地址和端口
PORT = 8091
tcp_server.bind(('', PORT))  # 空字符串代表本机
# 4. 开始监听，等待客户端连接
tcp_server.listen(128)

print(f"服务端已在端口 {PORT} 启动，等待连接...")

# 5. 接受客户端连接
client_socket, client_addr = tcp_server.accept()
print(f"客户端连接建立！套接字：{client_socket}, 地址：{client_addr}")

# 6. 与客户端进行数据交互
BUFFER_SIZE = 1024
while True:
    # 接收客户端消息（字节类型）
    recv_data = client_socket.recv(BUFFER_SIZE)
    if not recv_data:  # 客户端断开连接
        client_socket.close()
        print("客户端连接关闭。")
        break

    # 将字节数据解码为字符串
    recv_msg = recv_data.decode('utf-8')
    print(f"客户端发来消息：{recv_msg}")

    # 处理消息（这里示例为转为大写）并发送回去
    send_msg = recv_msg.upper()
    client_socket.send(send_msg.encode('utf-8'))

# 7. 关闭服务器套接字
tcp_server.close()
print("服务端关闭。")
```

### 客户端代码解析

客户端需要连接到服务器，并与之进行一问一答式的通信。

![](img/8e524eff8990bf76452db6bbf6b93a5e_1.png)

![](img/8e524eff8990bf76452db6bbf6b93a5e_3.png)

![](img/8e524eff8990bf76452db6bbf6b93a5e_5.png)

```python
from socket import *

![](img/8e524eff8990bf76452db6bbf6b93a5e_7.png)

![](img/8e524eff8990bf76452db6bbf6b93a5e_9.png)

![](img/8e524eff8990bf76452db6bbf6b93a5e_11.png)

# 配置参数，必须与服务端一致
SERVER_IP = '127.0.0.1'  # 本地回环地址，代表本机
SERVER_PORT = 8091
BUFFER_SIZE = 1024

![](img/8e524eff8990bf76452db6bbf6b93a5e_13.png)

![](img/8e524eff8990bf76452db6bbf6b93a5e_15.png)

# 1. 创建TCP套接字
client_socket = socket(AF_INET, SOCK_STREAM)
# 2. 连接到服务器
client_socket.connect((SERVER_IP, SERVER_PORT))

![](img/8e524eff8990bf76452db6bbf6b93a5e_17.png)

![](img/8e524eff8990bf76452db6bbf6b93a5e_19.png)

while True:
    # 3. 输入要发送的消息
    send_msg = input("请输入英文消息（输入‘quit’退出）: ").strip()
    if send_msg.lower() == 'quit':
        break

    # 4. 发送消息（需编码为字节）
    client_socket.send(send_msg.encode('utf-8'))

    # 5. 接收服务器返回的消息
    recv_data = client_socket.recv(BUFFER_SIZE)
    recv_msg = recv_data.decode('utf-8')
    print(f"收到服务器回复：{recv_msg}")

# 6. 关闭连接
client_socket.close()
print("对话结束。")
```

**运行演示**：
1.  首先在终端运行服务端脚本，它会进入等待状态。
2.  然后在另一个终端运行客户端脚本。
3.  在客户端输入信息（如“hello”），服务端会收到并显示，然后将大写后的“HELLO”发回客户端显示。

原生Socket编程虽然直观，但存在一些问题，例如需要手动处理粘包、多线程管理复杂、通信模式固定等。因此，在实际项目中，我们更常使用封装好的库。

## 第二部分：使用ZeroMQ简化网络通信

上一节我们使用原生Socket实现了一个简单的回声服务器，本节中我们来看看如何使用ZeroMQ这个更强大的库来简化开发。ZMQ封装了底层细节，提供了更高级的通信模式。

首先需要安装ZMQ库：`pip install pyzmq`。

![](img/8e524eff8990bf76452db6bbf6b93a5e_21.png)

![](img/8e524eff8990bf76452db6bbf6b93a5e_23.png)

![](img/8e524eff8990bf76452db6bbf6b93a5e_25.png)

ZMQ主要提供两种我们常用的模式：
1.  **发布-订阅模式**：服务端推送消息，客户端订阅接收。适用于行情广播等场景。
2.  **请求-应答模式**：客户端发起请求，服务端返回应答。适用于远程调用等场景。

### 发布-订阅模式示例

![](img/8e524eff8990bf76452db6bbf6b93a5e_27.png)

以下是该模式的实现步骤：

![](img/8e524eff8990bf76452db6bbf6b93a5e_29.png)

![](img/8e524eff8990bf76452db6bbf6b93a5e_31.png)

![](img/8e524eff8990bf76452db6bbf6b93a5e_33.png)

**服务端（发布者）**:
```python
import zmq

![](img/8e524eff8990bf76452db6bbf6b93a5e_35.png)

# 1. 创建上下文
context = zmq.Context()
# 2. 创建PUB类型的套接字
socket = context.socket(zmq.PUB)
# 3. 绑定地址
socket.bind("tcp://*:8900")  # * 表示绑定到本机所有IP

![](img/8e524eff8990bf76452db6bbf6b93a5e_37.png)

![](img/8e524eff8990bf76452db6bbf6b93a5e_39.png)

![](img/8e524eff8990bf76452db6bbf6b93a5e_41.png)

![](img/8e524eff8990bf76452db6bbf6b93a5e_43.png)

print("发布者已启动，开始输入消息...")
while True:
    # 4. 获取输入并发送
    message = input("请输入消息: ")
    socket.send(message.encode('utf-8'))
```

![](img/8e524eff8990bf76452db6bbf6b93a5e_45.png)

![](img/8e524eff8990bf76452db6bbf6b93a5e_47.png)

![](img/8e524eff8990bf76452db6bbf6b93a5e_49.png)

![](img/8e524eff8990bf76452db6bbf6b93a5e_51.png)

**客户端（订阅者）**:
```python
import zmq

![](img/8e524eff8990bf76452db6bbf6b93a5e_53.png)

![](img/8e524eff8990bf76452db6bbf6b93a5e_55.png)

context = zmq.Context()
# 1. 创建SUB类型的套接字
socket = context.socket(zmq.SUB)
# 2. 连接发布者
socket.connect("tcp://localhost:8900")
# 3. 设置订阅过滤器，空字符串表示接收所有消息
socket.setsockopt_string(zmq.SUBSCRIBE, '')

![](img/8e524eff8990bf76452db6bbf6b93a5e_57.png)

print("订阅者已启动，等待接收消息...")
while True:
    # 4. 接收消息
    message_bytes = socket.recv()
    message = message_bytes.decode('utf-8')
    print(f"收到消息: {message}")
```
*   **过滤器**：`setsockopt_string(zmq.SUBSCRIBE, ‘A’)` 表示只接收以‘A’开头的消息。

### 请求-应答模式示例

这种模式更接近RPC调用。

![](img/8e524eff8990bf76452db6bbf6b93a5e_59.png)

**服务端（应答者）**:
```python
import zmq

![](img/8e524eff8990bf76452db6bbf6b93a5e_61.png)

![](img/8e524eff8990bf76452db6bbf6b93a5e_63.png)

![](img/8e524eff8990bf76452db6bbf6b93a5e_65.png)

context = zmq.Context()
# 1. 创建REP类型的套接字
socket = context.socket(zmq.REP)
socket.bind("tcp://*:8901")

print("应答者已启动...")
while True:
    # 2. 接收Python对象（客户端发来的请求）
    data = socket.recv_pyobj()
    print(f"收到请求数据: {data}")
    # 3. 处理请求（这里简单包装成元组）并发送回应
    reply_data = (True, data)  # 第一个元素可表示调用成功与否
    socket.send_pyobj(reply_data)
```

![](img/8e524eff8990bf76452db6bbf6b93a5e_67.png)

![](img/8e524eff8990bf76452db6bbf6b93a5e_69.png)

**客户端（请求者）**:
```python
import zmq

![](img/8e524eff8990bf76452db6bbf6b93a5e_71.png)

context = zmq.Context()
# 1. 创建REQ类型的套接字
socket = context.socket(zmq.REQ)
socket.connect("tcp://localhost:8901")

while True:
    # 2. 准备请求数据（Python对象）
    message = input("请输入信息: ")
    # 3. 发送请求
    socket.send_pyobj(message)
    # 4. 接收应答
    reply = socket.recv_pyobj()
    print(f"收到服务器应答: {reply}")
```
ZMQ的`send_pyobj`和`recv_pyobj`方法可以直接序列化和反序列化Python对象，比手动处理字节流方便得多。

## 第三部分：构建一个简单的RPC原型

上一节我们学会了使用ZMQ进行请求-应答通信，本节中我们利用这个机制，尝试构建一个最简单的RPC系统。目标是让客户端能够远程调用服务端对象的方法。

核心思路是：
1.  客户端将**想调用的方法名**和**参数**打包发送给服务端。
2.  服务端根据方法名找到对应的函数，使用参数执行它。
3.  服务端将执行结果（或错误信息）打包返回给客户端。

以下是实现代码：

**服务端**:
```python
import zmq

# 定义一个示例类
class Person:
    def __init__(self):
        self.name = "小红"
        self.age = 18

    def get_name(self):
        return self.name

    def get_age(self):
        return self.age

    def change_name(self, new_name):
        self.name = new_name

    def change_age(self, new_age):
        self.age = new_age

# 实例化对象
p1 = Person()

# 创建方法映射字典，便于查找
functions = {
    ‘get_name‘: p1.get_name,
    ‘get_age‘: p1.get_age,
    ‘change_name‘: p1.change_name,
    ‘change_age‘: p1.change_age,
}

# ZMQ 服务端设置
context = zmq.Context()
socket = context.socket(zmq.REP)
socket.bind("tcp://*:8902")

print("RPC 服务端已启动...")
while True:
    # 接收客户端调用请求
    request = socket.recv_pyobj()  # 期望格式：(‘method_name‘, (args,), {kwargs})
    func_name, args, kwargs = request

    # 查找并执行方法
    if func_name in functions:
        try:
            func = functions[func_name]
            result = func(*args, **kwargs)  # 解包参数并调用
            reply = (True, result)  # (成功标志, 结果)
        except Exception as e:
            reply = (False, str(e))
    else:
        reply = (False, f"方法 ‘{func_name}‘ 不存在")

    # 返回执行结果
    socket.send_pyobj(reply)
```

**客户端**:
```python
import zmq

context = zmq.Context()
socket = context.socket(zmq.REQ)
socket.connect("tcp://localhost:8902")

![](img/8e524eff8990bf76452db6bbf6b93a5e_73.png)

![](img/8e524eff8990bf76452db6bbf6b93a5e_75.png)

![](img/8e524eff8990bf76452db6bbf6b93a5e_77.png)

while True:
    # 用户输入要调用的方法
    func_name = input("请输入要调用的方法名: ").strip()
    if func_name.lower() == ‘quit‘:
        break

    # 根据方法名准备参数（这是一个简单示例，实际需更复杂的解析）
    args = ()
    if func_name == ‘change_name‘:
        arg = input("请输入新名字: ")
        args = (arg,)
    elif func_name == ‘change_age‘:
        arg = int(input("请输入新年龄: "))
        args = (arg,)
    # get_name 和 get_age 无参数

    # 构建请求并发送
    request = (func_name, args, {})
    socket.send_pyobj(request)

    # 接收并打印结果
    reply = socket.recv_pyobj()
    success, data = reply
    if success:
        print(f"调用成功，结果: {data}")
    else:
        print(f"调用失败，错误: {data}")
```

![](img/8e524eff8990bf76452db6bbf6b93a5e_79.png)

![](img/8e524eff8990bf76452db6bbf6b93a5e_80.png)

![](img/8e524eff8990bf76452db6bbf6b93a5e_82.png)

![](img/8e524eff8990bf76452db6bbf6b93a5e_84.png)

![](img/8e524eff8990bf76452db6bbf6b93a5e_86.png)

**运行演示**：
1.  启动服务端和客户端。
2.  在客户端输入 `get_name`，会返回“小红”。
3.  输入 `change_name` 并输入参数“Alex”，再调用 `get_name`，会返回“Alex”。
4.  输入不存在的方法名，会返回错误信息。

![](img/8e524eff8990bf76452db6bbf6b93a5e_88.png)

![](img/8e524eff8990bf76452db6bbf6b93a5e_90.png)

![](img/8e524eff8990bf76452db6bbf6b93a5e_92.png)

这个简单的原型揭示了RPC的基本工作原理：**通过网络传输，将对本地对象的方法调用“代理”到了远程服务器上执行**。

![](img/8e524eff8990bf76452db6bbf6b93a5e_94.png)

![](img/8e524eff8990bf76452db6bbf6b93a5e_96.png)

## 总结 🎯

![](img/8e524eff8990bf76452db6bbf6b93a5e_98.png)

![](img/8e524eff8990bf76452db6bbf6b93a5e_100.png)

本节课中我们一起学习了RPC的基础知识。我们从最底层的网络通信原理和Socket编程入手，理解了服务器与客户端交互的基本模型。然后，我们引入了ZeroMQ库，它极大地简化了网络编程，并介绍了发布-订阅和请求-应答两种核心模式。最后，我们综合运用这些知识，实现了一个简易的RPC系统原型，让客户端能够远程调用服务端对象的方法。

![](img/8e524eff8990bf76452db6bbf6b93a5e_102.png)

这为我们下一节课解析VN.PY框架中成熟、复杂的RPC模块实现打下了坚实的基础。