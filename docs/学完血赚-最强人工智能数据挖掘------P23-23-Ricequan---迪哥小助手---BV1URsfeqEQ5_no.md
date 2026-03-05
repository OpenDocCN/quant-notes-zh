# 人工智能数据挖掘实战：P23：Ricequant交易平台简介 🚀

![](img/bc3323c13ab8dd1d2eb030ec1419b9af_1.png)

在本节课中，我们将学习如何使用Ricequant量化交易平台。这是一个专为量化策略开发和回测设计的平台，我们将了解其核心界面、关键函数以及如何配置策略参数。

![](img/bc3323c13ab8dd1d2eb030ec1419b9af_3.png)

## 平台界面与入门策略

首先，我们演示量化平台的使用方法。点击“量化平台”，然后选择“免费使用”。

![](img/bc3323c13ab8dd1d2eb030ec1419b9af_5.png)

![](img/bc3323c13ab8dd1d2eb030ec1419b9af_1.png)

![](img/bc3323c13ab8dd1d2eb030ec1419b9af_7.png)

![](img/bc3323c13ab8dd1d2eb030ec1419b9af_9.png)

进入平台后，会看到一些预设的策略。这些是平台提供的简单示例，方便用户熟悉平台操作和策略编写的基本流程。

![](img/bc3323c13ab8dd1d2eb030ec1419b9af_11.png)

![](img/bc3323c13ab8dd1d2eb030ec1419b9af_3.png)

![](img/bc3323c13ab8dd1d2eb030ec1419b9af_13.png)

这些策略展示了基本的编写方法。我们点开第一个名为“入门策略”的示例进行查看。

![](img/bc3323c13ab8dd1d2eb030ec1419b9af_15.png)

![](img/bc3323c13ab8dd1d2eb030ec1419b9af_5.png)

![](img/bc3323c13ab8dd1d2eb030ec1419b9af_17.png)

点开后，界面类似于Jupyter Notebook，可以在此编写Python代码。但平台对代码结构有特定要求。

![](img/bc3323c13ab8dd1d2eb030ec1419b9af_19.png)

![](img/bc3323c13ab8dd1d2eb030ec1419b9af_7.png)

![](img/bc3323c13ab8dd1d2eb030ec1419b9af_21.png)

所有操作都需要围绕平台规定的几个核心函数展开。以下是这些函数的列表。

![](img/bc3323c13ab8dd1d2eb030ec1419b9af_23.png)

![](img/bc3323c13ab8dd1d2eb030ec1419b9af_9.png)

![](img/bc3323c13ab8dd1d2eb030ec1419b9af_25.png)

![](img/bc3323c13ab8dd1d2eb030ec1419b9af_27.png)

我们可以看到三个函数：`init`、`before_trading` 和 `handle_bar`。

![](img/bc3323c13ab8dd1d2eb030ec1419b9af_29.png)

![](img/bc3323c13ab8dd1d2eb030ec1419b9af_11.png)

![](img/bc3323c13ab8dd1d2eb030ec1419b9af_31.png)

这三个函数不是随意编写的，而是平台要求必须使用的。所有策略操作都需要围绕这三个函数来实现。

![](img/bc3323c13ab8dd1d2eb030ec1419b9af_33.png)

![](img/bc3323c13ab8dd1d2eb030ec1419b9af_35.png)

![](img/bc3323c13ab8dd1d2eb030ec1419b9af_13.png)

![](img/bc3323c13ab8dd1d2eb030ec1419b9af_37.png)

最少需要围绕这三个函数来逐步实现策略逻辑。

![](img/bc3323c13ab8dd1d2eb030ec1419b9af_39.png)

![](img/bc3323c13ab8dd1d2eb030ec1419b9af_15.png)

![](img/bc3323c13ab8dd1d2eb030ec1419b9af_41.png)

## 核心函数详解

![](img/bc3323c13ab8dd1d2eb030ec1419b9af_43.png)

上一节我们看到了三个核心函数，本节我们来详细解释每个函数的作用。

![](img/bc3323c13ab8dd1d2eb030ec1419b9af_45.png)

![](img/bc3323c13ab8dd1d2eb030ec1419b9af_47.png)

首先解释第一个函数 `init`。

