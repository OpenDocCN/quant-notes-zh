# Python金融量化交易：P23：Ricequant交易平台简介 🚀

![](img/176b001e6d632f5bea446734264bc7ab_1.png)

在本节课中，我们将学习Ricequant量化交易平台的基本使用方法。我们将了解平台的核心结构、三个关键函数的作用以及如何配置策略回测参数，为后续编写量化策略打下基础。

![](img/176b001e6d632f5bea446734264bc7ab_3.png)

![](img/176b001e6d632f5bea446734264bc7ab_5.png)

## 平台界面与策略示例

![](img/176b001e6d632f5bea446734264bc7ab_7.png)

![](img/176b001e6d632f5bea446734264bc7ab_9.png)

首先，我们演示平台的使用方法。点击“量化平台”后，选择“免费使用”进入。

![](img/176b001e6d632f5bea446734264bc7ab_11.png)

![](img/176b001e6d632f5bea446734264bc7ab_13.png)

![](img/176b001e6d632f5bea446734264bc7ab_1.png)

![](img/176b001e6d632f5bea446734264bc7ab_15.png)

![](img/176b001e6d632f5bea446734264bc7ab_17.png)

平台提供了一些预先写好的策略示例，方便用户熟悉平台操作和策略编写的基本框架。

![](img/176b001e6d632f5bea446734264bc7ab_19.png)

![](img/176b001e6d632f5bea446734264bc7ab_3.png)

![](img/176b001e6d632f5bea446734264bc7ab_21.png)

![](img/176b001e6d632f5bea446734264bc7ab_23.png)

这些策略展示了基本的编写方法。我们点开默认的“第一个入门策略”进行查看。

![](img/176b001e6d632f5bea446734264bc7ab_25.png)

![](img/176b001e6d632f5bea446734264bc7ab_27.png)

![](img/176b001e6d632f5bea446734264bc7ab_5.png)

![](img/176b001e6d632f5bea446734264bc7ab_29.png)

![](img/176b001e6d632f5bea446734264bc7ab_31.png)

打开后，界面类似于Jupyter Notebook，可以在此编写Python代码，但平台对代码结构有特定要求。

![](img/176b001e6d632f5bea446734264bc7ab_33.png)

![](img/176b001e6d632f5bea446734264bc7ab_35.png)

![](img/176b001e6d632f5bea446734264bc7ab_7.png)

![](img/176b001e6d632f5bea446734264bc7ab_37.png)

![](img/176b001e6d632f5bea446734264bc7ab_39.png)

## 核心函数结构

![](img/176b001e6d632f5bea446734264bc7ab_41.png)

![](img/176b001e6d632f5bea446734264bc7ab_43.png)

在策略编辑区，我们可以看到平台列出了三个核心函数。

![](img/176b001e6d632f5bea446734264bc7ab_45.png)

![](img/176b001e6d632f5bea446734264bc7ab_47.png)

![](img/176b001e6d632f5bea446734264bc7ab_11.png)

![](img/176b001e6d632f5bea446734264bc7ab_49.png)

![](img/176b001e6d632f5bea446734264bc7ab_51.png)

这三个函数是平台要求必须使用的，所有操作都需要围绕它们来实现。

![](img/176b001e6d632f5bea446734264bc7ab_53.png)

![](img/176b001e6d632f5bea446734264bc7ab_13.png)

![](img/176b001e6d632f5bea446734264bc7ab_55.png)

![](img/176b001e6d632f5bea446734264bc7ab_57.png)

![](img/176b001e6d632f5bea446734264bc7ab_15.png)

![](img/176b001e6d632f5bea446734264bc7ab_59.png)

![](img/176b001e6d632f5bea446734264bc7ab_61.png)

接下来，我们逐一解释这三个函数的作用。

![](img/176b001e6d632f5bea446734264bc7ab_63.png)

![](img/176b001e6d632f5bea446734264bc7ab_65.png)

### 初始化函数：`init`

![](img/176b001e6d632f5bea446734264bc7ab_67.png)

![](img/176b001e6d632f5bea446734264bc7ab_69.png)

第一个函数是 `init`，其作用是进行初始化。

![](img/176b001e6d632f5bea446734264bc7ab_71.png)

![](img/176b001e6d632f5bea446734264bc7ab_73.png)

![](img/176b001e6d632f5bea446734264bc7ab_21.png)

![](img/176b001e6d632f5bea446734264bc7ab_75.png)

![](img/176b001e6d632f5bea446734264bc7ab_77.png)

这类似于Python类中的构造函数。在构造函数中，我们通常会传入参数并进行赋值等基本操作。

![](img/176b001e6d632f5bea446734264bc7ab_79.png)

