# Python基础课：39：线程和进程 - P1 🧵⚙️

在本节课中，我们将要学习Python中线程和进程的基本概念。我们将了解什么是线程和进程，如何在Python中创建和使用线程，以及如何通过线程锁来管理共享资源，避免数据冲突。这些知识是理解并发编程的基础。

## 概述：什么是线程和进程？

![](img/f5e5e65504e6969ffd6098e4e52df5b8_1.png)

当执行一段代码时，例如打印“你好”，操作系统会在计算机内存中创建一个**进程**。进程创建后，会在其内部创建一个**线程**，由这个线程来执行具体的指令。线程是操作系统能够调度的最小执行单元。

![](img/f5e5e65504e6969ffd6098e4e52df5b8_3.png)

在Python中，还有一个概念叫**协程**，它通过我们之前学过的生成器和 `yield` 关键字实现。但协程在物理层面并不真正存在，而线程和进程是真实存在的系统资源。

![](img/f5e5e65504e6969ffd6098e4e52df5b8_5.png)

例如，打开任务管理器，可以看到系统中正在运行的进程和线程数量。

![](img/f5e5e65504e6969ffd6098e4e52df5b8_1.png)

一个进程内部通常会包含多个线程。线程数量并非越多越好，需要根据实际需求分配。

协程通过 `yield` 机制，可以让函数在执行到一半时暂停，并返回值，然后去执行其他任务，之后再回来继续执行。这实现了**并发**，即多个任务交替执行，但由于计算机执行速度极快，看起来像是同时进行。这与**并行**不同，并行是真正意义上的多个任务在同一时刻同时执行。

在量化编程中，协程使用较少，因此本课程主要讲解线程。

## 创建与使用线程 🛠️

上一节我们介绍了线程的基本概念，本节中我们来看看如何在Python中创建和使用线程。

线程的英文是 `thread`。程序运行时，执行代码的主流程称为**主线程**。如果需要同时执行多个任务，就需要创建**子线程**来实现任务的并发。

要创建子线程，需要导入 `threading` 模块。

```python
import threading
from time import sleep
import time
```

以下是创建和启动线程的基本步骤：

1.  **定义任务函数**：首先，定义需要并发执行的任务。
2.  **创建线程对象**：使用 `threading.Thread` 类创建线程对象，并指定要执行的任务函数。
3.  **启动线程**：调用线程对象的 `start()` 方法来启动线程。
4.  **等待线程完成**：如果需要主线程等待子线程执行完毕，可以调用线程对象的 `join()` 方法。

让我们通过一个例子来具体说明。

### 示例：顺序执行与并发执行

首先，我们看看两个任务顺序执行需要多长时间。

```python
def task1():
    print(‘任务一开始执行‘)
    sleep(1)
    print(‘任务一执行完成‘)

def task2():
    print(‘任务二开始执行‘)
    sleep(2)
    print(‘任务二执行完成‘)

start = time.time()
task1()
task2()
end = time.time()
print(‘执行了{:.2f}秒‘.format(end - start))
```

运行上述代码，总耗时约为3秒，因为 `task1` 和 `task2` 是顺序执行的。

现在，我们使用线程让它们并发执行。

```python
start = time.time()
# 创建并启动执行task1的子线程
t1 = threading.Thread(target=task1)
t1.start()

# 主线程继续执行task2
task2()

# 等待子线程t1执行完毕
t1.join()
end = time.time()
print(‘执行了{:.2f}秒‘.format(end - start))
```

这次，总耗时约为2秒。因为主线程启动 `task1` 的子线程后，没有等待它完成，而是立即开始执行 `task2`。`task2` 耗时2秒，`task1` 耗时1秒，所以总时间取决于最长的任务（2秒）。`t1.join()` 确保了主线程会等待 `task1` 完成后再结束计时。

### 向线程传递参数

如果需要向任务函数传递参数，可以在创建线程时使用 `args` 参数。

```python
def task(name):
    print(f‘任务{name}开始执行‘)
    sleep(1)
    print(f‘任务{name}执行完成‘)

# 向线程传递参数，args必须是一个元组
t = threading.Thread(target=task, args=(‘吃饭‘,))
t.start()
t.join()
```

### 守护线程

守护线程（Daemon Thread）是一种特殊的线程。当主线程结束时，无论守护线程是否执行完毕，都会随之结束。

```python
def long_task():
    print(‘守护线程开始执行‘)
    sleep(5)
    print(‘守护线程执行完成‘) # 如果主线程先结束，这行可能不会打印

t = threading.Thread(target=long_task, daemon=True) # 设置为守护线程
t.start()
sleep(1) # 主线程等待1秒
print(‘主线程结束‘)
```

运行上述代码，主线程在1秒后结束，此时守护线程 `long_task` 才执行了1秒（共需5秒），但它会立即被终止，因此“守护线程执行完成”这行可能不会打印。

### 查看当前线程信息

可以使用 `threading.enumerate()` 查看当前进程中的所有线程。

```python
def my_task():
    print(‘子线程运行中‘)
    sleep(1)

t = threading.Thread(target=my_task, name=‘MyThread‘)
t.start()

# 打印所有线程信息
for thread in threading.enumerate():
    print(f‘线程名：{thread.name}, 是否存活：{thread.is_alive()}‘)

t.join()
```

## 线程的返回值与类继承写法 📦

