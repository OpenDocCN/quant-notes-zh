# Python金融时间序列分析与量化交易实战教程：P20：19.Ricequant交易平台简介 🚀

在本节课中，我们将学习Ricequant量化交易平台的基本使用方法，包括平台界面、核心函数结构以及如何配置回测参数。

![](img/07fc3d1e849fb01bd132c540518bdbdb_1.png)

---

![](img/07fc3d1e849fb01bd132c540518bdbdb_3.png)

## 平台入口与策略模板

![](img/07fc3d1e849fb01bd132c540518bdbdb_5.png)

首先，点击“量化平台”进入，然后选择“免费使用”。

![](img/07fc3d1e849fb01bd132c540518bdbdb_7.png)

![](img/07fc3d1e849fb01bd132c540518bdbdb_1.png)

![](img/07fc3d1e849fb01bd132c540518bdbdb_9.png)

进入平台后，会看到一些预设的策略模板。这些是平台提供的简单示例，方便用户熟悉平台的策略编写方式。

![](img/07fc3d1e849fb01bd132c540518bdbdb_11.png)

![](img/07fc3d1e849fb01bd132c540518bdbdb_3.png)

这些策略展示了基本的编写框架。例如，这里有一个默认命名的策略。

![](img/07fc3d1e849fb01bd132c540518bdbdb_13.png)

![](img/07fc3d1e849fb01bd132c540518bdbdb_5.png)

![](img/07fc3d1e849fb01bd132c540518bdbdb_15.png)

点击进入后，可以看到第一个“入门策略”。

![](img/07fc3d1e849fb01bd132c540518bdbdb_7.png)

打开后，界面类似于Jupyter Notebook，可以编写Python代码，但平台对代码结构有特定要求。

![](img/07fc3d1e849fb01bd132c540518bdbdb_9.png)

![](img/07fc3d1e849fb01bd132c540518bdbdb_17.png)

界面上列出了所有操作。可以看到，代码中默认包含三个函数。

![](img/07fc3d1e849fb01bd132c540518bdbdb_19.png)

![](img/07fc3d1e849fb01bd132c540518bdbdb_11.png)

这三个函数不是随意编写的，而是平台要求必须使用的。所有量化策略的操作都需要围绕这三个核心函数来实现。

![](img/07fc3d1e849fb01bd132c540518bdbdb_13.png)

---

## 核心函数详解

![](img/07fc3d1e849fb01bd132c540518bdbdb_21.png)

上一节我们看到了三个核心函数，本节我们来详细解释每个函数的作用。

![](img/07fc3d1e849fb01bd132c540518bdbdb_23.png)

### 初始化函数：`init`

![](img/07fc3d1e849fb01bd132c540518bdbdb_25.png)

第一个函数是 `init`，意为初始化。这类似于Python类中的构造函数 `__init__`。

![](img/07fc3d1e849fb01bd132c540518bdbdb_27.png)

在Python类中，构造函数用于在实例化对象时进行参数赋值等基本操作。`init` 函数的作用与此类似。

在策略执行时，无论后续操作多么复杂，`init` 函数总是最先被调用的。

![](img/07fc3d1e849fb01bd132c540518bdbdb_29.png)

![](img/07fc3d1e849fb01bd132c540518bdbdb_17.png)

观察三个函数，会发现它们都有一个共同的参数 `context`。

![](img/07fc3d1e849fb01bd132c540518bdbdb_31.png)

![](img/07fc3d1e849fb01bd132c540518bdbdb_19.png)

![](img/07fc3d1e849fb01bd132c540518bdbdb_33.png)

`context` 是一个全局上下文对象，用于在不同函数间传递数据和状态。例如，在 `init` 中初始化股票代码或获取数据，存入 `context`。

![](img/07fc3d1e849fb01bd132c540518bdbdb_35.png)

![](img/07fc3d1e849fb01bd132c540518bdbdb_21.png)

之后在预处理或策略逻辑函数中，就可以通过传入的 `context` 参数来访问这些预先设置好的数据。

![](img/07fc3d1e849fb01bd132c540518bdbdb_37.png)

![](img/07fc3d1e849fb01bd132c540518bdbdb_39.png)

![](img/07fc3d1e849fb01bd132c540518bdbdb_23.png)

