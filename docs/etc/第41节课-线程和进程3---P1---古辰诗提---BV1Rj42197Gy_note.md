# Python基础课：41：进程与进程锁、进程池及进程间通信

![](img/fd86a375816514636e24c0ceaf081637_1.png)

![](img/fd86a375816514636e24c0ceaf081637_3.png)

![](img/fd86a375816514636e24c0ceaf081637_5.png)

在本节课中，我们将深入学习Python多进程编程的核心概念，包括进程锁的使用、进程池的创建与管理，以及进程间通信的基本方法。我们将通过对比线程与进程的本质区别，帮助你理解为何在多进程环境中需要特殊的处理方式。

## 进程与线程的本质区别

![](img/fd86a375816514636e24c0ceaf081637_7.png)

上一节我们介绍了线程和进程的基本概念，本节中我们来看看它们在内存使用上的根本差异。

![](img/fd86a375816514636e24c0ceaf081637_9.png)

![](img/fd86a375816514636e24c0ceaf081637_11.png)

线程可以共享同一块内存空间中的所有数据。这意味着多个线程在操作同一个变量时，可能会造成数据污染。

**公式/代码描述：**
```python
# 线程共享内存
import threading
shared_data = 0

![](img/fd86a375816514636e24c0ceaf081637_13.png)

def thread_task():
    global shared_data
    shared_data += 1
```

![](img/fd86a375816514636e24c0ceaf081637_15.png)

![](img/fd86a375816514636e24c0ceaf081637_17.png)

进程则会为自身重新开辟一块独立的内存空间。两个进程之间的变量和资源默认不会共用。如果需要在进程间传递数据，必须使用特殊的方法。

![](img/fd86a375816514636e24c0ceaf081637_19.png)

![](img/fd86a375816514636e24c0ceaf081637_21.png)

![](img/fd86a375816514636e24c0ceaf081637_23.png)

![](img/fd86a375816514636e24c0ceaf081637_25.png)

**公式/代码描述：**
```python
# 进程内存独立
import multiprocessing
data = []

![](img/fd86a375816514636e24c0ceaf081637_27.png)

![](img/fd86a375816514636e24c0ceaf081637_29.png)

![](img/fd86a375816514636e24c0ceaf081637_31.png)

def process_task():
    # 此处的 data 是主进程中 data 的副本，修改它不影响主进程的 data
    data.append(1)
```

![](img/fd86a375816514636e24c0ceaf081637_33.png)

理解这个本质区别，是避免未来在多进程编程中出现“莫名其妙”的bug的关键。

![](img/fd86a375816514636e24c0ceaf081637_35.png)

## 进程锁

![](img/fd86a375816514636e24c0ceaf081637_37.png)

![](img/fd86a375816514636e24c0ceaf081637_39.png)

尽管进程拥有独立的内存空间，但它们仍可能访问和修改共享的“公共资源”，例如同一个文件、数据库连接或网络端口。为了防止资源竞争导致的数据错乱，进程也需要使用锁。

![](img/fd86a375816514636e24c0ceaf081637_41.png)

进程锁与线程锁非常相似，主要也有两种类型：
*   **互斥锁**：一次只允许一个进程持有锁。
*   **递归锁**：允许同一个进程多次获取同一把锁。

![](img/fd86a375816514636e24c0ceaf081637_43.png)

以下是创建进程锁的方法：

**公式/代码描述：**
```python
import multiprocessing

![](img/fd86a375816514636e24c0ceaf081637_45.png)

# 创建互斥锁
mutex_lock = multiprocessing.Lock()
# 创建递归锁
recursive_lock = multiprocessing.RLock()
```

![](img/fd86a375816514636e24c0ceaf081637_47.png)

![](img/fd86a375816514636e24c0ceaf081637_49.png)

![](img/fd86a375816514636e24c0ceaf081637_51.png)

### 进程锁的正确使用方式

![](img/fd86a375816514636e24c0ceaf081637_53.png)

![](img/fd86a375816514636e24c0ceaf081637_55.png)

由于进程内存独立，直接将主进程中创建的锁用在子进程的函数里是无效的，因为子进程会复制一份锁，导致两把锁不是同一个对象。

![](img/fd86a375816514636e24c0ceaf081637_57.png)

为了让锁生效，必须将锁作为参数传递给子进程函数。

![](img/fd86a375816514636e24c0ceaf081637_59.png)

![](img/fd86a375816514636e24c0ceaf081637_61.png)

