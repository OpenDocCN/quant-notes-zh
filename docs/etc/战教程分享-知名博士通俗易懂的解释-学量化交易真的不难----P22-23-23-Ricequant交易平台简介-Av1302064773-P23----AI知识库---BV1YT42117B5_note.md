# 量化交易完全可自学教程：P22：Ricequant交易平台简介

在本节课中，我们将学习如何使用Ricequant量化交易平台。我们将从平台的基本界面开始，逐步了解其核心功能、策略编写框架以及关键参数的配置方法。

![](img/72ed79a691cafcab10cadf5307ee5820_1.png)

## 平台界面与入门策略

首先，我们演示量化平台的使用方法。点击“量化平台”，然后选择“免费使用”。

![](img/72ed79a691cafcab10cadf5307ee5820_3.png)

![](img/72ed79a691cafcab10cadf5307ee5820_1.png)

![](img/72ed79a691cafcab10cadf5307ee5820_5.png)

进入平台后，会看到一些预设的策略。这些是平台提供的简单示例，方便用户熟悉环境。

![](img/72ed79a691cafcab10cadf5307ee5820_7.png)

![](img/72ed79a691cafcab10cadf5307ee5820_3.png)

![](img/72ed79a691cafcab10cadf5307ee5820_9.png)

这些策略展示了基本的编写方法。平台默认会带有一个名为“第一个入门策略”的示例，我们可以点开查看。

![](img/72ed79a691cafcab10cadf5307ee5820_5.png)

![](img/72ed79a691cafcab10cadf5307ee5820_11.png)

打开后，界面类似于Jupyter Notebook，可以在此编写Python代码，但平台对代码结构有特定要求。

![](img/72ed79a691cafcab10cadf5307ee5820_13.png)

![](img/72ed79a691cafcab10cadf5307ee5820_7.png)

## 核心策略函数框架

所有操作都需要围绕平台规定的几个核心函数来实现。以下是这些函数的列表：

![](img/72ed79a691cafcab10cadf5307ee5820_15.png)

![](img/72ed79a691cafcab10cadf5307ee5820_9.png)

![](img/72ed79a691cafcab10cadf5307ee5820_17.png)

可以看到代码中有三个函数：`init`、`before_trading` 和 `handle_bar`。这些不是用户随意编写的，而是平台要求必须使用的框架。所有策略逻辑都需要在这三个函数中实现。

![](img/72ed79a691cafcab10cadf5307ee5820_19.png)

### 1. 初始化函数 `init`

![](img/72ed79a691cafcab10cadf5307ee5820_21.png)

第一个函数是 `init`，用于初始化策略。

![](img/72ed79a691cafcab10cadf5307ee5820_23.png)

![](img/72ed79a691cafcab10cadf5307ee5820_13.png)

![](img/72ed79a691cafcab10cadf5307ee5820_25.png)

在Python中，类有一个构造函数（`__init__`），用于传递参数和进行赋值等基本操作。`init` 函数的作用与此类似，相当于策略的构造函数。

![](img/72ed79a691cafcab10cadf5307ee5820_27.png)

![](img/72ed79a691cafcab10cadf5307ee5820_15.png)

在Python中实例化对象时，会先执行构造函数。这里同理，无论后续操作多复杂，**执行策略时，`init` 函数会最先被调用**。

![](img/72ed79a691cafcab10cadf5307ee5820_17.png)

![](img/72ed79a691cafcab10cadf5307ee5820_29.png)

![](img/72ed79a691cafcab10cadf5307ee5820_31.png)

### 2. 上下文参数 `context`

观察三个函数，会发现它们都有一个共同的参数 `context`。

![](img/72ed79a691cafcab10cadf5307ee5820_33.png)

![](img/72ed79a691cafcab10cadf5307ee5820_19.png)

![](img/72ed79a691cafcab10cadf5307ee5820_35.png)

`context` 是一个全局上下文对象，用于在不同函数间传递数据。例如，在 `init` 函数中初始化股票代码或获取数据后，可以存入 `context`。

![](img/72ed79a691cafcab10cadf5307ee5820_37.png)

