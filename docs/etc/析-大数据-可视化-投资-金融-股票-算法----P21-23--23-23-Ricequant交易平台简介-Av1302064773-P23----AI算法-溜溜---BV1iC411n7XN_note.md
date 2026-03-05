# 量化交易与Python金融分析实战：P23：Ricequant交易平台简介 🚀

在本节课中，我们将学习如何使用Ricequant量化交易平台。我们将了解平台的基本界面、核心功能函数以及如何配置策略参数，为后续编写和回测交易策略打下基础。

## 平台界面与策略示例

首先，我们演示量化平台的使用方法。点击“量化平台”，然后选择“免费使用”。

![](img/f40d40190bef98a328cb7dc414b82a44_1.png)

![](img/f40d40190bef98a328cb7dc414b82a44_1.png)

进入平台后，可以看到一些预设的策略。这些是平台提供的简单示例，方便用户熟悉策略的编写方式。

![](img/f40d40190bef98a328cb7dc414b82a44_3.png)

![](img/f40d40190bef98a328cb7dc414b82a44_3.png)

这些策略展示了基本的编写方法。这里有一个默认的“第一个入门策略”，我们点开查看。

![](img/f40d40190bef98a328cb7dc414b82a44_5.png)

![](img/f40d40190bef98a328cb7dc414b82a44_5.png)

打开后，界面类似于Jupyter Notebook，可以编写Python代码，但平台对代码结构有特定要求。

![](img/f40d40190bef98a328cb7dc414b82a44_7.png)

![](img/f40d40190bef98a328cb7dc414b82a44_7.png)

![](img/f40d40190bef98a328cb7dc414b82a44_9.png)

## 核心功能函数

![](img/f40d40190bef98a328cb7dc414b82a44_11.png)

所有操作都需要围绕三个核心函数来实现。以下是这三个函数的列表：

![](img/f40d40190bef98a328cb7dc414b82a44_9.png)

可以看到，代码中包含三个函数。这三个函数不是由用户随意定义的，而是平台要求必须使用的。所有操作都需要围绕这三个函数逐步实现。

![](img/f40d40190bef98a328cb7dc414b82a44_13.png)

![](img/f40d40190bef98a328cb7dc414b82a44_11.png)

![](img/f40d40190bef98a328cb7dc414b82a44_15.png)

![](img/f40d40190bef98a328cb7dc414b82a44_13.png)

下面，我们解释这三个函数的具体含义。

### 1. 初始化函数 `init`

![](img/f40d40190bef98a328cb7dc414b82a44_17.png)

首先来看第一个函数。

![](img/f40d40190bef98a328cb7dc414b82a44_19.png)

![](img/f40d40190bef98a328cb7dc414b82a44_15.png)

第一个函数是 `init`，用于初始化。在Python中，类可以定义构造函数。构造函数通常接收参数，并对这些参数进行赋值或其他基本操作。`init` 函数就相当于Python中的构造函数。

![](img/f40d40190bef98a328cb7dc414b82a44_21.png)

![](img/f40d40190bef98a328cb7dc414b82a44_17.png)

![](img/f40d40190bef98a328cb7dc414b82a44_19.png)

![](img/f40d40190bef98a328cb7dc414b82a44_23.png)

在Python中实例化一个对象时，会先执行其构造函数。这里同理，无论后续操作多么复杂，当开始执行策略时，`init` 函数会首先被调用。

![](img/f40d40190bef98a328cb7dc414b82a44_25.png)

![](img/f40d40190bef98a328cb7dc414b82a44_21.png)

![](img/f40d40190bef98a328cb7dc414b82a44_27.png)

![](img/f40d40190bef98a328cb7dc414b82a44_23.png)

![](img/f40d40190bef98a328cb7dc414b82a44_25.png)

![](img/f40d40190bef98a328cb7dc414b82a44_29.png)

![](img/f40d40190bef98a328cb7dc414b82a44_27.png)

![](img/f40d40190bef98a328cb7dc414b82a44_31.png)

![](img/f40d40190bef98a328cb7dc414b82a44_29.png)

### 参数 `context` 的作用

函数调用时有一个参数 `context`。观察三个函数，发现它们都有一个共同的参数 `context`。

![](img/f40d40190bef98a328cb7dc414b82a44_33.png)

![](img/f40d40190bef98a328cb7dc414b82a44_31.png)

`context` 表示一个全局上下文对象。例如，在初始化阶段创建的对象、指定的股票代码或获取的股票数据，都可以存入这个全局对象中。

![](img/f40d40190bef98a328cb7dc414b82a44_35.png)

![](img/f40d40190bef98a328cb7dc414b82a44_33.png)

![](img/f40d40190bef98a328cb7dc414b82a44_37.png)