![](img/bc3323c13ab8dd1d2eb030ec1419b9af_49.png)

![](img/bc3323c13ab8dd1d2eb030ec1419b9af_17.png)

![](img/bc3323c13ab8dd1d2eb030ec1419b9af_51.png)

`init` 函数用于初始化。

![](img/bc3323c13ab8dd1d2eb030ec1419b9af_53.png)

![](img/bc3323c13ab8dd1d2eb030ec1419b9af_19.png)

![](img/bc3323c13ab8dd1d2eb030ec1419b9af_55.png)

![](img/bc3323c13ab8dd1d2eb030ec1419b9af_57.png)

在Python中，类有一个构造函数。构造函数通常用于接收参数并进行赋值等基本操作。

![](img/bc3323c13ab8dd1d2eb030ec1419b9af_59.png)

![](img/bc3323c13ab8dd1d2eb030ec1419b9af_21.png)

![](img/bc3323c13ab8dd1d2eb030ec1419b9af_61.png)

`init` 函数就相当于Python类中的构造函数。

![](img/bc3323c13ab8dd1d2eb030ec1419b9af_63.png)

![](img/bc3323c13ab8dd1d2eb030ec1419b9af_65.png)

![](img/bc3323c13ab8dd1d2eb030ec1419b9af_23.png)

![](img/bc3323c13ab8dd1d2eb030ec1419b9af_67.png)

在Python中实例化一个对象时，会首先执行其构造函数。这里也是一样，无论后续操作多么复杂，执行时 `init` 函数会最先被调用。

![](img/bc3323c13ab8dd1d2eb030ec1419b9af_69.png)

![](img/bc3323c13ab8dd1d2eb030ec1419b9af_25.png)

![](img/bc3323c13ab8dd1d2eb030ec1419b9af_71.png)

调用 `init` 函数时，它有一个参数 `context`。

![](img/bc3323c13ab8dd1d2eb030ec1419b9af_73.png)

![](img/bc3323c13ab8dd1d2eb030ec1419b9af_27.png)

![](img/bc3323c13ab8dd1d2eb030ec1419b9af_75.png)

观察三个函数，发现它们都有一个共同的参数 `context`。

![](img/bc3323c13ab8dd1d2eb030ec1419b9af_77.png)

![](img/bc3323c13ab8dd1d2eb030ec1419b9af_29.png)

![](img/bc3323c13ab8dd1d2eb030ec1419b9af_79.png)

`context` 参数用于在不同函数间传递数据和状态。例如，在初始化时创建或指定的对象（如股票代码、数据），可以存入 `context` 这个全局对象中。

![](img/bc3323c13ab8dd1d2eb030ec1419b9af_81.png)

![](img/bc3323c13ab8dd1d2eb030ec1419b9af_31.png)

![](img/bc3323c13ab8dd1d2eb030ec1419b9af_83.png)

![](img/bc3323c13ab8dd1d2eb030ec1419b9af_85.png)

在预处理函数 `before_trading` 中，若想使用之前存入的数据，只需将 `context` 参数传递进来即可。

![](img/bc3323c13ab8dd1d2eb030ec1419b9af_87.png)

![](img/bc3323c13ab8dd1d2eb030ec1419b9af_33.png)

![](img/bc3323c13ab8dd1d2eb030ec1419b9af_89.png)

它们之间可以进行相互调用。`context` 参数帮助我们在不同算法策略步骤间传递信息。

![](img/bc3323c13ab8dd1d2eb030ec1419b9af_91.png)

![](img/bc3323c13ab8dd1d2eb030ec1419b9af_35.png)

![](img/bc3323c13ab8dd1d2eb030ec1419b9af_93.png)

例如，在 `init` 中写入 `context.s1 = ‘000001.XSHE’`。

![](img/bc3323c13ab8dd1d2eb030ec1419b9af_95.png)

![](img/bc3323c13ab8dd1d2eb030ec1419b9af_97.png)

![](img/bc3323c13ab8dd1d2eb030ec1419b9af_37.png)

![](img/bc3323c13ab8dd1d2eb030ec1419b9af_99.png)

在 `before_trading` 函数中想使用这个变量，可以直接通过 `context.s1` 来获取。