上一节我们介绍了线程的基本操作，本节中我们来看看如何处理线程的返回值，以及如何通过继承 `Thread` 类来创建线程。

默认情况下，通过 `target` 指定的函数返回值无法直接获取。有几种方法可以处理：

1.  **使用共享变量（不推荐）**：将结果存入一个列表或字典等共享数据结构。但多线程同时读写共享变量可能导致数据污染。
2.  **继承 `Thread` 类（推荐）**：通过重写 `run` 和 `join` 方法来获取返回值。

以下是使用类继承的方式：

```python
class MyThread(threading.Thread):
    def __init__(self, name):
        super().__init__() # 调用父类初始化方法
        self.name = name
        self._result = None # 用于存储结果的私有变量

    def run(self):
        # 此方法在线程启动时自动调用
        print(f‘任务{self.name}开始执行‘)
        sleep(1)
        self._result = self.name # 模拟任务结果
        print(f‘任务{self.name}执行完成‘)

    def join(self):
        # 重写join方法，在等待线程结束后返回结果
        super().join() # 调用父类的join等待线程结束
        return self._result

# 使用自定义线程类
mt = MyThread(‘睡觉‘)
mt.start()
result = mt.join() # 这里会阻塞，直到线程执行完毕并返回结果
print(f‘线程返回的结果是：{result}‘)
```

这种方式结构清晰，且能安全地获取返回值。在后续课程中，我们还会介绍更便捷的**线程池**，它也能方便地处理返回值。

## 线程锁与数据安全 🔒

当多个线程需要访问和修改同一个共享变量（如全局变量）时，可能会发生**数据竞争**，导致结果出错。本节我们将学习如何使用**线程锁**来确保数据安全。

### 为什么需要线程锁？

考虑一个场景：一个全局变量 `number` 初始为0，我们创建两个线程，一个线程对其加100万次1，另一个线程对其减100万次1。理论上，最终结果应该还是0。

```python
import threading

number = 0

def add_ex():
    global number
    for _ in range(1000000):
        number += 1

def sub_ex():
    global number
    for _ in range(1000000):
        number -= 1

t1 = threading.Thread(target=add_ex)
t2 = threading.Thread(target=sub_ex)

t1.start()
t2.start()

t1.join()
t2.join()

print(f‘最终结果：{number}‘)
```

由于Python解释器的优化（全局解释器锁GIL，下文会讲），这个简单例子可能不会出错。但从原理上讲，`number += 1` 这个操作并非原子操作（它包含读取、计算、写入三步），在多线程环境下，两个线程的步骤可能交织在一起，导致最终结果不可预测。

### 使用线程锁

为了确保对共享资源的操作是串行的（即一个线程操作完，另一个才能操作），我们需要使用**线程锁**。

```python
import threading

number = 0
lock = threading.Lock() # 创建一个锁对象

def add_ex():
    global number
    for _ in range(1000000):
        lock.acquire() # 申请锁
        number += 1
        lock.release() # 释放锁

def sub_ex():
    global number
    for _ in range(1000000):
        lock.acquire() # 申请锁
        number -= 1
        lock.release() # 释放锁

t1 = threading.Thread(target=add_ex)
t2 = threading.Thread(target=sub_ex)

t1.start()
t2.start()

t1.join()
t2.join()

print(f‘加锁后最终结果：{number}‘) # 结果一定是0
```

使用 `with` 语句可以更优雅地管理锁，它能确保锁在代码块执行后自动释放。

```python
def add_ex():
    global number
    for _ in range(1000000):
        with lock: # 进入with块时自动加锁，离开时自动释放
            number += 1
```

### 全局解释器锁（GIL）

**全局解释器锁（Global Interpreter Lock, GIL）** 是Python（特指CPython解释器）中的一个机制，它规定任何时候都只有一个线程可以执行Python字节码。这意味着即使在多核CPU上，Python的多线程也无法实现真正的并行计算，只能实现并发。

GIL的设计主要是为了简化CPython解释器的实现，并保证内存管理（如垃圾回收）的线程安全。它是Python多线程性能的一个主要限制，也是Python在某些计算密集型任务上慢于C++、Java等语言的原因之一。

对于I/O密集型任务（如网络请求、文件读写），线程在等待I/O时会让出GIL，因此多线程仍然可以显著提升效率。对于CPU密集型任务，要充分利用多核优势，通常需要使用**多进程**（每个进程有独立的Python解释器和内存空间，因此也有独立的GIL）。

## 总结 📝

本节课中我们一起学习了Python中线程和进程的基础知识（第一部分）。

*   我们了解了**进程**是资源分配的单位，**线程**是CPU调度的最小单位。
*   我们学会了如何使用 `threading` 模块**创建和启动线程**，如何传递参数，以及什么是**守护线程**。
*   我们探讨了获取线程返回值的**类继承方法**。
*   我们认识到多线程操作共享资源可能导致**数据竞争**，并学会了使用**线程锁（Lock）** 来保证数据安全。
*   最后，我们了解了**全局解释器锁（GIL）** 的存在及其对Python多线程并行能力的限制，这引出了对于CPU密集型任务需要使用多进程的解决方案（将在后续课程中介绍）。

![](img/f5e5e65504e6969ffd6098e4e52df5b8_7.png)

掌握这些概念是进行高效并发编程的第一步。在下一节课中，我们将继续深入探讨进程的相关知识。