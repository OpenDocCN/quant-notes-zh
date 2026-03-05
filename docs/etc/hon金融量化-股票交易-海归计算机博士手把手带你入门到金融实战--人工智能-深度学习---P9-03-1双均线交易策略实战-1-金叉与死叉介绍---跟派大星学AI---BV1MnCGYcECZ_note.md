# 量化交易全教程：P9：03-1：双均线交易策略实战-1-金叉与死叉介绍 📈

![](img/8e1dfaf19fdb2adefc259fbe69335476_1.png)

![](img/8e1dfaf19fdb2adefc259fbe69335476_3.png)

![](img/8e1dfaf19fdb2adefc259fbe69335476_5.png)

在本节课中，我们将要学习双均线交易策略的核心概念，特别是如何通过计算短期和长期移动平均线来识别市场的买入和卖出信号，即“金叉”与“死叉”。

![](img/8e1dfaf19fdb2adefc259fbe69335476_7.png)

![](img/8e1dfaf19fdb2adefc259fbe69335476_9.png)

## 双均线策略的基本概念

![](img/8e1dfaf19fdb2adefc259fbe69335476_11.png)

![](img/8e1dfaf19fdb2adefc259fbe69335476_13.png)

上一节我们介绍了量化交易的基础，本节中我们来看看一个经典的技术分析策略——双均线策略。

![](img/8e1dfaf19fdb2adefc259fbe69335476_15.png)

![](img/8e1dfaf19fdb2adefc259fbe69335476_17.png)

在股票交易数据中，双均线策略是一种常用的分析方法。双均线指的是两条移动平均线，一条是短期均线，另一条是长期均线。

![](img/8e1dfaf19fdb2adefc259fbe69335476_19.png)

![](img/8e1dfaf19fdb2adefc259fbe69335476_21.png)

以苹果股票数据为例，每个交易日都会有一个收盘价。如果只看每一天的价格点，虽然能看到浮动，但很难从整体上把握价格的变化趋势。

![](img/8e1dfaf19fdb2adefc259fbe69335476_23.png)

![](img/8e1dfaf19fdb2adefc259fbe69335476_25.png)

![](img/8e1dfaf19fdb2adefc259fbe69335476_27.png)

## 移动平均线的计算

![](img/8e1dfaf19fdb2adefc259fbe69335476_29.png)

![](img/8e1dfaf19fdb2adefc259fbe69335476_31.png)

为了更好描述趋势，我们引入移动平均线的概念。移动平均线通过计算一定时间窗口内价格的平均值，来平滑价格波动，反映趋势。

![](img/8e1dfaf19fdb2adefc259fbe69335476_33.png)

![](img/8e1dfaf19fdb2adefc259fbe69335476_35.png)

**短均线**通常周期较短，例如5天或10天。**长均线**周期较长，例如20天、40天或60天。

![](img/8e1dfaf19fdb2adefc259fbe69335476_37.png)

![](img/8e1dfaf19fdb2adefc259fbe69335476_39.png)

以下是计算移动平均线的公式：

![](img/8e1dfaf19fdb2adefc259fbe69335476_41.png)

![](img/8e1dfaf19fdb2adefc259fbe69335476_43.png)

`移动平均值 = (周期内所有收盘价之和) / 周期天数`

![](img/8e1dfaf19fdb2adefc259fbe69335476_45.png)

![](img/8e1dfaf19fdb2adefc259fbe69335476_47.png)

例如，计算5日移动平均线，就是取最近5个交易日的收盘价，计算它们的算术平均值，并随着时间窗口滑动，不断计算新的平均值。

![](img/8e1dfaf19fdb2adefc259fbe69335476_49.png)

![](img/8e1dfaf19fdb2adefc259fbe69335476_51.png)

## 金叉与死叉：买卖信号

![](img/8e1dfaf19fdb2adefc259fbe69335476_53.png)

![](img/8e1dfaf19fdb2adefc259fbe69335476_55.png)

![](img/8e1dfaf19fdb2adefc259fbe69335476_57.png)

做出短期和长期两条移动平均线后，它们之间的交叉点就构成了交易信号。

*   **金叉（黄金交叉）**：当短期均线从下方向上穿越长期均线时，形成金叉。这通常被视为**买入信号**，预示着短期内价格可能上涨。
*   **死叉（死亡交叉）**：当短期均线从上方向下穿越长期均线时，形成死叉。这通常被视为**卖出信号**，预示着短期内价格可能下跌。

![](img/8e1dfaf19fdb2adefc259fbe69335476_59.png)

![](img/8e1dfaf19fdb2adefc259fbe69335476_61.png)

金叉意味着近期市场动能开始超越长期趋势，是潜在的上涨起点。死叉则意味着近期动能转弱，是潜在的下跌起点。

## 策略逻辑与应用

![](img/8e1dfaf19fdb2adefc259fbe69335476_63.png)

![](img/8e1dfaf19fdb2adefc259fbe69335476_65.png)

既然我们知道了如何识别买点和卖点，就可以设计一个简单的交易策略。

![](img/8e1dfaf19fdb2adefc259fbe69335476_67.png)

![](img/8e1dfaf19fdb2adefc259fbe69335476_69.png)

![](img/8e1dfaf19fdb2adefc259fbe69335476_71.png)

策略逻辑如下：
1.  当出现**金叉**时，执行买入操作。
2.  当出现**死叉**时，执行卖出操作。

![](img/8e1dfaf19fdb2adefc259fbe69335476_73.png)

理论上，如果每次都能准确预测并执行，就能实现低买高卖，从而获得收益。这个策略的核心在于利用均线的滞后性来捕捉趋势的转折点。

![](img/8e1dfaf19fdb2adefc259fbe69335476_75.png)

![](img/8e1dfaf19fdb2adefc259fbe69335476_77.png)

本节课中我们一起学习了双均线策略的基础，包括移动平均线的概念、计算方法，以及如何通过“金叉”和“死叉”来识别买卖信号。在接下来的课程中，我们将使用Python代码来实际构建并回测这个策略。