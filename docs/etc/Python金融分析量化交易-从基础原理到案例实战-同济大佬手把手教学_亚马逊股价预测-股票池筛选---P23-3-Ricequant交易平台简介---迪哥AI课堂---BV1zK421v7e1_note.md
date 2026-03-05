# Python金融分析量化交易：P23：Ricequant交易平台简介 📈

![](img/b231298999ea643af49bec42c6382a08_1.png)

在本节课中，我们将要学习Ricequant量化交易平台的基本使用方法。我们将了解平台的核心结构、关键函数的作用以及如何配置回测参数，为后续编写和测试量化策略打下基础。

![](img/b231298999ea643af49bec42c6382a08_3.png)

![](img/b231298999ea643af49bec42c6382a08_5.png)

## 平台界面与入门策略

![](img/b231298999ea643af49bec42c6382a08_7.png)

首先，我们演示量化平台的使用方法。点击“量化平台”，然后选择“免费使用”。

![](img/b231298999ea643af49bec42c6382a08_9.png)

![](img/b231298999ea643af49bec42c6382a08_11.png)

![](img/b231298999ea643af49bec42c6382a08_1.png)

![](img/b231298999ea643af49bec42c6382a08_13.png)

进入平台后，会看到一些预设的策略。这些是平台提供的简单示例，方便用户熟悉平台操作。

![](img/b231298999ea643af49bec42c6382a08_15.png)

![](img/b231298999ea643af49bec42c6382a08_17.png)

![](img/b231298999ea643af49bec42c6382a08_3.png)

![](img/b231298999ea643af49bec42c6382a08_19.png)

这些策略展示了基本的编写方法。平台默认会带有一个名为“第一个入门策略”的示例，我们点开查看。

![](img/b231298999ea643af49bec42c6382a08_21.png)

![](img/b231298999ea643af49bec42c6382a08_23.png)

![](img/b231298999ea643af49bec42c6382a08_5.png)

![](img/b231298999ea643af49bec42c6382a08_25.png)

打开后，界面类似于Jupyter Notebook，可以编写Python代码，但平台对代码结构有特定要求。

![](img/b231298999ea643af49bec42c6382a08_27.png)

![](img/b231298999ea643af49bec42c6382a08_29.png)

![](img/b231298999ea643af49bec42c6382a08_7.png)

![](img/b231298999ea643af49bec42c6382a08_31.png)

![](img/b231298999ea643af49bec42c6382a08_33.png)

## 核心函数解析

![](img/b231298999ea643af49bec42c6382a08_35.png)

![](img/b231298999ea643af49bec42c6382a08_37.png)

在代码区域，我们可以看到列出了三个函数。

![](img/b231298999ea643af49bec42c6382a08_39.png)

![](img/b231298999ea643af49c6382a08_9.png)

![](img/b231298999ea643af49bec42c6382a08_41.png)

![](img/b231298999ea643af49bec42c6382a08_43.png)

以下是三个核心函数：
*   `init`
*   `before_trading`
*   `handle_bar`

![](img/b231298999ea643af49bec42c6382a08_45.png)

![](img/b231298999ea643af49bec42c6382a08_47.png)

这三个函数不是随意编写的，而是平台要求必须使用的。所有量化策略的操作都需要围绕这三个函数来实现。

![](img/b231298999ea643af49bec42c6382a08_49.png)

![](img/b231298999ea643af49bec42c6382a08_51.png)

![](img/b231298999ea643af49bec42c6382a08_13.png)

![](img/b231298999ea643af49bec42c6382a08_53.png)

### 初始化函数：`init`

![](img/b231298999ea643af49bec42c6382a08_55.png)

![](img/b231298999ea643af49bec42c6382a08_57.png)

首先解释第一个函数 `init`。它是一个初始化函数。

![](img/b231298999ea643af49bec42c6382a08_59.png)

![](img/b231298999ea643af49bec42c6382a08_61.png)

![](img/b231298999ea643af49bec42c6382a08_15.png)

![](img/b231298999ea643af49bec42c6382a08_63.png)

![](img/b231298999ea643af49bec42c6382a08_65.png)

在Python中，类有一个构造函数（`__init__`）。构造函数通常用于传递参数并进行赋值等基本操作。`init` 函数就相当于Python中的构造函数。

