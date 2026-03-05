# Python金融分析与量化交易实战：P23：Ricequant交易平台简介 📈

在本节课中，我们将学习Ricequant量化交易平台的基本使用方法。我们将了解平台的核心结构，包括三个关键的函数，以及如何配置回测参数来执行一个简单的量化策略。

## 平台界面与策略模板

上一节我们介绍了量化交易的基本概念，本节中我们来看看如何在Ricequant平台上实际操作。

![](img/70ef654075fae8b908827c0a2e343ecb_1.png)

首先，进入Ricequant平台后，点击“量化平台”，然后选择“免费使用”。

![](img/70ef654075fae8b908827c0a2e343ecb_1.png)

![](img/70ef654075fae8b908827c0a2e343ecb_3.png)

平台提供了一些预先写好的策略模板，这些模板是简单的演示案例，方便用户熟悉平台的编写环境。

![](img/70ef654075fae8b908827c0a2e343ecb_3.png)

![](img/70ef654075fae8b908827c0a2e343ecb_5.png)

这些策略展示了代码的基本写法。例如，我们可以点开名为“第一个入门策略”的模板进行查看。

![](img/70ef654075fae8b908827c0a2e343ecb_5.png)

![](img/70ef654075fae8b908827c0a2e343ecb_7.png)

## 核心代码结构

点开策略后，界面类似于Jupyter Notebook，可以编写Python代码，但平台对代码结构有特定要求。

![](img/70ef654075fae8b908827c0a2e343ecb_9.png)

![](img/70ef654075fae8b908827c0a2e343ecb_7.png)

![](img/70ef654075fae8b908827c0a2e343ecb_11.png)

以下是策略代码的核心组成部分：

![](img/70ef654075fae8b908827c0a2e343ecb_9.png)

![](img/70ef654075fae8b908827c0a2e343ecb_13.png)

我们可以看到代码中默认包含三个函数。

![](img/70ef654075fae8b908827c0a2e343ecb_11.png)

![](img/70ef654075fae8b908827c0a2e343ecb_15.png)

这三个函数不是用户随意定义的，而是平台要求必须使用的。所有量化策略的操作都需要围绕这三个函数来实现。

![](img/70ef654075fae8b908827c0a2e343ecb_17.png)

![](img/70ef654075fae8b908827c0a2e343ecb_13.png)

![](img/70ef654075fae8b908827c0a2e343ecb_15.png)

### 初始化函数：`init`

![](img/70ef654075fae8b908827c0a2e343ecb_19.png)

首先，我们来解释第一个函数 `init`。

![](img/70ef654075fae8b908827c0a2e343ecb_21.png)

![](img/70ef654075fae8b908827c0a2e343ecb_17.png)

`init` 函数用于初始化策略。这类似于Python类中的构造函数。

![](img/70ef654075fae8b908827c0a2e343ecb_23.png)

![](img/70ef654075fae8b908827c0a2e343ecb_19.png)

![](img/70ef654075fae8b908827c0a2e343ecb_25.png)

在构造函数中，我们通常会传入参数并对它们进行赋值或其他基本操作。

![](img/70ef654075fae8b908827c0a2e343ecb_21.png)

![](img/70ef654075fae8b908827c0a2e343ecb_27.png)

![](img/70ef654075fae8b908827c0a2e343ecb_23.png)

`init` 函数的作用与Python的构造函数相同。当执行策略时，`init` 函数会首先被调用。

![](img/70ef654075fae8b908827c0a2e343ecb_29.png)

![](img/70ef654075fae8b908827c0a2e343ecb_25.png)

![](img/70ef654075fae8b908827c0a2e343ecb_31.png)

![](img/70ef654075fae8b908827c0a2e343ecb_27.png)

![](img/70ef654075fae8b908827c0a2e343ecb_29.png)

![](img/70ef654075fae8b908827c0a2e343ecb_33.png)

![](img/70ef654075fae8b908827c0a2e343ecb_31.png)

![](img/70ef654075fae8b908827c0a2e343ecb_35.png)

无论后续操作多么复杂，`init` 函数总是最先执行。

![](img/70ef654075fae8b908827c0a2e343ecb_37.png)

![](img/70ef654075fae8b908827c0a2e343ecb_33.png)

![](img/70ef654075fae8b908827c0a2e343ecb_35.png)

![](img/70ef654075fae8b908827c0a2e343ecb_39.png)

### 上下文对象：`context`

