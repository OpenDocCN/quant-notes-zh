# Python金融量化与股票交易：P23：Ricequant交易平台简介 🚀

![](img/10e4b0cebcadff1308d95057ad670ae5_1.png)

![](img/10e4b0cebcadff1308d95057ad670ae5_3.png)

在本节课中，我们将学习如何使用Ricequant量化交易平台。我们将了解平台的基本界面、核心的编程框架以及如何配置一个简单的回测策略。通过本节内容，你将掌握在Ricequant上编写和运行量化策略的基本流程。

![](img/10e4b0cebcadff1308d95057ad670ae5_5.png)

![](img/10e4b0cebcadff1308d95057ad670ae5_7.png)

![](img/10e4b0cebcadff1308d95057ad670ae5_9.png)

## 平台界面与策略示例

![](img/10e4b0cebcadff1308d95057ad670ae5_11.png)

![](img/10e4b0cebcadff1308d95057ad670ae5_13.png)

首先，我们演示量化平台的使用方法。点击“量化平台”，然后选择“免费使用”。

![](img/10e4b0cebcadff1308d95057ad670ae5_15.png)

![](img/10e4b0cebcadff1308d95057ad670ae5_17.png)

![](img/10e4b0cebcadff1308d95057ad670ae5_19.png)

![](img/10e4b0cebcadff1308d95057ad670ae5_1.png)

![](img/10e4b0cebcadff1308d95057ad670ae5_21.png)

![](img/10e4b0cebcadff1308d95057ad670ae5_23.png)

进入平台后，会看到一些预设的策略。这些是平台提供的简单示例，方便用户熟悉环境。

![](img/10e4b0cebcadff1308d95057ad670ae5_25.png)

![](img/10e4b0cebcadff1308d95057ad670ae5_27.png)

![](img/10e4b0cebcadff1308d95057ad670ae5_3.png)

![](img/10e4b0cebcadff1308d95057ad670ae5_29.png)

![](img/10e4b0cebcadff1308d95057ad670ae5_31.png)

![](img/10e4b0cebcadff1308d95057ad670ae5_33.png)

这些策略展示了基本的编写方法。我们点开第一个名为“入门策略”的示例。

![](img/10e4b0cebcadff1308d95057ad670ae5_35.png)

![](img/10e4b0cebcadff1308d95057ad670ae5_37.png)

![](img/10e4b0cebcadff1308d95057ad670ae5_39.png)

![](img/10e4b0cebcadff1308d95057ad670ae5_5.png)

![](img/10e4b0cebcadff1308d95057ad670ae5_41.png)

![](img/10e4b0cebcadff1308d95057ad670ae5_43.png)

界面类似于Jupyter Notebook，可以编写Python代码，但平台对代码结构有特定要求。

![](img/10e4b0cebcadff1308d95057ad670ae5_45.png)

![](img/10e4b0cebcadff1308d95057ad670ae5_47.png)

![](img/10e4b0cebcadff1308d95057ad670ae5_7.png)

![](img/10e4b0cebcadff1308d95057ad670ae5_49.png)

![](img/10e4b0cebcadff1308d95057ad670ae5_51.png)

![](img/10e4b0cebcadff1308d95057ad670ae5_53.png)

## 核心函数框架

![](img/10e4b0cebcadff1308d95057ad670ae5_55.png)

![](img/10e4b0cebcadff1308d95057ad670ae5_57.png)

![](img/10e4b0cebcadff1308d95057ad670ae5_59.png)

所有操作都需要围绕三个核心函数来实现。以下是这三个函数的列表：

![](img/10e4b0cebcadff1308d95057ad670ae5_61.png)

![](img/10e4b0cebcadff1308d95057ad670ae5_63.png)

*   `init(context)`
*   `before_trading(context)`
*   `handle_bar(context, bar_dict)`

![](img/10e4b0cebcadff1308d95057ad670ae5_65.png)

![](img/10e4b0cebcadff1308d95057ad670ae5_67.png)

![](img/10e4b0cebcadff1308d95057ad670ae5_69.png)

这三个函数是平台要求必须使用的，所有策略逻辑都需围绕它们构建。

![](img/10e4b0cebcadff1308d95057ad670ae5_71.png)

![](img/10e4b0cebcadff1308d95057ad670ae5_73.png)

