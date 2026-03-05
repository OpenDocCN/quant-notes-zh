# Python基础课：42：事件引擎（EventEngine）详解 🚀

![](img/8f8018eb8fc39bb20b3d75903d1c352c_1.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_3.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_5.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_7.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_9.png)

在本节课中，我们将学习Python中一个非常重要的概念——事件引擎（EventEngine）。这是量化交易平台（如vn.py）的核心组件之一，用于高效地管理和分发事件。我们将从队列（Queue）的回顾开始，逐步构建一个简化版的事件引擎，并理解其工作原理。

![](img/8f8018eb8fc39bb20b3d75903d1c352c_11.png)

上一节我们介绍了队列的基本操作，本节中我们来看看如何利用队列构建一个事件驱动的引擎。

![](img/8f8018eb8fc39bb20b3d75903d1c352c_13.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_15.png)

## 队列回顾与进程间通信

![](img/8f8018eb8fc39bb20b3d75903d1c352c_17.png)

首先，我们快速回顾一下队列（`queue.Queue`）的核心方法：`put`（放入数据）和`get`（取出数据）。以下是其基本用法：

![](img/8f8018eb8fc39bb20b3d75903d1c352c_19.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_21.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_23.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_25.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_27.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_29.png)

```python
from queue import Queue

![](img/8f8018eb8fc39bb20b3d75903d1c352c_30.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_32.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_34.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_36.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_38.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_40.png)

q = Queue(maxsize=1)  # 创建一个最大容量为1的队列
q.put(1, block=True)  # 放入数据1，如果队列满则阻塞
# q.put(2, block=False)  # 如果队列满且不阻塞，会立即报错 queue.Full
result = q.get(block=True)  # 取出数据，如果队列空则阻塞
# result = q.get(block=False)  # 如果队列空且不阻塞，会立即报错 queue.Empty
```

![](img/8f8018eb8fc39bb20b3d75903d1c352c_42.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_44.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_46.png)

队列本质上是一个先进先出（FIFO）的数据结构。我们甚至可以自己实现一个简易版本：

![](img/8f8018eb8fc39bb20b3d75903d1c352c_48.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_50.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_52.png)

```python
from typing import Any

class MyQueue:
    def __init__(self):
        self.data_list = []

    def put(self, item: Any):
        """将元素放入队列末尾"""
        self.data_list.append(item)

    def get(self) -> Any:
        """从队列头部取出并移除一个元素"""
        if not self.data_list:
            raise Exception("Queue is empty")
        return self.data_list.pop(0)
```

![](img/8f8018eb8fc39bb20b3d75903d1c352c_54.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_56.png)

然而，在**多进程**编程中，线程安全的`queue.Queue`无法直接在进程间共享数据。这时需要使用`multiprocessing`模块中的`Queue`，它通过开辟共享内存空间来实现进程间通信。

![](img/8f8018eb8fc39bb20b3d75903d1c352c_58.png)

以下是使用进程队列在自定义进程类中传递结果的示例：

![](img/8f8018eb8fc39bb20b3d75903d1c352c_60.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_62.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_64.png)

```python
import multiprocessing
import time

![](img/8f8018eb8fc39bb20b3d75903d1c352c_66.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_68.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_70.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_72.png)

class MyProcess(multiprocessing.Process):
    def __init__(self, name, sleep_time, q):
        super().__init__()
        self.name = name
        self.sleep_time = sleep_time
        self.q = q
        self._result = None

    def run(self):
        print(f"子进程 {self.name} 开始执行")
        time.sleep(self.sleep_time)
        self._result = f"{self.name} 任务完成"
        print(f"子进程 {self.name} 执行结束")
        # 将结果放入队列
        self.q.put(self._result)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_74.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_75.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_77.png)

if __name__ == "__main__":
    q = multiprocessing.Queue()
    mp = MyProcess(name="测试进程", sleep_time=2, q=q)
    mp.start()
    mp.join()
    # 从队列中获取子进程的结果
    result = q.get()
    print(f"主进程获取到结果: {result}")
    print("程序结束")
```

![](img/8f8018eb8fc39bb20b3d75903d1c352c_78.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_80.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_82.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_83.png)

