# 量化交易教程：P23：Ricequant交易平台简介 🚀

![](img/eed3496d877de4bff775877b2fd88a1a_1.png)

![](img/eed3496d877de4bff775877b2fd88a1a_3.png)

![](img/eed3496d877de4bff775877b2fd88a1a_5.png)

![](img/eed3496d877de4bff775877b2fd88a1a_7.png)

在本节课中，我们将学习如何使用Ricequant量化交易平台。我们将了解平台的基本界面、核心的编程框架以及如何配置策略回测参数。通过本节的学习，你将能够在该平台上创建并运行一个基础的量化策略。

![](img/eed3496d877de4bff775877b2fd88a1a_9.png)

![](img/eed3496d877de4bff775877b2fd88a1a_11.png)

![](img/eed3496d877de4bff775877b2fd88a1a_13.png)

## 平台界面与策略模板

![](img/eed3496d877de4bff775877b2fd88a1a_15.png)

![](img/eed3496d877de4bff775877b2fd88a1a_17.png)

![](img/eed3496d877de4bff775877b2fd88a1a_19.png)

首先，我们演示量化平台的使用方法。点击“量化平台”，然后选择“免费使用”。

![](img/eed3496d877de4bff775877b2fd88a1a_21.png)

![](img/eed3496d877de4bff775877b2fd88a1a_23.png)

![](img/eed3496d877de4bff775877b2fd88a1a_25.png)

进入平台后，会看到一些预设的策略。这些是平台提供的简单示例，方便用户熟悉策略的编写方式。

![](img/eed3496d877de4bff775877b2fd88a1a_27.png)

![](img/eed3496d877de4bff775877b2fd88a1a_29.png)

![](img/eed3496d877de4bff775877b2fd88a1a_31.png)

![](img/eed3496d877de4bff775877b2fd88a1a_33.png)

以下是查看入门策略的步骤：

![](img/eed3496d877de4bff775877b2fd88a1a_35.png)

![](img/eed3496d877de4bff775877b2fd88a1a_37.png)

1.  找到名为“第一个入门策略”的示例（此为默认名称）。
2.  点击该策略进入编辑界面。

![](img/eed3496d877de4bff775877b2fd88a1a_39.png)

![](img/eed3496d877de4bff775877b2fd88a1a_41.png)

![](img/eed3496d877de4bff775877b2fd88a1a_43.png)

![](img/eed3496d877de4bff775877b2fd88a1a_45.png)

编辑界面类似于Jupyter Notebook，可以在此编写Python代码，但代码结构需遵循平台要求。

![](img/eed3496d877de4bff775877b2fd88a1a_47.png)

![](img/eed3496d877de4bff775877b2fd88a1a_49.png)

![](img/eed3496d877de4bff775877b2fd88a1a_51.png)

![](img/eed3496d877de4bff775877b2fd88a1a_53.png)

## 核心策略函数框架

![](img/eed3496d877de4bff775877b2fd88a1a_55.png)

![](img/eed3496d877de4bff775877b2fd88a1a_57.png)

![](img/eed3496d877de4bff775877b2fd88a1a_59.png)

在策略编辑区，我们可以看到三个默认的函数。所有操作都需要围绕这三个函数来实现。

![](img/eed3496d877de4bff775877b2fd88a1a_61.png)

![](img/eed3496d877de4bff775877b2fd88a1a_63.png)

![](img/eed3496d877de4bff775877b2fd88a1a_65.png)

以下是三个核心函数及其作用：

![](img/eed3496d877de4bff775877b2fd88a1a_67.png)

![](img/eed3496d877de4bff775877b2fd88a1a_69.png)

![](img/eed3496d877de4bff775877b2fd88a1a_71.png)

![](img/eed3496d877de4bff775877b2fd88a1a_73.png)

*   **`init` 函数**：此函数用于初始化策略。它类似于Python类中的构造函数，在策略开始执行时**最先被调用一次**。通常在此函数中设置股票池、初始化全局变量等。
*   **`before_trading` 函数**：此函数在每个交易日开始前（即开盘前）被调用。可以在此进行数据预处理工作。
*   **`handle_bar` 函数**：此函数是策略逻辑的核心。根据你选择的频率（每日或每分钟），它会在每个**交易日**或每个**交易分钟**被调用。实际的买卖判断和下单操作都在此函数中实现。

![](img/eed3496d877de4bff775877b2fd88a1a_75.png)

![](img/eed3496d877de4bff775877b2fd88a1a_77.png)

![](img/eed3496d877de4bff775877b2fd88a1a_79.png)

## 上下文对象 `context` 的作用

![](img/eed3496d877de4bff775877b2fd88a1a_81.png)

![](img/eed3496d877de4bff775877b2fd88a1a_83.png)

![](img/eed3496d877de4bff775877b2fd88a1a_85.png)

观察上述三个函数，会发现它们都有一个共同的参数 `context`。

