# Python金融分析+量化交易：P23：Ricequant交易平台简介 🚀

在本节课中，我们将学习如何使用Ricequant量化交易平台。我们将了解平台的基本界面、核心的编程框架以及如何配置策略参数，为后续编写和回测量化策略打下基础。

![](img/c90f7c2583d4503b751fe6bacc8cb63a_1.png)

## 平台入口与策略示例

![](img/c90f7c2583d4503b751fe6bacc8cb63a_3.png)

首先，我们演示如何使用这个量化平台。点击“量化平台”，然后选择“免费使用”。

![](img/c90f7c2583d4503b751fe6bacc8cb63a_1.png)

![](img/c90f7c2583d4503b751fe6bacc8cb63a_5.png)

进入平台后，会看到一些预设的策略。这些是平台提供的简单示例，方便用户熟悉环境。

![](img/c90f7c2583d4503b751fe6bacc8cb63a_7.png)

![](img/c90f7c2583d4503b751fe6bacc8cb63a_3.png)

![](img/c90f7c2583d4503b751fe6bacc8cb63a_9.png)

这些策略展示了基本的编写方法。这里有一个默认自带的策略，名为“第一个入门策略”，我们点开查看。

![](img/c90f7c2583d4503b751fe6bacc8cb63a_5.png)

![](img/c90f7c2583d4503b751fe6bacc8cb63a_11.png)

打开后，界面类似于Jupyter Notebook，可以编写Python代码，但平台对代码结构有特定要求。

![](img/c90f7c2583d4503b751fe6bacc8cb63a_13.png)

![](img/c90f7c2583d4503b751fe6bacc8cb63a_7.png)

## 核心函数框架

![](img/c90f7c2583d4503b751fe6bacc8cb63a_15.png)

所有操作都需要围绕三个核心函数来实现。以下是这三个函数：

![](img/c90f7c2583d4503b751fe6bacc8cb63a_17.png)

![](img/c90f7c2583b751fe6bacc8cb63a_9.png)

![](img/c90f7c2583d4503b751fe6bacc8cb63a_19.png)

首先，我们看到三个函数。这三个函数不是由我们随意命名的，而是平台要求必须使用的。所有策略操作都需要围绕这三个函数逐步实现。

![](img/c90f7c2583d4503b751fe6bacc8cb63a_21.png)

![](img/c90f7c2583d4503b751fe6bacc8cb63a_11.png)

![](img/c90f7c2583d4503b751fe6bacc8cb63a_23.png)

接下来，我们解释这三个函数的含义。先看第一个函数。

![](img/c90f7c2583d4503b751fe6bacc8cb63a_25.png)

![](img/c90f7c2583d4503b751fe6bacc8cb63a_13.png)

### 初始化函数：`init`

![](img/c90f7c2583d4503b751fe6bacc8cb63a_27.png)

第一个函数是 `init`，用于初始化。在Python中，类有一个构造函数（`__init__`），用于传递参数并执行一些初始化操作。

![](img/c90f7c2583d4503b751fe6bacc8cb63a_29.png)

![](img/c90f7c2583d4503b751fe6bacc8cb63a_15.png)

`init` 函数的作用类似于Python中的构造函数。实例化一个对象时，首先会执行其构造函数。

![](img/c90f7c2583d4503b751fe6bacc8cb63a_31.png)

![](img/c90f7c2583d4503b751fe6bacc8cb63a_17.png)

![](img/c90f7c2583d4503b751fe6bacc8cb63a_33.png)

在这里是同样的道理。无论后续操作多么复杂，当策略开始执行时，`init` 函数会最先被调用。

![](img/c90f7c2583d4503b751fe6bacc8cb63a_19.png)

![](img/c90f7c2583d4503b751fe6bacc8cb63a_35.png)

### 上下文参数：`context`

![](img/c90f7c2583d4503b751fe6bacc8cb63a_37.png)

观察这三个函数，会发现它们都有一个共同的参数 `context`。

![](img/c90f7c2583d4503b751fe6bacc8cb63a_39.png)

![](img/c90f7c2583d4503b751fe6bacc8cb63a_21.png)

![](img/c90f7c2583d4503b751fe6bacc8cb63a_41.png)

`context` 参数用于在不同函数之间传递数据和状态。例如，在初始化时设置一些股票代码或数据，存入这个全局对象中。

![](img/c90f7c2583d4503b751fe6bacc8cb63a_23.png)

![](img/c90f7c2583d4503b751fe6bacc8cb63a_43.png)

在预处理或实际计算时，也需要用到这些数据，就可以通过传入的 `context` 参数来获取。

![](img/c90f7c2583d4503b751fe6bacc8cb63a_45.png)

![](img/c90f7c2583d4503b751fe6bacc8cb63a_25.png)

例如，在 `init` 中定义一个变量：
```python
context.s1 = “000001.XSHE”
```
在另一个函数中，可以直接通过 `context.s1` 来访问这个变量。`context` 主要起到数据传递的作用。

![](img/c90f7c2583d4503b751fe6bacc8cb63a_47.png)

![](img/c90f7c2583d4503b751fe6bacc8cb63a_27.png)

总结一下，`init` 函数在策略开始时最先执行一次。