**公式/代码描述：**
```python
import multiprocessing
import time

![](img/fd86a375816514636e24c0ceaf081637_63.png)

![](img/fd86a375816514636e24c0ceaf081637_65.png)

def task(lock, name, duration):
    with lock:  # 使用传入的锁
        print(f"子进程 {name} 任务开始执行")
        time.sleep(duration)
        print(f"子进程 {name} 任务执行结束")

![](img/fd86a375816514636e24c0ceaf081637_67.png)

![](img/fd86a375816514636e24c0ceaf081637_69.png)

if __name__ == '__main__':
    lock = multiprocessing.Lock()  # 在主进程创建锁
    p1 = multiprocessing.Process(target=task, args=(lock, "A", 2))  # 将锁作为参数传入
    p2 = multiprocessing.Process(target=task, args=(lock, "B", 1))
    p1.start()
    p2.start()
    p1.join()
    p2.join()
    print("End")
```
运行上述代码，你会看到任务A和任务B是串行执行的，说明锁起到了作用。

![](img/fd86a375816514636e24c0ceaf081637_71.png)

![](img/fd86a375816514636e24c0ceaf081637_73.png)

## 进程池

![](img/fd86a375816514636e24c0ceaf081637_75.png)

![](img/fd86a375816514636e24c0ceaf081637_77.png)

与线程池类似，当需要管理大量进程时，使用进程池（`ProcessPoolExecutor`）是更高效的方式。它可以复用固定数量的进程来执行多个任务，避免频繁创建和销毁进程的开销。

![](img/fd86a375816514636e24c0ceaf081637_79.png)

![](img/fd86a375816514636e24c0ceaf081637_81.png)

![](img/fd86a375816514636e24c0ceaf081637_83.png)

![](img/fd86a375816514636e24c0ceaf081637_85.png)

以下是导入和创建进程池的方法：

![](img/fd86a375816514636e24c0ceaf081637_87.png)

![](img/fd86a375816514636e24c0ceaf081637_88.png)

**公式/代码描述：**
```python
from concurrent.futures import ProcessPoolExecutor

![](img/fd86a375816514636e24c0ceaf081637_90.png)

![](img/fd86a375816514636e24c0ceaf081637_92.png)

![](img/fd86a375816514636e24c0ceaf081637_94.png)

# 创建一个最大进程数为3的进程池
with ProcessPoolExecutor(max_workers=3) as pool:
    # 使用 submit 方法提交任务
    future = pool.submit(task_function, arg1, arg2)
```
`ProcessPoolExecutor` 的接口与 `ThreadPoolExecutor` 几乎一致，主要区别在于底层的执行单元是进程而非线程。

![](img/fd86a375816514636e24c0ceaf081637_96.png)

![](img/fd86a375816514636e24c0ceaf081637_98.png)

![](img/fd86a375816514636e24c0ceaf081637_100.png)

**需要注意的参数：**
*   `mp_context`: 用于设置创建子进程的方式（如 `fork`, `spawn`）。在不同操作系统上默认值不同，通常无需手动设置。
*   `initializer` 和 `initargs`: 用于在每个工作进程启动时执行初始化函数并传入参数，常用于导入模块或设置环境。

![](img/fd86a375816514636e24c0ceaf081637_102.png)

![](img/fd86a375816514636e24c0ceaf081637_104.png)

![](img/fd86a375816514636e24c0ceaf081637_106.png)

**公式/代码描述：**
```python
def init_process(task_name):
    print(f"进程初始化，准备执行 {task_name} 任务")

![](img/fd86a375816514636e24c0ceaf081637_108.png)

![](img/fd86a375816514636e24c0ceaf081637_110.png)

with ProcessPoolExecutor(max_workers=2,
                         initializer=init_process,
                         initargs=('测试',)) as pool:
    # 提交任务...
    pass
```

![](img/fd86a375816514636e24c0ceaf081637_112.png)

![](img/fd86a375816514636e24c0ceaf081637_114.png)

### 进程池中的返回值处理

![](img/fd86a375816514636e24c0ceaf081637_116.png)

在进程池中获取任务返回值，推荐使用 `future.result()` 或回调函数 `add_done_callback`，这与线程池的做法相同。

![](img/fd86a375816514636e24c0ceaf081637_118.png)

![](img/fd86a375816514636e24c0ceaf081637_120.png)

**不推荐**使用继承 `multiprocessing.Process` 类并重写 `run` 和 `join` 方法来获取返回值。因为进程间内存不共享，主进程无法直接访问子进程内部设置的 `_result` 属性，会导致返回 `None`。

![](img/fd86a375816514636e24c0ceaf081637_122.png)

![](img/fd86a375816514636e24c0ceaf081637_124.png)

![](img/fd86a375816514636e24c0ceaf081637_126.png)

## 进程间通信

![](img/fd86a375816514636e24c0ceaf081637_128.png)

