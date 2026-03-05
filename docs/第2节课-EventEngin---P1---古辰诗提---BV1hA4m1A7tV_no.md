# 从零开始量化：2：事件引擎（EventEngine）详解 🚀

![](img/f60ec97a50afbe564bf6e4fa16b8e093_0.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_2.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_4.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_6.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_8.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_10.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_12.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_14.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_16.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_18.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_20.png)

在本节课中，我们将深入学习事件引擎（EventEngine）的核心实现。上一节课我们介绍了事件注册的基本概念，本节中我们将详细探讨事件的分发机制、取消注册、计时器功能以及一些高级应用和注意事项。

![](img/f60ec97a50afbe564bf6e4fa16b8e093_22.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_24.png)

## 概述

![](img/f60ec97a50afbe564bf6e4fa16b8e093_26.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_28.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_30.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_32.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_34.png)

事件引擎是量化交易系统中的核心驱动模块，负责接收、管理和分发各种事件（如行情数据、订单信息、日志等）。理解其工作原理对于构建稳定高效的系统至关重要。

![](img/f60ec97a50afbe564bf6e4fa16b8e093_36.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_38.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_40.png)

## 事件分发机制

![](img/f60ec97a50afbe564bf6e4fa16b8e093_42.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_44.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_45.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_47.png)

上一节我们介绍了如何向引擎注册处理特定事件类型的方法。注册完成后，当有对应类型的数据（事件）被放入引擎时，引擎需要将这些数据分发给所有已注册的处理方法。

![](img/f60ec97a50afbe564bf6e4fa16b8e093_49.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_51.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_53.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_55.png)

以下是实现事件分发的核心逻辑。当`process`函数处理一个事件时，它会根据事件的类型（`event.type`）从字典中获取对应的处理函数列表，然后依次调用它们。

![](img/f60ec97a50afbe564bf6e4fa16b8e093_57.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_59.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_61.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_63.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_65.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_66.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_68.png)

```python
def process(self, event):
    """
    处理单个事件。
    """
    if event.type in self._handlers:
        # 使用列表推导式执行所有注册的处理函数
        [handler(event) for handler in self._handlers[event.type]]
```

![](img/f60ec97a50afbe564bf6e4fa16b8e093_70.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_72.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_74.png)

**代码解释**：
*   `self._handlers` 是一个字典，键（key）是事件类型，值（value）是该类型事件对应的处理函数列表。
*   `[handler(event) for handler in self._handlers[event.type]]` 这行代码是列表推导式。它会遍历`self._handlers[event.type]`列表中的每一个处理函数（`handler`），并执行`handler(event)`。这样，所有注册的函数都会被调用。
*   使用列表推导式的好处是代码简洁，且原列表`self._handlers[event.type]`不会被修改。

![](img/f60ec97a50afbe564bf6e4fa16b8e093_76.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_78.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_80.png)

## 取消注册机制

![](img/f60ec97a50afbe564bf6e4fa16b8e093_82.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_84.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_86.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_88.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_90.png)

在程序运行过程中，某些处理模块（如策略）可能需要被动态移除。如果引擎仍在运行，而对应的处理函数已经失效，继续分发事件会导致错误。因此，我们需要一个取消注册的机制。

![](img/f60ec97a50afbe564bf6e4fa16b8e093_92.png)

取消注册的逻辑与注册相反，需要从对应事件类型的处理列表中移除指定的函数。

![](img/f60ec97a50afbe564bf6e4fa16b8e093_94.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_96.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_98.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_100.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_102.png)

```python
def unregister(self, type, handler):
    """
    取消注册特定事件类型的处理函数。
    """
    # 获取该事件类型对应的处理函数列表
    handler_list = self._handlers.get(type, None)

    # 如果列表存在且目标函数在列表中，则移除它
    if handler_list and handler in handler_list:
        handler_list.remove(handler)

    # 如果移除后列表为空，则从字典中删除这个键，以保持简洁
    if not handler_list:
        self._handlers.pop(type, None)
```

