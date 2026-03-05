# 量化交易：第23课：多进程穷举回测的实现 🚀

在本节课中，我们将学习如何将之前构建的回测引擎与多进程技术结合，实现高效的参数穷举优化。我们将从定义任务、获取参数、使用偏函数简化调用，到解决多进程中的数据库连接问题，并最终展示完整的优化流程和结果。

![](img/a84f8e153140902baf44918dbb57bb75_1.png)

## 概述

![](img/a84f8e153140902baf44918dbb57bb75_3.png)

上一节我们介绍了多进程的基础知识。本节中，我们将看看如何将单次回测任务封装起来，并利用多进程并行执行大量参数组合的回测，以寻找最优策略参数。

## 定义回测任务

![](img/a84f8e153140902baf44918dbb57bb75_5.png)

![](img/a84f8e153140902baf44918dbb57bb75_7.png)

首先，我们需要明确在多进程中执行的核心任务是什么。这个任务就是执行一次完整的策略回测。

以下是核心的回测任务函数 `task` 的代码框架：

![](img/a84f8e153140902baf44918dbb57bb75_9.png)

![](img/a84f8e153140902baf44918dbb57bb75_11.png)

```python
def task(vt_symbol: str,
         start: datetime,
         end: datetime,
         size: float,
         pricetick: float,
         interval: Interval,
         setting: dict,
         target_name: str):
    """
    执行单次回测的任务函数。
    :param vt_symbol: 合约代码
    :param start: 开始时间
    :param end: 结束时间
    :param size: 合约乘数
    :param pricetick: 最小价格变动
    :param interval: K线周期
    :param setting: 策略参数字典
    :param target_name: 要优化的目标指标名称
    :return: 包含参数设置和优化结果的元组 (setting, target_value)
    """
    # 1. 实例化回测引擎
    engine = MyBacktestEngine()
    
    # 2. 设置回测参数
    engine.set_parameters(
        vt_symbol=vt_symbol,
        interval=interval,
        start=start,
        end=end,
        rate=0.3/10000,  # 手续费率示例
        slippage=0,       # 滑点
        size=size,
        pricetick=pricetick,
        capital=1_000_000, # 初始资金
    )
    
    # 3. 添加策略并传入参数
    engine.add_strategy(MyStrategy, setting)
    
    # 4. 运行回测
    engine.run_backtesting()
    
    # 5. 计算并返回目标指标
    engine.calculate_result()
    engine.calculate_statistics()
    
    # 使用getattr安全地获取目标指标值，如果不存在则返回0
    target_value = getattr(engine, target_name, 0)
    
    # 返回参数和结果
    return (setting, target_value)
```

这个函数封装了回测的核心步骤：初始化、设置、运行和结果提取。它接收所有必要的参数，并返回一个包含参数字典和优化目标值的元组。

## 生成参数组合

接下来，我们需要生成所有要测试的参数组合。这通常通过嵌套循环来完成。

以下是生成参数组合列表 `setting_list` 的代码：

```python
def get_settings():
    """
    生成策略参数组合列表。
    :return: 参数字典的列表
    """
    setting_list = []
    
    # 定义快线均线参数范围
    for fast_window in range(10, 41, 5):  # 10, 15, 20, ..., 40
        # 定义慢线均线参数范围
        for slow_window in range(50, 101, 10): # 50, 60, 70, ..., 100
            # 创建参数字典，键名需与策略类中定义的参数名一致
            setting = {
                ‘fast_window‘: fast_window,
                ‘slow_window‘: slow_window,
            }
            setting_list.append(setting)
    
    return setting_list

# 获取参数列表
settings = get_settings()
print(f“共生成 {len(settings)} 组参数组合。“)
```

## 使用偏函数固定参数

由于 `task` 函数参数较多，而多进程的 `map` 或 `submit` 方法每次调用时，大部分参数（如合约、时间、周期等）是固定的。我们可以使用 `functools.partial` 创建一个“偏函数”，预先固定这些参数。

以下是创建偏函数的代码：

```python
from functools import partial
from typing import Callable

def wrap_task(func: Callable,
              vt_symbol: str,
              start: datetime,
              end: datetime,
              size: float,
              pricetick: float,
              interval: Interval,
              target_name: str) -> Callable:
    """
    包装任务函数，固定除setting外的所有参数。
    :param func: 原始任务函数
    :param ...: 所有需要固定的参数
    :return: 一个新的函数，它只接受一个setting参数
    """
    # 使用partial固定除setting外的所有参数
    new_func = partial(func,
                       vt_symbol=vt_symbol,
                       start=start,
                       end=end,
                       size=size,
                       pricetick=pricetick,
                       interval=interval,
                       target_name=target_name)
    return new_func

# 创建偏函数
fixed_task = wrap_task(task,
                       vt_symbol=“rb8888.SHFE“,
                       start=datetime(2022, 1, 1),
                       end=datetime.now(),
                       size=10,
                       pricetick=1,
                       interval=Interval.MINUTE,
                       target_name=“total_return“)
# 现在 fixed_task 只需要一个参数：setting
# result = fixed_task({‘fast_window‘: 20, ‘slow_window‘: 60})
```

