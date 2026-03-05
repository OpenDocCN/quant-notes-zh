# Python量化交易：P23：Ricequant交易平台简介 🚀

![](img/9ea70218ce8c3b5a83e1d4486c2f9e5c_1.png)

在本节课中，我们将要学习Ricequant量化交易平台的基本使用方法。我们将了解平台的核心界面、策略编写的基本框架以及关键参数的配置，为后续编写和回测量化策略打下基础。

![](img/9ea70218ce8c3b5a83e1d4486c2f9e5c_3.png)

## 平台界面与策略示例

![](img/9ea70218ce8c3b5a83e1d4486c2f9e5c_5.png)

![](img/9ea70218ce8c3b5a83e1d4486c2f9e5c_7.png)

首先，我们演示Ricequant量化交易平台的使用方法。点击“量化平台”，然后选择“免费使用”进入平台。

![](img/9ea70218ce8c3b5a83e1d4486c2f9e5c_9.png)

![](img/9ea70218ce8c3b5a83e1d4486c2f9e5c_11.png)

![](img/9ea70218ce8c3b5a83e1d4486c2f9e5c_1.png)

![](img/9ea70218ce8c3b5a83e1d4486c2f9e5c_13.png)

进入平台后，会看到一些预设的策略。这些策略是平台提供的简单示例，方便用户熟悉平台的策略编写方式。

![](img/9ea70218ce8c3b5a83e1d4486c2f9e5c_15.png)

![](img/9ea70218ce8c3b5a83e1d4486c2f9e5c_17.png)

![](img/9ea70218ce8c3b5a83e1d4486c2f9e5c_3.png)

![](img/9ea70218ce8c3b5a83e1d4486c2f9e5c_19.png)

![](img/9ea70218ce8c3b5a83e1d4486c2f9e5c_21.png)

这些策略展示了基本的编写方法。平台默认会带有一个策略名称，我们可以点开第一个名为“入门策略”的示例进行查看。

![](img/9ea70218ce8c3b5a83e1d4486c2f9e5c_23.png)

![](img/9ea70218ce8c3b5a83e1d4486c2f9e5c_25.png)

![](img/9ea70218ce8c3b5a83e1d4486c2f9e5c_5.png)

![](img/9ea70218ce8c3b5a83e1d4486c2f9e5c_27.png)

![](img/9ea70218ce8c3b5a83e1d4486c2f9e5c_29.png)

点开之后，界面类似于Jupyter Notebook，可以在此编写Python代码。但平台对代码结构有特定要求。

![](img/9ea70218ce8c3b5a83e1d4486c2f9e5c_31.png)

![](img/9ea70218ce8c3b5a83e1d4486c2f9e5c_33.png)

![](img/9ea70218ce8c3b5a83e1d4486c2f9e5c_7.png)

![](img/9ea70218ce8c3b5a83e1d4486c2f9e5c_35.png)

![](img/9ea70218ce8c3b5a83e1d4486c2f9e5c_37.png)

## 核心策略函数

![](img/9ea70218ce8c3b5a83e1d4486c2f9e5c_39.png)

![](img/9ea70218ce8c3b5a83e1d4486c2f9e5c_41.png)

所有操作都需要围绕平台规定的核心函数展开。以下是策略中必须包含的三个函数：

![](img/9ea70218ce8c3b5a83e1d4486c2f9e5c_43.png)

![](img/9ea70218ce8c3b5a83e1d4486c2f9e5c_9.png)

![](img/9ea70218ce8c3b5a83e1d4486c2f9e5c_45.png)

![](img/9ea70218ce8c3b5a83e1d4486c2f9e5c_47.png)

这三个函数不是由用户随意指定的，而是平台要求必须使用的。所有策略操作最少需要围绕这三个函数来实现。

![](img/9ea70218ce8c3b5a83e1d4486c2f9e5c_49.png)

![](img/9ea70218ce8c3b5a83e1d4486c2f9e5c_51.png)