![](img/72ed79a691cafcab10cadf5307ee5820_21.png)

在 `before_trading` 或 `handle_bar` 函数中，若需要处理这些股票或数据，就可以通过传入的 `context` 参数来获取。

![](img/72ed79a691cafcab10cadf5307ee5820_39.png)

![](img/72ed79a691cafcab10cadf5307ee5820_23.png)

![](img/72ed79a691cafcab10cadf5307ee5820_41.png)

`context` 起到了数据传递的作用。例如，在 `init` 中设置 `context.s1 = ‘股票代码’`，在其他函数中通过 `context.s1` 即可访问。

![](img/72ed79a691cafcab10cadf5307ee5820_43.png)

![](img/72ed79a691cafcab10cadf5307ee5820_25.png)

![](img/72ed79a691cafcab10cadf5307ee5820_45.png)

总结来说，`init` 函数在策略开始时执行一次，用于初始化设置。

![](img/72ed79a691cafcab10cadf5307ee5820_27.png)

## 每日执行的函数

![](img/72ed79a691cafcab10cadf5307ee5820_47.png)

`init` 函数只执行一次，而下面两个函数会被执行很多次。

![](img/72ed79a691cafcab10cadf5307ee5820_49.png)

![](img/72ed79a691cafcab10cadf5307ee5820_29.png)

举例说明：在 `init` 函数（蓝色框）中选定一只股票后，这个选择就固定了。

![](img/72ed79a691cafcab10cadf5307ee5820_51.png)

![](img/72ed79a691cafcab10cadf5307ee5820_31.png)

但策略需要每天对这只股票进行操作，例如获取每日收盘价、判断买卖点等。这些每日重复的操作就写在红色框的两个函数里。

![](img/72ed79a691cafcab10cadf5307ee5820_53.png)

![](img/72ed79a691cafcab10cadf5307ee5820_33.png)

![](img/72ed79a691cafcab10cadf5307ee5820_55.png)

### 1. 盘前处理函数 `before_trading`

`before_trading` 函数在每个交易日开始前（实际交易发生前）被调用。

![](img/72ed79a691cafcab10cadf5307ee5820_57.png)

![](img/72ed79a691cafcab10cadf5307ee5820_35.png)

可以在此函数中对当日的数据进行预处理，例如数据清洗、特征计算等。

![](img/72ed79a691cafcab10cadf5307ee5820_59.png)

![](img/72ed79a691cafcab10cadf5307ee5820_61.png)

![](img/72ed79a691cafcab10cadf5307ee5820_37.png)

![](img/72ed79a691cafcab10cadf5307ee5820_63.png)

### 2. 策略逻辑函数 `handle_bar`

`handle_bar` 函数是策略的核心，在每个交易时间点（如每日或每分钟）被调用。

![](img/72ed79a691cafcab10cadf5307ee5820_39.png)

![](img/72ed79a691cafcab10cadf5307ee5820_65.png)

策略的实际判断逻辑、买卖下单等操作都在此函数中执行。

![](img/72ed79a691cafcab10cadf5307ee5820_67.png)

![](img/72ed79a691cafcab10cadf5307ee5820_41.png)

![](img/72ed79a691cafcab10cadf5307ee5820_69.png)

简单总结三个函数：
*   **`init`**: 初始化，只执行一次。
*   **`before_trading`**: 每日盘前预处理，每个交易日执行一次。
*   **`handle_bar`**: 策略主逻辑，每个交易时间点（如每日）执行一次。

![](img/72ed79a691cafcab10cadf5307ee5820_43.png)

![](img/72ed79a691cafcab10cadf5307ee5820_71.png)

## 回测参数配置

既然提到函数每日执行，那么“每日”这个频率是如何体现的呢？这需要通过右侧的参数面板进行配置。

![](img/72ed79a691cafcab10cadf5307ee5820_45.png)

![](img/72ed79a691cafcab10cadf5307ee5820_73.png)

右侧面板的参数不是运行结果，而是需要用户自行设定的输入项。进行历史回测时，必须配置这些参数。

