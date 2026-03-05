# 从零开始量化：2：脚本语法及行情走势图 📈

![](img/ad1b565eaae59af9f54410bee2af40ea_1.png)

![](img/ad1b565eaae59af9f54410bee2af40ea_3.png)

![](img/ad1b565eaae59af9f54410bee2af40ea_5.png)

在本节课中，我们将学习MC（MultiCharts）中脚本的基本语法、核心概念以及行情走势图的本质。理解这些是编写有效量化策略的基础。

## 概述

上一节我们介绍了MC软件的基本使用。本节中，我们将深入探讨如何编写策略代码，即脚本。我们将学习脚本的分类、语法规则，并理解行情数据在MC中是如何被处理和执行的。

## 脚本及语法

![](img/ad1b565eaae59af9f54410bee2af40ea_7.png)

![](img/ad1b565eaae59af9f54410bee2af40ea_9.png)

一个完整的EasyLanguage程序被称为**脚本**。在公式编辑器中，无论是函数、指标还是信号，都被统称为一个脚本。

![](img/ad1b565eaae59af9f54410bee2af40ea_11.png)

![](img/ad1b565eaae59af9f54410bee2af40ea_13.png)

![](img/ad1b565eaae59af9f54410bee2af40ea_15.png)

脚本主要分为三类：
*   **信号脚本**：会产生交易指令的脚本。核心是包含`buy`（买入）、`sell`（平多）、`sellshort`（卖空）、`buytocover`（平空）等交易指令。
*   **指标脚本**：在图表中画图，帮助进行交易分析的脚本。核心是包含`plot`等画图函数，**绝对不能**出现交易指令。
*   **函数脚本**：独立运行、可被重复调用的脚本，用于封装常用功能，如浮动止盈止损。

![](img/ad1b565eaae59af9f54410bee2af40ea_17.png)

![](img/ad1b565eaae59af9f54410bee2af40ea_19.png)

![](img/ad1b565eaae59af9f54410bee2af40ea_21.png)

![](img/ad1b565eaae59af9f54410bee2af40ea_23.png)

![](img/ad1b565eaae59af9f54410bee2af40ea_25.png)

![](img/ad1b565eaae59af9f54410bee2af40ea_27.png)

EasyLanguage脚本由至少一行代码表达式组成。**每行代码都是一个完整的指令，必须以分号 `;` 结束。**

![](img/ad1b565eaae59af9f54410bee2af40ea_29.png)

![](img/ad1b565eaae59af9f54410bee2af40ea_31.png)

例如，一个简单的开平仓信号代码：
```easylanguage
buy next bar at open;
sell next bar at close or lower;
```
代码编写不区分大小写，顺序是从左到右，而脚本的执行顺序是从上到下。

![](img/ad1b565eaae59af9f54410bee2af40ea_33.png)

![](img/ad1b565eaae59af9f54410bee2af40ea_35.png)

![](img/ad1b565eaae59af9f54410bee2af40ea_37.png)

![](img/ad1b565eaae59af9f54410bee2af40ea_39.png)

![](img/ad1b565eaae59af9f54410bee2af40ea_41.png)

**编译**是将我们编写的、人类可读的代码“翻译”成计算机底层能理解的指令的过程。只有编译成功的脚本才能被加载到图表中使用。

![](img/ad1b565eaae59af9f54410bee2af40ea_43.png)

![](img/ad1b565eaae59af9f54410bee2af40ea_44.png)

## 行情走势图与代码执行逻辑

![](img/ad1b565eaae59af9f54410bee2af40ea_46.png)

![](img/ad1b565eaae59af9f54410bee2af40ea_48.png)

![](img/ad1b565eaae59af9f54410bee2af40ea_50.png)

行情走势图由一系列按时间顺序排列的K线组成。对于量化交易者，必须深刻理解：**每一根K线都是计算机根据数据（开盘价、最高价、最低价、收盘价、成交量等）绘制出来的。**

![](img/ad1b565eaae59af9f54410bee2af40ea_52.png)

![](img/ad1b565eaae59af9f54410bee2af40ea_54.png)

![](img/ad1b565eaae59af9f54410bee2af40ea_56.png)

![](img/ad1b565eaae59af9f54410bee2af40ea_58.png)

![](img/ad1b565eaae59af9f54410bee2af40ea_60.png)

在MC中，脚本代码会**在每一根K线上都执行一遍**。这是最重要的核心概念。