![](img/9ea70218ce8c3b5a83e1d4486c2f9e5c_11.png)

![](img/9ea70218ce8c3b5a83e1d4486c2f9e5c_53.png)

![](img/9ea70218ce8c3b5a83e1d4486c2f9e5c_55.png)

接下来，我们逐一解释这三个函数的作用。

![](img/9ea70218ce8c3b5a83e1d4486c2f9e5c_57.png)

![](img/9ea70218ce8c3b5a83e1d4486c2f9e5c_59.png)

### 1. 初始化函数 `init`

![](img/9ea70218ce8c3b5a83e1d4486c2f9e5c_61.png)

![](img/9ea70218ce8c3b5a83e1d4486c2f9e5c_63.png)

第一个函数是 `init` 函数，它的作用是进行策略初始化。

![](img/9ea70218ce8c3b5a83e1d4486c2f9e5c_65.png)

![](img/9ea70218ce8c3b5a83e1d4486c2f9e5c_67.png)

![](img/9ea70218ce8c3b5a83e1d4486c2f9e5c_17.png)

![](img/9ea70218ce8c3b5a83e1d4486c2f9e5c_69.png)

在Python中，我们定义一个类时，通常会有一个构造函数。构造函数用于接收参数并进行一些初始赋值和基本操作。

![](img/9ea70218ce8c3b5a83e1d4486c2f9e5c_71.png)

![](img/9ea70218ce8c3b5a83e1d4486c2f9e5c_73.png)

![](img/9ea70218ce8c3b5a83e1d4486c2f9e5c_21.png)

![](img/9ea70218ce8c3b5a83e1d4486c2f9e5c_75.png)

`init` 函数就相当于Python类中的构造函数。在实例化一个对象时，首先会执行其构造函数。

![](img/9ea70218ce8c3b5a83e1d4486c2f9e5c_77.png)

![](img/9ea70218ce8c3b5a83e1d4486c2f9e5c_79.png)

![](img/9ea70218ce8c3b5a83e1d4486c2f9e5c_29.png)

![](img/9ea70218ce8c3b5a83e1d4486c2f9e5c_81.png)

![](img/9ea70218ce8c3b5a83e1d4486c2f9e5c_83.png)

在量化策略中也是如此。无论后续操作多么复杂，当开始执行策略时，`init` 函数会最先被调用。

![](img/9ea70218ce8c3b5a83e1d4486c2f9e5c_85.png)

![](img/9ea70218ce8c3b5a83e1d4486c2f9e5c_87.png)

![](img/9ea70218ce8c3b5a83e1d4486c2f9e5c_35.png)

![](img/9ea70218ce8c3b5a83e1d4486c2f9e5c_89.png)

![](img/9ea70218ce8c3b5a83e1d4486c2f9e5c_91.png)

### 上下文对象 `context`

![](img/9ea70218ce8c3b5a83e1d4486c2f9e5c_93.png)

观察三个函数，会发现它们都有一个共同的参数 `context`。

![](img/9ea70218ce8c3b5a83e1d4486c2f9e5c_95.png)

![](img/9ea70218ce8c3b5a83e1d4486c2f9e5c_97.png)

![](img/9ea70218ce8c3b5a83e1d4486c2f9e5c_43.png)

![](img/9ea70218ce8c3b5a83e1d4486c2f9e5c_99.png)

![](img/9ea70218ce8c3b5a83e1d4486c2f9e5c_101.png)

`context` 是一个上下文对象，用于在不同函数之间传递数据和状态。例如，在初始化函数中，我们可能设置股票代码或获取股票数据，并将这些信息存入 `context` 对象。

![](img/9ea70218ce8c3b5a83e1d4486c2f9e5c_103.png)

![](img/9ea70218ce8c3b5a83e1d4486c2f9e5c_105.png)

![](img/9ea70218ce8c3b5a83e1d4486c2f9e5c_49.png)

![](img/9ea70218ce8c3b5a83e1d4486c2f9e5c_107.png)