### 初始化函数：`init`

![](img/10e4b0cebcadff1308d95057ad670ae5_75.png)

![](img/10e4b0cebcadff1308d95057ad670ae5_77.png)

![](img/10e4b0cebcadff1308d95057ad670ae5_79.png)

第一个函数是 `init`，用于初始化操作。它类似于Python类中的构造函数。

![](img/10e4b0cebcadff1308d95057ad670ae5_81.png)

![](img/10e4b0cebcadff1308d95057ad670ae5_83.png)

在Python中，实例化一个对象时，会先执行其构造函数。这里同理，当策略执行时，`init` 函数会最先被调用。

![](img/10e4b0cebcadff1308d95057ad670ae5_85.png)

![](img/10e4b0cebcadff1308d95057ad670ae5_87.png)

![](img/10e4b0cebcadff1308d95057ad670ae5_89.png)

该函数有一个参数 `context`。观察发现，三个函数都有这个共同的参数。

![](img/10e4b0cebcadff1308d95057ad670ae5_91.png)

![](img/10e4b0cebcadff1308d95057ad670ae5_93.png)

`context` 是一个全局上下文对象，用于在不同函数间传递数据和状态。例如，在初始化时设置股票代码或获取数据，可以存入 `context` 对象。

![](img/10e4b0cebcadff1308d95057ad670ae5_95.png)

![](img/10e4b0cebcadff1308d95057ad670ae5_97.png)

![](img/10e4b0cebcadff1308d95057ad670ae5_99.png)

```python
def init(context):
    # 初始化操作，例如选定股票
    context.s1 = “000001.XSHE” # 示例：平安银行股票代码
```

![](img/10e4b0cebcadff1308d95057ad670ae5_101.png)

![](img/10e4b0cebcadff1308d95057ad670ae5_103.png)

![](img/10e4b0cebcadff1308d95057ad670ae5_105.png)

之后在预处理或策略逻辑中，可以直接通过 `context` 参数访问这些数据。

![](img/10e4b0cebcadff1308d95057ad670ae5_107.png)

![](img/10e4b0cebcadff1308d95057ad670ae5_109.png)

### 预处理函数：`before_trading`

![](img/10e4b0cebcadff1308d95057ad670ae5_111.png)

![](img/10e4b0cebcadff1308d95057ad670ae5_113.png)

![](img/10e4b0cebcadff1308d95057ad670ae5_115.png)

接下来我们看看 `before_trading` 函数。初始化函数 `init` 只在策略开始时执行一次。

![](img/10e4b0cebcadff1308d95057ad670ae5_117.png)

![](img/10e4b0cebcadff1308d95057ad670ae5_119.png)

![](img/10e4b0cebcadff1308d95057ad670ae5_121.png)

而 `before_trading` 和 `handle_bar` 函数则会被执行很多次。举个例子，在 `init` 中选定了股票，后续每天都需要对这只股票进行操作，比如获取每日收盘价、判断买卖点等。

![](img/10e4b0cebcadff1308d95057ad670ae5_123.png)

![](img/10e4b0cebcadff1308d95057ad670ae5_125.png)

`before_trading` 函数在每个交易日开始前（实际交易发生前）被调用。你可以在这里进行数据预处理。

![](img/10e4b0cebcadff1308d95057ad670ae5_127.png)

![](img/10e4b0cebcadff1308d95057ad670ae5_129.png)

![](img/10e4b0cebcadff1308d95057ad670ae5_131.png)

### 策略逻辑函数：`handle_bar`

![](img/10e4b0cebcadff1308d95057ad670ae5_133.png)

![](img/10e4b0cebcadff1308d95057ad670ae5_135.png)

`handle_bar` 函数是策略的核心，每天（或每分钟）都会执行。实际的算法逻辑、买卖判断和下单操作都在这个函数中实现。

![](img/10e4b0cebcadff1308d95057ad670ae5_137.png)

![](img/10e4b0cebcadff1308d95057ad670ae5_139.png)

简单总结三个函数的作用：
*   `init`： 策略初始化，只执行一次。
*   `before_trading`： 每日交易前的预处理，每个交易日执行一次。
*   `handle_bar`： 每日（或每分钟）的策略核心逻辑与交易执行。