![](img/bc3323c13ab8dd1d2eb030ec1419b9af_101.png)

![](img/bc3323c13ab8dd1d2eb030ec1419b9af_39.png)

![](img/bc3323c13ab8dd1d2eb030ec1419b9af_103.png)

点号 `.` 表示访问 `context` 对象中你指定好的属性或变量。

![](img/bc3323c13ab8dd1d2eb030ec1419b9af_105.png)

![](img/bc3323c13ab8dd1d2eb030ec1419b9af_107.png)

![](img/bc3323c13ab8dd1d2eb030ec1419b9af_41.png)

![](img/bc3323c13ab8dd1d2eb030ec1419b9af_109.png)

调用时也是一样，使用点号访问即可。`context` 主要起传递作用。

![](img/bc3323c13ab8dd1d2eb030ec1419b9af_111.png)

![](img/bc3323c13ab8dd1d2eb030ec1419b9af_43.png)

![](img/bc3323c13ab8dd1d2eb030ec1419b9af_113.png)

至此，我们介绍了第一个函数 `init`，它会在策略开始时最先执行一次。

![](img/bc3323c13ab8dd1d2eb030ec1419b9af_115.png)

![](img/bc3323c13ab8dd1d2eb030ec1419b9af_117.png)

![](img/bc3323c13ab8dd1d2eb030ec1419b9af_45.png)

![](img/bc3323c13ab8dd1d2eb030ec1419b9af_119.png)

接下来看下面两个函数。

![](img/bc3323c13ab8dd1d2eb030ec1419b9af_121.png)

![](img/bc3323c13ab8dd1d2eb030ec1419b9af_47.png)

![](img/bc3323c13ab8dd1d2eb030ec1419b9af_123.png)

`init` 初始化只执行一次，而 `before_trading` 和 `handle_bar` 会被执行很多次。

![](img/bc3323c13ab8dd1d2eb030ec1419b9af_125.png)

![](img/bc3323c13ab8dd1d2eb030ec1419b9af_127.png)

![](img/bc3323c13ab8dd1d2eb030ec1419b9af_49.png)

![](img/bc3323c13ab8dd1d2eb030ec1419b9af_129.png)

举个例子，在 `init` 函数中，我们选择了一只股票，这个选择一旦完成就固定下来。

![](img/bc3323c13ab8dd1d2eb030ec1419b9af_131.png)

![](img/bc3323c13ab8dd1d2eb030ec1419b9af_51.png)

![](img/bc3323c13ab8dd1d2eb030ec1419b9af_133.png)

但在策略中，我们需要每天对这只股票进行操作，例如获取每日收盘价、判断买卖点等。这些每日重复的操作就写在 `handle_bar` 函数中。

![](img/bc3323c13ab8dd1d2eb030ec1419b9af_53.png)

![](img/bc3323c13ab8dd1d2eb030ec1419b9af_135.png)

`before_trading` 函数在每天交易开始前被执行，用于数据预处理。

![](img/bc3323c13ab8dd1d2eb030ec1419b9af_137.png)

![](img/bc3323c13ab8dd1d2eb030ec1419b9af_139.png)

![](img/bc3323c13ab8dd1d2eb030ec1419b9af_55.png)

![](img/bc3323c13ab8dd1d2eb030ec1419b9af_141.png)

`handle_bar` 函数则是每天（或每分钟）都会执行，是策略实际逻辑和买卖判断发生的地方。

![](img/bc3323c13ab8dd1d2eb030ec1419b9af_57.png)

![](img/bc3323c13ab8dd1d2eb030ec1419b9af_143.png)

![](img/bc3323c13ab8dd1d2eb030ec1419b9af_145.png)

简单总结三个函数：
*   `init`：初始化，只执行一次。
*   `before_trading`：交易日开始前的预处理，每个交易日执行一次。
*   `handle_bar`：策略核心逻辑，根据设定的频率（每日/每分钟）重复执行。

![](img/bc3323c13ab8dd1d2eb030ec1419b9af_147.png)

![](img/bc3323c13ab8dd1d2eb030ec1419b9af_59.png)

## 策略参数配置

![](img/bc3323c13ab8dd1d2eb030ec1419b9af_149.png)

