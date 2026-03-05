# Python基础课：第40课：线程和进程2 🧵⚙️

![](img/ea0382675c3de1b6f64a2f819236519a_1.png)

![](img/ea0382675c3de1b6f64a2f819236519a_3.png)

在本节课中，我们将继续深入学习Python中的线程与进程。我们将探讨如何批量创建线程、理解线程池的概念与用法，并初步了解进程与线程的核心区别。课程内容将注重实践，帮助你掌握多任务编程的关键技巧。

![](img/ea0382675c3de1b6f64a2f819236519a_5.png)

![](img/ea0382675c3de1b6f64a2f819236519a_7.png)

![](img/ea0382675c3de1b6f64a2f819236519a_8.png)

![](img/ea0382675c3de1b6f64a2f819236519a_10.png)

---

![](img/ea0382675c3de1b6f64a2f819236519a_12.png)

![](img/ea0382675c3de1b6f64a2f819236519a_13.png)

![](img/ea0382675c3de1b6f64a2f819236519a_15.png)

## 批量创建线程 🔄

![](img/ea0382675c3de1b6f64a2f819236519a_17.png)

![](img/ea0382675c3de1b6f64a2f819236519a_19.png)

![](img/ea0382675c3de1b6f64a2f819236519a_21.png)

上一节我们介绍了如何创建单个线程。本节中我们来看看如何批量创建多个线程。

![](img/ea0382675c3de1b6f64a2f819236519a_23.png)

![](img/ea0382675c3de1b6f64a2f819236519a_25.png)

![](img/ea0382675c3de1b6f64a2f819236519a_27.png)

创建多个线程时，可以使用循环结构。但需要注意，`join()`方法的位置至关重要，如果放置不当，会导致线程串行执行，失去多线程的意义。

![](img/ea0382675c3de1b6f64a2f819236519a_29.png)

![](img/ea0382675c3de1b6f64a2f819236519a_31.png)

以下是批量创建线程的步骤：

1.  初始化一个空列表用于存放线程对象。
2.  在循环中创建线程对象，并将其添加到列表中。
3.  启动所有线程。
4.  在循环结束后，再遍历列表，对每个线程调用`join()`方法，等待所有子线程执行完毕。

![](img/ea0382675c3de1b6f64a2f819236519a_33.png)

```python
import threading
import time

![](img/ea0382675c3de1b6f64a2f819236519a_35.png)

![](img/ea0382675c3de1b6f64a2f819236519a_37.png)

![](img/ea0382675c3de1b6f64a2f819236519a_39.png)

![](img/ea0382675c3de1b6f64a2f819236519a_41.png)

![](img/ea0382675c3de1b6f64a2f819236519a_43.png)

def task(name, sleep_time):
    print(f"{name}任务开始执行")
    time.sleep(sleep_time)
    print(f"{name}任务执行结束")

![](img/ea0382675c3de1b6f64a2f819236519a_45.png)

![](img/ea0382675c3de1b6f64a2f819236519a_47.png)

![](img/ea0382675c3de1b6f64a2f819236519a_49.png)

if __name__ == '__main__':
    start_time = time.time()
    thread_list = []
    
    # 创建并启动5个线程
    for i in range(5):
        t = threading.Thread(target=task, args=(f"睡觉{i}", 2))
        thread_list.append(t)
        t.start()
    
    # 等待所有线程执行完毕
    for t in thread_list:
        t.join()
    
    end_time = time.time()
    print(f"总用时：{end_time - start_time:.2f}秒")
```

![](img/ea0382675c3de1b6f64a2f819236519a_51.png)

![](img/ea0382675c3de1b6f64a2f819236519a_53.png)

![](img/ea0382675c3de1b6f64a2f819236519a_55.png)

![](img/ea0382675c3de1b6f64a2f819236519a_57.png)

**核心概念**：`join()`方法用于阻塞主线程，直到调用它的线程执行完毕。正确的使用方式是先启动所有线程，再统一等待。

![](img/ea0382675c3de1b6f64a2f819236519a_59.png)

![](img/ea0382675c3de1b6f64a2f819236519a_61.png)

