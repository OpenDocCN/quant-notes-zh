# 机器学习与量化交易：P23：Ricequant交易平台简介 🚀

在本节课中，我们将学习如何使用Ricequant量化交易平台。我们将了解平台的基本界面、核心功能函数以及如何配置策略回测参数。通过本节内容，你将能够理解并开始使用该平台进行基础的量化策略编写与测试。

---

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_1.png)

## 平台界面与策略示例

首先，我们演示量化平台的使用方法。点击“量化平台”，然后选择“免费使用”。

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_3.png)

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_1.png)

进入平台后，会看到一些预设的策略。这些是平台提供的简单演示，方便用户熟悉操作。

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_5.png)

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_3.png)

这些策略展示了基本的编写方法。点击第一个名为“入门策略”的示例进行查看。

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_5.png)

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_7.png)

打开后，界面类似于Jupyter Notebook，可以编写Python代码，但平台对代码结构有特定要求。

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_9.png)

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_7.png)

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_11.png)

---

## 核心功能函数

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_13.png)

所有操作都需要围绕三个核心函数来实现。以下是这三个函数的列表：

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_15.png)

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_9.png)

第一个函数是 `initialize`，第二个是 `before_trading`，第三个是 `handle_bar`。这三个函数不是由用户随意定义的，而是平台要求必须使用的框架。

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_17.png)

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_11.png)

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_19.png)

它们默认存在，用户的所有策略逻辑都需要在这三个函数中实现。

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_13.png)

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_21.png)

平台要求所有操作最少要围绕这三个函数来逐步实现。

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_15.png)

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_23.png)

---

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_25.png)

### 1. 初始化函数：`initialize`

首先解释 `initialize` 函数。它是一个初始化函数。

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_27.png)

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_19.png)

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_29.png)

在Python中，类有一个构造函数。`initialize` 函数就类似于Python类中的构造函数。

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_21.png)

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_31.png)

在构造函数中，通常会传入参数并对这些参数进行赋值或其他基本操作。

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_33.png)

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_23.png)

`initialize` 函数的作用与此相同。在Python中实例化一个对象时，会先执行其构造函数。

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_35.png)

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_25.png)

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_37.png)

在这里也一样，无论后续操作多么复杂，开始执行时，`initialize` 函数会最先被调用。

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_39.png)

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_27.png)

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_29.png)

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_41.png)

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_31.png)

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_33.png)

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_43.png)

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_35.png)

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_45.png)

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_37.png)

---

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_47.png)

### 上下文对象：`context` 参数

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_49.png)

观察三个函数，它们都有一个共同的参数 `context`。

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_39.png)

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_51.png)

`context` 是一个上下文对象，用于在不同函数之间传递数据和状态。

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_41.png)

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_53.png)

例如，在初始化时创建或获取的对象（如股票代码、数据），可以存入 `context`。

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_55.png)

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_43.png)

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_57.png)

在预处理或实际计算时，其他函数可以通过传入的 `context` 参数来访问这些数据。

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_45.png)

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_59.png)

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_47.png)

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_61.png)

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_49.png)

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_51.png)

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_63.png)

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_53.png)

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_65.png)

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_55.png)

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_67.png)

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_57.png)

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_59.png)

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_69.png)

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_61.png)

它帮助我们在不同算法策略步骤间进行调用和传递。

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_71.png)

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_63.png)

例如，在 `initialize` 中设置 `context.s1 = ‘000001.XSHE‘`。

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_73.png)

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_65.png)

在 `before_trading` 中，可以直接通过 `context.s1` 来获取这个股票代码。

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_75.png)

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_67.png)

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_77.png)

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_69.png)

点号 `.` 表示访问 `context` 对象中用户自定义的属性。

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_79.png)

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_71.png)

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_81.png)

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_73.png)

`context` 主要承担数据传递的角色。

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_83.png)

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_75.png)

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_85.png)

---

### 2. 预处理与每日执行函数

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_87.png)

介绍完 `initialize` 函数，我们来看下面两个函数。

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_89.png)

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_77.png)

`initialize` 只在策略开始时执行一次。

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_91.png)

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_79.png)

而 `before_trading` 和 `handle_bar` 会被执行很多次。

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_93.png)

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_81.png)

举例说明：在 `initialize` 中，我们选择了一只股票。

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_95.png)

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_83.png)

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_97.png)

这只股票选定后，后续操作都基于它。

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_99.png)

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_85.png)

策略需要每天对这只股票进行操作，例如获取每日收盘价、判断买卖点。

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_101.png)

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_87.png)

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_103.png)

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_89.png)

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_91.png)

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_105.png)

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_93.png)

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_107.png)

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_95.png)

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_97.png)

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_109.png)

