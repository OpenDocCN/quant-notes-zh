# 金融数据分析：1：Ricequant交易平台简介 🚀

在本节课中，我们将学习Ricequant量化交易平台的基本使用方法。我们将了解平台的核心结构、关键函数以及如何配置参数来运行一个简单的策略回测。

![](img/a2742a24e454f6bf9fb5856d01b581cc_1.png)

---

![](img/a2742a24e454f6bf9fb5856d01b581cc_3.png)

首先，我们演示量化平台的使用方法。点击“量化平台”，然后点击“免费使用”。

![](img/a2742a24e454f6bf9fb5856d01b581cc_5.png)

![](img/a2742a24e454f6bf9fb5856d01b581cc_1.png)

进入平台后，会看到一些预设的策略。这些是平台提供的简单示例，方便用户熟悉策略的编写方式。

![](img/a2742a24e454f6bf9fb5856d01b581cc_7.png)

![](img/a2742a24e454f6bf9fb5856d01b581cc_3.png)

![](img/a2742a24e454f6bf9fb5856d01b581cc_9.png)

这些策略展示了基本的编写方法。这里有一个默认名称的策略。

![](img/a2742a24e454f6bf9fb5856d01b581cc_11.png)

![](img/a2742a24e454f6bf9fb5856d01b581cc_5.png)

点击进入，可以看到第一个入门策略。

![](img/a2742a24e454f6bf9fb5856d01b581cc_13.png)

![](img/a2742a24e454f6bf9fb5856d01b581cc_7.png)

界面类似于Jupyter Notebook，可以编写Python代码，但代码结构有特定要求。

![](img/a2742a24e454f6bf9fb5856d01b581cc_9.png)

所有操作都围绕三个核心函数展开。

![](img/a2742a24e454f6bf9fb5856d01b581cc_15.png)

![](img/a2742a24e454f6bf9fb5856d01b581cc_11.png)

这三个函数是平台要求必须使用的，所有策略操作都需围绕它们实现。

![](img/a2742a24e454f6bf9fb5856d01b581cc_13.png)

上一节我们看到了三个核心函数，本节我们来详细解释它们的含义。

第一个函数是 `initialize` 函数，用于初始化操作。在Python中，类有构造函数，用于初始化参数和进行基本设置。`initialize` 函数的作用与此类似。

当执行策略时，`initialize` 函数会首先被调用一次。

![](img/a2742a24e454f6bf9fb5856d01b581cc_15.png)

![](img/a2742a24e454f6bf9fb5856d01b581cc_17.png)

三个函数都有一个共同的参数 `context`。这个参数用于在不同函数间传递数据和状态。

例如，在初始化函数中，可以选定股票代码或获取数据，并将其存入 `context` 这个全局对象中。在后续的预处理或交易函数中，可以直接通过 `context` 来访问这些预先设置好的数据。

![](img/a2742a24e454f6bf9fb5856d01b581cc_19.png)

`context` 参数帮助我们在不同算法步骤间进行数据传递和调用。

![](img/a2742a24e454f6bf9fb5856d01b581cc_17.png)

例如，在 `initialize` 中定义：
```python
context.s1 = “股票代码”
```
在另一个函数中，可以通过 `context.s1` 来访问这个变量。

![](img/a2742a24e454f6bf9fb5856d01b581cc_21.png)

![](img/a2742a24e454f6bf9fb5856d01b581cc_19.png)

![](img/a2742a24e454f6bf9fb5856d01b581cc_23.png)

`context` 主要起到数据传递的作用。至此，我们介绍了第一个函数 `initialize`，它会在策略开始时执行一次。

![](img/a2742a24e454f6bf9fb5856d01b581cc_21.png)

接下来，我们看另外两个函数。

![](img/a2742a24e454f6bf9fb5856d01b581cc_23.png)

`initialize` 只执行一次，而下面两个函数会被重复执行多次。举个例子，在 `initialize` 中选定了股票后，后续每天都需要对该股票进行判断和操作。

红色框内的两个函数就用于处理这些每日重复的操作。第一个函数 `before_trading` 在每个交易日开始前执行；第二个函数 `handle_bar` 在每个交易日（或每分钟）都会执行。

![](img/a2742a24e454f6bf9fb5856d01b581cc_25.png)

![](img/a2742a24e454f6bf9fb5856d01b581cc_25.png)