![](img/ea0382675c3de1b6f64a2f819236519a_63.png)

---

![](img/ea0382675c3de1b6f64a2f819236519a_65.png)

![](img/ea0382675c3de1b6f64a2f819236519a_67.png)

![](img/ea0382675c3de1b6f64a2f819236519a_69.png)

## 线程池 🏊‍♂️

![](img/ea0382675c3de1b6f64a2f819236519a_71.png)

![](img/ea0382675c3de1b6f64a2f819236519a_73.png)

![](img/ea0382675c3de1b6f64a2f819236519a_75.png)

![](img/ea0382675c3de1b6f64a2f819236519a_77.png)

![](img/ea0382675c3de1b6f64a2f819236519a_79.png)

创建线程本身消耗资源，并非线程越多越好。对于大量任务，更高效的方式是使用线程池。线程池维护固定数量的线程，复用它们来执行任务，避免了频繁创建和销毁线程的开销。

![](img/ea0382675c3de1b6f64a2f819236519a_81.png)

线程池的核心思想是：预先创建一定数量的线程放入“池”中。当有任务提交时，分配一个空闲线程执行。任务完成后，线程返回池中等待下一个任务。

![](img/ea0382675c3de1b6f64a2f819236519a_83.png)

![](img/ea0382675c3de1b6f64a2f819236519a_85.png)

![](img/ea0382675c3de1b6f64a2f819236519a_86.png)

![](img/ea0382675c3de1b6f64a2f819236519a_88.png)

### 使用线程池

![](img/ea0382675c3de1b6f64a2f819236519a_90.png)

![](img/ea0382675c3de1b6f64a2f819236519a_92.png)

![](img/ea0382675c3de1b6f64a2f819236519a_94.png)

Python通过`concurrent.futures`模块提供线程池功能。

![](img/ea0382675c3de1b6f64a2f819236519a_96.png)

![](img/ea0382675c3de1b6f64a2f819236519a_98.png)

![](img/ea0382675c3de1b6f64a2f819236519a_100.png)

以下是使用线程池的基本步骤：

![](img/ea0382675c3de1b6f64a2f819236519a_102.png)

![](img/ea0382675c3de1b6f64a2f819236519a_104.png)

![](img/ea0382675c3de1b6f64a2f819236519a_106.png)

1.  导入`ThreadPoolExecutor`。
2.  实例化线程池，指定最大线程数（`max_workers`）。
3.  使用`submit()`方法提交任务。**注意**：传参方式是顺序传递，而非元组。
4.  可以使用`shutdown(wait=True)`来等待所有任务完成，其作用类似于`join()`。

![](img/ea0382675c3de1b6f64a2f819236519a_108.png)

![](img/ea0382675c3de1b6f64a2f819236519a_110.png)

![](img/ea0382675c3de1b6f64a2f819236519a_112.png)

![](img/ea0382675c3de1b6f64a2f819236519a_114.png)

```python
from concurrent.futures import ThreadPoolExecutor
import time

![](img/ea0382675c3de1b6f64a2f819236519a_116.png)

![](img/ea0382675c3de1b6f64a2f819236519a_118.png)

![](img/ea0382675c3de1b6f64a2f819236519a_120.png)

![](img/ea0382675c3de1b6f64a2f819236519a_122.png)

def task(name, sleep_time):
    print(f"{name}任务开始执行")
    time.sleep(sleep_time)
    print(f"{name}任务执行结束")
    return f"{name}执行完毕"  # 任务可以返回值

![](img/ea0382675c3de1b6f64a2f819236519a_124.png)

![](img/ea0382675c3de1b6f64a2f819236519a_126.png)

![](img/ea0382675c3de1b6f64a2f819236519a_128.png)

![](img/ea0382675c3de1b6f64a2f819236519a_130.png)

if __name__ == '__main__':
    # 创建最多包含3个线程的线程池
    with ThreadPoolExecutor(max_workers=3, thread_name_prefix='TaskPool') as pool:
        future_list = []
        
        # 提交10个任务
        for i in range(10):
            # submit传参：函数名， 参数1， 参数2...
            future = pool.submit(task, f"睡觉{i}", 2)
            future_list.append(future)
        
        # shutdown(wait=True) 会等待池中所有任务完成
        # 在使用 `with` 语句时，退出时会自动调用，无需手动写
    
    # 获取所有任务的结果
    for future in future_list:
        print(future.result())
```