在后续的预处理或实际计算阶段，不同的方法之间需要进行数据传递。这时，就可以通过 `context` 参数来传递这些信息。

![](img/f40d40190bef98a328cb7dc414b82a44_39.png)

![](img/f40d40190bef98a328cb7dc414b82a44_35.png)

例如，在初始化阶段，可以对股票对象进行操作，指定股票代码或获取股票数据，并将这些信息存入 `context`。

![](img/f40d40190bef98a328cb7dc414b82a44_41.png)

![](img/f40d40190bef98a328cb7dc414b82a44_37.png)

![](img/f40d40190bef98a328cb7dc414b82a44_43.png)

![](img/f40d40190bef98a328cb7dc414b82a44_39.png)

![](img/f40d40190bef98a328cb7dc414b82a44_45.png)

![](img/f40d40190bef98a328cb7dc414b82a44_41.png)

在预处理阶段，若想处理之前指定的股票或数据，由于它们已存入 `context`，只需将 `context` 参数传入即可。

![](img/f40d40190bef98a328cb7dc414b82a44_47.png)

![](img/f40d40190bef98a328cb7dc414b82a44_43.png)

![](img/f40d40190bef98a328cb7dc414b82a44_49.png)

![](img/f40d40190bef98a328cb7dc414b82a44_45.png)

![](img/f40d40190bef98a328cb7dc414b82a44_51.png)

![](img/f40d40190bef98a328cb7dc414b82a44_47.png)

![](img/f40d40190bef98a328cb7dc414b82a44_49.png)

![](img/f40d40190bef98a328cb7dc414b82a44_53.png)

这意味着函数之间可以通过 `context` 参数相互调用和传递数据，它帮助我们在不同算法策略步骤间传递信息。

![](img/f40d40190bef98a328cb7dc414b82a44_55.png)

![](img/f40d40190bef98a328cb7dc414b82a44_51.png)

例如，在代码中写 `context.s1 = ‘000001.XSHE’`。

![](img/f40d40190bef98a328cb7dc414b82a44_57.png)

![](img/f40d40190bef98a328cb7dc414b82a44_53.png)

![](img/f40d40190bef98a328cb7dc414b82a44_59.png)

如果在预处理阶段也想使用这个变量，可以直接通过传入的 `context` 参数来获取，使用 `context.s1` 即可。

![](img/f40d40190bef98a328cb7dc414b82a44_55.png)

![](img/f40d40190bef98a328cb7dc414b82a44_57.png)

![](img/f40d40190bef98a328cb7dc414b82a44_59.png)

![](img/f40d40190bef98a328cb7dc414b82a44_61.png)

关键是为 `context` 的属性指定好变量名，调用时使用 `context.变量名` 即可。`context` 主要用于数据传递。

![](img/f40d40190bef98a328cb7dc414b82a44_63.png)

![](img/f40d40190bef98a328cb7dc414b82a44_61.png)

至此，我们介绍了第一个函数 `init`，它会在策略开始时首先执行一次。

![](img/f40d40190bef98a328cb7dc414b82a44_65.png)

![](img/f40d40190bef98a328cb7dc414b82a44_63.png)

![](img/f40d40190bef98a328cb7dc414b82a44_67.png)

### 2. 每日执行函数

![](img/f40d40190bef98a328cb7dc414b82a44_69.png)

接下来看下面两个函数。

![](img/f40d40190bef98a328cb7dc414b82a44_65.png)

![](img/f40d40190bef98a328cb7dc414b82a44_71.png)

`init` 函数只在开始时执行一次。而下面两个函数会被执行很多次。

![](img/f40d40190bef98a328cb7dc414b82a44_67.png)

![](img/f40d40190bef98a328cb7dc414b82a44_73.png)

![](img/f40d40190bef98a328cb7dc414b82a44_69.png)

举例说明：在 `init` 函数（蓝色框）中，我们选择了一只股票。

![](img/f40d40190bef98a328cb7dc414b82a44_75.png)

![](img/f40d40190bef98a328cb7dc414b82a44_71.png)

选择完成后，这只股票就固定下来，后续不再更改。

![](img/f40d40190bef98a328cb7dc414b82a44_77.png)

![](img/f40d40190bef98a328cb7dc414b82a44_73.png)

![](img/f40d40190bef98a328cb7dc414b82a44_79.png)

在红色框的函数中，策略需要对这只股票进行操作。通常，我们需要知道股票每天的收盘价，并每天进行判断，决定是买入、卖出还是其他操作。

![](img/f40d40190bef98a328cb7dc414b82a44_81.png)

![](img/f40d40190bef98a328cb7dc414b82a44_75.png)

![](img/f40d40190bef98a328cb7dc414b82a44_77.png)

![](img/f40d40190bef98a328cb7dc414b82a44_83.png)

