# 从零开始量化：1：EventEngine 事件引擎入门

![](img/79f072d372d3565f5f18aaeb5e38550a_1.png)

![](img/79f072d372d3565f5f18aaeb5e38550a_3.png)

![](img/79f072d372d3565f5f18aaeb5e38550a_5.png)

![](img/79f072d372d3565f5f18aaeb5e38550a_7.png)

![](img/79f072d372d3565f5f18aaeb5e38550a_9.png)

在本节课中，我们将学习 VNPY 量化交易框架的核心组件之一：事件引擎。我们将从安装环境开始，逐步深入到事件引擎的工作原理，并通过一个简化的代码示例来理解其内部机制。

![](img/79f072d372d3565f5f18aaeb5e38550a_11.png)

## 概述

![](img/79f072d372d3565f5f18aaeb5e38550a_13.png)

![](img/79f072d372d3565f5f18aaeb5e38550a_15.png)

![](img/79f072d372d3565f5f18aaeb5e38550a_17.png)

事件引擎是 VNPY 框架的数据中枢，负责接收、管理和分发来自不同源头的数据。理解它是掌握 VNPY 源码的第一步。

![](img/79f072d372d3565f5f18aaeb5e38550a_19.png)

![](img/79f072d372d3565f5f18aaeb5e38550a_21.png)

## 环境安装与启动

首先需要安装 VNPY。建议在 Windows 10 系统上进行，并关闭 360 等安全软件以避免安装冲突。安装完成后，可以通过两种方式启动：
1.  双击桌面图标启动图形界面。
2.  通过 PowerShell 或 CMD 命令行启动。

经过版本升级后，所有输出内容（如 `print` 语句）已被重定向到图形界面的日志窗口中，因此通常无需再使用命令行方式启动。

为了说明重定向的概念，以下是一个简单的代码示例：
```python
import sys
from PySide6.QtWidgets import QApplication, QTextEdit
from PySide6.QtCore import Signal, QObject

class MyStdOut(QObject):
    signal_str = Signal(str)

    def write(self, text):
        if text:
            self.signal_str.emit(str(text))

![](img/79f072d372d3565f5f18aaeb5e38550a_23.png)

![](img/79f072d372d3565f5f18aaeb5e38550a_25.png)

![](img/79f072d372d3565f5f18aaeb5e38550a_27.png)

class MyWindow(QTextEdit):
    def __init__(self):
        super().__init__()
        self.setWindowTitle("输出重定向演示")
        sys.stdout = MyStdOut()
        sys.stdout.signal_str.connect(self.append)

![](img/79f072d372d3565f5f18aaeb5e38550a_29.png)

![](img/79f072d372d3565f5f18aaeb5e38550a_31.png)

![](img/79f072d372d3565f5f18aaeb5e38550a_33.png)

![](img/79f072d372d3565f5f18aaeb5e38550a_35.png)

# 运行窗口并打印数字
app = QApplication([])
window = MyWindow()
window.show()
for i in range(10):
    print(f"当前数字是：{i}")
app.exec()
```
这段代码创建了一个图形窗口，并将 `print` 函数的输出从终端重定向到了该窗口中。VNPY 采用了类似的原理。

![](img/79f072d372d3565f5f18aaeb5e38550a_37.png)

![](img/79f072d372d3565f5f18aaeb5e38550a_39.png)

![](img/79f072d372d3565f5f18aaeb5e38550a_41.png)

![](img/79f072d372d3565f5f18aaeb5e38550a_43.png)

## VNPY 源码结构简介

安装 VNPY 后，其源代码位于 `site-packages` 目录下，所有核心模块均以 `vnpy_` 开头。了解这个结构有助于我们查找和学习源码。

![](img/79f072d372d3565f5f18aaeb5e38550a_45.png)

![](img/79f072d372d3565f5f18aaeb5e38550a_47.png)

## 深入事件引擎

事件引擎是 VNPY 的“发动机”，所有数据流都通过它传递。其核心思想是：将不同类型的数据包装成带有标识的“事件”，放入一个队列，再由引擎分发给已注册的对应处理函数。

上一节我们介绍了事件引擎的基本概念，本节中我们来看看如何实现一个简化版的事件引擎。

以下是实现一个简化事件引擎的关键步骤：

1.  **定义事件类**：用于包装数据和标识类型。
    ```python
    from typing import Any

    class Event:
        def __init__(self, type: str, data: Any = None):
            self.type = type  # 事件类型，如 "tick", "order"
            self.data = data  # 事件携带的具体数据
    ```

2.  **创建事件引擎类**：包含队列、事件处理函数注册表和线程。
    ```python
    from threading import Thread
    from queue import Queue, Empty
    from collections import defaultdict

    class MyEventEngine:
        def __init__(self):
            self._queue = Queue()  # 事件队列
            self._active = False   # 引擎活动状态
            self._thread = Thread(target=self._run)  # 处理线程
            # 使用 defaultdict 自动为新的 type 创建空列表
            self._handlers = defaultdict(list)

        def start(self):
            """启动引擎"""
            self._active = True
            self._thread.start()

        def stop(self):
            """停止引擎"""
            self._active = False
            self._thread.join()  # 等待线程结束

        def put(self, event: Event):
            """向队列中放入事件"""
            self._queue.put(event)

        def register(self, type: str, handler):
            """注册事件处理函数"""
            handler_list = self._handlers[type]
            if handler not in handler_list:
                handler_list.append(handler)

        def _run(self):
            """引擎主循环，在独立线程中运行"""
            while self._active:
                try:
                    # 从队列获取事件，阻塞1秒
                    event = self._queue.get(block=True, timeout=1)
                    self._process(event)
                except Empty:
                    # 超时后继续循环
                    continue

        def _process(self, event: Event):
            """处理事件：分发给已注册的函数"""
            if event.type in self._handlers:
                for handler in self._handlers[event.type]:
                    handler(event)  # 调用处理函数
    ```

3.  **使用事件引擎**：
    ```python
    # 创建引擎实例
    engine = MyEventEngine()

    # 定义处理函数
    def on_tick(event):
        print(f"收到Tick数据: {event.data}")

    def on_order(event):
        print(f"收到委托数据: {event.data}")

    # 注册处理函数
    engine.register("tick", on_tick)
    engine.register("order", on_order)

    # 启动引擎
    engine.start()

    # 模拟产生事件
    engine.put(Event("tick", {"symbol": "rb2401", "price": 3800}))
    engine.put(Event("order", {"orderid": "001", "volume": 5}))

    # 运行一段时间后停止
    import time
    time.sleep(1)
    engine.stop()
    ```

这个简化模型清晰地展示了事件引擎的三大核心：**事件队列**、**处理函数注册表**和**分发线程**。VNPY 官方的事件引擎在此基础上增加了更多功能，如定时事件、日志记录等，但基本架构与此一致。

## 总结

![](img/79f072d372d3565f5f18aaeb5e38550a_49.png)

本节课中我们一起学习了 VNPY 事件引擎的核心原理。我们从环境安装讲起，理解了输出重定向，浏览了源码结构，并最终通过自建一个简化的事件引擎，掌握了其**事件封装**、**队列管理**、**函数注册**和**线程分发**的工作机制。事件引擎是理解 VNPY 所有高级功能（如自动交易模块、回测模块）的基石，后续课程将在此基础上展开。