![](img/176b001e6d632f5bea446734264bc7ab_81.png)

![](img/176b001e6d632f5bea446734264bc7ab_25.png)

![](img/176b001e6d632f5bea446734264bc7ab_83.png)

在实例化一个Python对象时，会首先执行其构造函数。这里的 `init` 函数同理，它在策略执行时会被最先调用。

![](img/176b001e6d632f5bea446734264bc7ab_85.png)

![](img/176b001e6d632f5bea446734264bc7ab_87.png)

![](img/176b001e6d632f5bea446734264bc7ab_35.png)

![](img/176b001e6d632f5bea446734264bc7ab_89.png)

![](img/176b001e6d632f5bea446734264bc7ab_91.png)

### 上下文参数：`context`

![](img/176b001e6d632f5bea446734264bc7ab_93.png)

![](img/176b001e6d632f5bea446734264bc7ab_95.png)

观察三个函数，会发现它们都有一个共同的参数 `context`。

![](img/176b001e6d632f5bea446734264bc7ab_97.png)

![](img/176b001e6d632f5bea446734264bc7ab_99.png)

![](img/176b001e6d632f5bea446734264bc7ab_47.png)

![](img/176b001e6d632f5bea446734264bc7ab_101.png)

![](img/176b001e6d632f5bea446734264bc7ab_103.png)

`context` 是一个全局上下文对象，用于在不同函数间传递数据和状态。例如，在 `init` 函数中，我们可以设置股票代码、获取初始数据，并将其存入 `context`。

![](img/176b001e6d632f5bea446734264bc7ab_105.png)

![](img/176b001e6d632f5bea446734264bc7ab_107.png)

```python
context.s1 = ‘000001.XSHE‘  # 示例：在context中存储股票代码
```

![](img/176b001e6d632f5bea446734264bc7ab_109.png)

![](img/176b001e6d632f5bea446734264bc7ab_111.png)

在后续的 `before_trading` 或 `handle_bar` 函数中，我们可以通过传入的 `context` 参数来访问这些预先存储好的数据。

![](img/176b001e6d632f5bea446734264bc7ab_113.png)

![](img/176b001e6d632f5bea446734264bc7ab_115.png)

```python
def before_trading(context):
    current_stock = context.s1  # 获取在init中设置的股票代码
```

![](img/176b001e6d632f5bea446734264bc7ab_117.png)

`context` 主要起到数据传递和共享的作用。

![](img/176b001e6d632f5bea446734264bc7ab_119.png)

![](img/176b001e6d632f5bea446734264bc7ab_121.png)

![](img/176b001e6d632f5bea446734264bc7ab_71.png)

![](img/176b001e6d632f5bea446734264bc7ab_123.png)

![](img/176b001e6d632f5bea446734264bc7ab_125.png)

### 每日预处理与策略执行函数

![](img/176b001e6d632f5bea446734264bc7ab_127.png)

![](img/176b001e6d632f5bea446734264bc7ab_129.png)

`init` 函数仅在策略开始时执行一次。而另外两个函数则会多次执行。

![](img/176b001e6d632f5bea446734264bc7ab_131.png)

![](img/176b001e6d632f5bea446734264bc7ab_133.png)

![](img/176b001e6d632f5bea446734264bc7ab_91.png)

![](img/176b001e6d632f5bea446734264bc7ab_135.png)

![](img/176b001e6d632f5bea446734264bc7ab_137.png)

举个例子，在 `init` 函数中选定股票后，策略需要每天对这些股票数据进行判断和操作。这部分重复性的工作就由下面两个函数完成。

![](img/176b001e6d632f5bea446734264bc7ab_139.png)

![](img/176b001e6d632f5bea446734264bc7ab_105.png)

![](img/176b001e6d632f5bea446734264bc7ab_141.png)

![](img/176b001e6d632f5bea446734264bc7ab_143.png)

以下是这两个函数的介绍：

![](img/176b001e6d632f5bea446734264bc7ab_145.png)

![](img/176b001e6d632f5bea446734264bc7ab_147.png)

*   **`before_trading`**：在每个交易日开始前（交易信号产生前）执行。可以在此函数中进行数据预处理工作。
*   **`handle_bar`**：在每个交易时间单位（如每天或每分钟）都会执行。这里是策略逻辑的核心，包括生成交易信号、判断买卖点、执行下单等实际操作。

![](img/176b001e6d632f5bea446734264bc7ab_149.png)

![](img/176b001e6d632f5bea446734264bc7ab_151.png)

简单来说，`before_trading` 用于每日的预处理，`handle_bar` 用于每日实际的策略判断与交易。