![](img/b231298999ea643af49bec42c6382a08_67.png)

![](img/b231298999ea643af49bec42c6382a08_19.png)

![](img/b231298999ea643af49bec42c6382a08_69.png)

![](img/b231298999ea643af49bec42c6382a08_71.png)

在Python中实例化一个对象时，会先执行其构造函数。这里同理，无论后续操作多么复杂，执行策略时，`init` 函数会最先被调用。

![](img/b231298999ea643af49bec42c6382a08_73.png)

![](img/b231298999ea643af49bec42c6382a08_31.png)

![](img/b231298999ea643af49bec42c6382a08_75.png)

![](img/b231298999ea643af49bec42c6382a08_77.png)

### 上下文参数：`context`

![](img/b231298999ea643af49bec42c6382a08_79.png)

![](img/b231298999ea643af49bec42c6382a08_81.png)

观察三个函数，会发现它们都有一个共同的参数 `context`。

![](img/b231298999ea643af49bec42c6382a08_83.png)

![](img/b231298999ea643af49bec42c6382a08_85.png)

![](img/b231298999ea643af49bec42c6382a08_37.png)

![](img/b231298999ea643af49bec42c6382a08_87.png)

`context` 是一个全局上下文对象，用于在不同函数间传递数据和状态。例如，在 `init` 函数中初始化的一些对象（如股票代码、数据），在 `before_trading` 或 `handle_bar` 函数中也需要使用。通过 `context` 参数，可以将这些信息传递下去。

![](img/b231298999ea643af49bec42c6382a08_89.png)

![](img/b231298999ea643af49bec42c6382a08_91.png)

![](img/b231298999ea643af49bec42c6382a08_43.png)

![](img/b231298999ea643af49bec42c6382a08_93.png)

![](img/b231298999ea643af49bec42c6382a08_95.png)

具体使用方式是，在 `init` 函数中将数据存入 `context` 对象。

![](img/b231298999ea643af49bec42c6382a08_97.png)

```python
context.s1 = ‘000001.XSHE’ # 示例：存入股票代码
```

![](img/b231298999ea643af49bec42c6382a08_99.png)

![](img/b231298999ea643af49bec42c6382a08_101.png)

在 `before_trading` 或 `handle_bar` 函数中，通过传入的 `context` 参数来访问这些数据。

![](img/b231298999ea643af49bec42c6382a08_103.png)

![](img/b231298999ea643af49bec42c6382a08_105.png)

```python
current_stock = context.s1 # 获取之前存入的股票代码
```

![](img/b231298999ea643af49bec42c6382a08_107.png)

![](img/b231298999ea643af49bec42c6382a08_109.png)

`context` 主要起到在不同函数间传递和共享数据的作用。

![](img/b231298999ea643af49bec42c6382a08_111.png)

![](img/b231298999ea643af49bec42c6382a08_113.png)

![](img/b231298999ea643af49bec42c6382a08_73.png)

![](img/b231298999ea643af49bec42c6382a08_115.png)

### 每日执行函数：`before_trading` 与 `handle_bar`

![](img/b231298999ea643af49bec42c6382a08_117.png)

![](img/b231298999ea643af49bec42c6382a08_119.png)

`init` 函数只在策略开始时执行一次。而 `before_trading` 和 `handle_bar` 函数会被执行很多次。

![](img/b231298999ea643af49bec42c6382a08_121.png)

![](img/b231298999ea643af49bec42c6382a08_123.png)

![](img/b231298999ea643af49bec42c6382a08_79.png)

![](img/b231298999ea643af49bec42c6382a08_125.png)

![](img/b231298999ea643af49bec42c6382a08_127.png)

举个例子，在 `init` 函数中，我们选定了一只股票。这个选择是固定的。

![](img/b231298999ea643af49bec42c6382a08_129.png)

![](img/b231298999ea643af49bec42c6382a08_131.png)

![](img/b231298999ea643af49bec42c6382a08_87.png)

![](img/b231298999ea643af49bec42c6382a08_133.png)

而策略需要对这只股票每天进行操作，比如获取每日收盘价、判断买卖点等。这些每日重复的操作就写在 `before_trading` 和 `handle_bar` 函数中。

![](img/b231298999ea643af49bec42c6382a08_135.png)

![](img/b231298999ea643af49bec42c6382a08_97.png)