![](img/ea0382675c3de1b6f64a2f819236519a_132.png)

![](img/ea0382675c3de1b6f64a2f819236519a_134.png)

![](img/ea0382675c3de1b6f64a2f819236519a_136.png)

![](img/ea0382675c3de1b6f64a2f819236519a_138.png)

**核心概念**：`submit()`方法返回一个`Future`对象，它封装了异步执行的操作。通过`future.result()`可以获取任务的返回值（会阻塞直到任务完成）。

![](img/ea0382675c3de1b6f64a2f819236519a_140.png)

![](img/ea0382675c3de1b6f64a2f819236519a_142.png)

### 回调函数

![](img/ea0382675c3de1b6f64a2f819236519a_144.png)

![](img/ea0382675c3de1b6f64a2f819236519a_146.png)

![](img/ea0382675c3de1b6f64a2f819236519a_148.png)

![](img/ea0382675c3de1b6f64a2f819236519a_150.png)

你可以在任务完成后自动执行一个回调函数来处理结果。

![](img/ea0382675c3de1b6f64a2f819236519a_152.png)

![](img/ea0382675c3de1b6f64a2f819236519a_154.png)

![](img/ea0382675c3de1b6f64a2f819236519a_156.png)

```python
def callback_func(future):
    """任务结束后的回调函数"""
    print(f"回调收到结果：{future.result()}")

![](img/ea0382675c3de1b6f64a2f819236519a_158.png)

![](img/ea0382675c3de1b6f64a2f819236519a_160.png)

![](img/ea0382675c3de1b6f64a2f819236519a_162.png)

with ThreadPoolExecutor(max_workers=3) as pool:
    for i in range(5):
        future = pool.submit(task, f"工作{i}", 1)
        future.add_done_callback(callback_func)  # 添加回调
```

![](img/ea0382675c3de1b6f64a2f819236519a_164.png)

![](img/ea0382675c3de1b6f64a2f819236519a_166.png)

![](img/ea0382675c3de1b6f64a2f819236519a_168.png)

![](img/ea0382675c3de1b6f64a2f819236519a_170.png)

![](img/ea0382675c3de1b6f64a2f819236519a_172.png)

---

![](img/ea0382675c3de1b6f64a2f819236519a_174.png)

![](img/ea0382675c3de1b6f64a2f819236519a_176.png)

## 进程初探 🖥️

![](img/ea0382675c3de1b6f64a2f819236519a_178.png)

进程是操作系统进行资源分配和调度的基本单位。与线程最大的区别在于：**线程共享同一进程的内存空间，而每个进程拥有独立的内存空间**。

![](img/ea0382675c3de1b6f64a2f819236519a_180.png)

![](img/ea0382675c3de1b6f64a2f819236519a_182.png)

这意味着多进程之间数据不直接共享，通信需要通过特殊机制（如队列、管道）。在Windows系统中，创建子进程时，会通过序列化（pickle）的方式将必要的代码和数据传递过去。

![](img/ea0382675c3de1b6f64a2f819236519a_184.png)

![](img/ea0382675c3de1b6f64a2f819236519a_186.png)

![](img/ea0382675c3de1b6f64a2f819236519a_188.png)

![](img/ea0382675c3de1b6f64a2f819236519a_190.png)

### 创建进程

![](img/ea0382675c3de1b6f64a2f819236519a_192.png)

![](img/ea0382675c3de1b6f64a2f819236519a_194.png)

![](img/ea0382675c3de1b6f64a2f819236519a_196.png)

![](img/ea0382675c3de1b6f64a2f819236519a_198.png)

Python使用`multiprocessing`模块创建进程，其接口与`threading`模块非常相似。

![](img/ea0382675c3de1b6f64a2f819236519a_200.png)

