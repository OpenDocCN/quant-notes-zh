# Python金融量化分析：23：Ricequant交易平台简介 🚀

![](img/2728867884d3bc69ac9020eae5f1bebe_1.png)

在本节课中，我们将学习如何使用Ricequant量化交易平台。我们将了解平台的基本界面、核心的编程框架以及如何配置策略回测参数。通过本节内容，你将能够理解并开始编写自己的第一个量化策略。

![](img/2728867884d3bc69ac9020eae5f1bebe_3.png)

![](img/2728867884d3bc69ac9020eae5f1bebe_5.png)

## 平台界面与策略模板

![](img/2728867884d3bc69ac9020eae5f1bebe_7.png)

首先，我们演示量化平台的使用方法。点击“量化平台”，然后选择“免费使用”。

![](img/2728867884d3bc69ac9020eae5f1bebe_9.png)

![](img/2728867884d3bc69ac9020eae5f1bebe_11.png)

![](img/2728867884d3bc69ac9020eae5f1bebe_1.png)

![](img/2728867884d3bc69ac9020eae5f1bebe_13.png)

进入平台后，会看到一些预设的策略。这些是平台提供的简单示例，方便用户熟悉策略的编写方式。

![](img/2728867884d3bc69ac9020eae5f1bebe_15.png)

![](img/2728867884d3bc69ac9020eae5f1bebe_17.png)

![](img/2728867884d3bc69ac9020eae5f1bebe_3.png)

![](img/2728867884d3bc69ac9020eae5f1bebe_19.png)

这些策略展示了基本的写法。我们点击第一个名为“入门策略”的示例进行查看。

![](img/2728867884d3bc69ac9020eae5f1bebe_21.png)

![](img/2728867884d3bc69ac9020eae5f1bebe_23.png)

![](img/2728867884d3bc69ac9020eae5f1bebe_5.png)

![](img/2728867884d3bc69ac9020eae5f1bebe_25.png)

打开后，界面类似于Jupyter Notebook，可以在此编写Python代码，但平台对代码结构有特定要求。

![](img/2728867884d3bc69ac9020eae5f1bebe_27.png)

![](img/2728867884d3bc69ac9020eae5f1bebe_29.png)

![](img/2728867884d3bc69ac9020eae5f1bebe_7.png)

![](img/2728867884d3bc69ac9020eae5f1bebe_31.png)

所有操作都需要围绕平台规定的几个核心函数展开。

![](img/2728867884d3bc69ac9020eae5f1bebe_33.png)

![](img/2728867884d3bc69ac9020eae5f1bebe_35.png)

![](img/2728867884d3bc69ac9020eae5f1bebe_37.png)

![](img/2728867884d3bc69ac9020eae5f1bebe_9.png)

![](img/2728867884d3bc69ac9020eae5f1bebe_39.png)

## 核心函数解析

![](img/2728867884d3bc69ac9020eae5f1bebe_41.png)

我们可以看到代码中有三个函数，这不是我们随意指定的，而是平台要求必须使用的框架。所有策略操作都需围绕这三个函数实现。

![](img/2728867884d3bc69ac9020eae5f1bebe_43.png)

![](img/2728867884d3bc69ac9020eae5f1bebe_45.png)

![](img/2728867884d3bc69ac9020eae5f1bebe_11.png)

![](img/2728867884d3bc69ac9020eae5f1bebe_47.png)

![](img/2728867884d3bc69ac9020eae5f1bebe_49.png)

以下是这三个函数的解释。

![](img/2728867884d3bc69ac9020eae5f1bebe_51.png)

![](img/2728867884d3bc69ac9020eae5f1bebe_53.png)

### 初始化函数：`init`

![](img/2728867884d3bc69ac9020eae5f1bebe_55.png)

第一个函数是 `init`，其作用是进行初始化。

![](img/2728867884d3bc69ac9020eae5f1bebe_57.png)

![](img/2728867884d3bc69ac9020eae5f1bebe_59.png)

![](img/2728867884d3bc69ac9020eae5f1bebe_17.png)

![](img/2728867884d3bc69ac9020eae5f1bebe_61.png)

![](img/2728867884d3bc69ac9020eae5f1bebe_63.png)

这类似于Python类中的构造函数。在构造函数中，我们通常会传入参数并进行赋值等基本操作。

![](img/2728867884d3bc69ac9020eae5f1bebe_65.png)

![](img/2728867884d3bc69ac9020eae5f1bebe_21.png)

![](img/2728867884d3bc69ac9020eae5f1bebe_67.png)