![](img/9ea70218ce8c3b5a83e1d4486c2f9e5c_109.png)

在后续的预处理函数或策略逻辑函数中，我们可以通过传入的 `context` 参数来访问这些预先存储好的数据。

![](img/9ea70218ce8c3b5a83e1d4486c2f9e5c_111.png)

![](img/9ea70218ce8c3b5a83e1d4486c2f9e5c_113.png)

![](img/9ea70218ce8c3b5a83e1d4486c2f9e5c_61.png)

![](img/9ea70218ce8c3b5a83e1d4486c2f9e5c_115.png)

![](img/9ea70218ce8c3b5a83e1d4486c2f9e5c_117.png)

`context` 对象帮助我们在不同的算法步骤间进行数据传递和调用。例如，在 `init` 函数中设置 `context.s1 = ‘000001.XSHE‘`，那么在 `handle_bar` 函数中就可以通过 `context.s1` 来获取这个股票代码。

![](img/9ea70218ce8c3b5a83e1d4486c2f9e5c_119.png)

![](img/9ea70218ce8c3b5a83e1d4486c2f9e5c_121.png)

![](img/9ea70218ce8c3b5a83e1d4486c2f9e5c_71.png)

![](img/9ea70218ce8c3b5a83e1d4486c2f9e5c_123.png)

![](img/9ea70218ce8c3b5a83e1d4486c2f9e5c_125.png)

### 2. 预处理函数 `before_trading` 与 3. 策略逻辑函数 `handle_bar`

![](img/9ea70218ce8c3b5a83e1d4486c2f9e5c_127.png)

初始化函数只在策略开始时执行一次。而接下来的两个函数 `before_trading` 和 `handle_bar` 会被执行很多次。

![](img/9ea70218ce8c3b5a83e1d4486c2f9e5c_129.png)

![](img/9ea70218ce8c3b5a83e1d4486c2f9e5c_131.png)

![](img/9ea70218ce8c3b5a83e1d4486c2f9e5c_83.png)

![](img/9ea70218ce8c3b5a83e1d4486c2f9e5c_133.png)

![](img/9ea70218ce8c3b5a83e1d4486c2f9e5c_135.png)

举个例子，在 `init` 函数中，我们选定了一只股票。这个选择一旦确定，在后续过程中通常不会改变。

![](img/9ea70218ce8c3b5a83e1d4486c2f9e5c_137.png)

![](img/9ea70218ce8c3b5a83e1d4486c2f9e5c_89.png)

![](img/9ea70218ce8c3b5a83e1d4486c2f9e5c_139.png)

但我们的策略需要每天对这只股票进行操作，例如获取每日收盘价、判断买卖点等。这些每日重复的操作就写在 `before_trading` 和 `handle_bar` 函数中。

![](img/9ea70218ce8c3b5a83e1d4486c2f9e5c_141.png)

![](img/9ea70218ce8c3b5a83e1d4486c2f9e5c_143.png)

![](img/9ea70218ce8c3b5a83e1d4486c2f9e5c_103.png)

![](img/9ea70218ce8c3b5a83e1d4486c2f9e5c_145.png)

![](img/9ea70218ce8c3b5a83e1d4486c2f9e5c_147.png)

*   **`before_trading` 函数**：在每个交易日开始前、实际交易发生前被执行。可以在这里进行数据预处理工作。
*   **`handle_bar` 函数**：在每个交易日（或每分钟）都会被执行。这里是策略核心逻辑所在，包括具体的买卖判断、下单等操作。

![](img/9ea70218ce8c3b5a83e1d4486c2f9e5c_149.png)

简单总结，`init` 函数用于初始化，`before_trading` 函数用于每日预处理，`handle_bar` 函数用于执行每日的实际交易策略。

![](img/9ea70218ce8c3b5a83e1d4486c2f9e5c_151.png)

## 回测参数配置

![](img/9ea70218ce8c3b5a83e1d4486c2f9e5c_153.png)