`init` 函数有一个参数 `context`。观察三个函数，可以发现它们都有一个共同的参数 `context`。

![](img/70ef654075fae8b908827c0a2e343ecb_41.png)

![](img/70ef654075fae8b908827c0a2e343ecb_37.png)

![](img/70ef654075fae8b908827c0a2e343ecb_43.png)

![](img/70ef654075fae8b908827c0a2e343ecb_39.png)

`context` 是一个全局上下文对象，用于在不同函数之间传递数据和状态。

![](img/70ef654075fae8b908827c0a2e343ecb_45.png)

![](img/70ef654075fae8b908827c0a2e343ecb_41.png)

![](img/70ef654075fae8b908827c0a2e343ecb_47.png)

例如，在初始化时，我们可能选择股票代码或获取股票数据，这些信息可以存入 `context` 对象。

![](img/70ef654075fae8b908827c0a2e343ecb_43.png)

![](img/70ef654075fae8b908827c0a2e343ecb_49.png)

![](img/70ef654075fae8b908827c0a2e343ecb_45.png)

![](img/70ef654075fae8b908827c0a2e343ecb_47.png)

![](img/70ef654075fae8b908827c0a2e343ecb_51.png)

在后续的预处理或交易函数中，我们可以从 `context` 中取出这些预先存储的数据来使用。

![](img/70ef654075fae8b908827c0a2e343ecb_53.png)

![](img/70ef654075fae8b908827c0a2e343ecb_49.png)

![](img/70ef654075fae8b908827c0a2e343ecb_51.png)

![](img/70ef654075fae8b908827c0a2e343ecb_55.png)

![](img/70ef654075fae8b908827c0a2e343ecb_53.png)

![](img/70ef654075fae8b908827c0a2e343ecb_57.png)

![](img/70ef654075fae8b908827c0a2e343ecb_55.png)

![](img/70ef654075fae8b908827c0a2e343ecb_59.png)

![](img/70ef654075fae8b908827c0a2e343ecb_57.png)

`context` 对象帮助我们在不同函数间调用算法策略并传递信息。

![](img/70ef654075fae8b908827c0a2e343ecb_61.png)

![](img/70ef654075fae8b908827c0a2e343ecb_59.png)

![](img/70ef654075fae8b908827c0a2e343ecb_61.png)

![](img/70ef654075fae8b908827c0a2e343ecb_63.png)

例如，在 `init` 中，我们可以这样写：
```python
context.s1 = ‘000001.XSHE‘
```
这表示将股票代码 ‘000001.XSHE‘ 赋值给 `context` 的一个属性 `s1`。

![](img/70ef654075fae8b908827c0a2e343ecb_65.png)

![](img/70ef654075fae8b908827c0a2e343ecb_63.png)

![](img/70ef654075fae8b908827c0a2e343ecb_65.png)

![](img/70ef654075fae8b908827c0a2e343ecb_67.png)

在另一个函数（如 `before_trading`）中，如果想使用这个股票代码，可以直接通过 `context.s1` 来获取。

![](img/70ef654075fae8b908827c0a2e343ecb_67.png)

![](img/70ef654075fae8b908827c0a2e343ecb_69.png)

![](img/70ef654075fae8b908827c0a2e343ecb_69.png)

![](img/70ef654075fae8b908827c0a2e343ecb_71.png)

![](img/70ef654075fae8b908827c0a2e343ecb_71.png)

这里的点号 `.` 表示访问 `context` 对象的属性，属性名由用户自行指定。

![](img/70ef654075fae8b908827c0a2e343ecb_73.png)

![](img/70ef654075fae8b908827c0a2e343ecb_73.png)

`context` 主要承担数据传递的作用。以上就是对 `init` 函数的介绍，它会在策略开始时首先执行一次。

![](img/70ef654075fae8b908827c0a2e343ecb_75.png)

![](img/70ef654075fae8b908827c0a2e343ecb_75.png)

![](img/70ef654075fae8b908827c0a2e343ecb_77.png)

![](img/70ef654075fae8b908827c0a2e343ecb_77.png)

## 每日执行函数

![](img/70ef654075fae8b908827c0a2e343ecb_79.png)

接下来，我们看另外两个函数。

![](img/70ef654075fae8b908827c0a2e343ecb_79.png)

![](img/70ef654075fae8b908827c0a2e343ecb_81.png)

`init` 函数只在策略开始时执行一次。而下面两个函数会被执行很多次。