![](img/f60ec97a50afbe564bf6e4fa16b8e093_104.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_106.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_108.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_110.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_112.png)

**核心步骤**：
1.  **定位列表**：根据事件类型`type`，从`self._handlers`字典中获取对应的处理函数列表`handler_list`。
2.  **移除函数**：如果列表存在且目标函数`handler`在列表中，则使用`list.remove()`方法将其移除。
3.  **清理空列表**：如果移除后列表变为空，则使用`dict.pop()`方法将这个事件类型从字典中删除，避免字典中存在无用的空列表。

![](img/f60ec97a50afbe564bf6e4fa16b8e093_114.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_116.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_118.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_120.png)

## 通用事件处理

![](img/f60ec97a50afbe564bf6e4fa16b8e093_122.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_124.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_126.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_128.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_130.png)

除了按类型分发事件，有时我们需要一个函数来处理所有类型的事件。例如，一个负责将所有事件转发到网络或记录到日志的模块。

![](img/f60ec97a50afbe564bf6e4fa16b8e093_132.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_133.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_135.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_137.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_139.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_141.png)

为了实现这个功能，我们可以维护一个单独的“通用处理函数”列表。

![](img/f60ec97a50afbe564bf6e4fa16b8e093_143.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_145.png)

以下是注册通用处理函数的方法：

![](img/f60ec97a50afbe564bf6e4fa16b8e093_147.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_149.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_151.png)

```python
def register_general(self, handler):
    """
    注册一个通用处理函数，它将接收所有类型的事件。
    """
    if handler not in self._general_handlers:
        self._general_handlers.append(handler)
```

![](img/f60ec97a50afbe564bf6e4fa16b8e093_153.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_155.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_157.png)

在事件分发时（`process`函数中），除了调用特定类型的处理函数，也需要调用所有通用处理函数。

![](img/f60ec97a50afbe564bf6e4fa16b8e093_159.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_161.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_163.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_165.png)

```python
def process(self, event):
    """
    处理单个事件。
    """
    # 1. 调用特定类型的处理函数
    if event.type in self._handlers:
        [handler(event) for handler in self._handlers[event.type]]

    # 2. 调用通用处理函数
    if self._general_handlers:
        [handler(event) for handler in self._general_handlers]
```

![](img/f60ec97a50afbe564bf6e4fa16b8e093_167.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_169.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_171.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_173.png)

## 计时器功能

![](img/f60ec97a50afbe564bf6e4fa16b8e093_175.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_177.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_179.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_181.png)

在交易系统中，有些操作需要定期执行，例如定时查询账户资金、持仓信息，或者执行某些定时任务。事件引擎通过内置的计时器线程来实现这个功能。

![](img/f60ec97a50afbe564bf6e4fa16b8e093_183.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_185.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_187.png)

计时器线程会按照设定的时间间隔，周期性地向事件队列中放入一个特殊类型的事件（例如`EVENT_TIMER`）。

![](img/f60ec97a50afbe564bf6e4fa16b8e093_189.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_191.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_193.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_195.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_197.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_199.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_200.png)

以下是计时器线程的核心逻辑：

![](img/f60ec97a50afbe564bf6e4fa16b8e093_202.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_204.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_206.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_208.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_210.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_212.png)

```python
def _timer_loop(self):
    """
    计时器线程循环函数。
    """
    while self._active:
        # 创建一个计时器事件
        event = Event(type=EVENT_TIMER)
        # 将事件放入队列
        self.put(event)
        # 休眠指定的时间间隔
        sleep(self._timer_interval)
```

![](img/f60ec97a50afbe564bf6e4fa16b8e093_213.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_215.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_217.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_219.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_221.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_223.png)

**实现要点**：
*   `self._active`是一个标志，控制线程是否继续运行。
*   `self._timer_interval`是时间间隔，可以在初始化引擎时设置。
*   其他模块可以像处理普通事件一样，注册处理`EVENT_TIMER`类型事件的函数，从而定期执行某些逻辑。

![](img/f60ec97a50afbe564bf6e4fa16b8e093_225.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_227.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_229.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_230.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_232.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_234.png)