![](img/2728867884d3bc69ac9020eae5f1bebe_69.png)

`init` 函数就相当于Python中的构造函数。实例化对象时，会首先执行构造函数。在这里也一样，无论后续操作多复杂，`init` 函数都会最先被调用。

![](img/2728867884d3bc69ac9020eae5f1bebe_71.png)

![](img/2728867884d3bc69ac9020eae5f1bebe_33.png)

![](img/2728867884d3bc69ac9020eae5f1bebe_73.png)

### 上下文参数：`context`

![](img/2728867884d3bc69ac9020eae5f1bebe_75.png)

![](img/2728867884d3bc69ac9020eae5f1bebe_77.png)

三个函数都有一个共同的参数 `context`。

![](img/2728867884d3bc69ac9020eae5f1bebe_79.png)

![](img/2728867884d3bc69ac9020eae5f1bebe_81.png)

![](img/2728867884d3bc69ac9020eae5f1bebe_41.png)

![](img/2728867884d3bc69ac9020eae5f1bebe_83.png)

![](img/2728867884d3bc69ac9020eae5f1bebe_85.png)

`context` 是一个全局上下文对象，用于在不同函数间传递数据。例如，在初始化时设置股票代码或获取数据，可以存入 `context` 对象。

![](img/2728867884d3bc69ac9020eae5f1bebe_87.png)

![](img/2728867884d3bc69ac9020eae5f1bebe_47.png)

![](img/2728867884d3bc69ac9020eae5f1bebe_89.png)

在预处理或实际计算时，就可以从传入的 `context` 参数中获取这些数据。

![](img/2728867884d3bc69ac9020eae5f1bebe_91.png)

![](img/2728867884d3bc69ac9020eae5f1bebe_93.png)

![](img/2728867884d3bc69ac9020eae5f1bebe_59.png)

![](img/2728867884d3bc69ac9020eae5f1bebe_95.png)

![](img/2728867884d3bc69ac9020eae5f1bebe_97.png)

其使用方式是通过点号 `.` 访问属性。例如，在 `init` 中设置 `context.s1 = ‘000001.XSHE‘`，在其他函数中通过 `context.s1` 即可调用。

![](img/2728867884d3bc69ac9020eae5f1bebe_99.png)

![](img/2728867884d3bc69ac9020eae5f1bebe_101.png)

```python
# 在 init 函数中赋值
context.s1 = “000001.XSHE“

![](img/2728867884d3bc69ac9020eae5f1bebe_103.png)

# 在 handle_bar 函数中调用
current_stock = context.s1
```

![](img/2728867884d3bc69ac9020eae5f1bebe_105.png)

![](img/2728867884d3bc69ac9020eae5f1bebe_107.png)

`context` 主要帮助我们在策略的不同部分之间传递信息和状态。

![](img/2728867884d3bc69ac9020eae5f1bebe_109.png)

![](img/2728867884d3bc69ac9020eae5f1bebe_111.png)

![](img/2728867884d3bc69ac9020eae5f1bebe_75.png)

![](img/2728867884d3bc69ac9020eae5f1bebe_113.png)

### 每日预处理与策略执行函数

![](img/2728867884d3bc69ac9020eae5f1bebe_115.png)

![](img/2728867884d3bc69ac9020eae5f1bebe_117.png)

`init` 函数仅在策略开始时执行一次。而接下来的两个函数会被反复执行。

![](img/2728867884d3bc69ac9020eae5f1bebe_119.png)

![](img/2728867884d3bc69ac9020eae5f1bebe_121.png)

![](img/2728867884d3bc69ac9020eae5f1bebe_81.png)

![](img/2728867884d3bc69ac9020eae5f1bebe_123.png)

举个例子，在 `init` 函数中选定股票后，后续每天都需要对这些股票数据进行判断和操作。

![](img/2728867884d3bc69ac9020eae5f1bebe_125.png)

![](img/2728867884d3bc69ac9020eae5f1bebe_127.png)

![](img/2728867884d3bc69ac9020eae5f1bebe_89.png)

![](img/2728867884d3bc69ac9020eae5f1bebe_129.png)

![](img/2728867884d3bc69ac9020eae5f1bebe_131.png)

红色框内的两个函数即用于此：
1.  **`before_trading`**：在每个交易日开始前（交易前）被调用。可以在此进行数据预处理。
2.  **`handle_bar`**：在每个交易日（或每分钟）被调用。这里是策略逻辑的核心，根据数据判断并执行买卖操作。

![](img/2728867884d3bc69ac9020eae5f1bebe_133.png)

![](img/2728867884d3bc69ac9020eae5f1bebe_105.png)