## 实现多进程优化

现在，我们可以使用进程池来并行执行所有参数组合的回测。

以下是使用 `concurrent.futures.ProcessPoolExecutor` 执行多进程优化的完整代码：

```python
import os
from concurrent.futures import ProcessPoolExecutor, as_completed
from tqdm import tqdm  # 用于显示进度条
import time

def run_multi_process_optimization():
    """
    主函数：执行多进程参数优化。
    """
    # 1. 获取所有参数组合
    settings = get_settings()
    
    # 2. 创建固定了大部分参数的偏函数
    fixed_task = wrap_task(task,
                           vt_symbol=“rb8888.SHFE“,
                           start=datetime(2022, 1, 1),
                           end=datetime.now(),
                           size=10,
                           pricetick=1,
                           interval=Interval.MINUTE,
                           target_name=“total_return“)
    
    # 3. 创建进程池，最大进程数等于CPU核心数
    max_workers = os.cpu_count()
    results_list = []
    
    print(f“开始多进程优化，共 {len(settings)} 个任务，使用 {max_workers} 个进程。“)
    start_time = time.perf_counter()
    
    # 4. 使用进程池的map方法批量提交任务
    # 使用tqdm包装可迭代对象以显示进度条
    with ProcessPoolExecutor(max_workers=max_workers) as executor:
        # executor.map会将settings中的每个元素作为参数传给fixed_task
        # 它返回一个迭代器，按任务完成顺序产出结果
        futures_iterator = executor.map(fixed_task, settings)
        
        # 使用tqdm显示进度，total参数设置总任务数
        for future in tqdm(futures_iterator, total=len(settings), desc=“优化进度“):
            results_list.append(future)
    
    end_time = time.perf_counter()
    print(f“所有任务完成，总耗时：{end_time - start_time:.2f} 秒。“)
    
    # 5. 对结果进行排序（例如按总收益率降序排列）
    # 每个结果是 (setting, target_value) 的元组，我们按target_value（索引1）排序
    sorted_results = sorted(results_list, key=lambda x: x[1], reverse=True)
    
    # 6. 输出前10个最优结果
    print(“\n优化结果排名（前10）：“)
    for i, (setting, value) in enumerate(sorted_results[:10]):
        print(f“排名 {i+1}: 参数 {setting}, 目标值：{value:.2f}“)
    
    return sorted_results

![](img/a84f8e153140902baf44918dbb57bb75_13.png)

![](img/a84f8e153140902baf44918dbb57bb75_15.png)

if __name__ == ‘__main__‘:
    # 确保在Windows系统下使用多进程时，主模块被正确保护
    final_results = run_multi_process_optimization()
```

## 关键问题与解决方案

在实现过程中，我们遇到了两个关键问题并提供了解决方案。

### 1. 数据库连接冲突

**问题**：多个进程同时初始化回测引擎，会尝试创建多个数据库连接，导致 `“connection already opened“` 错误。
**解决方案**：将数据库类改为单例模式，确保在整个程序中只存在一个数据库连接实例。

以下是单例模式的数据库类示例代码：

```python
class SingletonDatabase:
    _instance = None
    
    def __new__(cls, *args, **kwargs):
        if not cls._instance:
            # 如果实例不存在，则创建一个
            cls._instance = super().__new__(cls)
            # 可以在这里初始化数据库连接
            # cls._instance.connection = create_connection()
        return cls._instance
    
    # ... 其他数据库操作方法 ...
```

### 2. 控制日志输出

**问题**：多进程同时打印日志会导致控制台输出混乱，难以阅读。
**解决方案**：重写回测引擎中的日志记录方法，在优化期间暂时禁止输出。

以下是重写日志方法的示例代码：

![](img/a84f8e153140902baf44918dbb57bb75_17.png)

![](img/a84f8e153140902baf44918dbb57bb75_19.png)

```python
def mute_log(*args, **kwargs):
    """一个什么都不做的日志函数，用于静音输出。"""
    pass

# 在任务函数中，执行回测前替换日志方法
engine.write_log = mute_log
```

## 总结

![](img/a84f8e153140902baf44918dbb57bb75_21.png)

本节课中我们一起学习了如何构建一个完整的**多进程穷举回测系统**。我们从定义清晰的任务函数开始，学习了如何生成参数网格、使用偏函数来简化函数调用，并利用 `ProcessPoolExecutor` 实现并行计算。我们还解决了多进程环境下的数据库连接冲突和日志输出混乱等实际问题。最后，通过排序和展示结果，我们能够高效地找到策略的最优参数组合。这套框架是进行量化策略研究和优化的强大基础，你可以在此基础上扩展为多策略、多品种的复杂优化系统。