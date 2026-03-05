# Python机器学习与量化交易：P22：23.23.3-Ricequant交易平台简介 📈

![](img/72cb72d0ded88de27f036dab7226f75f_1.png)

![](img/72cb72d0ded88de27f036dab7226f75f_3.png)

在本节课中，我们将要学习Ricequant量化交易平台的基本使用方法。我们将了解平台的核心结构、关键函数的作用以及如何配置策略参数，为后续编写和回测量化策略打下基础。

![](img/72cb72d0ded88de27f036dab7226f75f_5.png)

## 平台界面与策略示例

![](img/72cb72d0ded88de27f036dab7226f75f_7.png)

![](img/72cb72d0ded88de27f036dab7226f75f_9.png)

首先演示量化平台的使用方法。点击“量化平台”，然后点击“免费使用”。

![](img/72cb72d0ded88de27f036dab7226f75f_11.png)

![](img/72cb72d0ded88de27f036dab7226f75f_1.png)

![](img/72cb72d0ded88de27f036dab7226f75f_13.png)

进入平台后，会看到一些预设的策略。这些是平台提供的简单示例，方便用户熟悉平台操作。

![](img/72cb72d0ded88de27f036dab7226f75f_15.png)

![](img/72cb72d0ded88de27f036dab7226f75f_3.png)

![](img/72cb72d0ded88de27f036dab7226f75f_17.png)

这些策略展示了基本的编写方法。这里有一个默认名称的策略，我们点开第一个“入门策略”进行查看。

![](img/72cb72d0ded88de27f036dab7226f75f_19.png)

![](img/72cb72d0ded88de27f036dab7226f75f_21.png)

![](img/72cb72d0ded88de27f036dab7226f75f_5.png)

![](img/72cb72d0ded88de27f036dab7226f75f_23.png)

点开之后，界面类似于Jupyter Notebook，可以编写Python代码，但代码结构有特定要求。

![](img/72cb72d0ded88de27f036dab7226f75f_25.png)

![](img/72cb72d0ded88de27f036dab7226f75f_27.png)

![](img/72cb72d0ded88de27f036dab7226f75f_7.png)

![](img/72cb72d0ded88de27f036dab7226f75f_29.png)

![](img/72cb72d0ded88de27f036dab7226f75f_31.png)

界面上列出了所有操作。可以看到代码区域默认包含三个函数。

![](img/72cb72d0ded88de27f036dab7226f75f_33.png)

![](img/72cb72d0ded88de27f036dab7226f75f_35.png)

![](img/72cb72d0ded88de27f036dab7226f75f_9.png)

![](img/72cb72d0ded88de27f036dab7226f75f_37.png)

这三个函数不是随意编写的，而是平台要求必须使用的。所有策略操作都需要围绕这三个函数来实现。

![](img/72cb72d0ded88de27f036dab7226f75f_39.png)

![](img/72cb72d0ded88de27f036dab7226f75f_41.png)

## 核心函数详解

![](img/72cb72d0ded88de27f036dab7226f75f_43.png)

![](img/72cb72d0ded88de27f036dab7226f75f_45.png)

上一节我们看到了平台的代码结构，本节中我们来看看这三个核心函数的具体含义。

![](img/72cb72d0ded88de27f036dab7226f75f_47.png)

### 初始化函数：`init`

![](img/72cb72d0ded88de27f036dab7226f75f_49.png)

![](img/72cb72d0ded88de27f036dab7226f75f_51.png)

第一个函数是 `init` 函数，其作用是进行初始化。

![](img/72cb72d0ded88de27f036dab7226f75f_53.png)

![](img/72cb72d0ded88de27f036dab7226f75f_55.png)

在Python中，类有一个构造函数，用于在实例化对象时进行参数赋值等基本操作。`init` 函数的作用就类似于Python类中的构造函数。

![](img/72cb72d0ded88de27f036dab7226f75f_57.png)

![](img/72cb72d0ded88de27f036dab7226f75f_59.png)

既然它类似于构造函数，那么在Python中实例化一个对象时，首先会执行其构造函数。这里也是一样，无论后续操作多么复杂，当策略开始执行时，`init` 函数会最先被调用。

![](img/72cb72d0ded88de27f036dab7226f75f_61.png)

![](img/72cb72d0ded88de27f036dab7226f75f_63.png)

```python
def init(context):
    # 初始化操作写在这里
    context.s1 = “000001.XSHE”
```

![](img/72cb72d0ded88de27f036dab7226f75f_65.png)

![](img/72cb72d0ded88de27f036dab7226f75f_67.png)

### 上下文参数：`context`

![](img/72cb72d0ded88de27f036dab7226f75f_69.png)

![](img/72cb72d0ded88de27f036dab7226f75f_71.png)

在调用 `init` 函数时，它有一个参数 `context`。观察三个函数，会发现它们都有一个共同的参数 `context`。

![](img/72cb72d0ded88de27f036dab7226f75f_73.png)

![](img/72cb72d0ded88de27f036dab7226f75f_75.png)

`context` 参数表示一个全局上下文对象。它的作用是**在不同函数间传递数据和状态**。例如，在初始化函数中创建或指定的对象（如股票代码、数据），在预处理函数和实际计算函数中也需要使用。

![](img/72cb72d0ded88de27f036dab7226f75f_77.png)

![](img/72cb72d0ded88de27f036dab7226f75f_79.png)

通过 `context` 对象，可以将这些数据存储起来，并在其他函数中通过 `context.变量名` 的方式调用，实现数据的共享和传递。