![](img/2728867884d3bc69ac9020eae5f1bebe_135.png)

简单总结：
*   **`init`**：初始化，只执行一次。
*   **`before_trading`**：每日交易前的预处理，每日执行。
*   **`handle_bar`**：策略逻辑主体，进行实际买卖判断，每日或每分钟执行。

![](img/2728867884d3bc69ac9020eae5f1bebe_137.png)

![](img/2728867884d3bc69ac9020eae5f1bebe_133.png)

![](img/2728867884d3bc69ac9020eae5f1bebe_139.png)

![](img/2728867884d3bc69ac9020eae5f1bebe_141.png)

## 回测参数配置

![](img/2728867884d3bc69ac9020eae5f1bebe_143.png)

“每天执行”是如何体现的呢？这需要通过右侧的参数面板进行配置。

![](img/2728867884d3bc69ac9020eae5f1bebe_145.png)

![](img/2728867884d3bc69ac9020eae5f1bebe_135.png)

![](img/2728867884d3bc69ac9020eae5f1bebe_147.png)

![](img/2728867884d3bc69ac9020eae5f1bebe_149.png)

这些参数不是结果，需要用户自行设置。要进行策略回测，必须指定以下信息：

![](img/2728867884d3bc69ac9020eae5f1bebe_151.png)

![](img/2728867884d3bc69ac9020eae5f1bebe_141.png)

![](img/2728867884d3bc69ac9020eae5f1bebe_153.png)

![](img/2728867884d3bc69ac9020eae5f1bebe_155.png)

以下是需要配置的关键参数：

![](img/2728867884d3bc69ac9020eae5f1bebe_157.png)

*   **起止日期**：回测的时间范围，包括开始日期和结束日期。
*   **初始资金**：策略启动时的本金数额，例如 `100000` 代表十万元。
*   **频率**：策略执行的频率，可选“每日”或“每分钟”。这决定了 `handle_bar` 函数的调用频率。

![](img/2728867884d3bc69ac9020eae5f1bebe_159.png)

![](img/2728867884d3bc69ac9020eae5f1bebe_161.png)

![](img/2728867884d3bc69ac9020eae5f1bebe_161.png)

![](img/2728867884d3bc69ac9020eae5f1bebe_163.png)

![](img/2728867884d3bc69ac9020eae5f1bebe_165.png)

## 第三方库支持

![](img/2728867884d3bc69ac9020eae5f1bebe_167.png)

![](img/2728867884d3bc69ac9020eae5f1bebe_169.png)

在策略中，你可以自由导入常用的Python库。

![](img/2728867884d3bc69ac9020eae5f1bebe_171.png)

![](img/2728867884d3bc69ac9020eae5f1bebe_169.png)

![](img/2728867884d3bc69ac9020eae5f1bebe_173.png)

![](img/2728867884d3bc69ac9020eae5f1bebe_175.png)

例如，导入Pandas和NumPy进行数据分析：

![](img/2728867884d3bc69ac9020eae5f1bebe_177.png)

```python
import pandas as pd
import numpy as np
```

![](img/2728867884d3bc69ac9020eae5f1bebe_179.png)

![](img/2728867884d3bc69ac9020eae5f1bebe_181.png)

平台提供的API接口返回的数据格式（如DataFrame或ndarray）与这些库完全兼容，因此可以无缝结合使用。

![](img/2728867884d3bc69ac9020eae5f1bebe_183.png)

![](img/2728867884d3bc69ac9020eae5f1bebe_179.png)

![](img/2728867884d3bc69ac9020eae5f1bebe_185.png)

![](img/2728867884d3bc69ac9020eae5f1bebe_187.png)

## 总结

![](img/2728867884d3bc69ac9020eae5f1bebe_189.png)

本节课我们一起学习了Ricequant量化平台的基本使用。
1.  我们认识了平台的界面和策略模板。
2.  深入理解了策略框架的三个核心函数：**`init`**、**`before_trading`** 和 **`handle_bar`**，以及用于数据传递的 **`context`** 参数。
3.  掌握了回测前必须配置的参数：**起止日期**、**初始资金**和**运行频率**。
4.  了解到在策略中可以灵活使用Python生态的数据分析库（如Pandas、NumPy）与平台API相结合。

![](img/2728867884d3bc69ac9020eae5f1bebe_191.png)

![](img/2728867884d3bc69ac9020eae5f1bebe_193.png)

理解这些基础概念是编写和回测量化策略的第一步。在接下来的课程中，我们将利用这个框架，开始构建具体的交易策略。