理解了队列在进程间的应用后，我们就可以进入事件引擎的核心构建环节。

![](img/8f8018eb8fc39bb20b3d75903d1c352c_85.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_87.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_89.png)

## 事件引擎（EventEngine）设计与实现

![](img/8f8018eb8fc39bb20b3d75903d1c352c_91.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_92.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_94.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_96.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_98.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_100.png)

事件引擎的核心思想是**解耦**。它将事件的**产生**和**处理**分离：一个线程负责将不同类型的事件放入队列，另一个线程负责从队列中取出事件，并根据事件类型调用预先注册好的处理函数。

![](img/8f8018eb8fc39bb20b3d75903d1c352c_102.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_104.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_106.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_108.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_110.png)

### 1. 事件封装（Event）

![](img/8f8018eb8fc39bb20b3d75903d1c352c_112.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_114.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_116.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_118.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_119.png)

首先，我们需要定义一个`Event`类来封装所有事件。每个事件包含一个类型（`type`）和对应的数据（`data`）。

![](img/8f8018eb8fc39bb20b3d75903d1c352c_121.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_123.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_125.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_127.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_129.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_131.png)

```python
from typing import Any

![](img/8f8018eb8fc39bb20b3d75903d1c352c_133.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_135.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_136.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_138.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_140.png)

class Event:
    """事件类，用于封装所有传递的数据"""
    def __init__(self, type: str, data: Any = None):
        self.type = type  # 事件类型，例如 'tick', 'order'
        self.data = data  # 事件携带的具体数据
```

![](img/8f8018eb8fc39bb20b3d75903d1c352c_142.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_144.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_146.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_148.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_150.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_152.png)

### 2. 事件引擎主体（EventEngine）

![](img/8f8018eb8fc39bb20b3d75903d1c352c_154.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_156.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_158.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_160.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_162.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_164.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_166.png)

接下来，我们构建事件引擎的主体类`EventEngine`。

![](img/8f8018eb8fc39bb20b3d75903d1c352c_168.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_170.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_172.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_174.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_176.png)

以下是其初始化部分，包含核心组件：

![](img/8f8018eb8fc39bb20b3d75903d1c352c_178.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_180.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_182.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_184.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_186.png)

```python
from queue import Queue
from threading import Thread
from collections import defaultdict
from typing import Callable, List
import time

![](img/8f8018eb8fc39bb20b3d75903d1c352c_188.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_190.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_192.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_194.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_196.png)

class EventEngine:
    def __init__(self, interval: float = 1.0):
        """
        初始化事件引擎
        :param interval: 定时器触发间隔（秒）
        """
        # 事件队列
        self._queue = Queue()
        # 事件处理函数字典，键为事件类型，值为处理函数列表
        self._handlers = defaultdict(list)
        # 通用事件处理函数列表（所有事件都会触发）
        self._general_handlers = []
        # 引擎活动状态开关
        self._active = False
        # 事件处理线程
        self._thread = Thread(target=self._run)
        # 定时器线程（用于周期性触发事件，如查询持仓）
        self._timer_thread = Thread(target=self._run_timer)
        self._interval = interval
```

![](img/8f8018eb8fc39bb20b3d75903d1c352c_198.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_200.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_202.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_204.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_206.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_208.png)

### 3. 核心方法详解

![](img/8f8018eb8fc39bb20b3d75903d1c352c_210.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_212.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_214.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_216.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_218.png)

事件引擎需要提供注册/注销处理函数、启动/停止引擎、放入事件等方法。

![](img/8f8018eb8fc39bb20b3d75903d1c352c_220.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_221.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_223.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_225.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_226.png)

**注册与注销处理函数**

![](img/8f8018eb8fc39bb20b3d75903d1c352c_228.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_230.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_232.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_234.png)

以下是注册和注销特定类型事件处理函数的方法：

![](img/8f8018eb8fc39bb20b3d75903d1c352c_236.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_238.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_240.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_242.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_244.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_246.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_248.png)