![](img/9ea70218ce8c3b5a83e1d4486c2f9e5c_155.png)

既然策略需要每日执行，那么这个“每日”是如何体现的呢？这需要通过平台右侧的参数配置区域来设置，这些参数需要用户自己选择。

![](img/9ea70218ce8c3b5a83e1d4486c2f9e5c_157.png)

![](img/9ea70218ce8c3b5a83e1d4486c2f9e5c_159.png)

![](img/9ea70218ce8c3b5a83e1d4486c2f9e5c_137.png)

![](img/9ea70218ce8c3b5a83e1d4486c2f9e5c_161.png)

![](img/9ea70218ce8c3b5a83e1d4486c2f9e5c_163.png)

进行策略回测，即用历史数据检验策略效果，必须配置以下参数：

![](img/9ea70218ce8c3b5a83e1d4486c2f9e5c_165.png)

![](img/9ea70218ce8c3b5a83e1d4486c2f9e5c_167.png)

1.  **起止日期**：指定回测的时间范围，包括开始日期和结束日期。
2.  **初始资金**：设定策略开始运行时拥有的资金量，例如 `100000` 代表十万元。这是计算回测收益率等指标的基础。
3.  **频率**：选择策略执行的频率，可以是“每日”或“每分钟”。这决定了 `handle_bar` 函数被调用的时间间隔。本教程示例通常选择“每日”。

![](img/9ea70218ce8c3b5a83e1d4486c2f9e5c_169.png)

![](img/9ea70218ce8c3b5a83e1d4486c2f9e5c_171.png)

## 第三方库的使用

![](img/9ea70218ce8c3b5a83e1d4486c2f9e5c_173.png)

在策略代码中，不仅可以调用Ricequant平台提供的API，还可以导入并使用常用的Python数据分析库。

![](img/9ea70218ce8c3b5a83e1d4486c2f9e5c_175.png)

![](img/9ea70218ce8c3b5a83e1d4486c2f9e5c_177.png)

![](img/9ea70218ce8c3b5a83e1d4486c2f9e5c_171.png)

![](img/9ea70218ce8c3b5a83e1d4486c2f9e5c_179.png)

![](img/9ea70218ce8c3b5a83e1d4486c2f9e5c_181.png)

例如，可以导入 `pandas` 和 `numpy` 进行数据处理和计算。平台API返回的数据格式也常常是 `DataFrame` 或 `ndarray`，与这些库可以无缝结合。

![](img/9ea70218ce8c3b5a83e1d4486c2f9e5c_183.png)

![](img/9ea70218ce8c3b5a83e1d4486c2f9e5c_185.png)

![](img/9ea70218ce8c3b5a83e1d4486c2f9e5c_181.png)

![](img/9ea70218ce8c3b5a83e1d4486c2f9e5c_187.png)

![](img/9ea70218ce8c3b5a83e1d4486c2f9e5c_189.png)

这意味着，你可以灵活地将平台API与 `pandas`、`numpy` 甚至 `scikit-learn` 等机器学习库结合使用。

![](img/9ea70218ce8c3b5a83e1d4486c2f9e5c_191.png)

## 总结

![](img/9ea70218ce8c3b5a83e1d4486c2f9e5c_193.png)

![](img/9ea70218ce8c3b5a83e1d4486c2f9e5c_195.png)

本节课中，我们一起学习了Ricequant量化交易平台的基本结构。我们了解了编写策略时必须使用的三个核心函数：**`init`（初始化）、`before_trading`（盘前处理）和 `handle_bar`（策略逻辑）**，并认识了用于传递数据的 **`context`** 对象。同时，我们知道了在进行回测前，必须在平台右侧配置好**起止日期、初始资金和运行频率**等关键参数。最后，我们了解到在策略中可以自由导入和使用像 `pandas` 这样的第三方Python库，与平台API协同工作。掌握这些基础知识是开始编写和测试量化策略的第一步。