上一节我们介绍了三个核心函数，本节我们来看看如何配置策略的运行参数。“每天”这个频率是如何体现的呢？这需要通过右侧的参数面板来设置。

![](img/bc3323c13ab8dd1d2eb030ec1419b9af_151.png)

![](img/bc3323c13ab8dd1d2eb030ec1419b9af_153.png)

![](img/bc3323c13ab8dd1d2eb030ec1419b9af_61.png)

![](img/bc3323c13ab8dd1d2eb030ec1419b9af_155.png)

右侧面板的参数需要用户自行选择和配置。因为我们要进行回测，即检验策略在历史数据上的表现，所以必须指定测试的股票、时间范围等。

![](img/bc3323c13ab8dd1d2eb030ec1419b9af_157.png)

![](img/bc3323c13ab8dd1d2eb030ec1419b9af_63.png)

![](img/bc3323c13ab8dd1d2eb030ec1419b9af_159.png)

以下是需要配置的关键参数列表：

![](img/bc3323c13ab8dd1d2eb030ec1419b9af_161.png)

![](img/bc3323c13ab8dd1d2eb030ec1419b9af_163.png)

![](img/bc3323c13ab8dd1d2eb030ec1419b9af_65.png)

![](img/bc3323c13ab8dd1d2eb030ec1419b9af_165.png)

1.  **起止日期**：回测的时间范围，包括开始日期和结束日期。
2.  **初始资金**：策略开始运行时拥有的资金量，例如 `100000` 代表10万元。这是计算回测收益率等指标的起点。
3.  **频率**：选择策略执行的频率是“每日”还是“每分钟”。每日表示 `handle_bar` 函数每天执行一次；每分钟则表示每分钟执行一次。本教程示例按每日执行。

![](img/bc3323c13ab8dd1d2eb030ec1419b9af_167.png)

![](img/bc3323c13ab8dd1d2eb030ec1419b9af_67.png)

![](img/bc3323c13ab8dd1d2eb030ec1419b9af_169.png)

## 平台扩展性与API

![](img/bc3323c13ab8dd1d2eb030ec1419b9af_171.png)

在策略中，你不仅可以调用Ricequant平台提供的API，还可以使用常用的Python库。

![](img/bc3323c13ab8dd1d2eb030ec1419b9af_173.png)

![](img/bc3323c13ab8dd1d2eb030ec1419b9af_175.png)

![](img/bc3323c13ab8dd1d2eb030ec1419b9af_69.png)

![](img/bc3323c13ab8dd1d2eb030ec1419b9af_177.png)

例如，你可以通过 `import pandas as pd` 或 `import numpy as np` 导入Pandas和NumPy库进行计算。

![](img/bc3323c13ab8dd1d2eb030ec1419b9af_179.png)

![](img/bc3323c13ab8dd1d2eb030ec1419b9af_71.png)

![](img/bc3323c13ab8dd1d2eb030ec1419b9af_181.png)

Ricequant平台自身的API返回的数据格式，如DataFrame或ndarray，也与这些库兼容。

![](img/bc3323c13ab8dd1d2eb030ec1419b9af_183.png)

![](img/bc3323c13ab8dd1d2eb030ec1419b9af_73.png)

![](img/bc3323c13ab8dd1d2eb030ec1419b9af_185.png)

![](img/bc3323c13ab8dd1d2eb030ec1419b9af_187.png)

因此，你可以将平台API与Pandas、NumPy乃至Scikit-learn等机器学习库结合使用，构建复杂的策略。

![](img/bc3323c13ab8dd1d2eb030ec1419b9af_75.png)

![](img/bc3323c13ab8dd1d2eb030ec1419b9af_189.png)

![](img/bc3323c13ab8dd1d2eb030ec1419b9af_191.png)

## 总结

![](img/bc3323c13ab8dd1d2eb030ec1419b9af_193.png)

本节课我们一起学习了Ricequant量化交易平台的基础知识。我们了解了平台的界面和入门策略，重点掌握了三个核心函数 **`init`**、**`before_trading`** 和 **`handle_bar`** 的作用与执行顺序。同时，我们也学会了如何在右侧面板配置策略的回测参数，包括起止日期、初始资金和运行频率。最后，我们知道了该平台支持与主流Python数据分析库结合使用，功能强大且灵活。