# Python金融分析与量化交易实战：P23：Ricequant交易平台简介 🚀

![](img/35036d085a51beda55fc27b79a4a2b71_1.png)

在本节课中，我们将学习Ricequant量化交易平台的基本使用方法。我们将了解平台的核心结构，包括三个关键的函数，以及如何配置策略参数以进行回测。

![](img/35036d085a51beda55fc27b79a4a2b71_3.png)

---

![](img/35036d085a51beda55fc27b79a4a2b71_5.png)

## 平台界面与策略示例

![](img/35036d085a51beda55fc27b79a4a2b71_7.png)

首先，演示量化平台的使用方法。点击“量化平台”，然后点击“免费使用”。

![](img/35036d085a51beda55fc27b79a4a2b71_9.png)

![](img/35036d085a51beda55fc27b79a4a2b71_11.png)

![](img/35036d085a51beda55fc27b79a4a2b71_1.png)

![](img/35036d085a51beda55fc27b79a4a2b71_13.png)

进入平台后，会看到一些预设的策略。这些策略是平台提供的简单示例，方便用户熟悉平台操作。

![](img/35036d085a51beda55fc27b79a4a2b71_15.png)

![](img/35036d085a51beda55fc27b79a4a2b71_3.png)

![](img/35036d085a51beda55fc27b79a4a2b71_17.png)

这些策略展示了基本的编写方法。点击默认名称的第一个“入门策略”进行查看。

![](img/35036d085a51beda55fc27b79a4a2b71_19.png)

![](img/35036d085a51beda55fc27b79a4a2b71_5.png)

![](img/35036d085a51beda55fc27b79a4a2b71_21.png)

![](img/35036d085a51beda55fc27b79a4a2b71_23.png)

打开后，界面类似于Jupyter Notebook，可以编写Python代码，但平台对代码结构有特定要求。

![](img/35036d085a51beda55fc27b79a4a2b71_25.png)

![](img/35036d085a51beda55fc27b79a4a2b71_7.png)

![](img/35036d085a51beda55fc27b79a4a2b71_27.png)

所有操作都需要围绕平台规定的几个核心函数展开。

![](img/35036d085a51beda55fc27b79a4a2b71_29.png)

![](img/35036d085a51beda55fc27b79a4a2b71_31.png)

![](img/35036d085a51beda55fc27b79a4a2b71_9.png)

![](img/35036d085a51beda55fc27b79a4a2b71_33.png)

![](img/35036d085a51beda55fc27b79a4a2b71_35.png)

---

![](img/35036d085a51beda55fc27b79a4a2b71_37.png)

## 核心函数解析

![](img/35036d085a51beda55fc27b79a4a2b71_39.png)

可以看到代码中有三个函数。这三个函数不是用户随意定义的，而是平台要求必须使用的。所有策略操作都需要围绕这三个函数来实现。

![](img/35036d085a51beda55fc27b79a4a2b71_41.png)

![](img/35036d085a51beda55fc27b79a4a2b71_43.png)

![](img/35036d085a51beda55fc27b79a4a2b71_11.png)

![](img/35036d085a51beda55fc27b79a4a2b71_45.png)

![](img/35036d085a51beda55fc27b79a4a2b71_13.png)

![](img/35036d085a51beda55fc27b79a4a2b71_47.png)

以下是对这三个函数的解释。

![](img/35036d085a51beda55fc27b79a4a2b71_49.png)

![](img/35036d085a51beda55fc27b79a4a2b71_51.png)

### 1. `initialize` 初始化函数

![](img/35036d085a51beda55fc27b79a4a2b71_53.png)

第一个函数是 `initialize`，其作用是进行初始化。

![](img/35036d085a51beda55fc27b79a4a2b71_55.png)

![](img/35036d085a51beda55fc27b79a4a2b71_57.png)

![](img/35036d085a51beda55fc27b79a4a2b71_17.png)

![](img/35036d085a51beda55fc27b79a4a2b71_59.png)

在Python中，类有一个构造函数。构造函数通常用于接收参数并进行赋值等基本操作。`initialize` 函数就类似于Python类中的构造函数。

![](img/35036d085a51beda55fc27b79a4a2b71_61.png)

![](img/35036d085a51beda55fc27b79a4a2b71_21.png)

![](img/35036d085a51beda55fc27b79a4a2b71_63.png)

![](img/35036d085a51beda55fc27b79a4a2b71_65.png)

在Python中实例化一个对象时，会首先执行其构造函数。这里同理，无论后续操作多么复杂，当执行策略时，`initialize` 函数会最先被调用。

![](img/35036d085a51beda55fc27b79a4a2b71_67.png)

![](img/35036d085a51beda55fc27b79a4a2b71_33.png)

![](img/35036d085a51beda55fc27b79a4a2b71_69.png)