当你将信号加载到图表时，可以设置“指标运算参考的最大Bar数量”。例如，设为5，则前5根K线仅作为数据参考，不执行交易逻辑，从第6根K线开始，代码才会真正运行。

![](img/ad1b565eaae59af9f54410bee2af40ea_62.png)

![](img/ad1b565eaae59af9f54410bee2af40ea_64.png)

![](img/ad1b565eaae59af9f54410bee2af40ea_66.png)

![](img/ad1b565eaae59af9f54410bee2af40ea_68.png)

![](img/ad1b565eaae59af9f54410bee2af40ea_70.png)

代码执行时，有一个“当前K线”的概念：
*   **当前K线**：用 `currentbar` 表示，指代码正在运算的那一根K线。
*   **前一根K线**：用 `barago` 表示，也可以用中括号加偏移量表示，例如 `close[1]` 代表前一根K线的收盘价。

![](img/ad1b565eaae59af9f54410bee2af40ea_72.png)

![](img/ad1b565eaae59af9f54410bee2af40ea_74.png)

当代码处理完当前K线，移动到下一根时，原来的当前K线就变成了前一根K线。

![](img/ad1b565eaae59af9f54410bee2af40ea_76.png)

我们可以用 `print` 函数输出信息来验证这个过程：
```easylanguage
print("当前Bar编号: ", currentbar, " 最高价: ", high, " 前Bar最高价: ", high[1]);
```
输出结果会显示每一根K线执行时，这些变量的值。

K线的基本价格用以下关键字表示：
*   开盘价：`open` 或 `O`
*   最高价：`high` 或 `H`
*   最低价：`low` 或 `L`
*   收盘价：`close` 或 `C`

一个简单的条件开仓例子：
```easylanguage
if close > high[1] then buy next bar at market;
```
此代码意为：如果当前K线收盘价高于前一根K线最高价，则在下一根K线以市价买入。

## 语言要素

![](img/ad1b565eaae59af9f54410bee2af40ea_78.png)

![](img/ad1b565eaae59af9f54410bee2af40ea_80.png)

在EasyLanguage中，使用关键字、常量、变量、运算符等组成交易条件。

![](img/ad1b565eaae59af9f54410bee2af40ea_82.png)

![](img/ad1b565eaae59af9f54410bee2af40ea_84.png)

以下是关键的语言要素：

![](img/ad1b565eaae59af9f54410bee2af40ea_86.png)

![](img/ad1b565eaae59af9f54410bee2af40ea_88.png)

*   **关键字**：又称保留字，是语言的内置函数，在编辑器中通常显示为蓝色。例如 `buy`, `sell`, `plot`, `print` 等。
*   **忽略字**：主要是介词，显示为红色，用于使语句更易读，但非必需。例如 `at`, `on` 等。
*   **数据类型**：共有三种。
    *   **数值**：整数或小数，如 `100`, `3.14`。
    *   **字符串**：用双引号引住的文本，如 `"MA"`。
    *   **布尔值**：`true`（真）或 `false`（假）。
*   **时间和日期**：
    *   日期：使用 `date` 函数，返回格式如 `1220926`（代表2022年9月26日）。
    *   时间：使用 `time` 函数返回至分钟（如 `1500`代表15:00），`time_s` 函数返回至秒。
    *   **重要提示**：MC中K线的时间戳标识的是该K线的**结束时刻**。这与一些国内平台（如文华财经）以K线开始时刻为标识的习惯不同。此外，不同平台在主力合约切换、指数合约合成规则上也可能存在差异，这会导致同一策略在不同平台回测结果不同，需要特别注意。

![](img/ad1b565eaae59af9f54410bee2af40ea_90.png)

![](img/ad1b565eaae59af9f54410bee2af40ea_92.png)

![](img/ad1b565eaae59af9f54410bee2af40ea_94.png)

## 总结

![](img/ad1b565eaae59af9f54410bee2af40ea_96.png)

![](img/ad1b565eaae59af9f54410bee2af40ea_98.png)

本节课我们一起学习了MC量化策略开发的基础知识。我们明确了脚本的三种类型及其区别，掌握了代码必须以分号结束的基本语法。最重要的是，我们深入理解了**行情走势图的数据本质**和**代码在每一根K线上顺序执行**的核心逻辑。同时，我们也认识了EasyLanguage的关键字、数据类型以及时间和日期的表示方法，并注意到了不同平台在数据细节上可能存在的差异。理解这些是后续编写复杂策略的坚实基石。