![](img/70ef654075fae8b908827c0a2e343ecb_81.png)

![](img/70ef654075fae8b908827c0a2e343ecb_83.png)

![](img/70ef654075fae8b908827c0a2e343ecb_83.png)

![](img/70ef654075fae8b908827c0a2e343ecb_85.png)

举个例子：在 `init` 函数中，我们选择了一只股票，这个选择是固定的。

![](img/70ef654075fae8b908827c0a2e343ecb_87.png)

![](img/70ef654075fae8b908827c0a2e343ecb_85.png)

![](img/70ef654075fae8b908827c0a2e343ecb_87.png)

![](img/70ef654075fae8b908827c0a2e343ecb_89.png)

![](img/70ef654075fae8b908827c0a2e343ecb_89.png)

选择完成后，在后续的策略中，我们需要每天对这只股票进行操作，比如查看每日收盘价、判断买卖点等。

![](img/70ef654075fae8b908827c0a2e343ecb_91.png)

![](img/70ef654075fae8b908827c0a2e343ecb_91.png)

![](img/70ef654075fae8b908827c0a2e343ecb_93.png)

![](img/70ef654075fae8b908827c0a2e343ecb_93.png)

![](img/70ef654075fae8b908827c0a2e343ecb_95.png)

![](img/70ef654075fae8b908827c0a2e343ecb_95.png)

![](img/70ef654075fae8b908827c0a2e343ecb_97.png)

![](img/70ef654075fae8b908827c0a2e343ecb_97.png)

这些每日重复的操作就写在下面两个函数中。

![](img/70ef654075fae8b908827c0a2e343ecb_99.png)

![](img/70ef654075fae8b908827c0a2e343ecb_99.png)

![](img/70ef654075fae8b908827c0a2e343ecb_101.png)

![](img/70ef654075fae8b908827c0a2e343ecb_101.png)

### 盘前处理函数：`before_trading`

第一个是 `before_trading` 函数，它在每个交易日开始前、实际交易发生前被执行。

![](img/70ef654075fae8b908827c0a2e343ecb_103.png)

![](img/70ef654075fae8b908827c0a2e343ecb_103.png)

![](img/70ef654075fae8b908827c0a2e343ecb_105.png)

![](img/70ef654075fae8b908827c0a2e343ecb_105.png)

它的作用是对当天的数据进行预处理。例如，当新数据到来后，在做出买卖决策前，你可能想先对数据进行一些计算或过滤。

![](img/70ef654075fae8b908827c0a2e343ecb_107.png)

![](img/70ef654075fae8b908827c0a2e343ecb_107.png)

![](img/70ef654075fae8b908827c0a2e343ecb_109.png)

![](img/70ef654075fae8b908827c0a2e343ecb_109.png)

![](img/70ef654075fae8b908827c0a2e343ecb_111.png)

![](img/70ef654075fae8b908827c0a2e343ecb_111.png)

这些操作就可以写在 `before_trading` 函数中，相当于每日的数据预处理步骤。

![](img/70ef654075fae8b908827c0a2e343ecb_113.png)

![](img/70ef654075fae8b908827c0a2e343ecb_113.png)

![](img/70ef654075fae8b908827c0a2e343ecb_115.png)

![](img/70ef654075fae8b908827c0a2e343ecb_115.png)

### 策略执行函数：`handle_bar`

![](img/70ef654075fae8b908827c0a2e343ecb_117.png)

另一个是 `handle_bar` 函数，它每天都会执行。

![](img/70ef654075fae8b908827c0a2e343ecb_117.png)

![](img/70ef654075fae8b908827c0a2e343ecb_119.png)

在这里，我们实际编写策略逻辑，基于策略做出判断并执行下单、买卖等后续操作。

![](img/70ef654075fae8b908827c0a2e343ecb_121.png)

![](img/70ef654075fae8b908827c0a2e343ecb_119.png)

![](img/70ef654075fae8b908827c0a2e343ecb_121.png)

![](img/70ef654075fae8b908827c0a2e343ecb_123.png)

所以，`handle_bar` 函数是算法策略实际运行的核心。

![](img/70ef654075fae8b908827c0a2e343ecb_123.png)

![](img/70ef654075fae8b908827c0a2e343ecb_125.png)

![](img/70ef654075fae8b908827c0a2e343ecb_125.png)

![](img/70ef654075fae8b908827c0a2e343ecb_127.png)

注意，`before_trading` 和 `handle_bar` 都是每个交易日都会执行的函数。

