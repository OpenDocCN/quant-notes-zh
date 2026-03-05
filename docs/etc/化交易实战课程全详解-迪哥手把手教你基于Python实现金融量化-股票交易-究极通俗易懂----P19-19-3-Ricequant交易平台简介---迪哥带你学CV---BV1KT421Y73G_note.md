# Python金融量化实战课程：P19：Ricequant交易平台简介 🚀

在本节课中，我们将学习Ricequant量化交易平台的基本使用方法。我们将了解平台的核心结构、关键函数的作用以及如何配置回测参数，为后续编写和测试量化策略打下基础。

![](img/6ed2fb933e6aa6052d21d73cedc43aa3_1.png)

## 平台界面与策略模板

首先，我们进入Ricequant平台的“量化平台”模块，点击“免费使用”。平台提供了一些预先写好的策略示例，方便用户熟悉策略的编写方式。

我们点开第一个名为“入门策略”的示例。

![](img/6ed2fb933e6aa6052d21d73cedc43aa3_1.png)

界面类似于Jupyter Notebook，可以编写Python代码。但平台对代码结构有特定要求，所有操作都需要围绕三个核心函数来实现。

## 核心函数详解

以下是平台要求必须使用的三个核心函数，它们构成了策略执行的骨架。

### 1. 初始化函数 `init`

`init` 函数用于策略的初始化，类似于Python类中的构造函数。当策略开始执行时，`init` 函数会首先被调用一次。

```python
def init(context):
    # 初始化操作，例如选定股票、设置全局变量等
    context.s1 = "000001.XSHE"
```

![](img/6ed2fb933e6aa6052d21d73cedc43aa3_3.png)

**参数 `context`**：这是一个上下文对象，用于在不同函数间传递数据和状态。在`init`中初始化的变量（如股票代码），可以通过`context`传递给后续的`before_trading`和`handle_bar`函数。

### 2. 盘前处理函数 `before_trading`

`before_trading` 函数在每个交易日开始前（开盘前）被调用。它主要用于数据预处理或准备工作。

```python
def before_trading(context):
    # 每日开盘前执行的操作，例如获取最新数据、进行计算等
    stock_data = context.s1
```

### 3. 盘中处理函数 `handle_bar`

`handle_bar` 函数是策略逻辑的核心。根据回测频率（每日或每分钟），它会在每个时间单位被调用，用于执行买卖判断和下单操作。

![](img/6ed2fb933e6aa6052d21d73cedc43aa3_5.png)

```python
def handle_bar(context, bar_dict):
    # 策略主要逻辑，判断买卖点并执行交易
    # 例如：if 条件: order_shares(context.s1, 100)
    pass
```

**函数调用顺序总结**：
1.  策略启动时，`init` 执行一次。
2.  每个交易日，先执行 `before_trading`。
3.  接着，根据频率（每日/每分钟）重复执行 `handle_bar`。

![](img/6ed2fb933e6aa6052d21d73cedc43aa3_7.png)

## 回测参数配置

上一节我们介绍了策略的核心函数，本节我们来看看如何配置回测环境。策略的执行离不开参数设置，这些设置在界面右侧，需要用户自行定义。

![](img/6ed2fb933e6aa6052d21d73cedc43aa3_9.png)

以下是需要配置的关键参数：

*   **起止日期**：定义回测的时间范围，即策略将在哪一段历史数据上运行。
*   **初始资金**：设定策略开始运行时拥有的本金数额，例如 `100000` 代表10万元。
*   **频率**：选择策略执行的时间粒度，可选“每日”或“每分钟”。这决定了 `handle_bar` 函数的调用频率。

## 第三方库与平台API结合

在策略中，我们不仅可以调用Ricequant平台提供的API来获取数据，还可以自由导入常用的Python数据分析库。

例如，你可以这样导入并使用 `pandas` 和 `numpy`：

```python
import pandas as pd
import numpy as np
# 结合平台API返回的DataFrame进行分析
```

平台API返回的数据格式（如DataFrame或ndarray）与这些库完全兼容，使得复杂的数据分析和机器学习模型能够轻松集成到量化策略中。

## 回测结果分析

配置好参数并编写策略后，可以运行回测。平台会输出一系列绩效指标。

![](img/6ed2fb933e6aa6052d21d73cedc43aa3_11.png)

这些指标用于评估策略在历史数据上的表现，例如收益率、夏普比率、最大回撤等，帮助我们优化策略逻辑。

## 总结

![](img/6ed2fb933e6aa6052d21d73cedc43aa3_11.png)

![](img/6ed2fb933e6aa6052d21d73cedc43aa3_13.png)

本节课中，我们一起学习了Ricequant量化平台的基本框架。
我们掌握了三个核心函数（`init`, `before_trading`, `handle_bar`）的作用与执行顺序，学会了如何设置回测的起止日期、初始资金和运行频率。
我们还了解到，在策略中可以灵活结合平台API和Python第三方库（如pandas, numpy）进行数据分析。
这些知识是开始编写和测试个人量化策略的基础。