`context` 起到了数据传递和共享的作用。例如，在 `init` 中设置：
```python
context.s1 = “000001.XSHE”
```
在另一个函数中，可以通过 `context.s1` 来获取这个股票代码。

![](img/07fc3d1e849fb01bd132c540518bdbdb_41.png)

![](img/07fc3d1e849fb01bd132c540518bdbdb_25.png)

![](img/07fc3d1e849fb01bd132c540518bdbdb_43.png)

![](img/07fc3d1e849fb01bd132c540518bdbdb_27.png)

![](img/07fc3d1e849fb01bd132c540518bdbdb_45.png)

![](img/07fc3d1e849fb01bd132c540518bdbdb_29.png)

总结来说，`init` 函数在策略开始时执行一次，用于初始化设置。

![](img/07fc3d1e849fb01bd132c540518bdbdb_47.png)

![](img/07fc3d1e849fb01bd132c540518bdbdb_31.png)

![](img/07fc3d1e849fb01bd132c540518bdbdb_49.png)

---

![](img/07fc3d1e849fb01bd132c540518bdbdb_51.png)

### 每日预处理与策略执行函数

初始化只执行一次，但策略的核心逻辑需要每天重复执行。下面介绍另外两个函数。

`init` 函数执行一次，例如，在蓝色框的 `init` 函数中选择好要交易的股票。

![](img/07fc3d1e849fb01bd132c540518bdbdb_53.png)

![](img/07fc3d1e849fb01bd132c540518bdbdb_39.png)

![](img/07fc3d1e849fb01bd132c540518bdbdb_55.png)

之后，针对这只股票的每日操作（如获取收盘价、判断买卖点）就需要在另外两个函数中完成。

![](img/07fc3d1e849fb01bd132c540518bdbdb_57.png)

![](img/07fc3d1e849fb01bd132c540518bdbdb_43.png)

![](img/07fc3d1e849fb01bd132c540518bdbdb_59.png)

以下是这两个函数的说明：

![](img/07fc3d1e849fb01bd132c540518bdbdb_61.png)

1.  **`before_trading`**：在每个交易日开始前、实际交易发生前被执行。可以在此函数中进行数据预处理。
    ![](img/07fc3d1e849fb01bd132c540518bdbdb_47.png)
    ![](img/07fc3d1e849fb01bd132c540518bdbdb_49.png)

2.  **`handle_bar`**：在每个交易日（或每分钟）都会执行。这里是策略逻辑的核心，包括买卖判断、下单等实际操作。
    ![](img/07fc3d1e849fb01bd132c540518bdbdb_53.png)
    ![](img/07fc3d1e849fb01bd132c540518bdbdb_55.png)

![](img/07fc3d1e849fb01bd132c540518bdbdb_63.png)

**关键点**：`before_trading` 和 `handle_bar` 函数在回测期间的**每一天**（或每分钟）都会执行。

![](img/07fc3d1e849fb01bd132c540518bdbdb_65.png)

![](img/07fc3d1e849fb01bd132c540518bdbdb_57.png)
![](img/07fc3d1e849fb01bd132c540518bdbdb_59.png)

![](img/07fc3d1e849fb01bd132c540518bdbdb_67.png)

简单概括：
*   `init`：初始化，只执行一次。
*   `before_trading`：每日开盘前预处理，每日执行。
*   `handle_bar`：策略逻辑与交易执行，每日（或每分钟）执行。

![](img/07fc3d1e849fb01bd132c540518bdbdb_69.png)

![](img/07fc3d1e849fb01bd132c540518bdbdb_61.png)

![](img/07fc3d1e849fb01bd132c540518bdbdb_71.png)

---

![](img/07fc3d1e849fb01bd132c540518bdbdb_73.png)

## 回测参数配置

![](img/07fc3d1e849fb01bd132c540518bdbdb_75.png)

上一节我们了解了策略的逻辑结构，本节我们来看看如何配置回测参数以运行策略。

![](img/07fc3d1e849fb01bd132c540518bdbdb_77.png)

策略需要基于历史数据进行回测，以评估其表现。因此，必须设置相关参数。

![](img/07fc3d1e849fb01bd132c540518bdbdb_79.png)

![](img/07fc3d1e849fb01bd132c540518bdbdb_65.png)

![](img/07fc3d1e849fb01bd132c540518bdbdb_81.png)

界面右侧的配置区域需要用户自行设置，而不是查看结果。

