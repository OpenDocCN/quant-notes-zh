# Python金融量化分析：P23：Ricequant交易平台简介 🚀

![](img/d43c0329aa135e0683aea880ea4b948b_1.png)

![](img/d43c0329aa135e0683aea880ea4b948b_3.png)

在本节课中，我们将学习如何使用Ricequant量化交易平台。我们将了解平台的基本界面、核心的编程框架以及如何配置策略回测参数。通过本节内容，你将能够理解并开始在Ricequant平台上编写和运行自己的量化策略。

![](img/d43c0329aa135e0683aea880ea4b948b_5.png)

![](img/d43c0329aa135e0683aea880ea4b948b_7.png)

![](img/d43c0329aa135e0683aea880ea4b948b_9.png)

## 平台界面与策略模板

![](img/d43c0329aa135e0683aea880ea4b948b_11.png)

![](img/d43c0329aa135e0683aea880ea4b948b_13.png)

首先，我们演示量化平台的使用方法。点击“量化平台”，然后选择“免费使用”。

![](img/d43c0329aa135e0683aea880ea4b948b_15.png)

![](img/d43c0329aa135e0683aea880ea4b948b_17.png)

![](img/d43c0329aa135e0683aea880ea4b948b_19.png)

![](img/d43c0329aa135e0683aea880ea4b948b_1.png)

![](img/d43c0329aa135e0683aea880ea4b948b_21.png)

![](img/d43c0329aa135e0683aea880ea4b948b_23.png)

进入平台后，会看到一些预设的策略。这些是平台提供的简单示例，方便用户熟悉环境。

![](img/d43c0329aa135e0683aea880ea4b948b_25.png)

![](img/d43c0329aa135e0683aea880ea4b948b_27.png)

![](img/d43c0329aa135e0683aea880ea4b948b_29.png)

![](img/d43c0329aa135e0683aea880ea4b948b_3.png)

![](img/d43c0329aa135e0683aea880ea4b948b_31.png)

![](img/d43c0329aa135e0683aea880ea4b948b_33.png)

这些策略展示了基本的编写方法。策略名称是平台默认设置的。我们点开第一个“入门策略”查看。

![](img/d43c0329aa135e0683aea880ea4b948b_35.png)

![](img/d43c0329aa135e0683aea880ea4b948b_37.png)

![](img/d43c0329aa135e0683aea880ea4b948b_39.png)

![](img/d43c0329aa135e0683aea880ea4b948b_5.png)

![](img/d43c0329aa135e0683aea880ea4b948b_41.png)

![](img/d43c0329aa135e0683aea880ea4b948b_43.png)

![](img/d43c0329aa135e0683aea880ea4b948b_45.png)

打开后，界面类似于Jupyter Notebook，可以在此编写Python代码。

![](img/d43c0329aa135e0683aea880ea4b948b_47.png)

![](img/d43c0329aa135e0683aea880ea4b948b_49.png)

![](img/d43c0329aa135e0683aea880ea4b948b_7.png)

![](img/d43c0329aa135e0683aea880ea4b948b_51.png)

![](img/d43c0329aa135e0683aea880ea4b948b_53.png)

![](img/d43c0329aa135e0683aea880ea4b948b_55.png)

但平台对代码结构有特定要求。

![](img/d43c0329aa135e0683aea880ea4b948b_57.png)

![](img/d43c0329aa135e0683aea880ea4b948b_59.png)

![](img/d43c0329aa135e0683aea880ea4b948b_9.png)

![](img/d43c0329aa135e0683aea880ea4b948b_61.png)

![](img/d43c0329aa135e0683aea880ea4b948b_63.png)

![](img/d43c0329aa135e0683aea880ea4b948b_65.png)

界面上列出了所有操作。首先可以看到三个函数。

![](img/d43c0329aa135e0683aea880ea4b948b_67.png)

![](img/d43c0329aa135e0683aea880ea4b948b_69.png)

![](img/d43c0329aa135e0683aea880ea4b948b_11.png)

![](img/d43c0329aa135e0683aea880ea4b948b_71.png)

![](img/d43c0329aa135e0683aea880ea4b948b_73.png)

![](img/d43c0329aa135e0683aea880ea4b948b_75.png)

第一个函数、第二个函数和第三个函数。

![](img/d43c0329aa135e0683aea880ea4b948b_77.png)

![](img/d43c0329aa135e0683aea880ea4b948b_79.png)

![](img/d43c0329aa135e0683aea880ea4b948b_13.png)

![](img/d43c0329aa135e0683aea880ea4b948b_81.png)

![](img/d43c0329aa135e0683aea880ea4b948b_83.png)