`initialize` 函数中有一个参数 `context`。观察三个函数，发现它们都有一个共同的参数 `context`。

![](img/35036d085a51beda55fc27b79a4a2b71_71.png)

![](img/35036d085a51beda55fc27b79a4a2b71_73.png)

![](img/35036d085a51beda55fc27b79a4a2b71_39.png)

![](img/35036d085a51beda55fc27b79a4a2b71_75.png)

`context` 是一个全局上下文对象，用于在不同函数间传递数据。例如，在初始化阶段设置股票代码或获取数据，可以存入 `context` 对象。

![](img/35036d085a51beda55fc27b79a4a2b71_77.png)

![](img/35036d085a51beda55fc27b79a4a2b71_49.png)

![](img/35036d085a51beda55fc27b79a4a2b71_79.png)

在后续的预处理或计算函数中，可以直接通过传入的 `context` 参数来访问这些数据。

![](img/35036d085a51beda55fc27b79a4a2b71_81.png)

![](img/35036d085a51beda55fc27b79a4a2b71_83.png)

![](img/35036d085a51beda55fc27b79a4a2b71_59.png)

![](img/35036d085a51beda55fc27b79a4a2b71_85.png)

例如，在 `initialize` 中设置 `context.s1 = ‘000001.XSHE‘`。在 `before_trading` 函数中，可以通过 `context.s1` 来获取这个股票代码。

![](img/35036d085a51beda55fc27b79a4a2b71_87.png)

```python
def initialize(context):
    # 初始化时设置股票代码
    context.s1 = ‘000001.XSHE‘

![](img/35036d085a51beda55fc27b79a4a2b71_89.png)

![](img/35036d085a51beda55fc27b79a4a2b71_91.png)

def before_trading(context):
    # 预处理时使用初始化设置的股票代码
    stock_code = context.s1
```

![](img/35036d085a51beda55fc27b79a4a2b71_93.png)

`context` 参数的主要作用是在不同算法策略步骤间传递信息。

![](img/35036d085a51beda55fc27b79a4a2b71_95.png)

![](img/35036d085a51beda55fc27b79a4a2b71_97.png)

![](img/35036d085a51beda55fc27b79a4a2b71_75.png)

![](img/35036d085a51beda55fc27b79a4a2b71_99.png)

总结一下，`initialize` 函数在策略开始时首先执行一次。

![](img/35036d085a51beda55fc27b79a4a2b71_101.png)

![](img/35036d085a51beda55fc27b79a4a2b71_103.png)

![](img/35036d085a51beda55fc27b79a4a2b71_77.png)

![](img/35036d085a51beda55fc27b79a4a2b71_105.png)

### 2. `before_trading` 与 `handle_bar` 函数

![](img/35036d085a51beda55fc27b79a4a2b71_107.png)

`initialize` 函数只在开始时执行一次。而接下来的两个函数会被执行很多次。

![](img/35036d085a51beda55fc27b79a4a2b71_109.png)

![](img/35036d085a51beda55fc27b79a4a2b71_111.png)

![](img/35036d085a51beda55fc27b79a4a2b71_83.png)

![](img/35036d085a51beda55fc27b79a4a2b71_113.png)

举例说明：在 `initialize` 函数中选定一只股票。这个操作只执行一次，股票确定后就不再改变。

![](img/35036d085a51beda55fc27b79a4a2b71_115.png)

![](img/35036d085a51beda55fc27b79a4a2b71_117.png)

![](img/35036d085a51beda55fc27b79a4a2b71_89.png)

![](img/35036d085a51beda55fc27b79a4a2b71_119.png)

接下来，策略需要对这只股票进行操作。通常需要每天获取它的收盘价，并判断是买入、卖出还是持有。

![](img/35036d085a51beda55fc27b79a4a2b71_121.png)

![](img/35036d085a51beda55fc27b79a4a2b71_97.png)

![](img/35036d085a51beda55fc27b79a4a2b71_123.png)

![](img/35036d085a51beda55fc27b79a4a2b71_125.png)

这些每天都需要重复的操作，就写在 `before_trading` 和 `handle_bar` 函数中。

![](img/35036d085a51beda55fc27b79a4a2b71_127.png)

![](img/35036d085a51beda55fc27b79a4a2b71_101.png)

![](img/35036d085a51beda55fc27b79a4a2b71_129.png)

![](img/35036d085a51beda55fc27b79a4a2b71_131.png)

*   **`before_trading`**：在每个交易日开始前（交易前）被执行。可以在这里进行数据预处理。
*   **`handle_bar`**：在每个交易日（或每分钟）都会执行。这里是策略实际逻辑的核心，包括买卖判断和下单操作。

![](img/35036d085a51beda55fc27b79a4a2b71_133.png)

![](img/35036d085a51beda55fc27b79a4a2b71_115.png)