![](img/a2742a24e454f6bf9fb5856d01b581cc_27.png)

`before_trading` 函数的作用是进行数据预处理。

![](img/a2742a24e454f6bf9fb5856d01b581cc_27.png)

每天数据到来后，在实际交易前，如果需要处理数据，可以将代码写在 `before_trading` 中，相当于每日的数据预处理。

![](img/a2742a24e454f6bf9fb5856d01b581cc_29.png)

![](img/a2742a24e454f6bf9fb5856d01b581cc_29.png)

![](img/a2742a24e454f6bf9fb5856d01b581cc_31.png)

而 `handle_bar` 函数则是实际执行策略逻辑、进行买卖判断和下单操作的地方。

![](img/a2742a24e454f6bf9fb5856d01b581cc_33.png)

![](img/a2742a24e454f6bf9fb5856d01b581cc_31.png)

注意，`before_trading` 和 `handle_bar` 都是每个交易日（或每分钟）会执行的操作。

![](img/a2742a24e454f6bf9fb5856d01b581cc_35.png)

![](img/a2742a24e454f6bf9fb5856d01b581cc_33.png)

`handle_bar` 包含了算法策略的实际判断和交易逻辑。

![](img/a2742a24e454f6bf9fb5856d01b581cc_35.png)

![](img/a2742a24e454f6bf9fb5856d01b581cc_37.png)

简单总结三个函数：
*   `initialize`：用于初始化。
*   `before_trading`：用于每日数据预处理。
*   `handle_bar`：用于执行每日的实际交易策略。

既然提到每日操作，那么“每日”这个频率如何体现呢？这需要我们在右侧进行参数配置。

![](img/a2742a24e454f6bf9fb5856d01b581cc_39.png)

![](img/a2742a24e454f6bf9fb5856d01b581cc_37.png)

![](img/a2742a24e454f6bf9fb5856d01b581cc_41.png)

右侧的配置区域不是结果输出区，而是需要用户自行设置的参数区。要进行策略回测，即检验策略在历史数据上的表现，必须设置以下参数。

![](img/a2742a24e454f6bf9fb5856d01b581cc_39.png)

以下是需要配置的核心参数列表：

1.  **起止日期**：需要指定回测的时间范围，包括开始日期和结束日期。
2.  **初始资金**：例如 `100000`，代表策略开始运行时拥有的资金量，用于计算最终的收益率等指标。
3.  **频率**：选择“每日”或“每分钟”。这决定了 `handle_bar` 函数的执行频率。选择“每日”则每天执行一次；选择“每分钟”则每分钟执行一次。本教程示例按每日执行。

![](img/a2742a24e454f6bf9fb5856d01b581cc_43.png)

![](img/a2742a24e454f6bf9fb5856d01b581cc_43.png)

在代码中，可以导入常用的Python库，如 `pandas` 和 `numpy`。

![](img/a2742a24e454f6bf9fb5856d01b581cc_45.png)

平台提供的API接口返回的数据类型（如DataFrame或ndarray）与pandas、numpy兼容。因此，可以将平台API与pandas、numpy甚至scikit-learn等库结合使用，并非只能使用平台API。

![](img/a2742a24e454f6bf9fb5856d01b581cc_45.png)

![](img/a2742a24e454f6bf9fb5856d01b581cc_47.png)

平台API与Python生态库可以很好地结合使用。

![](img/a2742a24e454f6bf9fb5856d01b581cc_49.png)

设置好右侧参数后，才能运行回测并得到各项策略评估指标。

![](img/a2742a24e454f6bf9fb5856d01b581cc_51.png)

![](img/a2742a24e454f6bf9fb5856d01b581cc_47.png)

这些指标用于评估策略的基本表现。

![](img/a2742a24e454f6bf9fb5856d01b581cc_49.png)

![](img/a2742a24e454f6bf9fb5856d01b581cc_53.png)

![](img/a2742a24e454f6bf9fb5856d01b581cc_51.png)

---

![](img/a2742a24e454f6bf9fb5856d01b581cc_53.png)

本节课中，我们一起学习了Ricequant平台的核心结构。我们了解了三个必备函数（`initialize`, `before_trading`, `handle_bar`）的作用与执行顺序，并掌握了如何配置回测的起止日期、初始资金和运行频率。同时，我们也知道了平台API能与常见的Python数据分析库协同工作，为后续构建复杂策略奠定了基础。