![](img/eed3496d877de4bff775877b2fd88a1a_87.png)

![](img/eed3496d877de4bff775877b2fd88a1a_89.png)

![](img/eed3496d877de4bff775877b2fd88a1a_91.png)

![](img/eed3496d877de4bff775877b2fd88a1a_93.png)

`context` 是一个上下文对象，用于在不同函数间传递数据和状态。例如，在 `init` 函数中初始化的股票代码或数据，可以存入 `context`。随后，在 `before_trading` 和 `handle_bar` 函数中，可以通过传入的 `context` 参数来访问这些数据。

![](img/eed3496d877de4bff775877b2fd88a1a_95.png)

![](img/eed3496d877de4bff775877b2fd88a1a_97.png)

![](img/eed3496d877de4bff775877b2fd88a1a_99.png)

其使用方式如下：在 `init` 函数中为 `context` 添加属性（如 `context.s1 = ‘000001.XSHE’`），在其他函数中即可通过 `context.s1` 来获取该值。

![](img/eed3496d877de4bff775877b2fd88a1a_101.png)

![](img/eed3496d877de4bff775877b2fd88a1a_103.png)

## 回测参数配置

![](img/eed3496d877de4bff775877b2fd88a1a_105.png)

![](img/eed3496d877de4bff775877b2fd88a1a_107.png)

上一节我们介绍了策略的核心函数，本节我们来看看如何配置策略的运行环境。策略的执行频率和初始条件需要在右侧参数区进行设置。

![](img/eed3496d877de4bff775877b2fd88a1a_109.png)

![](img/eed3496d877de4bff775877b2fd88a1a_111.png)

![](img/eed3496d877de4bff775877b2fd88a1a_113.png)

以下是必须配置的几个关键参数：

![](img/eed3496d877de4bff775877b2fd88a1a_115.png)

![](img/eed3496d877de4bff775877b2fd88a1a_117.png)

![](img/eed3496d877de4bff775877b2fd88a1a_119.png)

*   **起始日期与结束日期**：定义策略回测所覆盖的历史时间段。
*   **初始资金**：设定策略开始运行时拥有的资金量，例如 `100000` 代表十万元。后续的收益率等指标将基于此初始资金计算。
*   **频率**：选择策略执行频率，可选“每日”或“每分钟”。这决定了 `handle_bar` 函数的调用频率。本教程示例通常选择“每日”。

![](img/eed3496d877de4bff775877b2fd88a1a_121.png)

![](img/eed3496d877de4bff775877b2fd88a1a_123.png)

![](img/eed3496d877de4bff775877b2fd88a1a_125.png)

![](img/eed3496d877de4bff775877b2fd88a1a_127.png)

## 第三方库与平台API的结合

![](img/eed3496d877de4bff775877b2fd88a1a_129.png)

![](img/eed3496d877de4bff775877b2fd88a1a_131.png)

![](img/eed3496d877de4bff775877b2fd88a1a_133.png)

在策略代码中，不仅可以调用Ricequant平台提供的API，还可以导入常用的Python第三方库。

![](img/eed3496d877de4bff775877b2fd88a1a_135.png)

![](img/eed3496d877de4bff775877b2fd88a1a_137.png)

![](img/eed3496d877de4bff775877b2fd88a1a_139.png)

平台API返回的数据格式（如 `DataFrame` 或 `ndarray`）与 `pandas`、`numpy` 等库兼容。因此，你可以方便地使用 `pandas` 进行数据分析，使用 `scikit-learn` 进行机器学习建模，并与平台API结合使用。

![](img/eed3496d877de4bff775877b2fd88a1a_141.png)

![](img/eed3496d877de4bff775877b2fd88a1a_143.png)

![](img/eed3496d877de4bff775877b2fd88a1a_145.png)

![](img/eed3496d877de4bff775877b2fd88a1a_147.png)

导入方式与常规Python编程一致，例如：
```python
import pandas as pd
import numpy as np
```

![](img/eed3496d877de4bff775877b2fd88a1a_149.png)

![](img/eed3496d877de4bff775877b2fd88a1a_151.png)

![](img/eed3496d877de4bff775877b2fd88a1a_153.png)

## 总结

![](img/eed3496d877de4bff775877b2fd88a1a_155.png)

![](img/eed3496d877de4bff775877b2fd88a1a_157.png)

![](img/eed3496d877de4bff775877b2fd88a1a_159.png)

本节课中，我们一起学习了Ricequant量化交易平台的基础操作。我们了解了策略编写的三个核心函数（`init`, `before_trading`, `handle_bar`）及其作用，掌握了通过 `context` 对象传递数据的方法，学会了如何配置回测的起止日期、初始资金和运行频率。最后，我们还了解到可以在策略中灵活使用Python第三方库来增强分析能力。这些是开始在该平台上进行量化策略开发的基础。