```python
    def register(self, type: str, handler: Callable):
        """注册特定类型事件的处理函数"""
        handler_list = self._handlers[type]
        if handler not in handler_list:
            handler_list.append(handler)

    def unregister(self, type: str, handler: Callable):
        """注销特定类型事件的处理函数"""
        handler_list = self._handlers.get(type, [])
        if handler in handler_list:
            handler_list.remove(handler)
        # 如果该类型下已无处理函数，则删除这个键
        if not handler_list:
            self._handlers.pop(type, None)
```

![](img/8f8018eb8fc39bb20b3d75903d1c352c_250.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_252.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_254.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_256.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_258.png)

对于需要处理所有事件的通用函数，也有对应的方法：

![](img/8f8018eb8fc39bb20b3d75903d1c352c_260.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_262.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_264.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_266.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_268.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_270.png)

```python
    def register_general(self, handler: Callable):
        """注册通用事件处理函数（处理所有类型事件）"""
        if handler not in self._general_handlers:
            self._general_handlers.append(handler)

    def unregister_general(self, handler: Callable):
        """注销通用事件处理函数"""
        if handler in self._general_handlers:
            self._general_handlers.remove(handler)
```

![](img/8f8018eb8fc39bb20b3d75903d1c352c_272.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_274.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_276.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_278.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_280.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_282.png)

**启动与停止引擎**

![](img/8f8018eb8fc39bb20b3d75903d1c352c_284.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_286.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_288.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_290.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_292.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_294.png)

引擎需要启动线程来运行：

![](img/8f8018eb8fc39bb20b3d75903d1c352c_296.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_298.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_300.png)

```python
    def start(self):
        """启动事件引擎"""
        self._active = True
        self._thread.start()
        self._timer_thread.start()
        print("事件引擎启动")

    def stop(self):
        """停止事件引擎"""
        self._active = False
        # 等待线程结束
        self._thread.join()
        self._timer_thread.join()
        print("事件引擎停止")
```

![](img/8f8018eb8fc39bb20b3d75903d1c352c_302.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_304.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_306.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_308.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_309.png)

**放入事件**

![](img/8f8018eb8fc39bb20b3d75903d1c352c_311.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_313.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_315.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_317.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_319.png)

任何地方都可以通过`put`方法向引擎发送事件：

![](img/8f8018eb8fc39bb20b3d75903d1c352c_321.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_323.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_325.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_327.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_329.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_331.png)

```python
    def put(self, event: Event):
        """向事件队列中放入一个新事件"""
        self._queue.put(event)
```

![](img/8f8018eb8fc39bb20b3d75903d1c352c_333.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_335.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_337.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_339.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_341.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_343.png)

### 4. 引擎内部运行逻辑

![](img/8f8018eb8fc39bb20b3d75903d1c352c_345.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_347.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_349.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_351.png)

引擎的核心运行逻辑在两个后台线程中：`_run`负责处理事件，`_run_timer`负责定时触发事件。

![](img/8f8018eb8fc39bb20b3d75903d1c352c_353.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_355.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_357.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_359.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_361.png)

**事件处理线程（_run）**

![](img/8f8018eb8fc39bb20b3d75903d1c352c_363.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_365.png)

这个线程不断从队列中获取事件并分发给对应的处理函数。

![](img/8f8018eb8fc39bb20b3d75903d1c352c_367.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_369.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_371.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_373.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_375.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_377.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_379.png)

```python
    def _run(self):
        """事件处理线程的主循环"""
        while self._active:
            try:
                # 从队列中获取事件，阻塞等待最多1秒
                event = self._queue.get(block=True, timeout=1)
                # 处理事件
                self._process(event)
            except Exception:  # 主要是捕获 queue.Empty 超时异常
                continue  # 继续循环

    def _process(self, event: Event):
        """处理单个事件"""
        # 1. 处理特定类型事件的函数
        if event.type in self._handlers:
            # 使用列表推导式简洁地调用所有注册函数
            [handler(event) for handler in self._handlers[event.type]]
        # 2. 处理通用事件函数
        if self._general_handlers:
            [handler(event) for handler in self._general_handlers]
```

![](img/8f8018eb8fc39bb20b3d75903d1c352c_381.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_383.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_385.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_387.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_389.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_391.png)

**定时器线程（_run_timer）**