![](img/07fc3d1e849fb01bd132c540518bdbdb_83.png)

![](img/07fc3d1e849fb01bd132c540518bdbdb_69.png)

![](img/07fc3d1e849fb01bd132c540518bdbdb_85.png)

以下是需要配置的关键参数：

![](img/07fc3d1e849fb01bd132c540518bdbdb_87.png)

![](img/07fc3d1e849fb01bd132c540518bdbdb_71.png)

![](img/07fc3d1e849fb01bd132c540518bdbdb_89.png)

1.  **回测起止日期**：指定策略回测的历史时间段，包括开始日期和结束日期。
    ![](img/07fc3d1e849fb01bd132c540518bdbdb_77.png)
    ![](img/07fc3d1e849fb01bd132c540518bdbdb_79.png)

![](img/07fc3d1e849fb01bd132c540518bdbdb_91.png)

2.  **初始资金**：策略开始运行时拥有的资金量，例如 `100000` 表示10万元。这是计算收益率等指标的基础。
    ![](img/07fc3d1e849fb01bd132c540518bdbdb_85.png)
    ![](img/07fc3d1e849fb01bd132c540518bdbdb_87.png)
    ![](img/07fc3d1e849fb01bd132c540518bdbdb_91.png)

![](img/07fc3d1e849fb01bd132c540518bdbdb_93.png)

3.  **运行频率**：选择策略是按“每日”还是“每分钟”执行 `handle_bar` 函数。这决定了策略的触发粒度。
    ![](img/07fc3d1e849fb01bd132c540518bdbdb_95.png)

![](img/07fc3d1e849fb01bd132c540518bdbdb_95.png)

---

## 平台功能扩展性

![](img/07fc3d1e849fb01bd132c540518bdbdb_97.png)

除了使用平台内置的API，Ricequant平台也支持与主流的Python数据分析库结合使用。

![](img/07fc3d1e849fb01bd132c540518bdbdb_99.png)

平台代码环境中可以自行导入库，例如：
```python
import pandas as pd
import numpy as np
```
![](img/07fc3d1e849fb01bd132c540518bdbdb_99.png)

![](img/07fc3d1e849fb01bd132c540518bdbdb_101.png)

平台提供的API接口返回的数据格式（如DataFrame, ndarray）与pandas、numpy兼容，因此可以无缝衔接进行数据分析。

![](img/07fc3d1e849fb01bd132c540518bdbdb_103.png)

![](img/07fc3d1e849fb01bd132c540518bdbdb_105.png)

![](img/07fc3d1e849fb01bd132c540518bdbdb_105.png)

此外，也可以导入如scikit-learn等机器学习库来构建更复杂的模型。

![](img/07fc3d1e849fb01bd132c540518bdbdb_109.png)

![](img/07fc3d1e849fb01bd132c540518bdbdb_107.png)

这意味着，在策略中既可以灵活使用通用的Python科学计算库，也可以调用平台便捷的金融数据API，二者能够很好地结合。

![](img/07fc3d1e849fb01bd132c540518bdbdb_109.png)

![](img/07fc3d1e849fb01bd132c540518bdbdb_113.png)

![](img/07fc3d1e849fb01bd132c540518bdbdb_111.png)

---

![](img/07fc3d1e849fb01bd132c540518bdbdb_113.png)

## 总结

![](img/07fc3d1e849fb01bd132c540518bdbdb_115.png)

本节课中，我们一起学习了Ricequant量化交易平台的基础知识：

![](img/07fc3d1e849fb01bd132c540518bdbdb_117.png)

1.  **核心三函数**：策略围绕 `init`（初始化）、`before_trading`（盘前处理）、`handle_bar`（策略执行）三个函数构建。
2.  **上下文对象**：`context` 用于在函数间传递和共享数据。
3.  **参数配置**：运行回测前，必须正确设置**起止日期**、**初始资金**和**运行频率**。
4.  **平台扩展性**：平台支持与pandas、numpy、scikit-learn等主流Python库结合使用，以构建强大的量化策略。

![](img/07fc3d1e849fb01bd132c540518bdbdb_119.png)

正确理解这些基本概念和结构，是使用Ricequant平台编写和回测量化策略的第一步。

![](img/07fc3d1e849fb01bd132c540518bdbdb_121.png)

![](img/07fc3d1e849fb01bd132c540518bdbdb_121.png)