![](img/70ef654075fae8b908827c0a2e343ecb_127.png)

![](img/70ef654075fae8b908827c0a2e343ecb_129.png)

具体的判断和交易指令都在 `handle_bar` 函数中执行。

![](img/70ef654075fae8b908827c0a2e343ecb_129.png)

![](img/70ef654075fae8b908827c0a2e343ecb_131.png)

![](img/70ef654075fae8b908827c0a2e343ecb_131.png)

![](img/70ef654075fae8b908827c0a2e343ecb_133.png)

简单总结一下这三个函数：
*   `init`：初始化，只执行一次。
*   `before_trading`：每日盘前预处理，每个交易日执行一次。
*   `handle_bar`：策略核心逻辑与交易执行，每个交易日执行一次。

![](img/70ef654075fae8b908827c0a2e343ecb_133.png)

![](img/70ef654075fae8b908827c0a2e343ecb_135.png)

## 回测参数配置

既然提到“每日”执行，那么这个“每日”是如何体现的呢？这需要通过配置回测参数来实现。

![](img/70ef654075fae8b908827c0a2e343ecb_135.png)

![](img/70ef654075fae8b908827c0a2e343ecb_137.png)

在代码编辑区的右侧，有几个非常重要的参数需要用户自行设置，它们不是运行结果。

![](img/70ef654075fae8b908827c0a2e343ecb_139.png)

![](img/70ef654075fae8b908827c0a2e343ecb_137.png)

![](img/70ef654075fae8b908827c0a2e343ecb_139.png)

![](img/70ef654075fae8b908827c0a2e343ecb_141.png)

要进行回测，即在历史数据上检验策略效果，必须指定相关参数。

![](img/70ef654075fae8b908827c0a2e343ecb_141.png)

![](img/70ef654075fae8b908827c0a2e343ecb_143.png)

![](img/70ef654075fae8b908827c0a2e343ecb_143.png)

![](img/70ef654075fae8b908827c0a2e343ecb_145.png)

以下是需要配置的主要参数：

![](img/70ef654075fae8b908827c0a2e343ecb_145.png)

![](img/70ef654075fae8b908827c0a2e343ecb_147.png)

1.  **起止日期**：你需要指定回测的时间范围，包括开始日期和结束日期。

![](img/70ef654075fae8b908827c0a2e343ecb_149.png)

![](img/70ef654075fae8b908827c0a2e343ecb_147.png)

![](img/70ef654075fae8b908827c0a2e343ecb_151.png)

![](img/70ef654075fae8b908827c0a2e343ecb_149.png)

![](img/70ef654075fae8b908827c0a2e343ecb_151.png)

![](img/70ef654075fae8b908827c0a2e343ecb_153.png)

2.  **初始资金**：例如，图中显示为100,000。这表示你用于执行该策略的起始资金金额。回测结束后，平台会计算基于该初始资金的收益率等指标。

![](img/70ef654075fae8b908827c0a2e343ecb_155.png)

![](img/70ef654075fae8b908827c0a2e343ecb_153.png)

![](img/70ef654075fae8b908827c0a2e343ecb_155.png)

![](img/70ef654075fae8b908827c0a2e343ecb_157.png)

![](img/70ef654075fae8b908827c0a2e343ecb_157.png)

![](img/70ef654075fae8b908827c0a2e343ecb_159.png)

![](img/70ef654075fae8b908827c0a2e343ecb_159.png)

![](img/70ef654075fae8b908827c0a2e343ecb_161.png)

![](img/70ef654075fae8b908827c0a2e343ecb_161.png)

初始资金由用户根据策略需求自行设定。

![](img/70ef654075fae8b908827c0a2e343ecb_163.png)

![](img/70ef654075fae8b908827c0a2e343ecb_163.png)

![](img/70ef654075fae8b908827c0a2e343ecb_165.png)

![](img/70ef654075fae8b908827c0a2e343ecb_165.png)

3.  **频率**：选项有“每日”和“每分钟”。这决定了 `handle_bar` 函数的执行频率。
    *   **每日**：每个交易日执行一次 `handle_bar`。
    *   **每分钟**：每个交易日的每分钟执行一次 `handle_bar`。

![](img/70ef654075fae8b908827c0a2e343ecb_167.png)

![](img/70ef654075fae8b908827c0a2e343ecb_167.png)

![](img/70ef654075fae8b908827c0a2e343ecb_169.png)