简单来说：
*   `initialize` 用于初始化。
*   `before_trading` 用于每日的预处理。
*   `handle_bar` 用于执行实际的交易策略。

![](img/35036d085a51beda55fc27b79a4a2b71_135.png)

![](img/35036d085a51beda55fc27b79a4a2b71_133.png)

![](img/35036d085a51beda55fc27b79a4a2b71_137.png)

![](img/35036d085a51beda55fc27b79a4a2b71_139.png)

---

![](img/35036d085a51beda55fc27b79a4a2b71_141.png)

## 策略参数配置

![](img/35036d085a51beda55fc27b79a4a2b71_143.png)

“每天执行”是如何体现的呢？这需要通过右侧的参数配置来实现。

![](img/35036d085a51beda55fc27b79a4a2b71_145.png)

![](img/35036d085a51beda55fc27b79a4a2b71_135.png)

![](img/35036d085a51beda55fc27b79a4a2b71_147.png)

右侧的配置项不是运行结果，而是需要用户自己设定的。要进行历史回测，必须指定相关参数。

![](img/35036d085a51beda55fc27b79a4a2b71_149.png)

![](img/35036d085a51beda55fc27b79a4a2b71_139.png)

![](img/35036d085a51beda55fc27b79a4a2b71_151.png)

![](img/35036d085a51beda55fc27b79a4a2b71_153.png)

以下是需要配置的关键参数：

![](img/35036d085a51beda55fc27b79a4a2b71_155.png)

1.  **起止日期**：定义回测的时间范围，包括开始日期和结束日期。
    ![](img/35036d085a51beda55fc27b79a4a2b71_143.png)

![](img/35036d085a51beda55fc27b79a4a2b71_157.png)

2.  **初始资金**：策略启动时的资金量，用于计算回测收益率。例如，设置为 `100000` 代表10万元。
    ![](img/35036d085a51beda55fc27b79a4a2b71_151.png)

![](img/35036d085a51beda55fc27b79a4a2b71_159.png)

![](img/35036d085a51beda55fc27b79a4a2b71_161.png)

3.  **频率**：选择策略执行的时间频率。有两种选择：
    *   **每日**：`handle_bar` 函数每天执行一次。
    *   **每分钟**：`handle_bar` 函数每分钟执行一次。
    需要根据策略需求进行选择。在示例中，我们通常选择“每日”。
    ![](img/35036d085a51beda55fc27b79a4a2b71_163.png)

![](img/35036d085a51beda55fc27b79a4a2b71_163.png)

---

![](img/35036d085a51beda55fc27b79a4a2b71_165.png)

![](img/35036d085a51beda55fc27b79a4a2b71_167.png)

## 第三方库支持

![](img/35036d085a51beda55fc27b79a4a2b71_169.png)

平台支持导入常用的Python库。代码中已经提示，可以自行导入 `pandas as pd` 或 `numpy as np`。

![](img/35036d085a51beda55fc27b79a4a2b71_171.png)

![](img/35036d085a51beda55fc27b79a4a2b71_169.png)

![](img/35036d085a51beda55fc27b79a4a2b71_173.png)

![](img/35036d085a51beda55fc27b79a4a2b71_175.png)

平台自身的API接口返回的数据，如 `DataFrame` 或 `ndarray`，与 `pandas`、`numpy` 的数据格式是兼容的。

![](img/35036d085a51beda55fc27b79a4a2b71_177.png)

![](img/35036d085a51beda55fc27b79a4a2b71_177.png)

![](img/35036d085a51beda55fc27b79a4a2b71_179.png)

因此，可以将平台API与 `pandas`、`numpy` 甚至 `scikit-learn` 等库结合使用，并非只能使用平台API。

![](img/35036d085a51beda55fc27b79a4a2b71_181.png)

![](img/35036d085a51beda55fc27b79a4a2b71_183.png)

![](img/35036d085a51beda55fc27b79a4a2b71_183.png)

![](img/35036d085a51beda55fc27b79a4a2b71_185.png)

---

![](img/35036d085a51beda55fc27b79a4a2b71_187.png)

## 本节总结

![](img/35036d085a51beda55fc27b79a4a2b71_189.png)

![](img/35036d085a51beda55fc27b79a4a2b71_191.png)

本节课我们一起学习了Ricequant量化平台的基本结构：
1.  **三个核心函数**：`initialize`（初始化）、`before_trading`（盘前处理）、`handle_bar`（策略执行逻辑）。
2.  **关键参数配置**：必须正确设置回测的起止日期、初始资金和运行频率，才能进行有效的策略回测并获得评估指标。
3.  **扩展性**：平台支持与主流的Python数据分析库（如Pandas、NumPy）无缝结合，增强了策略开发的灵活性。

![](img/35036d085a51beda55fc27b79a4a2b71_193.png)

通过理解这些基础概念，我们已经为在Ricequant平台上编写和测试自己的量化策略做好了准备。