![](img/d43c0329aa135e0683aea880ea4b948b_85.png)

这三个函数不是用户随意编写的，而是平台要求必须使用的。

![](img/d43c0329aa135e0683aea880ea4b948b_87.png)

![](img/d43c0329aa135e0683aea880ea4b948b_89.png)

![](img/d43c0329aa135e0683aea880ea4b948b_15.png)

![](img/d43c0329aa135e0683aea880ea4b948b_91.png)

![](img/d43c0329aa135e0683aea880ea4b948b_93.png)

所有操作都需要围绕这三个函数来实现。

![](img/d43c0329aa135e0683aea880ea4b948b_95.png)

![](img/d43c0329aa135e0683aea880ea4b948b_97.png)

![](img/d43c0329aa135e0683aea880ea4b948b_99.png)

![](img/d43c0329aa135e0683aea880ea4b948b_17.png)

![](img/d43c0329aa135e0683aea880ea4b948b_101.png)

![](img/d43c0329aa135e0683aea880ea4b948b_103.png)

## 核心函数详解

![](img/d43c0329aa135e0683aea880ea4b948b_105.png)

![](img/d43c0329aa135e0683aea880ea4b948b_107.png)

![](img/d43c0329aa135e0683aea880ea4b948b_109.png)

上一节我们介绍了平台的策略模板界面，本节中我们来详细看看这三个核心函数的作用。

![](img/d43c0329aa135e0683aea880ea4b948b_111.png)

![](img/d43c0329aa135e0683aea880ea4b948b_113.png)

![](img/d43c0329aa135e0683aea880ea4b948b_115.png)

以下是三个核心函数的解释：

![](img/d43c0329aa135e0683aea880ea4b948b_117.png)

![](img/d43c0329aa135e0683aea880ea4b948b_119.png)

**1. `init` 函数**
第一个函数是 `init`，它用于初始化。在Python中，类有一个构造函数。构造函数中一般会传入参数并对这些参数进行赋值或其他基本操作。`init` 函数就相当于Python中的构造函数。实例化一个对象时，需要先执行其构造函数。这里也一样，无论后续操作多复杂，执行时 `init` 函数会最先被调用。

![](img/d43c0329aa135e0683aea880ea4b948b_121.png)

![](img/d43c0329aa135e0683aea880ea4b948b_123.png)

![](img/d43c0329aa135e0683aea880ea4b948b_125.png)

**2. `context` 参数**
观察三个函数，会发现它们都有一个共同的参数 `context`。`context` 表示一个全局上下文对象。例如，在初始化时创建的对象、指定的股票代码或获取的股票数据，都可以存入这个全局对象。在预处理或实际计算时，若需要用到这些数据，可以通过传入 `context` 参数来获取。它帮助我们在不同函数间传递数据和状态。

![](img/d43c0329aa135e0683aea880ea4b948b_127.png)

![](img/d43c0329aa135e0683aea880ea4b948b_129.png)

例如，在 `init` 中写入：
```python
context.s1 = ‘000001.XSHE’
```
在另一个函数中，可以通过 `context.s1` 来访问这个变量。

![](img/d43c0329aa135e0683aea880ea4b948b_131.png)

![](img/d43c0329aa135e0683aea880ea4b948b_133.png)

![](img/d43c0329aa135e0683aea880ea4b948b_135.png)

**3. 函数的执行顺序**
`init` 函数只在策略开始时执行一次。而另外两个函数会被执行很多次。举个例子：在 `init` 函数中选定一只股票，这个选择是固定的。后续策略需要每天对这只股票进行操作，比如获取每日收盘价、判断买卖点。这些每日重复的操作写在另外两个函数中。

![](img/d43c0329aa135e0683aea880ea4b948b_137.png)

![](img/d43c0329aa135e0683aea880ea4b948b_139.png)

## 每日处理函数

![](img/d43c0329aa135e0683aea880ea4b948b_141.png)

![](img/d43c0329aa135e0683aea880ea4b948b_143.png)

![](img/d43c0329aa135e0683aea880ea4b948b_145.png)

我们已经了解了只执行一次的初始化函数，现在来看看那些需要每日重复执行的函数。

![](img/d43c0329aa135e0683aea880ea4b948b_147.png)

红色框内有两个函数。第一个函数 `before_trading` 在每个交易日开始前被执行。第二个函数 `handle_bar` 在每个交易日（或每分钟）都会执行。

![](img/d43c0329aa135e0683aea880ea4b948b_149.png)

![](img/d43c0329aa135e0683aea880ea4b948b_151.png)