![](img/72ed79a691cafcab10cadf5307ee5820_75.png)

![](img/72ed79a691cafcab10cadf5307ee5820_47.png)

![](img/72ed79a691cafcab10cadf5307ee5820_77.png)

以下是需要配置的关键参数列表：

1.  **起止日期**：定义回测的时间范围，包括开始日期和结束日期。平台将获取此时间段内的历史数据进行模拟。

    ![](img/72ed79a691cafcab10cadf5307ee5820_49.png)

![](img/72ed79a691cafcab10cadf5307ee5820_79.png)

2.  **初始资金**：策略开始运行时拥有的资金量，例如 `100000` 代表10万元。所有交易和收益计算都基于此初始资金。

    ![](img/72ed79a691cafcab10cadf5307ee5820_51.png)

![](img/72ed79a691cafcab10cadf5307ee5820_81.png)

3.  **频率**：决定 `handle_bar` 函数的调用频率。可选“每日”或“每分钟”。
    *   **每日**：每个交易日执行一次 `handle_bar`。
    *   **每分钟**：每个交易日的每分钟执行一次 `handle_bar`。
    本教程中我们主要使用“每日”频率。实际项目中可根据策略需求选择。

    ![](img/72ed79a691cafcab10cadf5307ee5820_53.png)

## 第三方库与平台API的结合

![](img/72ed79a691cafcab10cadf5307ee5820_83.png)

平台支持导入常用的Python库。所有与pandas、NumPy相关的操作都可以进行。

![](img/72ed79a691cafcab10cadf5307ee5820_85.png)

![](img/72ed79a691cafcab10cadf5307ee5820_55.png)

用户可以自行导入 `pandas`、`numpy` 甚至 `scikit-learn` 等库进行计算。

![](img/72ed79a691cafcab10cadf5307ee5820_87.png)

![](img/72ed79a691cafcab10cadf5307ee5820_57.png)

平台提供的API接口（用于获取行情数据、财务数据等）其底层也是基于pandas和NumPy实现的。

![](img/72ed79a691cafcab10cadf5307ee5820_89.png)

![](img/72ed79a691cafcab10cadf5307ee5820_59.png)

![](img/72ed79a691cafcab10cadf5307ee5820_91.png)

API返回的数据通常是 `DataFrame` 或 `ndarray` 格式，与直接使用pandas/NumPy得到的数据类型一致。

![](img/72ed79a691cafcab10cadf5307ee5820_61.png)

![](img/72ed79a691cafcab10cadf5307ee5820_93.png)

因此，在策略中可以同时使用平台API和第三方库（如pandas, numpy, sklearn），并能将它们无缝结合。

![](img/72ed79a691cafcab10cadf5307ee5820_95.png)

![](img/72ed79a691cafcab10cadf5307ee5820_63.png)

![](img/72ed79a691cafcab10cadf5307ee5820_97.png)

## 总结

本节课中，我们一起学习了Ricequant量化交易平台的基础知识。

我们首先介绍了平台界面和入门策略。然后，重点讲解了策略编写的核心框架，包括三个关键函数：**只执行一次的初始化函数 `init`**、**每个交易日盘前执行的预处理函数 `before_trading`**，以及**承载核心策略逻辑的函数 `handle_bar`**。我们还了解了用于在函数间传递数据的 **`context`** 参数。

![](img/72ed79a691cafcab10cadf5307ee5820_99.png)

接着，我们学习了如何进行回测参数配置，包括设置**回测起止日期**、**初始资金**和策略执行**频率**。

![](img/72ed79a691cafcab10cadf5307ee5820_101.png)

最后，我们了解到平台支持与 `pandas`、`numpy` 等主流Python库结合使用，增强了策略开发的灵活性。

![](img/72ed79a691cafcab10cadf5307ee5820_103.png)

掌握这些基础知识后，您就可以开始在Ricequant平台上编写和回测自己的量化策略了。

![](img/72ed79a691cafcab10cadf5307ee5820_105.png)

![](img/72ed79a691cafcab10cadf5307ee5820_65.png)