![](img/176b001e6d632f5bea446734264bc7ab_153.png)

![](img/176b001e6d632f5bea446734264bc7ab_155.png)

![](img/176b001e6d632f5bea446734264bc7ab_135.png)

![](img/176b001e6d632f5bea446734264bc7ab_157.png)

![](img/176b001e6d632f5bea446734264bc7ab_159.png)

## 回测参数配置

![](img/176b001e6d632f5bea446734264bc7ab_161.png)

![](img/176b001e6d632f5bea446734264bc7ab_163.png)

既然 `handle_bar` 函数每天都会执行，那么“每天”这个频率是如何定义的呢？这需要通过右侧的参数配置区域来设置。

![](img/176b001e6d632f5bea446734264bc7ab_165.png)

![](img/176b001e6d632f5bea446734264bc7ab_167.png)

![](img/176b001e6d632f5bea446734264bc7ab_157.png)

![](img/176b001e6d632f5bea446734264bc7ab_169.png)

![](img/176b001e6d632f5bea446734264bc7ab_171.png)

这些参数不是运行结果，而是需要用户自行设定的回测条件。

![](img/176b001e6d632f5bea446734264bc7ab_173.png)

![](img/176b001e6d632f5bea446734264bc7ab_175.png)

![](img/176b001e6d632f5bea446734264bc7ab_159.png)

![](img/176b001e6d632f5bea446734264bc7ab_177.png)

以下是需要配置的主要参数：

![](img/176b001e6d632f5bea446734264bc7ab_179.png)

![](img/176b001e6d632f5bea446734264bc7ab_181.png)

*   **起止日期**：定义策略回测的历史时间范围，需要指定开始日期和结束日期。
*   **初始资金**：设定策略开始运行时拥有的资金量，例如 `100000` 代表10万元。后续的收益率等指标将基于此初始资金计算。
*   **频率**：选择策略执行的时间频率，可选“每天”或“每分钟”。这决定了 `handle_bar` 函数的调用频率。本教程示例中使用“每天”。

![](img/176b001e6d632f5bea446734264bc7ab_183.png)

![](img/176b001e6d632f5bea446734264bc7ab_185.png)

## 第三方库支持

![](img/176b001e6d632f5bea446734264bc7ab_187.png)

![](img/176b001e6d632f5bea446734264bc7ab_189.png)

平台支持导入常用的Python数据分析库。

![](img/176b001e6d632f5bea446734264bc7ab_191.png)

![](img/176b001e6d632f5bea446734264bc7ab_193.png)

![](img/176b001e6d632f5bea446734264bc7ab_201.png)

![](img/176b001e6d632f5bea446734264bc7ab_195.png)

![](img/176b001e6d632f5bea446734264bc7ab_197.png)

例如，你可以通过 `import pandas as pd` 或 `import numpy as np` 来导入Pandas和NumPy库。

![](img/176b001e6d632f5bea446734264bc7ab_199.png)

![](img/176b001e6d632f5bea446734264bc7ab_201.png)

平台自身的API接口返回的数据格式（如DataFrame或ndarray）也与这些库兼容，因此可以方便地结合使用，进行复杂的数据分析和计算。

![](img/176b001e6d632f5bea446734264bc7ab_203.png)

![](img/176b001e6d632f5bea446734264bc7ab_207.png)

![](img/176b001e6d632f5bea446734264bc7ab_205.png)

![](img/176b001e6d632f5bea446734264bc7ab_207.png)

## 总结

![](img/176b001e6d632f5bea446734264bc7ab_209.png)

![](img/176b001e6d632f5bea446734264bc7ab_211.png)

本节课我们一起学习了Ricequant量化平台的基本框架。

![](img/176b001e6d632f5bea446734264bc7ab_213.png)

![](img/176b001e6d632f5bea446734264bc7ab_215.png)

我们首先认识了平台的三个核心函数：**`init`**（初始化，执行一次）、**`before_trading`**（每日预处理）和 **`handle_bar`**（每日策略执行与交易）。

![](img/176b001e6d632f5bea446734264bc7ab_217.png)

![](img/176b001e6d632f5bea446734264bc7ab_219.png)

其次，我们了解了 **`context`** 参数在函数间传递数据的重要作用。

![](img/176b001e6d632f5bea446734264bc7ab_221.png)

最后，我们掌握了如何配置回测的**关键参数**，包括起止日期、初始资金和运行频率，并知道了平台支持与Pandas、NumPy等主流数据分析库结合使用。

![](img/176b001e6d632f5bea446734264bc7ab_223.png)

![](img/176b001e6d632f5bea446734264bc7ab_225.png)

理解这些基础概念是编写和回测量化策略的第一步。