![](img/f40d40190bef98a328cb7dc414b82a44_79.png)

![](img/f40d40190bef98a328cb7dc414b82a44_85.png)

因此，这些每天都需要重复执行的操作，就写在红色框的函数里。

![](img/f40d40190bef98a328cb7dc414b82a44_87.png)

![](img/f40d40190bef98a328cb7dc414b82a44_81.png)

红色框内有两个函数：
1.  `before_trading`：在每个交易日开始前执行。
2.  `handle_bar`：每个交易日（或每分钟）都会执行。

![](img/f40d40190bef98a328cb7dc414b82a44_89.png)

![](img/f40d40190bef98a328cb7dc414b82a44_83.png)

![](img/f40d40190bef98a328cb7dc414b82a44_91.png)

![](img/f40d40190bef98a328cb7dc414b82a44_85.png)

![](img/f40d40190bef98a328cb7dc414b82a44_93.png)

![](img/f40d40190bef98a328cb7dc414b82a44_87.png)

`before_trading` 函数可以理解为：每天数据到来后，在实际买卖操作之前，如果你想对数据进行一些预处理，可以写在这个函数里。

![](img/f40d40190bef98a328cb7dc414b82a44_95.png)

![](img/f40d40190bef98a328cb7dc414b82a44_89.png)

![](img/f40d40190bef98a328cb7dc414b82a44_91.png)

![](img/f40d40190bef98a328cb7dc414b82a44_97.png)

![](img/f40d40190bef98a328cb7dc414b82a44_93.png)

![](img/f40d40190bef98a328cb7dc414b82a44_99.png)

它相当于在交易执行前先运行一次，进行数据预处理。

![](img/f40d40190bef98a328cb7dc414b82a44_95.png)

![](img/f40d40190bef98a328cb7dc414b82a44_101.png)

而 `handle_bar` 函数则是实际执行策略逻辑的地方。在这里，我们基于策略做出判断，并执行下单、买卖等后续操作。

![](img/f40d40190bef98a328cb7dc414b82a44_103.png)

![](img/f40d40190bef98a328cb7dc414b82a44_97.png)

![](img/f40d40190bef98a328cb7dc414b82a44_99.png)

![](img/f40d40190bef98a328cb7dc414b82a44_105.png)

总结一下：
*   `before_trading` 是每日的预处理函数，注意它也是每天执行。
*   `handle_bar` 是算法或策略的实际逻辑所在，具体的判断和操作都在 `handle_bar` 中执行。

![](img/f40d40190bef98a328cb7dc414b82a44_107.png)

![](img/f40d40190bef98a328cb7dc414b82a44_101.png)

![](img/f40d40190bef98a328cb7dc414b82a44_109.png)

![](img/f40d40190bef98a328cb7dc414b82a44_103.png)

![](img/f40d40190bef98a328cb7dc414b82a44_105.png)

![](img/f40d40190bef98a328cb7dc414b82a44_111.png)

![](img/f40d40190bef98a328cb7dc414b82a44_107.png)

![](img/f40d40190bef98a328cb7dc414b82a44_109.png)

![](img/f40d40190bef98a328cb7dc414b82a44_113.png)

![](img/f40d40190bef98a328cb7dc414b82a44_111.png)

简单介绍完这三个函数，我们可以初步理解：
*   `init`：用于初始化。
*   `before_trading`：用于每日预处理。
*   `handle_bar`：用于实际策略的买卖逻辑。

![](img/f40d40190bef98a328cb7dc414b82a44_113.png)

![](img/f40d40190bef98a328cb7dc414b82a44_115.png)

## 策略参数配置

![](img/f40d40190bef98a328cb7dc414b82a44_117.png)

既然提到这些函数每天都会执行，那么“每天”这个概念如何体现呢？这需要通过右侧的参数配置来实现。

![](img/f40d40190bef98a328cb7dc414b82a44_115.png)

![](img/f40d40190bef98a328cb7dc414b82a44_119.png)

右侧框架内的参数不是自动生成的，需要用户自行选择和配置。如果我们要进行回测，即检验策略在历史数据上的表现，就必须指定相关参数。

![](img/f40d40190bef98a328cb7dc414b82a44_121.png)

![](img/f40d40190bef98a328cb7dc414b82a44_117.png)

![](img/f40d40190bef98a328cb7dc414b82a44_119.png)

![](img/f40d40190bef98a328cb7dc414b82a44_123.png)

你需要告诉系统回测的时间范围，即从哪一天开始到哪一天结束。

![](img/f40d40190bef98a328cb7dc414b82a44_125.png)

![](img/f40d40190bef98a328cb7dc414b82a44_121.png)

![](img/f40d40190bef98a328cb7dc414b82a44_123.png)