![](img/10e4b0cebcadff1308d95057ad670ae5_141.png)

![](img/10e4b0cebcadff1308d95057ad670ae5_143.png)

## 回测参数配置

![](img/10e4b0cebcadff1308d95057ad670ae5_145.png)

![](img/10e4b0cebcadff1308d95057ad670ae5_147.png)

既然提到 `handle_bar` 函数每天都会执行，那么“每天”这个概念如何体现呢？这需要通过右侧的参数配置区域来设定。

![](img/10e4b0cebcadff1308d95057ad670ae5_149.png)

![](img/10e4b0cebcadff1308d95057ad670ae5_151.png)

![](img/10e4b0cebcadff1308d95057ad670ae5_153.png)

![](img/10e4b0cebcadff1308d95057ad670ae5_139.png)

![](img/10e4b0cebcadff1308d95057ad670ae5_155.png)

![](img/10e4b0cebcadff1308d95057ad670ae5_157.png)

右侧区域不是运行结果，而是需要你自行配置的参数。要进行历史回测，必须指定以下信息：

![](img/10e4b0cebcadff1308d95057ad670ae5_159.png)

![](img/10e4b0cebcadff1308d95057ad670ae5_161.png)

![](img/10e4b0cebcadff1308d95057ad670ae5_163.png)

以下是需要配置的核心参数列表：

![](img/10e4b0cebcadff1308d95057ad670ae5_165.png)

![](img/10e4b0cebcadff1308d95057ad670ae5_167.png)

![](img/10e4b0cebcadff1308d95057ad670ae5_169.png)

1.  **起止日期**： 回测的时间范围，包括开始日期和结束日期。
2.  **初始资金**： 策略启动时的本金金额，例如 `100000` 代表10万元。回测结束后将基于此计算收益率等指标。
3.  **频率**： 策略执行频率，可选择“每日”或“每分钟”。这决定了 `handle_bar` 函数的调用周期。本教程示例使用“每日”频率。

![](img/10e4b0cebcadff1308d95057ad670ae5_171.png)

![](img/10e4b0cebcadff1308d95057ad670ae5_173.png)

## 第三方库与平台API结合

![](img/10e4b0cebcadff1308d95057ad670ae5_175.png)

![](img/10e4b0cebcadff1308d95057ad670ae5_177.png)

![](img/10e4b0cebcadff1308d95057ad670ae5_179.png)

在实际编码中，你不仅可以使用平台提供的API，还可以导入常用的Python库。

![](img/10e4b0cebcadff1308d95057ad670ae5_181.png)

![](img/10e4b0cebcadff1308d95057ad670ae5_183.png)

平台允许在代码中导入如 `pandas`、`numpy` 等库进行计算。

![](img/10e4b0cebcadff1308d95057ad670ae5_185.png)

![](img/10e4b0cebcadff1308d95057ad670ae5_187.png)

```python
# 在策略代码中导入第三方库
import pandas as pd
import numpy as np
```

![](img/10e4b0cebcadff1308d95057ad670ae5_189.png)

![](img/10e4b0cebcadff1308d95057ad670ae5_191.png)

![](img/10e4b0cebcadff1308d95057ad670ae5_193.png)

平台API返回的数据格式（如DataFrame或ndarray）与这些库兼容，因此可以无缝结合使用。你既可以调用平台API获取数据，也能利用 `pandas`、`numpy` 甚至 `scikit-learn` 进行复杂的数据处理和模型构建。

![](img/10e4b0cebcadff1308d95057ad670ae5_195.png)

![](img/10e4b0cebcadff1308d95057ad670ae5_197.png)

## 总结

![](img/10e4b0cebcadff1308d95057ad670ae5_199.png)

![](img/10e4b0cebcadff1308d95057ad670ae5_201.png)

![](img/10e4b0cebcadff1308d95057ad670ae5_203.png)

本节课我们一起学习了Ricequant量化平台的基本使用。我们介绍了平台界面的三个核心函数 `init`、`before_trading` 和 `handle_bar` 的作用与调用时机，并讲解了如何配置回测的起止日期、初始资金和运行频率等关键参数。最后，我们了解到在策略中可以灵活结合平台API和第三方Python库（如pandas）来构建复杂的量化模型。掌握这些基础知识是开始编写和测试自己量化策略的第一步。