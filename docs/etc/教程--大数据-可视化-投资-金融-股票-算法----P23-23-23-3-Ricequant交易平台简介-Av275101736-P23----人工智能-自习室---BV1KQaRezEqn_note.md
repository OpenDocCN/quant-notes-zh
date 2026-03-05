# Python金融量化交易：P23：Ricequant交易平台简介 🚀

![](img/6bdcde9260573a1f7af9136fd961ab99_1.png)

在本节课中，我们将学习如何使用Ricequant量化交易平台。我们将了解平台的基本界面、核心的编程框架以及如何配置策略回测的基本参数。通过本节内容，你将能够理解并开始在Ricequant平台上编写和运行简单的量化策略。

![](img/6bdcde9260573a1f7af9136fd961ab99_3.png)

## 平台界面与策略示例

![](img/6bdcde9260573a1f7af9136fd961ab99_5.png)

首先，我们演示量化平台的使用方法。点击“量化平台”，然后选择“免费使用”。

![](img/6bdcde9260573a1f7af9136fd961ab99_7.png)

![](img/6bdcde9260573a1f7af9136fd961ab99_9.png)

![](img/6bdcde9260573a1f7af9136fd961ab99_1.png)

![](img/6bdcde9260573a1f7af9136fd961ab99_11.png)

进入平台后，会看到一些预设的策略。这些是平台提供的简单示例，方便用户熟悉环境。

![](img/6bdcde9260573a1f7af9136fd961ab99_13.png)

![](img/6bdcde9260573a1f7af9136fd961ab99_3.png)

![](img/6bdcde9260573a1f7af9136fd961ab99_15.png)

![](img/6bdcde9260573a1f7af9136fd961ab99_17.png)

这些策略展示了基本的编写方法。我们点开第一个名为“入门策略”的示例。

![](img/6bdcde9260573a1f7af9136fd961ab99_19.png)

![](img/6bdcde9260573a1f7af9136fd961ab99_5.png)

![](img/6bdcde9260573a1f7af9136fd961ab99_21.png)

界面类似于Jupyter Notebook，可以编写Python代码，但平台对代码结构有特定要求。

![](img/6bdcde9260573a1f7af9136fd961ab99_23.png)

![](img/6bdcde9260573a1f7af9136fd961ab99_25.png)

![](img/6bdcde9260573a1f7af9136fd961ab99_7.png)

![](img/6bdcde9260573a1f7af9136fd961ab99_27.png)

## 核心函数框架

![](img/6bdcde9260573a1f7af9136fd961ab99_29.png)

![](img/6bdcde9260573a1f7af9136fd961ab99_31.png)

所有操作都需要围绕三个核心函数来实现。以下是这三个函数的列表：

![](img/6bdcde9260573a1f7af9136fd961ab99_33.png)

![](img/6bdcde9260573a1f7af9136fd961ab99_35.png)

![](img/6bdcde9260573a1f7af9136fd961ab99_9.png)

![](img/6bdcde9260573a1f7af9136fd961ab99_37.png)

第一个函数是 `init`。

![](img/6bdcde9260573a1f7af9136fd961ab99_39.png)

![](img/6bdcde9260573a1f7af9136fd961ab99_41.png)

![](img/6bdcde9260573a1f7af9136fd961ab99_11.png)

![](img/6bdcde9260573a1f7af9136fd961ab99_43.png)

第二个和第三个函数分别是 `before_trading` 和 `handle_bar`。这三个函数是平台要求必须使用的，所有策略操作都需围绕它们实现。

![](img/6bdcde9260573a1f7af9136fd961ab99_45.png)

![](img/6bdcde9260573a1f7af9136fd961ab99_47.png)

![](img/6bdcde9260573a1f7af9136fd961ab99_13.png)

![](img/6bdcde9260573a1f7af9136fd961ab99_49.png)

上一节我们介绍了平台的界面，本节中我们来看看这三个核心函数的具体含义和作用。

![](img/6bdcde9260573a1f7af9136fd961ab99_51.png)

![](img/6bdcde9260573a1f7af9136fd961ab99_53.png)

### 初始化函数：init

![](img/6bdcde9260573a1f7af9136fd961ab99_55.png)

![](img/6bdcde9260573a1f7af9136fd961ab99_57.png)

第一个函数是 `init`，其作用是进行初始化。

![](img/6bdcde9260573a1f7af9136fd961ab99_59.png)

![](img/6bdcde9260573a1f7af9136fd961ab99_15.png)

![](img/6bdcde9260573a1f7af9136fd961ab99_61.png)

![](img/6bdcde9260573a1f7af9136fd961ab99_63.png)

在Python中，类有一个构造函数。构造函数通常用于接收参数并进行赋值等基本操作。

![](img/6bdcde9260573a1f7af9136fd961ab99_65.png)

![](img/6bdcde9260573a1f7af9136fd961ab99_17.png)

