# 量化交易教程：P23：Ricequant交易平台简介 🚀

![](img/a55d141edb78b44d1ea7194967da43dc_1.png)

![](img/a55d141edb78b44d1ea7194967da43dc_2.png)

![](img/a55d141edb78b44d1ea7194967da43dc_3.png)

在本节课中，我们将学习Ricequant量化交易平台的基本使用方法。我们将了解平台的核心结构，特别是编写策略时必须使用的三个关键函数，以及如何配置回测参数来验证策略的有效性。

![](img/a55d141edb78b44d1ea7194967da43dc_5.png)

![](img/a55d141edb78b44d1ea7194967da43dc_7.png)

## 平台界面与策略模板

![](img/a55d141edb78b44d1ea7194967da43dc_8.png)

![](img/a55d141edb78b44d1ea7194967da43dc_9.png)

![](img/a55d141edb78b44d1ea7194967da43dc_11.png)

首先，我们进入Ricequant平台并点击“免费使用”。平台提供了一些预先编写好的简单策略示例，方便用户熟悉策略的编写方式。

![](img/a55d141edb78b44d1ea7194967da43dc_13.png)

![](img/a55d141edb78b44d1ea7194967da43dc_14.png)

我们点开第一个名为“入门策略”的示例。其界面类似于Jupyter Notebook，可以编写Python代码，但平台对代码结构有特定要求。

![](img/a55d141edb78b44d1ea7194967da43dc_15.png)

![](img/a55d141edb78b44d1ea7194967da43dc_16.png)

![](img/a55d141edb78b44d1ea7194967da43dc_17.png)

![](img/a55d141edb78b44d1ea7194967da43dc_18.png)

## 核心函数解析

![](img/a55d141edb78b44d1ea7194967da43dc_20.png)

上一节我们看到了策略模板的界面，本节中我们来看看构成策略骨架的三个核心函数。所有操作都需要围绕这三个函数来实现。

![](img/a55d141edb78b44d1ea7194967da43dc_21.png)

![](img/a55d141edb78b44d1ea7194967da43dc_22.png)

![](img/a55d141edb78b44d1ea7194967da43dc_23.png)

以下是这三个函数的详细解释：

![](img/a55d141edb78b44d1ea7194967da43dc_25.png)

![](img/a55d141edb78b44d1ea7194967da43dc_26.png)

![](img/a55d141edb78b44d1ea7194967da43dc_27.png)

1.  **`init` 函数**
    这个函数用于初始化策略。它类似于Python类中的构造函数。当策略开始执行时，`init`函数会最先被调用一次。我们可以在其中设置一些初始参数、选择股票或进行其他基础配置。

![](img/a55d141edb78b44d1ea7194967da43dc_29.png)

![](img/a55d141edb78b44d1ea7194967da43dc_30.png)

![](img/a55d141edb78b44d1ea7194967da43dc_31.png)

2.  **`before_trading` 函数**
    这个函数在每个交易日开始前（即实际交易发生前）被调用。你可以在这里进行数据预处理工作。**注意**，与`init`函数不同，`before_trading`函数会在回测期间的**每一个交易日**都被执行。

![](img/a55d141edb78b44d1ea7194967da43dc_33.png)

3.  **`handle_bar` 函数**
    这个函数是策略逻辑的核心。它会在**每一个**交易时间单位（例如每天或每分钟）被调用。我们实际的买卖判断、下单操作等策略逻辑主要写在这个函数里。

![](img/a55d141edb78b44d1ea7194967da43dc_35.png)

![](img/a55d141edb78b44d1ea7194967da43dc_36.png)

## 上下文对象 `context` 的作用

![](img/a55d141edb78b44d1ea7194967da43dc_38.png)

![](img/a55d141edb78b44d1ea7194967da43dc_39.png)

我们注意到，上述三个函数都有一个共同的参数 `context`。这个参数是一个上下文对象，用于在不同函数之间传递数据和状态。

![](img/a55d141edb78b44d1ea7194967da43dc_40.png)

![](img/a55d141edb78b44d1ea7194967da43dc_41.png)

![](img/a55d141edb78b44d1ea7194967da43dc_42.png)

例如，在`init`函数中，我们可以将选定的股票代码存入 `context`：
```python
context.s1 = ‘000001.XSHE’
```
然后，在`before_trading`或`handle_bar`函数中，我们可以直接通过 `context.s1` 来获取这个股票代码，从而实现数据的共享和传递。

![](img/a55d141edb78b44d1ea7194967da43dc_44.png)

## 回测参数配置

![](img/a55d141edb78b44d1ea7194967da43dc_45.png)

![](img/a55d141edb78b44d1ea7194967da43dc_46.png)

![](img/a55d141edb78b44d1ea7194967da43dc_47.png)

![](img/a55d141edb78b44d1ea7194967da43dc_48.png)

![](img/a55d141edb78b44d1ea7194967da43dc_49.png)

理解了核心函数后，我们需要配置回测参数来运行和验证策略。这些参数位于策略编辑区的右侧，需要用户自行设置。

以下是需要配置的关键参数：

![](img/a55d141edb78b44d1ea7194967da43dc_51.png)

![](img/a55d141edb78b44d1ea7194967da43dc_52.png)

![](img/a55d141edb78b44d1ea7194967da43dc_53.png)

![](img/a55d141edb78b44d1ea7194967da43dc_54.png)

1.  **起止日期**：定义策略回测的时间范围，即从哪一天开始到哪一天结束。
2.  **初始资金**：设定策略开始运行时拥有的资金量，例如 `100000` 代表十万元。
3.  **频率**：选择策略执行的时间频率，可选“每日”或“每分钟”。这决定了`handle_bar`函数被调用的时间间隔。

![](img/a55d141edb78b44d1ea7194967da43dc_55.png)

## 平台扩展性与结果分析

![](img/a55d141edb78b44d1ea7194967da43dc_57.png)

![](img/a55d141edb78b44d1ea7194967da43dc_58.png)

Ricequant平台具有良好的扩展性。你可以在代码中导入常用的Python库（如`pandas`, `numpy`），结合平台提供的API接口返回的数据（通常是DataFrame格式）进行复杂的计算和分析。

![](img/a55d141edb78b44d1ea7194967da43dc_60.png)

![](img/a55d141edb78b44d1ea7194967da43dc_61.png)

配置好参数并编写完策略后，点击运行即可进行回测。平台会输出一系列回测结果和绩效指标，例如收益率曲线、夏普比率、最大回撤等，帮助你评估策略的表现。

![](img/a55d141edb78b44d1ea7194967da43dc_63.png)

![](img/a55d141edb78b44d1ea7194967da43dc_64.png)

## 总结

![](img/a55d141edb78b44d1ea7194967da43dc_65.png)

![](img/a55d141edb78b44d1ea7194967da43dc_66.png)

![](img/a55d141edb78b44d1ea7194967da43dc_67.png)

![](img/a55d141edb78b44d1ea7194967da43dc_68.png)

![](img/a55d141edb78b44d1ea7194967da43dc_70.png)

本节课中我们一起学习了Ricequant量化交易平台的基础知识。我们掌握了策略编写的三个核心函数：**`init`**（初始化）、**`before_trading`**（盘前处理）和**`handle_bar`**（策略主逻辑），理解了上下文对象 **`context`** 的数据传递作用，并学会了如何配置回测的**起止日期**、**初始资金**和**运行频率**。这些是使用该平台进行量化策略开发和回测的基础。