![](img/c90f7c2583d4503b751fe6bacc8cb63a_49.png)

![](img/c90f7c2583d4503b751fe6bacc8cb63a_29.png)

![](img/c90f7c2583d4503b751fe6bacc8cb63a_51.png)

## 每日执行函数

`init` 函数只执行一次，而下面两个函数会被执行很多次。

![](img/c90f7c2583d4503b751fe6bacc8cb63a_31.png)

![](img/c90f7c2583d4503b751fe6bacc8cb63a_53.png)

![](img/c90f7c2583d4503b751fe6bacc8cb63a_55.png)

举个例子，在 `init` 函数中选定一只股票后，策略通常需要每天对这只股票进行操作，比如获取每日收盘价、判断买卖点等。

![](img/c90f7c2583d4503b751fe6bacc8cb63a_57.png)

![](img/c90f7c2583d4503b751fe6bacc8cb63a_33.png)

红色框内的两个函数就是用于处理这些每日重复的操作。

![](img/c90f7c2583d4503b751fe6bacc8cb63a_35.png)

![](img/c90f7c2583d4503b751fe6bacc8cb63a_59.png)

### 盘前处理函数：`before_trading`

![](img/c90f7c2583d4503b751fe6bacc8cb63a_61.png)

第一个是 `before_trading` 函数，在每个交易日开始前（实际交易发生前）执行。可以在这里进行数据预处理。

![](img/c90f7c2583d4503b751fe6bacc8cb63a_63.png)

![](img/c90f7c2583d4503b751fe6bacc8cb63a_37.png)

### 策略逻辑函数：`handle_bar`

![](img/c90f7c2583d4503b751fe6bacc8cb63a_65.png)

第二个是 `handle_bar` 函数，这是策略的核心逻辑所在。在这里编写具体的买卖判断、下单等实际操作。

![](img/c90f7c2583d4503b751fe6bacc8cb63a_39.png)

![](img/c90f7c2583d4503b751fe6bacc8cb63a_67.png)

简单来说：
*   `init`：用于初始化，只执行一次。
*   `before_trading`：用于每日的盘前数据预处理。
*   `handle_bar`：用于执行每日的实际策略逻辑和交易。

![](img/c90f7c2583d4503b751fe6bacc8cb63a_41.png)

![](img/c90f7c2583d4503b751fe6bacc8cb63a_69.png)

![](img/c90f7c2583d4503b751fe6bacc8cb63a_71.png)

## 策略参数配置

既然提到每日操作，那么“每日”这个频率如何体现？这需要通过右侧的参数面板进行配置，这些参数不是自动生成的，需要用户手动设置。

![](img/c90f7c2583d4503b751fe6bacc8cb63a_73.png)

![](img/c90f7c2583d4503b751fe6bacc8cb63a_43.png)

![](img/c90f7c2583d4503b751fe6bacc8cb63a_75.png)

要进行历史回测，需要设定以下关键参数：

![](img/c90f7c2583d4503b751fe6bacc8cb63a_77.png)

![](img/c90f7c2583d4503b751fe6bacc8cb63a_45.png)

以下是需要配置的主要参数：

![](img/c90f7c2583d4503b751fe6bacc8cb63a_79.png)

*   **起止日期**：定义回测的时间范围，包括开始日期和结束日期。
*   **初始资金**：策略启动时的本金数额，例如 `100000` 代表十万元。
*   **频率**：选择策略执行频率是“每日”还是“每分钟”。这决定了 `handle_bar` 函数的调用频率。本教程示例使用“每日”。

![](img/c90f7c2583d4503b751fe6bacc8cb63a_47.png)

## 平台扩展性

平台支持导入常用的Python库。代码中可以看到，可以自行导入 `pandas`、`numpy` 等库进行计算。

![](img/c90f7c2583d4503b751fe6bacc8cb63a_81.png)

![](img/c90f7c2583d4503b751fe6bacc8cb63a_49.png)

平台提供的API接口返回的数据格式（如 `DataFrame` 或 `ndarray`）与这些库是兼容的。因此，你可以结合使用平台API和 `pandas`、`numpy` 乃至 `scikit-learn` 等库来构建策略，灵活性很高。

![](img/c90f7c2583d4503b751fe6bacc8cb63a_51.png)

## 总结

![](img/c90f7c2583d4503b751fe6bacc8cb63a_83.png)

本节课我们一起学习了Ricequant量化交易平台的基础知识。

![](img/c90f7c2583d4503b751fe6bacc8cb63a_85.png)

我们首先介绍了平台的入口和预设策略。然后，重点讲解了策略代码必须遵循的三个核心函数框架：**只执行一次的初始化函数 `init`**、**每日盘前处理的 `before_trading`** 函数以及**包含核心策略逻辑的 `handle_bar`** 函数，并通过 `context` 参数在它们之间传递数据。

![](img/c90f7c2583d4503b751fe6bacc8cb63a_87.png)

最后，我们了解了如何在平台右侧配置回测的**关键参数**，包括时间范围、初始资金和运行频率，并知道了平台支持与主流Python数据分析库结合使用。

![](img/c90f7c2583d4503b751fe6bacc8cb63a_89.png)

掌握这些内容是开始编写和回测你自己的量化策略的第一步。