![](img/f40d40190bef98a328cb7dc414b82a44_127.png)

因此，需要设置起始日期和终止日期。

![](img/f40d40190bef98a328cb7dc414b82a44_125.png)

![](img/f40d40190bef98a328cb7dc414b82a44_129.png)

![](img/f40d40190bef98a328cb7dc414b82a44_127.png)

![](img/f40d40190bef98a328cb7dc414b82a44_131.png)

另一个重要参数是初始资金。执行策略时，你手中有初始资金，通过买卖交易，经过一段时间后，查看回测收益率等指标。这个参数就是策略准备使用的初始资金数额。

![](img/f40d40190bef98a328cb7dc414b82a44_129.png)

![](img/f40d40190bef98a328cb7dc414b82a44_133.png)

![](img/f40d40190bef98a328cb7dc414b82a44_131.png)

![](img/f40d40190bef98a328cb7dc414b82a44_133.png)

![](img/f40d40190bef98a328cb7dc414b82a44_135.png)

注意，初始资金需要用户根据策略自行设定。

![](img/f40d40190bef98a328cb7dc414b82a44_137.png)

![](img/f40d40190bef98a328cb7dc414b82a44_135.png)

![](img/f40d40190bef98a328cb7dc414b82a44_137.png)

![](img/f40d40190bef98a328cb7dc414b82a44_139.png)

第三个参数是频率，可选择“每日”或“每分钟”。“每日”表示每天执行一次 `handle_bar`；“每分钟”则表示每分钟执行一次。这需要根据策略需求来指定。在历史回测中，我们通常按天执行。

![](img/f40d40190bef98a328cb7dc414b82a44_141.png)

![](img/f40d40190bef98a328cb7dc414b82a44_139.png)

![](img/f40d40190bef98a328cb7dc414b82a44_141.png)

![](img/f40d40190bef98a328cb7dc414b82a44_143.png)

实际任务中，可根据需求选择，但平台只提供这两种频率。如果想进行其他周期的统计，可以在代码中实现。

![](img/f40d40190bef98a328cb7dc414b82a44_143.png)

![](img/f40d40190bef98a328cb7dc414b82a44_145.png)

平台支持导入常用的Python库，例如 `pandas` 和 `numpy`。

![](img/f40d40190bef98a328cb7dc414b82a44_147.png)

![](img/f40d40190bef98a328cb7dc414b82a44_145.png)

代码中已说明，可以自行导入 `pandas`、`numpy` 等库。我们之前讲过如何使用这些库进行计算。

![](img/f40d40190bef98a328cb7dc414b82a44_149.png)

![](img/f40d40190bef98a328cb7dc414b82a44_147.png)

平台提供的API接口，其返回值也是 `DataFrame` 或 `ndarray` 格式。

![](img/f40d40190bef98a328cb7dc414b82a44_151.png)

![](img/f40d40190bef98a328cb7dc414b82a44_149.png)

![](img/f40d40190bef98a328cb7dc414b82a44_153.png)

![](img/f40d40190bef98a328cb7dc414b82a44_151.png)

![](img/f40d40190bef98a328cb7dc414b82a44_153.png)

![](img/f40d40190bef98a328cb7dc414b82a44_155.png)

因此，平台API可以与 `pandas`、`numpy` 甚至 `scikit-learn` 等库结合使用。并非只能使用平台API，而是可以同时使用Python常用库和平台API，关键在于将它们结合起来。

![](img/f40d40190bef98a328cb7dc414b82a44_155.png)

![](img/f40d40190bef98a328cb7dc414b82a44_157.png)

![](img/f40d40190bef98a328cb7dc414b82a44_157.png)

![](img/f40d40190bef98a328cb7dc414b82a44_159.png)

![](img/f40d40190bef98a328cb7dc414b82a44_159.png)

![](img/f40d40190bef98a328cb7dc414b82a44_161.png)

![](img/f40d40190bef98a328cb7dc414b82a44_161.png)

## 本节总结

本节课中，我们一起学习了Ricequant量化平台的基本使用。我们介绍了三个核心函数 `init`、`before_trading` 和 `handle_bar` 的作用，并讲解了如何配置右侧的策略参数，如回测时间、初始资金和运行频率。理解这些是编写和回测交易策略的基础。

![](img/f40d40190bef98a328cb7dc414b82a44_163.png)

![](img/f40d40190bef98a328cb7dc414b82a44_163.png)

![](img/f40d40190bef98a328cb7dc414b82a44_165.png)

![](img/f40d40190bef98a328cb7dc414b82a44_165.png)

平台输出的指标，如策略收益和风险等，我们之前已经介绍过。

![](img/f40d40190bef98a328cb7dc414b82a44_167.png)

![](img/f40d40190bef98a328cb7dc414b82a44_167.png)