![](img/8f8018eb8fc39bb20b3d75903d1c352c_393.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_395.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_397.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_399.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_401.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_403.png)

这个线程周期性地向队列中放入定时事件，常用于定时查询账户或持仓信息。

![](img/8f8018eb8fc39bb20b3d75903d1c352c_405.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_407.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_409.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_411.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_413.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_415.png)

```python
    def _run_timer(self):
        """定时器线程，用于周期性触发事件"""
        while self._active:
            time.sleep(self._interval)
            # 创建一个定时事件
            event = Event(type="timer")
            self.put(event)
```

![](img/8f8018eb8fc39bb20b3d75903d1c352c_417.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_419.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_421.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_423.png)

## 事件引擎使用示例

![](img/8f8018eb8fc39bb20b3d75903d1c352c_425.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_427.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_429.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_431.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_433.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_435.png)

现在，让我们看一个完整的使用示例，演示如何注册事件并运行引擎。

![](img/8f8018eb8fc39bb20b3d75903d1c352c_437.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_439.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_441.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_443.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_445.png)

```python
# 定义一个处理函数
def process_tick(event: Event):
    print(f"处理Tick事件，数据: {event.data}")

![](img/8f8018eb8fc39bb20b3d75903d1c352c_447.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_449.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_451.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_453.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_455.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_457.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_459.png)

def process_general(event: Event):
    print(f"通用处理器收到事件，类型: {event.type}")

![](img/8f8018eb8fc39bb20b3d75903d1c352c_461.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_463.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_465.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_467.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_469.png)

if __name__ == "__main__":
    # 1. 实例化事件引擎
    engine = EventEngine(interval=2.0)  # 定时器每2秒触发一次

    # 2. 注册事件处理函数
    engine.register("tick", process_tick)
    engine.register_general(process_general)

    # 3. 启动引擎
    engine.start()

    # 4. 模拟产生一些事件
    engine.put(Event(type="tick", data={"price": 100.5, "volume": 200}))
    engine.put(Event(type="order", data={"order_id": "12345"}))

    # 5. 让主线程等待一段时间，观察定时器事件和已放入的事件被处理
    time.sleep(5)

    # 6. 停止引擎
    engine.stop()
```

![](img/8f8018eb8fc39bb20b3d75903d1c352c_471.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_473.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_475.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_477.png)

运行上述代码，你会看到`tick`事件被`process_tick`函数处理，而所有事件（包括`tick`、`order`和定时产生的`timer`事件）都会被`process_general`函数处理。这完美演示了事件引擎如何将事件的产生与处理解耦。

![](img/8f8018eb8fc39bb20b3d75903d1c352c_479.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_481.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_483.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_485.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_487.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_489.png)

## 总结

![](img/8f8018eb8fc39bb20b3d75903d1c352c_491.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_493.png)

本节课中我们一起学习了Python事件引擎（EventEngine）的核心原理与实现。我们首先回顾了队列在多线程和多进程环境下的应用，然后逐步构建了一个功能完整的事件引擎。这个引擎通过**事件队列**、**事件分发**和**处理函数注册**机制，实现了程序组件间的松耦合通信，这是构建复杂异步系统（如量化交易平台）的基石。

![](img/8f8018eb8fc39bb20b3d75903d1c352c_495.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_497.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_499.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_501.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_503.png)

事件引擎的设计模式并不复杂，但其思想非常强大。在vn.py等框架中，它就像**心脏或血管**，连接着行情获取（Gateway）、策略逻辑（Strategy）、风险控制（RiskManager）等各个模块，让数据能够有序、高效地流动。理解它，对你后续深入学习量化框架源码至关重要。

![](img/8f8018eb8fc39bb20b3d75903d1c352c_505.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_507.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_509.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_511.png)

![](img/8f8018eb8fc39bb20b3d75903d1c352c_513.png)

Python基础课的核心内容至此告一段落。掌握**函数**、**类**（特别是魔术方法）、**多线程/多进程**以及像事件引擎这样的**设计模式**，是你能读懂乃至贡献开源项目代码的关键。建议下一步可以深入阅读vn.py等框架的源码，在实践中巩固和提升。