![](img/72cb72d0ded88de27f036dab7226f75f_81.png)

![](img/72cb72d0ded88de27f036dab7226f75f_83.png)

```python
def init(context):
    context.s1 = “000001.XSHE”  # 在context中存储股票代码

![](img/72cb72d0ded88de27f036dab7226f75f_85.png)

![](img/72cb72d0ded88de27f036dab7226f75f_87.png)

def before_trading(context):
    stock_code = context.s1  # 在其他函数中取出使用
```

![](img/72cb72d0ded88de27f036dab7226f75f_89.png)

### 每日预处理与策略执行函数

![](img/72cb72d0ded88de27f036dab7226f75f_91.png)

![](img/72cb72d0ded88de27f036dab7226f75f_93.png)

初始化函数只在策略开始时执行一次。而接下来的两个函数，`before_trading` 和 `handle_bar`，会被执行很多次。

![](img/72cb72d0ded88de27f036dab7226f75f_95.png)

举个例子：在蓝色的 `init` 函数框中，我们只选择了一只股票，这个操作只执行一次就确定了。而在红色的函数框中，我们的策略需要对这只股票每天进行操作，比如获取每日收盘价、判断买卖点等。这些每日重复的操作就写在红色框的函数里。

红色框中有两个函数：
1.  `before_trading`：在每个交易日开始前（实际交易前）被执行，可以用于数据预处理。
2.  `handle_bar`：每个交易日（或每分钟）都会执行，这里是**策略实际逻辑和买卖操作**发生的地方。

![](img/72cb72d0ded88de27f036dab7226f75f_97.png)

![](img/72cb72d0ded88de27f036dab7226f75f_99.png)

简单总结这三个函数：
*   `init`：用于初始化，只执行一次。
*   `before_trading`：用于每日数据预处理，每个交易日执行一次。
*   `handle_bar`：用于执行每日（或每分钟）的核心策略逻辑和交易指令。

![](img/72cb72d0ded88de27f036dab7226f75f_101.png)

![](img/72cb72d0ded88de27f036dab7226f75f_103.png)

## 策略参数配置

![](img/72cb72d0ded88de27f036dab7226f75f_105.png)

![](img/72cb72d0ded88de27f036dab7226f75f_107.png)

刚才提到 `handle_bar` 函数每天都会执行，那么“每天”这个频率是如何体现的呢？这需要通过右侧的参数配置区域来设置。

![](img/72cb72d0ded88de27f036dab7226f75f_97.png)

右侧框选的区域不是回测结果，而是需要用户自行配置的参数。要进行策略回测（即在历史数据上测试策略表现），必须设置以下参数：

![](img/72cb72d0ded88de27f036dab7226f75f_109.png)

![](img/72cb72d0ded88de27f036dab7226f75f_111.png)

以下是需要配置的核心参数列表：
*   **起止日期与终止日期**：定义回测的时间范围，即从哪一天开始到哪一天结束。
*   **初始资金**：策略开始运行时拥有的资金量，例如 `100000` 代表10万元。这是计算最终收益率等指标的起点。
*   **频率**：选择策略执行的频率，有“每日”和“每分钟”两种选项。“每日”表示 `handle_bar` 函数每天执行一次；“每分钟”则表示每分钟执行一次。本教程示例通常选择“每日”。

![](img/72cb72d0ded88de27f036dab7226f75f_113.png)

## 第三方库与平台API的结合

![](img/72cb72d0ded88de27f036dab7226f75f_115.png)

![](img/72cb72d0ded88de27f036dab7226f75f_117.png)

在实际编写策略时，平台允许并鼓励使用Python常用的数据科学库。

![](img/72cb72d0ded88de27f036dab7226f75f_119.png)

![](img/72cb72d0ded88de27f036dab7226f75f_121.png)

平台说明中指出，可以自行导入所需的库，例如 `pandas as pd` 或 `numpy as np`。之前课程讲过如何使用这些库进行计算。

![](img/72cb72d0ded88de27f036dab7226f75f_123.png)

平台提供的API接口（用于获取数据、下单等）其底层也是基于 `pandas` 和 `numpy` 实现的，返回的数据通常是 `DataFrame` 或 `ndarray` 格式。

![](img/72cb72d0ded88de27f036dab7226f75f_125.png)

![](img/72cb72d0ded88de27f036dab7226f75f_127.png)

因此，**平台的API可以与 `pandas`、`numpy` 乃至 `scikit-learn` 等机器学习库无缝结合使用**。你不仅可以使用平台API，也能使用其他Python库，重点在于将它们有机结合来实现复杂的策略。

![](img/72cb72d0ded88de27f036dab7226f75f_129.png)

![](img/72cb72d0ded88de27f036dab7226f75f_131.png)

## 总结

![](img/72cb72d0ded88de27f036dab7226f75f_133.png)

![](img/72cb72d0ded88de27f036dab7226f75f_135.png)

本节课中我们一起学习了Ricequant量化交易平台的基础知识。我们了解了策略代码必须围绕三个核心函数（`init`, `before_trading`, `handle_bar`）来构建，并掌握了 `context` 参数在函数间传递数据的作用。同时，我们学会了如何配置回测的起止日期、初始资金和运行频率等关键参数。最后，我们知道了在平台上可以灵活结合使用平台API和诸如 `pandas`、`numpy` 这样的第三方库来开发策略。这些是开始编写和测试量化策略的第一步。