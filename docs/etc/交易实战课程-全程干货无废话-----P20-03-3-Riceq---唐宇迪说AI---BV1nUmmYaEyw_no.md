# Python金融分析与量化交易实战：P20：03-3-Ricequant交易平台简介 📊

![](img/13e1e90fe261ae277b0463eb3c629e05_1.png)

## 概述
在本节课程中，我们将学习Ricequant量化交易平台的基本使用方法。我们将了解平台的核心结构、关键函数的作用以及如何配置参数来运行一个量化策略。

## 平台入口与策略模板
首先，我们进入量化平台并点击“免费使用”。平台提供了一些预先写好的策略示例，这些示例可以帮助我们熟悉策略的编写方式。我们点开名为“第一个入门策略”的示例进行查看。

![](img/13e1e90fe261ae277b0463eb3c629e05_1.png)

界面类似于Jupyter Notebook，可以编写Python代码，但平台对代码结构有特定要求。

## 核心函数解析
所有操作都必须围绕三个核心函数来实现。以下是这三个函数的详细解释。

### 初始化函数：`init`
`init`函数用于执行初始化操作。它类似于Python类中的构造函数。当策略开始执行时，`init`函数会首先被调用一次。

![](img/13e1e90fe261ae277b0463eb3c629e05_3.png)

该函数包含一个参数`context`。`context`是一个全局上下文对象，用于在不同函数间传递数据和状态。例如，在`init`中初始化股票代码或数据后，可以存入`context`，以便在后续函数中调用。

```python
def init(context):
    # 初始化操作，例如选定股票
    context.s1 = “000001.XSHE”
```

### 每日开盘前处理函数：`before_trading`
`before_trading`函数在每个交易日开始前被调用。它的作用是对当天的数据进行预处理。这个函数也是每天都会执行的。

```python
def before_trading(context):
    # 每日开盘前的数据预处理
    stock_data = context.s1
```

![](img/13e1e90fe261ae277b0463eb3c629e05_5.png)

### 策略主逻辑函数：`handle_bar`
`handle_bar`函数是策略的核心，包含了实际的交易逻辑和下单指令。它会在每个交易周期（如每天或每分钟）被调用一次，用于判断买卖点并执行操作。

![](img/13e1e90fe261ae277b0463eb3c629e05_7.png)

```python
def handle_bar(context, bar_dict):
    # 策略主逻辑，判断买卖并执行
    order_shares(context.s1, 1000)
```

简单总结：`init`用于初始化，`before_trading`用于每日预处理，`handle_bar`用于执行每日的实际交易策略。

## 回测参数配置
策略的执行依赖于右侧的参数配置，这些参数不是运行结果，而是需要用户自行设定的输入。

以下是需要配置的关键参数列表：

*   **起止日期**：定义回测的时间范围，需要指定开始日期和结束日期。
*   **初始资金**：设定策略开始运行时的本金金额，例如100,000元。
*   **运行频率**：选择策略是以“每日”还是“每分钟”的频率执行`handle_bar`函数。本教程示例使用每日频率。

## 第三方库与平台API的结合
平台支持导入常用的Python数据分析库，例如`pandas`和`numpy`。平台自身的API返回的数据格式（如DataFrame或ndarray）与这些库兼容，因此可以无缝结合使用。

```python
import pandas as pd
import numpy as np
# 可以结合平台API和pandas/numpy进行计算
```

这意味着在策略中，我们既能使用平台提供的便捷API获取金融数据，也能利用`pandas`、`numpy`乃至`scikit-learn`等库进行复杂的数据分析和建模。

![](img/13e1e90fe261ae277b0463eb3c629e05_9.png)

![](img/13e1e90fe261ae277b0463eb3c629e05_11.png)

## 总结
本节课我们一起学习了Ricequant量化交易平台的基本框架。我们掌握了三个核心函数（`init`、`before_trading`、`handle_bar`）的分工与用法，了解了如何配置回测的起止日期、初始资金和运行频率等关键参数，并知道了平台原生API可以与主流Python数据分析库结合使用。这是构建和回测量化策略的基础。