![](img/70ef654075fae8b908827c0a2e343ecb_171.png)

![](img/70ef654075fae8b908827c0a2e343ecb_169.png)

用户需要根据策略需求选择执行频率。在入门示例中，我们通常选择“每日”。

![](img/70ef654075fae8b908827c0a2e343ecb_171.png)

![](img/70ef654075fae8b908827c0a2e343ecb_173.png)

![](img/70ef654075fae8b908827c0a2e343ecb_175.png)

![](img/70ef654075fae8b908827c0a2e343ecb_173.png)

平台目前主要支持这两种频率。

![](img/70ef654075fae8b908827c0a2e343ecb_175.png)

![](img/70ef654075fae8b908827c0a2e343ecb_177.png)

## 第三方库支持

![](img/70ef654075fae8b908827c0a2e343ecb_177.png)

如果你想进行更复杂的计算（如统计指标），平台完全支持导入常用的Python库。

![](img/70ef654075fae8b908827c0a2e343ecb_179.png)

![](img/70ef654075fae8b908827c0a2e343ecb_179.png)

例如，你可以在代码开头导入 `pandas` 和 `numpy`：
```python
import pandas as pd
import numpy as np
```

![](img/70ef654075fae8b908827c0a2e343ecb_181.png)

![](img/70ef654075fae8b908827c0a2e343ecb_181.png)

Ricequant平台自身的API接口返回的数据，很多也是 `DataFrame`（pandas）或 `ndarray`（numpy）格式。

![](img/70ef654075fae8b908827c0a2e343ecb_183.png)

![](img/70ef654075fae8b908827c0a2e343ecb_183.png)

![](img/70ef654075fae8b908827c0a2e343ecb_185.png)

![](img/70ef654075fae8b908827c0a2e343ecb_185.png)

![](img/70ef654075fae8b908827c0a2e343ecb_187.png)

![](img/70ef654075fae8b908827c0a2e343ecb_187.png)

因此，你可以将平台API与 `pandas`、`numpy` 甚至 `scikit-learn` 等机器学习库无缝结合使用。

![](img/70ef654075fae8b908827c0a2e343ecb_189.png)

![](img/70ef654075fae8b908827c0a2e343ecb_189.png)

![](img/70ef654075fae8b908827c0a2e343ecb_191.png)

![](img/70ef654075fae8b908827c0a2e343ecb_191.png)

![](img/70ef654075fae8b908827c0a2e343ecb_193.png)

这意味着，在策略中既能使用Ricequant提供的专用API，也能使用通用的Python科学计算库，重点在于将它们有机结合。

![](img/70ef654075fae8b908827c0a2e343ecb_193.png)

![](img/70ef654075fae8b908827c0a2e343ecb_195.png)

![](img/70ef654075fae8b908827c0a2e343ecb_197.png)

![](img/70ef654075fae8b908827c0a2e343ecb_195.png)

![](img/70ef654075fae8b908827c0a2e343ecb_199.png)

![](img/70ef654075fae8b908827c0a2e343ecb_197.png)

## 总结

本节课中我们一起学习了Ricequant量化平台的基本框架。

![](img/70ef654075fae8b908827c0a2e343ecb_199.png)

![](img/70ef654075fae8b908827c0a2e343ecb_201.png)

![](img/70ef654075fae8b908827c0a2e343ecb_201.png)

我们介绍了三个核心函数：
1.  **`init(context)`**：初始化函数，策略启动时执行一次。
2.  **`before_trading(context)`**：盘前处理函数，每个交易日执行一次，用于数据预处理。
3.  **`handle_bar(context)`**：策略主函数，每个交易日（或每分钟）执行一次，包含核心交易逻辑。

同时，我们了解了如何配置右侧的回测参数，包括**起止日期**、**初始资金**和**执行频率**，这些是运行回测并获得策略评估指标的前提。

![](img/70ef654075fae8b908827c0a2e343ecb_203.png)

![](img/70ef654075fae8b908827c0a2e343ecb_203.png)

![](img/70ef654075fae8b908827c0a2e343ecb_205.png)

最后，我们知道了在平台中可以灵活使用Python生态的强大工具（如pandas, numpy）与平台API相结合，来构建复杂的量化策略。

![](img/70ef654075fae8b908827c0a2e343ecb_205.png)

![](img/70ef654075fae8b908827c0a2e343ecb_207.png)

![](img/70ef654075fae8b908827c0a2e343ecb_207.png)