![](img/ea0382675c3de1b6f64a2f819236519a_202.png)

![](img/ea0382675c3de1b6f64a2f819236519a_204.png)

```python
from multiprocessing import Process
import os
import time

![](img/ea0382675c3de1b6f64a2f819236519a_206.png)

![](img/ea0382675c3de1b6f64a2f819236519a_208.png)

![](img/ea0382675c3de1b6f64a2f819236519a_209.png)

def task(name, sleep_time):
    print(f"子进程开始执行任务：{name}, PID: {os.getpid()}, 父进程PID: {os.getppid()}")
    time.sleep(sleep_time)
    print(f"子进程任务结束：{name}")

![](img/ea0382675c3de1b6f64a2f819236519a_211.png)

![](img/ea0382675c3de1b6f64a2f819236519a_213.png)

![](img/ea0382675c3de1b6f64a2f819236519a_215.png)

if __name__ == '__main__':  # 在Windows下使用多进程必须加这行
    print(f"主进程PID: {os.getpid()}")
    start_time = time.time()
    
    # 创建进程对象
    p = Process(target=task, args=('睡觉', 2), name='子进程1')
    # 启动进程
    p.start()
    
    # 等待子进程结束
    p.join()
    
    end_time = time.time()
    print(f"总用时：{end_time - start_time:.2f}秒")
```

![](img/ea0382675c3de1b6f64a2f819236519a_217.png)

![](img/ea0382675c3de1b6f64a2f819236519a_219.png)

![](img/ea0382675c3de1b6f64a2f819236519a_221.png)

**核心概念**：
*   `os.getpid()`：获取当前进程的ID。
*   `os.getppid()`：获取父进程的ID。
*   进程间内存隔离，默认不共享数据。
*   在Windows系统下，多进程代码必须放在`if __name__ == '__main__':`保护块中执行。

![](img/ea0382675c3de1b6f64a2f819236519a_223.png)

![](img/ea0382675c3de1b6f64a2f819236519a_225.png)

![](img/ea0382675c3de1b6f64a2f819236519a_227.png)

![](img/ea0382675c3de1b6f64a2f819236519a_228.png)

![](img/ea0382675c3de1b6f64a2f819236519a_229.png)

---

![](img/ea0382675c3de1b6f64a2f819236519a_231.png)

![](img/ea0382675c3de1b6f64a2f819236519a_233.png)

![](img/ea0382675c3de1b6f64a2f819236519a_235.png)

![](img/ea0382675c3de1b6f64a2f819236519a_236.png)

![](img/ea0382675c3de1b6f64a2f819236519a_238.png)

![](img/ea0382675c3de1b6f64a2f819236519a_240.png)

## 总结 📚

![](img/ea0382675c3de1b6f64a2f819236519a_241.png)

![](img/ea0382675c3de1b6f64a2f819236519a_243.png)

![](img/ea0382675c3de1b6f64a2f819236519a_245.png)

![](img/ea0382675c3de1b6f64a2f819236519a_247.png)

本节课中我们一起学习了：
1.  **批量创建线程**：通过循环和列表管理多个线程，并正确使用`join()`方法实现并发。
2.  **线程池**：使用`ThreadPoolExecutor`高效管理线程，学习了提交任务、获取结果、设置回调函数等操作。
3.  **进程基础**：了解了进程与线程的核心区别（内存隔离），并掌握了使用`multiprocessing.Process`创建和管理进程的基本方法。

![](img/ea0382675c3de1b6f64a2f819236519a_249.png)

![](img/ea0382675c3de1b6f64a2f819236519a_251.png)

![](img/ea0382675c3de1b6f64a2f819236519a_253.png)

![](img/ea0382675c3de1b6f64a2f819236519a_255.png)

![](img/ea0382675c3de1b6f64a2f819236519a_256.png)

线程适用于I/O密集型任务（如网络请求、文件读写），而进程适用于CPU密集型任务（如大量计算）。理解它们的特性和适用场景，是进行高效多任务编程的关键。在接下来的课程中，我们将深入探讨进程间的通信与数据共享。