![](img/6bdcde9260573a1f7af9136fd961ab99_67.png)

`init` 函数就类似于Python类中的构造函数。

![](img/6bdcde9260573a1f7af9136fd961ab99_69.png)

![](img/6bdcde9260573a1f7af9136fd961ab99_71.png)

![](img/6bdcde9260573a1f7af9136fd961ab99_19.png)

![](img/6bdcde9260573a1f7af9136fd961ab99_73.png)

在Python中实例化一个对象时，会首先执行其构造函数。这里同理，当执行策略时，`init` 函数会最先被调用。

![](img/6bdcde9260573a1f7af9136fd961ab99_75.png)

![](img/6bdcde9260573a1f7af9136fd961ab99_77.png)

![](img/6bdcde9260573a1f7af9136fd961ab99_21.png)

![](img/6bdcde9260573a1f7af9136fd961ab99_79.png)

![](img/6bdcde9260573a1f7af9136fd961ab99_23.png)

![](img/6bdcde9260573a1f7af9136fd961ab99_81.png)

![](img/6bdcde9260573a1f7af9136fd961ab99_83.png)

![](img/6bdcde9260573a1f7af9136fd961ab99_25.png)

![](img/6bdcde9260573a1f7af9136fd961ab99_85.png)

### 上下文参数：context

![](img/6bdcde9260573a1f7af9136fd961ab99_87.png)

![](img/6bdcde9260573a1f7af9136fd961ab99_89.png)

观察三个函数，会发现它们都有一个共同的参数 `context`。

![](img/6bdcde9260573a1f7af9136fd961ab99_91.png)

![](img/6bdcde9260573a1f7af9136fd961ab99_27.png)

![](img/6bdcde9260573a1f7af9136fd961ab99_93.png)

![](img/6bdcde9260573a1f7af9136fd961ab99_95.png)

`context` 是一个全局上下文对象，用于在不同函数间传递数据。例如，在 `init` 函数中初始化股票代码或获取数据后，可以存入 `context`。

![](img/6bdcde9260573a1f7af9136fd961ab99_97.png)

![](img/6bdcde9260573a1f7af9136fd961ab99_99.png)

![](img/6bdcde9260573a1f7af9136fd961ab99_29.png)

![](img/6bdcde9260573a1f7af9136fd961ab99_101.png)

在 `before_trading` 函数中，若需要处理之前指定的股票或数据，可以直接从传入的 `context` 参数中获取。

![](img/6bdcde9260573a1f7af9136fd961ab99_103.png)

![](img/6bdcde9260573a1f7af9136fd961ab99_105.png)

![](img/6bdcde9260573a1f7af9136fd961ab99_31.png)

![](img/6bdcde9260573a1f7af9136fd961ab99_107.png)

`context` 帮助我们在不同算法步骤间传递信息和状态。例如，在 `init` 中设置 `context.s1 = ‘000001.XSHE‘`，在 `before_trading` 中就可以通过 `context.s1` 来访问。

![](img/6bdcde9260573a1f7af9136fd961ab99_109.png)

![](img/6bdcde9260573a1f7af9136fd961ab99_33.png)

![](img/6bdcde9260573a1f7af9136fd961ab99_111.png)

![](img/6bdcde9260573a1f7af9136fd961ab99_113.png)

### 每日执行函数：before_trading 与 handle_bar

![](img/6bdcde9260573a1f7af9136fd961ab99_115.png)

![](img/6bdcde9260573a1f7af9136fd961ab99_117.png)

`init` 函数只在策略开始时执行一次。而 `before_trading` 和 `handle_bar` 函数则会多次执行。

![](img/6bdcde9260573a1f7af9136fd961ab99_119.png)

![](img/6bdcde9260573a1f7af9136fd961ab99_35.png)

![](img/6bdcde9260573a1f7af9136fd961ab99_121.png)

举个例子，在 `init` 函数中选定一只股票后，策略需要每天对这只股票进行操作，例如判断每日收盘价以决定买卖。

![](img/6bdcde9260573a1f7af9136fd961ab99_123.png)

![](img/6bdcde9260573a1f7af9136fd961ab99_37.png)

![](img/6bdcde9260573a1f7af9136fd961ab99_125.png)

这种每日重复的操作就写在 `before_trading` 和 `handle_bar` 函数中。

![](img/6bdcde9260573a1f7af9136fd961ab99_127.png)

![](img/6bdcde9260573a1f7af9136fd961ab99_129.png)

![](img/6bdcde9260573a1f7af9136fd961ab99_39.png)

![](img/6bdcde9260573a1f7af9136fd961ab99_131.png)

`before_trading` 函数在每个交易日开始前（交易前）被调用，可用于数据预处理。

![](img/6bdcde9260573a1f7af9136fd961ab99_133.png)

![](img/6bdcde9260573a1f7af9136fd961ab99_135.png)

![](img/6bdcde9260573a1f7af9136fd961ab99_41.png)

