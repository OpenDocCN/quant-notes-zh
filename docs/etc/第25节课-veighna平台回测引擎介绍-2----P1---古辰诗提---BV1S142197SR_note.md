# 量化交易：第25课：VeighNa平台回测引擎详解(2)

![](img/4dce6d0f2623c48bc455eac6a5380aac_1.png)

![](img/4dce6d0f2623c48bc455eac6a5380aac_3.png)

在本节课中，我们将继续深入解析VeighNa平台的回测引擎。我们将重点学习如何计算回测统计指标、如何绘制图表，以及如何进行策略参数优化。通过理解这些核心模块，你将能够更灵活地使用和定制回测功能。

## 统计指标计算

![](img/4dce6d0f2623c48bc455eac6a5380aac_5.png)

![](img/4dce6d0f2623c48bc455eac6a5380aac_7.png)

![](img/4dce6d0f2623c48bc455eac6a5380aac_9.png)

![](img/4dce6d0f2623c48bc455eac6a5380aac_11.png)

上一节我们介绍了回测引擎的基本流程，本节我们来看看如何计算最终的统计指标。`calculate_result` 方法负责根据每日盈亏数据（`daily_df`）计算一系列统计指标。

![](img/4dce6d0f2623c48bc455eac6a5380aac_13.png)

以下是计算过程中的核心步骤与概念：

![](img/4dce6d0f2623c48bc455eac6a5380aac_15.png)

![](img/4dce6d0f2623c48bc455eac6a5380aac_17.png)

![](img/4dce6d0f2623c48bc455eac6a5380aac_19.png)

*   **累计盈亏计算**：`balance = self.daily_df["net_pnl"].cumsum() + self.capital`。这行代码计算了每日的累计盈亏加上初始本金，得到资金曲线。
*   **空值处理**：使用 `fillna(0)` 将数据中的空值（NaN）填充为0，确保后续计算不会出错。
*   **累计与整体求和**：
    *   `cumsum()` 是累计求和。例如序列 `[1, 2, 3, 4]` 的累计求和结果为 `[1, 3, 6, 10]`。
    *   `sum()` 是整体求和，对整列数据直接相加。
*   **平均值与标准差**：
    *   `mean()` 用于计算平均值，例如平均每日收益率。
    *   `std()` 用于计算标准差，衡量收益的波动性。
*   **极值处理**：使用 `np.inf`（正无穷大）和 `-np.inf`（负无穷大）来标识或替换计算中出现的极大、极小异常值。
*   **数据转换**：`pd.to_numeric` 可以将数据安全地转换为数值类型。

这些计算主要依赖于 `pandas` 的 `DataFrame`、`Series` 以及 `NumPy` 库。掌握这些工具是进行量化分析的基础。

## 图表绘制

![](img/4dce6d0f2623c48bc455eac6a5380aac_21.png)

![](img/4dce6d0f2623c48bc455eac6a5380aac_23.png)

计算完统计指标后，我们通常需要可视化结果。`show_chart` 方法负责在图形界面中绘制资金曲线、回撤等图表。

该方法的核心是使用 `matplotlib` 库创建包含多个子图的画布。与直接使用 `DataFrame` 的 `plot()` 方法不同，`show_chart` 需要精细控制每个子图的位置和内容，例如资金曲线图、回撤图、每日盈亏图等。

对于初学者，如果只需要绘制简单的图表，直接使用 `df.plot()` 更为便捷。但当需要复杂的多图布局时，则需要像 `show_chart` 方法中那样，先创建画布和子图区域，再分别进行绘制。

## 策略参数优化

策略性能常常依赖于参数设置。回测引擎提供了参数优化功能，主要包括穷举法（`run_bf`）和遗传算法（`run_ga`）。本节我们重点讲解更易理解的穷举法。

参数优化的本质是：遍历所有给定的参数组合，分别进行回测，然后根据选定的目标指标（如夏普比率、总收益率）进行排序，找出最优参数。

### 优化流程解析

在图形界面点击“参数优化”按钮后，会触发以下逻辑：

