# Python金融量化交易实战教程：P20：03-3-Ricequant交易平台简介

![](img/b2df5f32ea316ce9067d958d70fe7ad6_1.png)

## 📖 概述
在本节课程中，我们将学习Ricequant量化交易平台的基本使用方法。我们将了解平台的核心结构，包括三个关键的函数，以及如何配置回测参数来运行一个简单的策略。

---

## 🏗️ 平台核心结构
上一节我们介绍了量化交易的基本概念，本节中我们来看看如何在Ricequant平台上实现一个策略。平台提供了一个类似Notebook的在线编程环境，允许用户编写Python代码进行策略回测。

平台默认提供了三个核心函数，所有策略操作都需要围绕这三个函数来实现。

以下是三个核心函数及其作用：

![](img/b2df5f32ea316ce9067d958d70fe7ad6_3.png)

1.  **`init` 函数**
    此函数用于策略的初始化，类似于Python类中的构造函数。当策略开始执行时，`init`函数会首先被调用一次。其参数`context`是一个全局上下文对象，用于在不同函数间传递数据和状态。

    ```python
    def init(context):
        # 初始化操作，例如选定股票池
        context.s1 = "000001.XSHE"
    ```

2.  **`before_trading` 函数**
    此函数在每个交易日开始前（即开盘前）被调用。可以在这里进行数据预处理或准备工作。它也是每日执行的。

    ```python
    def before_trading(context):
        # 每日开盘前的预处理，例如获取前日收盘价
        price = history_bars(context.s1, 1, '1d', 'close')
        context.previous_close = price[0]
    ```

3.  **`handle_bar` 函数**
    此函数是策略逻辑的核心，会根据你选择的频率（每日或每分钟）被重复调用。在这里实现具体的买卖判断和下单操作。

    ```python
    def handle_bar(context, bar_dict):
        # 策略核心逻辑，例如基于价格判断买卖
        current_price = bar_dict[context.s1].close
        if current_price > context.previous_close * 1.01:
            # 执行买入操作
            order_target_percent(context.s1, 0.1)
    ```

参数`context`在上述所有函数中都会出现，它作为一个容器，使得在`init`中初始化的变量（如股票代码`s1`）可以在`before_trading`和`handle_bar`中被访问和修改，实现了函数间的数据传递。

![](img/b2df5f32ea316ce9067d958d70fe7ad6_5.png)

---

![](img/b2df5f32ea316ce9067d958d70fe7ad6_7.png)

## ⚙️ 回测参数配置
了解了核心函数后，我们需要配置策略的运行环境。在代码编辑区的右侧，有几个关键参数需要设置，它们决定了回测的规则。

以下是必须配置的回测参数：

*   **起始日期与结束日期**：定义回测所覆盖的历史时间段。
*   **初始资金**：设定策略开始运行时的本金数额，例如 `100000` 代表十万元。
*   **频率**：选择策略执行的频率，可选 **“每日”** 或 **“每分钟”**。这决定了`handle_bar`函数的调用间隔。

---

## 🔗 平台与Python生态的结合
Ricequant平台并非封闭系统，它允许并鼓励使用丰富的Python生态工具。平台提供的API返回的数据格式（如`DataFrame`或`ndarray`）与主流数据分析库兼容。

这意味着你可以自由地导入并使用诸如`pandas`、`numpy`甚至`scikit-learn`等库来进行复杂的数据处理、分析和建模，并与平台的原生API无缝结合，构建强大的量化策略。

---

![](img/b2df5f32ea316ce9067d958d70fe7ad6_9.png)

![](img/b2df5f32ea316ce9067d958d70fe7ad6_11.png)

## 📝 总结
本节课我们一起学习了Ricequant量化平台的基本框架。我们掌握了构成策略的**三个核心函数**（`init`, `before_trading`, `handle_bar`）的作用与执行顺序，学会了如何设置关键的**回测参数**（时间、资金、频率），并了解到平台支持与外部Python库结合使用。这是开始编写和回测你自己的量化策略的第一步。