![](img/6bdcde9260573a1f7af9136fd961ab99_137.png)

`handle_bar` 函数在每个交易时间单位（如每天或每分钟）被调用，是策略实际逻辑判断和执行买卖操作的地方。

![](img/6bdcde9260573a1f7af9136fd961ab99_139.png)

![](img/6bdcde9260573a1f7af9136fd961ab99_43.png)

![](img/6bdcde9260573a1f7af9136fd961ab99_141.png)

简单总结这三个函数：
*   `init`：用于初始化，只执行一次。
*   `before_trading`：用于每日交易前的预处理，每个交易日执行一次。
*   `handle_bar`：用于实现策略核心逻辑和交易指令，每个设定的时间单位（如每日）执行一次。

![](img/6bdcde9260573a1f7af9136fd961ab99_143.png)

![](img/6bdcde9260573a1f7af9136fd961ab99_145.png)

![](img/6bdcde9260573a1f7af9136fd961ab99_45.png)

![](img/6bdcde9260573a1f7af9136fd961ab99_147.png)

## 回测参数配置

![](img/6bdcde9260573a1f7af9136fd961ab99_149.png)

![](img/6bdcde9260573a1f7af9136fd961ab99_151.png)

了解了核心函数后，我们还需要配置策略回测的参数，这些参数决定了策略在何种条件下运行。参数配置区域在界面右侧。

![](img/6bdcde9260573a1f7af9136fd961ab99_153.png)

![](img/6bdcde9260573a1f7af9136fd961ab99_155.png)

![](img/6bdcde9260573a1f7af9136fd961ab99_47.png)

![](img/6bdcde9260573a1f7af9136fd961ab99_157.png)

以下是需要配置的主要参数列表：

![](img/6bdcde9260573a1f7af9136fd961ab99_159.png)

![](img/6bdcde9260573a1f7af9136fd961ab99_161.png)

1.  **起止日期**：策略回测的时间范围，需要指定开始日期和结束日期。这决定了使用哪一段历史数据进行回测。
    ![](img/6bdcde9260573a1f7af9136fd961ab99_49.png)

![](img/6bdcde9260573a1f7af9136fd961ab99_163.png)

2.  **初始资金**：策略开始运行时拥有的资金量，例如 `100000` 代表十万元。这是计算收益率等指标的基础。
    ![](img/6bdcde9260573a1f7af9136fd961ab99_51.png)

![](img/6bdcde9260573a1f7af9136fd961ab99_165.png)

![](img/6bdcde9260573a1f7af9136fd961ab99_167.png)

3.  **频率**：设定 `handle_bar` 函数的执行频率。可选择“每日”或“每分钟”。这决定了策略是基于日线数据还是分钟线数据进行判断和交易。
    ![](img/6bdcde9260573a1f7af9136fd961ab99_53.png)

![](img/6bdcde9260573a1f7af9136fd961ab99_169.png)

## Python库与平台API的结合

![](img/6bdcde9260573a1f7af9136fd961ab99_171.png)

![](img/6bdcde9260573a1f7af9136fd961ab99_173.png)

在策略代码中，不仅可以调用Ricequant平台提供的API，还可以导入并使用常用的Python第三方库。

![](img/6bdcde9260573a1f7af9136fd961ab99_175.png)

平台说明指出，可以自行导入如 `pandas` 或 `numpy` 等库。

![](img/6bdcde9260573a1f7af9136fd961ab99_177.png)

![](img/6bdcde9260573a1f7af9136fd961ab99_179.png)

```python
import pandas as pd
import numpy as np
```

![](img/6bdcde9260573a1f7af9136fd961ab99_181.png)

平台API返回的数据格式（如DataFrame或ndarray）与这些库的数据结构兼容，因此可以无缝结合使用。例如，你可以用平台API获取数据，再用pandas进行分析，或用scikit-learn进行机器学习建模。

![](img/6bdcde9260573a1f7af9136fd961ab99_183.png)

![](img/6bdcde9260573a1f7af9136fd961ab99_185.png)

![](img/6bdcde9260573a1f7af9136fd961ab99_55.png)

![](img/6bdcde9260573a1f7af9136fd961ab99_187.png)

## 总结

![](img/6bdcde9260573a1f7af9136fd961ab99_189.png)

![](img/6bdcde9260573a1f7af9136fd961ab99_191.png)

本节课中我们一起学习了Ricequant量化交易平台的基础知识。我们了解了平台的界面布局，掌握了策略代码必须围绕的三个核心函数：**`init`**、**`before_trading`** 和 **`handle_bar`**，并理解了它们各自的作用和执行时机。同时，我们学习了如何配置回测的关键参数，包括起止日期、初始资金和运行频率。最后，我们知道了在平台上可以灵活结合使用Python生态库（如pandas, numpy）和平台原生API来构建策略。这些是开始使用Ricequant进行量化策略编写和回测的第一步。