## 高级应用与注意事项

![](img/f60ec97a50afbe564bf6e4fa16b8e093_236.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_238.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_240.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_242.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_244.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_246.png)

事件引擎是一个基础且灵活的模块，可以根据实际需求进行改造和优化。

![](img/f60ec97a50afbe564bf6e4fa16b8e093_248.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_250.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_252.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_254.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_256.png)

### 多队列分离

![](img/f60ec97a50afbe564bf6e4fa16b8e093_258.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_259.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_261.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_263.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_265.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_267.png)

在默认实现中，所有事件共享同一个队列。在高频或数据量大的场景下，某些低优先级的事件（如日志）可能会因为队列前方堆积了大量高优先级事件（如行情Tick）而被延迟处理。

![](img/f60ec97a50afbe564bf6e4fa16b8e093_269.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_271.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_273.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_275.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_277.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_279.png)

解决方案是为不同优先级或不同类型的事件创建独立的队列和处理线程。例如，可以单独创建一个日志队列和日志处理线程。

![](img/f60ec97a50afbe564bf6e4fa16b8e093_281.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_283.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_285.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_287.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_289.png)

```python
# 在初始化时创建额外的队列
self._log_queue = Queue()

![](img/f60ec97a50afbe564bf6e4fa16b8e093_291.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_293.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_295.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_297.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_299.png)

# 在put事件时进行分流
def put(self, event):
    if event.type == EVENT_LOG:
        self._log_queue.put(event)
    else:
        self._queue.put(event)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_301.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_302.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_304.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_306.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_308.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_309.png)

# 单独启动一个线程处理日志队列
self._log_thread = Thread(target=self._process_log)
self._log_thread.start()
```

![](img/f60ec97a50afbe564bf6e4fa16b8e093_311.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_313.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_315.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_317.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_318.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_320.png)

### 进程间通信限制

![](img/f60ec97a50afbe564bf6e4fa16b8e093_322.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_324.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_326.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_328.png)

需要特别注意，Python标准库`queue.Queue`是线程安全的，但**仅限于同一进程内的多个线程之间共享**。它不能用于不同进程之间的通信。

![](img/f60ec97a50afbe564bf6e4fa16b8e093_330.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_332.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_334.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_336.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_338.png)

如果你的系统设计涉及多个进程（例如，一个进程负责行情录制，另一个进程负责交易），并需要在这些进程间传递事件，则必须使用`multiprocessing`模块中的`Queue`或其他进程间通信（IPC）机制，例如管道（Pipe）、共享内存（Shared Memory）或网络套接字（Socket）。

![](img/f60ec97a50afbe564bf6e4fa16b8e093_340.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_342.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_343.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_345.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_347.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_349.png)

**重要提醒**：`threading.Queue`用于线程间通信；`multiprocessing.Queue`用于进程间通信。两者不可混淆。

![](img/f60ec97a50afbe564bf6e4fa16b8e093_351.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_353.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_355.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_357.png)

## 总结

![](img/f60ec97a50afbe564bf6e4fa16b8e093_359.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_361.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_363.png)

本节课我们一起深入学习了事件引擎（EventEngine）的完整实现。我们从核心的事件分发机制开始，了解了如何使用列表推导式高效地调用处理函数。接着，我们探讨了取消注册机制的必要性和实现方法，以确保引擎在动态环境下的稳定性。然后，我们介绍了通用事件处理和计时器功能，扩展了引擎的适用场景。最后，我们讨论了一些高级应用，如多队列分离以优化性能，并重点强调了线程队列与进程队列的区别，这是实际开发中一个关键的注意事项。

![](img/f60ec97a50afbe564bf6e4fa16b8e093_365.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_367.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_369.png)

![](img/f60ec97a50afbe564bf6e4fa16b8e093_371.png)

事件引擎作为整个量化交易框架的驱动器，其稳定性和效率直接影响整个系统。务必充分理解并熟练掌握其原理，才能在此基础上构建出健壮的交易应用。