这些每天重复的操作，就写在 `before_trading` 和 `handle_bar` 中。

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_111.png)

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_99.png)

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_113.png)

`before_trading` 在每个交易日开始前、实际交易前被执行。

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_101.png)

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_115.png)

`handle_bar` 在每个交易日（或每分钟）的数据到来时被执行。

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_103.png)

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_117.png)

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_105.png)

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_119.png)

`before_trading` 可以理解为数据预处理阶段。

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_121.png)

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_107.png)

在数据实际用于交易判断前，可以在这里进行处理。

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_123.png)

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_109.png)

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_111.png)

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_125.png)

`handle_bar` 则是实际策略逻辑和交易执行的地方。

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_127.png)

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_113.png)

在这里基于策略做出判断，并执行下单等操作。

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_129.png)

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_115.png)

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_131.png)

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_117.png)

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_133.png)

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_119.png)

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_121.png)

总结：`before_trading` 是每日的预处理，`handle_bar` 是每日策略逻辑的实际执行。两者在回测期间每天都会执行。

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_135.png)

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_123.png)

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_125.png)

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_127.png)

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_137.png)

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_129.png)

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_139.png)

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_131.png)

简单来说：
*   `initialize`：用于初始化。
*   `before_trading`：用于每日预处理。
*   `handle_bar`：用于实际策略判断和交易。

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_141.png)

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_133.png)

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_143.png)

---

## 回测参数配置

“每天执行”这个特性如何体现？这需要通过右侧的参数配置来实现。

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_145.png)

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_135.png)

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_147.png)

右侧区域需要用户自行设置几个关键参数，而不是显示结果。

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_137.png)

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_149.png)

进行回测是为了检验策略在历史数据上的表现。

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_139.png)

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_151.png)

因此需要指定回测的时间范围和初始条件。

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_141.png)

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_153.png)

以下是需要配置的参数列表：

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_155.png)

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_143.png)

1.  **起止日期**：回测开始和结束的日期。需要指定从哪一天到哪一天。

    ![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_145.png)

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_157.png)

2.  **初始资金**：策略开始时的本金。例如设置为 `100000`，代表10万元。这是你用于交易的起始资金，回测的收益率将基于此计算。

    ![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_147.png)

    ![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_149.png)

    ![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_151.png)

    ![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_153.png)

3.  **频率**：选择 `handle_bar` 函数的执行频率。可选“每日”或“每分钟”。
    *   **每日**：每天执行一次 `handle_bar`。
    *   **每分钟**：每分钟执行一次 `handle_bar`。
    这决定了策略逻辑的触发粒度。本教程示例通常选择“每日”。

    ![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_155.png)

    ![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_157.png)

    ![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_159.png)

    ![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_161.png)

    ![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_163.png)

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_159.png)

---

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_161.png)

## 第三方库的使用

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_163.png)

平台支持使用主流的Python数据分析库。

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_165.png)

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_165.png)

在代码中，可以自行导入 `pandas` 或 `numpy`。

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_167.png)

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_167.png)

导入后即可进行相应的数据计算。

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_169.png)

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_169.png)

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_171.png)

平台提供的API接口返回的数据格式（如DataFrame或ndarray）与这些库兼容。

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_171.png)

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_173.png)

因此，可以将平台API与 `pandas`、`numpy` 甚至 `scikit-learn` 等机器学习库结合使用。

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_173.png)

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_175.png)

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_175.png)

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_177.png)

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_177.png)

重点在于它们之间能够无缝结合。

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_179.png)

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_179.png)

---

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_181.png)

## 总结

本节课我们一起学习了Ricequant量化交易平台的基础入门知识。

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_183.png)

简单总结一下：
1.  **三个核心函数**：策略代码必须围绕 `initialize`（初始化）、`before_trading`（盘前预处理）、`handle_bar`（策略逻辑与交易）这三个函数构建。
2.  **上下文对象**：`context` 参数用于在函数间传递数据和状态。
3.  **参数配置**：进行回测前，必须在右侧配置**起止日期**、**初始资金**和**运行频率**等关键参数。
4.  **库支持**：平台支持使用 `pandas`、`numpy` 等常用Python库，可与平台原生API结合，完成复杂的数据分析和策略计算。

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_185.png)

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_181.png)

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_183.png)

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_187.png)

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_185.png)

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_187.png)

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_189.png)

理解这些基本概念后，你就可以开始在Ricequant平台上编写和回测你的第一个量化策略了。

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_191.png)

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_189.png)

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_191.png)

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_193.png)

![](img/dcb0b1728c68a1bcbb1bf7fd5081e4a6_193.png)