![](img/fd86a375816514636e24c0ceaf081637_130.png)

![](img/fd86a375816514636e24c0ceaf081637_132.png)

既然进程内存独立，那么它们如何交换数据呢？这就需要进程间通信（IPC）机制。最常用和简单的方式是使用队列（`Queue`）。

![](img/fd86a375816514636e24c0ceaf081637_134.png)

![](img/fd86a375816514636e24c0ceaf081637_135.png)

![](img/fd86a375816514636e24c0ceaf081637_137.png)

![](img/fd86a375816514636e24c0ceaf081637_139.png)

### 队列 (Queue)

![](img/fd86a375816514636e24c0ceaf081637_141.png)

![](img/fd86a375816514636e24c0ceaf081637_143.png)

![](img/fd86a375816514636e24c0ceaf081637_145.png)

队列是一个遵循先进先出（FIFO）原则的管道。一个进程可以向队列中放入数据，另一个进程可以从队列中取出数据。

![](img/fd86a375816514636e24c0ceaf081637_147.png)

进程使用的队列需要从 `multiprocessing` 模块导入。

![](img/fd86a375816514636e24c0ceaf081637_149.png)

![](img/fd86a375816514636e24c0ceaf081637_150.png)

![](img/fd86a375816514636e24c0ceaf081637_152.png)

![](img/fd86a375816514636e24c0ceaf081637_154.png)

**公式/代码描述：**
```python
from multiprocessing import Queue

![](img/fd86a375816514636e24c0ceaf081637_156.png)

![](img/fd86a375816514636e24c0ceaf081637_158.png)

![](img/fd86a375816514636e24c0ceaf081637_160.png)

# 创建一个最大容量为10的队列
q = Queue(maxsize=10)
```

![](img/fd86a375816514636e24c0ceaf081637_162.png)

![](img/fd86a375816514636e24c0ceaf081637_164.png)

![](img/fd86a375816514636e24c0ceaf081637_166.png)

以下是队列的常用方法：

![](img/fd86a375816514636e24c0ceaf081637_168.png)

![](img/fd86a375816514636e24c0ceaf081637_170.png)

**公式/代码描述：**
```python
# 放入数据
q.put(item, block=True, timeout=None)
# 取出数据
item = q.get(block=True, timeout=None)
# 判断队列是否为空
is_empty = q.empty()
# 判断队列是否已满
is_full = q.full()
# 获取队列当前大小
current_size = q.qsize()
```

![](img/fd86a375816514636e24c0ceaf081637_172.png)

![](img/fd86a375816514636e24c0ceaf081637_174.png)

![](img/fd86a375816514636e24c0ceaf081637_175.png)

**参数说明：**
*   `block`: 当操作无法立即完成时（如队列已满时执行 `put`，或队列为空时执行 `get`），是否阻塞等待。默认为 `True`。
*   `timeout`: 设置阻塞等待的超时时间（秒）。如果超时后操作仍未完成，则会抛出异常（如 `queue.Full` 或 `queue.Empty`）。

![](img/fd86a375816514636e24c0ceaf081637_177.png)

**注意**：标准库 `queue.Queue` 是为线程间通信设计的，不能用于多进程。多进程必须使用 `multiprocessing.Queue`。

![](img/fd86a375816514636e24c0ceaf081637_179.png)

![](img/fd86a375816514636e24c0ceaf081637_181.png)

## 总结

![](img/fd86a375816514636e24c0ceaf081637_183.png)

![](img/fd86a375816514636e24c0ceaf081637_185.png)

本节课我们一起学习了Python多进程编程的几个核心部分。

![](img/fd86a375816514636e24c0ceaf081637_187.png)

![](img/fd86a375816514636e24c0ceaf081637_189.png)

我们首先明确了**进程与线程在内存使用上的本质区别**，这是理解后续所有内容的基础。正因为进程内存独立，我们在使用**进程锁**时需要将锁对象作为参数传递给子进程函数。

![](img/fd86a375816514636e24c0ceaf081637_191.png)

![](img/fd86a375816514636e24c0ceaf081637_193.png)

![](img/fd86a375816514636e24c0ceaf081637_195.png)

接着，我们学习了如何使用**进程池**（`ProcessPoolExecutor`）来高效管理多个进程，并了解了其初始化参数的用途。最后，我们介绍了**进程间通信**的基本方法，重点讲解了如何使用**队列（`Queue`）** 在不同进程间安全地传递数据。

![](img/fd86a375816514636e24c0ceaf081637_197.png)

掌握这些知识，你就能在Python中有效地利用多进程来执行CPU密集型任务，并处理好进程间的同步与数据交换问题。