`before_trading` 的作用是进行数据预处理。在新数据到来后、实际交易发生前，可以在此函数中对数据进行处理。

![](img/d43c0329aa135e0683aea880ea4b948b_153.png)

![](img/d43c0329aa135e0683aea880ea4b948b_155.png)

![](img/d43c0329aa135e0683aea880ea4b948b_157.png)

`handle_bar` 则是策略实际逻辑的核心。基于策略的判断、下单买卖等具体操作都在此函数中执行。

![](img/d43c0329aa135e0683aea880ea4b948b_159.png)

![](img/d43c0329aa135e0683aea880ea4b948b_161.png)

简单总结：
*   `init`：初始化，只执行一次。
*   `before_trading`：每日开盘前预处理，每日执行。
*   `handle_bar`：策略逻辑与交易执行，每日（或每分钟）执行。

![](img/d43c0329aa135e0683aea880ea4b948b_163.png)

![](img/d43c0329aa135e0683aea880ea4b948b_165.png)

## 回测参数配置

![](img/d43c0329aa135e0683aea880ea4b948b_167.png)

![](img/d43c0329aa135e0683aea880ea4b948b_169.png)

刚才提到函数会每日执行，那么这个“每日”是如何体现的呢？接下来我们看看如何配置策略的回测环境。

![](img/d43c0329aa135e0683aea880ea4b948b_171.png)

![](img/d43c0329aa135e0683aea880ea4b948b_173.png)

右边区域有几个非常重要的参数需要用户自行设置。进行回测是为了检验策略在历史数据上的表现，因此需要指定回测的时间范围和初始条件。

![](img/d43c0329aa135e0683aea880ea4b948b_175.png)

![](img/d43c0329aa135e0683aea880ea4b948b_177.png)

![](img/d43c0329aa135e0683aea880ea4b948b_179.png)

以下是需要配置的关键参数列表：

![](img/d43c0329aa135e0683aea880ea4b948b_181.png)

![](img/d43c0329aa135e0683aea880ea4b948b_183.png)

![](img/d43c0329aa135e0683aea880ea4b948b_185.png)

1.  **起始日期与终止日期**：定义回测的时间段，从哪一天开始到哪一天结束。
2.  **初始资金**：策略开始运行时拥有的资金量，例如 `100000` 代表十万元。回测结束后会基于此计算收益率等指标。
3.  **频率**：选择策略执行的频率，是“每日”执行还是“每分钟”执行。这决定了 `handle_bar` 函数的调用间隔。本教程示例按每日执行。

![](img/d43c0329aa135e0683aea880ea4b948b_187.png)

![](img/d43c0329aa135e0683aea880ea4b948b_189.png)

## 第三方库与平台API结合

![](img/d43c0329aa135e0683aea880ea4b948b_191.png)

![](img/d43c0329aa135e0683aea880ea4b948b_193.png)

在实际任务中，你可以根据需求选择每日或每分钟的频率。平台只提供这两种选项。如果你想进行更复杂的统计计算，代码中可以导入常用的Python库。

![](img/d43c0329aa135e0683aea880ea4b948b_195.png)

![](img/d43c0329aa135e0683aea880ea4b948b_197.png)

平台允许导入如 `pandas as pd` 或 `numpy as np` 等库。之前课程讲过如何使用这些库，导入后即可进行数据计算。

![](img/d43c0329aa135e0683aea880ea4b948b_199.png)

![](img/d43c0329aa135e0683aea880ea4b948b_201.png)

![](img/d43c0329aa135e0683aea880ea4b948b_203.png)

平台提供的API接口，其返回值通常也是 `DataFrame` 或 `ndarray` 格式。因此，平台API可以与 `pandas`、`numpy` 乃至 `scikit-learn` 等库无缝结合使用。你并非只能使用平台API，而是可以将Python常用库与平台API结合，共同构建策略。

![](img/d43c0329aa135e0683aea880ea4b948b_205.png)

![](img/d43c0329aa135e0683aea880ea4b948b_207.png)

## 总结

![](img/d43c0329aa135e0683aea880ea4b948b_209.png)

![](img/d43c0329aa135e0683aea880ea4b948b_211.png)

![](img/d43c0329aa135e0683aea880ea4b948b_213.png)

本节课中我们一起学习了Ricequant量化平台的基本使用。我们介绍了三个核心函数（`init`, `before_trading`, `handle_bar`）的作用与执行顺序，讲解了全局上下文对象 `context` 的用途，并说明了如何配置回测的起止日期、初始资金和运行频率。最后，我们了解到在平台上可以灵活结合Python数据科学库与平台原生API来开发策略。掌握这些基础知识是开始编写和回测量化策略的第一步。