1.  **参数设置**：通过 `OptimizationSetting` 类来设置需要优化的参数、其取值范围、步长以及目标指标。
2.  **单次回测包装**：`evaluate` 函数封装了一次完整的回测流程：实例化引擎、设置参数、运行回测、计算指标，并返回参数组合、目标指标值和其他统计信息。
3.  **函数包装**：`wrap_evaluate` 函数利用 `functools.partial` 创建了一个偏函数。它将策略类、合约等固定参数预先填入，最终返回一个只接受“参数组合”这一个输入的函数，方便后续进行映射计算。
4.  **多进程并行优化**：`run_bf` 方法是优化的执行核心。
    *   它首先获取所有待测试的参数组合列表（`settings`）。
    *   然后使用 `multiprocessing.Pool` 创建进程池。`spawn` 启动方式适用于Windows系统。
    *   使用 `pool.imap` 方法，将包装好的 `evaluate` 函数映射到每一个参数组合上，并行执行回测。
    *   使用 `tqdm` 库显示优化进度条。
    *   所有回测完成后，根据 `get_target_value` 函数从结果中提取目标指标值，并以此进行排序（默认降序，即最优结果在前）。

### 关键代码逻辑

![](img/4dce6d0f2623c48bc455eac6a5380aac_25.png)

*   **进程数设置**：`max_workers = max_workers or (os.cpu_count() and 1)`。这行代码确保 `max_workers` 至少为1。其逻辑是：如果 `max_workers` 为 `None` 或 0，则 `os.cpu_count() and 1` 的结果是1。
*   **排序关键**：`results.sort(key=get_target_value, reverse=True)`。排序的依据是 `get_target_value` 函数，它从单次回测结果（一个三元组）中取出第二个元素，即目标指标值。

![](img/4dce6d0f2623c48bc455eac6a5380aac_27.png)

理解了这个流程，你就掌握了参数优化的核心。遗传算法（GA）涉及更复杂的进化算法，但其底层依然依赖于上述的单次回测评估机制。

## 引擎其他功能与界面集成

![](img/4dce6d0f2623c48bc455eac6a5380aac_29.png)

![](img/4dce6d0f2623c48bc455eac6a5380aac_31.png)

`BacktestingEngine` 类中的其他方法，如 `update_daily_close`（更新每日收盘价）、`new_bar`/`new_tick`（处理K线和Tick数据）、各类订单处理函数（`send_limit_order`, `cancel_order` 等），其逻辑与我们之前自己编写的回测框架大同小异，主要是为了与事件驱动引擎和图形界面集成。

![](img/4dce6d0f2623c48bc455eac6a5380aac_33.png)

![](img/4dce6d0f2623c48bc455eac6a5380aac_35.png)

![](img/4dce6d0f2623c48bc455eac6a5380aac_37.png)

![](img/4dce6d0f2623c48bc455eac6a5380aac_39.png)

`BtEngine` 类（通常指 `ui/backtester/engine.py` 中的引擎）是连接图形界面和核心回测引擎 `BacktestingEngine` 的桥梁。

![](img/4dce6d0f2623c48bc455eac6a5380aac_41.png)

![](img/4dce6d0f2623c48bc455eac6a5380aac_43.png)

*   **线程控制**：为了防止图形界面在执行耗时任务（如回测、优化、下载数据）时卡死，`BtEngine` 使用单独的线程来运行这些任务。例如，`start_backtesting` 方法会创建一个新线程来执行 `run_backtesting` 函数。
*   **功能封装**：`BtEngine` 提供了界面按钮所需的各种方法，如加载策略、初始化数据服务、运行回测、运行优化、下载数据等。这些方法内部再调用真正的 `BacktestingEngine` 实例来完成工作。
*   **日志与错误处理**：它还将回测过程中的日志和错误信息传递到图形界面进行显示。

## 总结

本节课我们一起深入学习了VeighNa回测引擎的后半部分。我们详细分析了统计指标的计算方法，了解了图表绘制的原理，并重点剖析了策略参数优化（特别是穷举法）的完整实现流程。我们还看到了引擎如何与图形界面协作，通过多线程避免界面卡顿。

![](img/4dce6d0f2623c48bc455eac6a5380aac_45.png)

通过对 `BacktestingEngine` 和上层 `BtEngine` 的解析，你已经掌握了该回测框架的核心机制。这为你日后定制个性化回测功能、理解VeighNa其他模块（如实盘交易引擎）打下了坚实的基础。