![](img/b231298999ea643af49bec42c6382a08_137.png)

`before_trading` 函数在每个交易日开始前（实际交易前）被执行。可以在这里进行数据预处理。

![](img/b231298999ea643af49bec42c6382a08_139.png)

![](img/b231298999ea643af49bec42c6382a08_141.png)

![](img/b231298999ea643af49bec42c6382a08_109.png)

![](img/b231298999ea643af49bec42c6382a08_143.png)

![](img/b231298999ea643af49bec42c6382a08_145.png)

`handle_bar` 函数是策略逻辑的核心。它会在每个交易日（或每分钟）被调用，在这里编写具体的买卖判断、下单等操作。

![](img/b231298999ea643af49bec42c6382a08_147.png)

![](img/b231298999ea643af49bec42c6382a08_125.png)

![](img/b231298999ea643af49bec42c6382a08_149.png)

**总结一下三个函数：**
*   `init`：初始化，只执行一次。
*   `before_trading`：交易日开始前的预处理，每日执行。
*   `handle_bar`：策略逻辑主体，进行实际买卖判断与操作，每日或每分钟执行。

![](img/b231298999ea643af49bec42c6382a08_151.png)

![](img/b231298999ea643af49bec42c6382a08_153.png)

![](img/b231298999ea643af49bec42c6382a08_133.png)

![](img/b231298999ea643af49bec42c6382a08_155.png)

![](img/b231298999ea643af49bec42c6382a08_157.png)

## 回测参数配置

![](img/b231298999ea643af49bec42c6382a08_159.png)

既然 `handle_bar` 函数每天都要执行，这个“每天”如何体现？这需要通过右侧的参数配置来实现。

![](img/b231298999ea643af49bec42c6382a08_161.png)

![](img/b231298999ea643af49bec42c6382a08_163.png)

![](img/b231298999ea643af49bec42c6382a08_135.png)

![](img/b231298999ea643af49bec42c6382a08_165.png)

![](img/b231298999ea643af49bec42c6382a08_167.png)

右侧区域不是运行结果，而是需要用户自己设置的参数。进行回测（在历史数据上测试策略）时，必须指定以下信息：

![](img/b231298999ea643af49bec42c6382a08_169.png)

以下是需要配置的核心参数：
1.  **起止日期**：回测的时间范围，包括开始日期和结束日期。
2.  **初始资金**：策略启动时的本金金额，例如 `100000` 代表10万元。回测收益率等指标将基于此计算。
3.  **频率**：选择策略执行的频率是“每日”还是“每分钟”。这决定了 `handle_bar` 函数的调用周期。本教程示例通常选择“每日”。

![](img/b231298999ea643af49bec42c6382a08_171.png)

![](img/b231298999ea643af49bec42c6382a08_173.png)

![](img/b231298999ea643af49bec42c6382a08_159.png)

![](img/b231298999ea643af49bec42c6382a08_175.png)

![](img/b231298999ea643af49bec42c6382a08_177.png)

## 第三方库支持

![](img/b231298999ea643af49bec42c6382a08_179.png)

平台支持导入常用的Python库。代码中提到了pandas和NumPy。

![](img/b231298999ea643af49bec42c6382a08_181.png)

![](img/b231298999ea643af49bec42c6382a08_183.png)

![](img/b231298999ea643af49bec42c6382a08_169.png)

![](img/b231298999ea643af49bec42c6382a08_185.png)

![](img/b231298999ea643af49bec42c6382a08_187.png)

你可以自行导入pandas、NumPy甚至scikit-learn等库进行计算。平台提供的API接口返回的数据格式（如DataFrame或ndarray）也与这些库兼容。因此，你可以将平台API与这些第三方库结合使用，构建复杂的策略。

![](img/b231298999ea643af49bec42c6382a08_189.png)

![](img/b231298999ea643af49bec42c6382a08_181.png)

![](img/b231298999ea643af49bec42c6382a08_191.png)

![](img/b231298999ea643af49bec42c6382a08_193.png)

本节课中我们一起学习了Ricequant量化平台的基本框架。我们了解了三个核心函数（`init`, `before_trading`, `handle_bar`）的分工与用法，掌握了如何配置回测的关键参数（时间、资金、频率），并知道了平台支持与主流Python数据分析库结合使用。这是编写和运行量化策略的第一步。