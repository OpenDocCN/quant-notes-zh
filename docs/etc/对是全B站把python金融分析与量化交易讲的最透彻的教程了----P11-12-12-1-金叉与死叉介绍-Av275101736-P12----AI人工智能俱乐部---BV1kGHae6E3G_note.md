# Python金融量化：P11：12.12.1-金叉与死叉介绍 📈

![](img/6f8bba1594defe06545d793e4eb3f841_0.png)

在本节课中，我们将要学习双均线策略中的两个核心概念：**金叉**与**死叉**。这是量化交易中判断买卖时机的基础工具。

![](img/6f8bba1594defe06545d793e4eb3f841_2.png)

![](img/6f8bba1594defe06545d793e4eb3f841_3.png)

![](img/6f8bba1594defe06545d793e4eb3f841_4.png)

![](img/6f8bba1594defe06545d793e4eb3f841_5.png)

![](img/6f8bba1594defe06545d793e4eb3f841_6.png)

![](img/6f8bba1594defe06545d793e4eb3f841_7.png)

## 概述：什么是双均线？

![](img/6f8bba1594defe06545d793e4eb3f841_8.png)

![](img/6f8bba1594defe06545d793e4eb3f841_9.png)

![](img/6f8bba1594defe06545d793e4eb3f841_10.png)

![](img/6f8bba1594defe06545d793e4eb3f841_11.png)

![](img/6f8bba1594defe06545d793e4eb3f841_12.png)

在交易数据中，双均线策略是一种常用的技术分析方法。首先需要解释什么是“双均线”。

![](img/6f8bba1594defe06545d793e4eb3f841_13.png)

以股票数据为例，例如苹果公司的股票。在每个交易日，我们都能获得一个收盘价。如果只看每一天的收盘价，我们只能看到价格的波动，但很难从整体上把握其变化趋势。

![](img/6f8bba1594defe06545d793e4eb3f841_15.png)

![](img/6f8bba1594defe06545d793e4eb3f841_16.png)

![](img/6f8bba1594defe06545d793e4eb3f841_17.png)

为了解决这个问题，我们引入“均线”的概念。双均线，顾名思义，包含两条均线：一条是**短期均线**，另一条是**长期均线**。

![](img/6f8bba1594defe06545d793e4eb3f841_18.png)

![](img/6f8bba1594defe06545d793e4eb3f841_19.png)

![](img/6f8bba1594defe06545d793e4eb3f841_20.png)

![](img/6f8bba1594defe06545d793e4eb3f841_21.png)

![](img/6f8bba1594defe06545d793e4eb3f841_22.png)

## 短期均线与长期均线

![](img/6f8bba1594defe06545d793e4eb3f841_23.png)

![](img/6f8bba1594defe06545d793e4eb3f841_24.png)

![](img/6f8bba1594defe06545d793e4eb3f841_25.png)

![](img/6f8bba1594defe06545d793e4eb3f841_26.png)

以下是短期均线与长期均线的定义与区别：

![](img/6f8bba1594defe06545d793e4eb3f841_28.png)

**短期均线**的计算周期较短。例如，我们可以计算过去5个交易日（一周）收盘价的平均值。具体做法是：将数据划分为多个连续的5天窗口，计算每个窗口内数据的均值，然后用这些均值连成一条新的曲线。这条曲线反映了价格的短期趋势。

![](img/6f8bba1594defe06545d793e4eb3f841_30.png)

![](img/6f8bba1594defe06545d793e4eb3f841_31.png)

![](img/6f8bba1594defe06545d793e4eb3f841_32.png)

用公式表示，对于时间点 `t`，其5日简单移动平均（SMA）为：
`SMA_5(t) = (Price(t) + Price(t-1) + ... + Price(t-4)) / 5`

![](img/6f8bba1594defe06545d793e4eb3f841_33.png)

![](img/6f8bba1594defe06545d793e4eb3f841_34.png)

![](img/6f8bba1594defe06545d793e4eb3f841_35.png)

![](img/6f8bba1594defe06545d793e4eb3f841_36.png)

![](img/6f8bba1594defe06545d793e4eb3f841_37.png)

![](img/6f8bba1594defe06545d793e4eb3f841_38.png)

**长期均线**的计算周期则较长。常见的周期有20天（约一个月）、40天或60天等。其计算方法与短期均线相同，只是窗口更大。例如，20日均线的公式为：
`SMA_20(t) = (Price(t) + Price(t-1) + ... + Price(t-19)) / 20`

![](img/6f8bba1594defe06545d793e4eb3f841_39.png)

![](img/6f8bba1594defe06545d793e4eb3f841_40.png)

![](img/6f8bba1594defe06545d793e4eb3f841_42.png)

长线和短线唯一的区别在于计算均值时所选取的**时间窗口大小**：短线窗口小，对近期价格变化敏感；长线窗口大，反映长期趋势，更为平滑。

![](img/6f8bba1594defe06545d793e4eb3f841_44.png)

![](img/6f8bba1594defe06545d793e4eb3f841_45.png)

![](img/6f8bba1594defe06545d793e4eb3f841_46.png)

![](img/6f8bba1594defe06545d793e4eb3f841_48.png)

## 金叉与死叉：买卖信号

![](img/6f8bba1594defe06545d793e4eb3f841_49.png)

![](img/6f8bba1594defe06545d793e4eb3f841_51.png)

绘制出短期均线（例如用红色表示）和长期均线（例如用蓝色表示）后，它们有什么用呢？关键在于观察两条线的交叉点。

![](img/6f8bba1594defe06545d793e4eb3f841_52.png)

![](img/6f8bba1594defe06545d793e4eb3f841_53.png)

![](img/6f8bba1594defe06545d793e4eb3f841_54.png)

![](img/6f8bba1594defe06545d793e4eb3f841_55.png)

![](img/6f8bba1594defe06545d793e4eb3f841_56.png)

以下是两种关键的交叉信号：

![](img/6f8bba1594defe06545d793e4eb3f841_58.png)

![](img/6f8bba1594defe06545d793e4eb3f841_59.png)

![](img/6f8bba1594defe06545d793e4eb3f841_60.png)

1.  **黄金交叉（金叉） - 买点**
    当短期均线从下方**向上穿越**长期均线时，形成黄金交叉。这通常意味着短期上涨动能开始超越长期趋势，预示着价格可能开始上涨，是一个潜在的**买入信号**。

![](img/6f8bba1594defe06545d793e4eb3f841_61.png)

![](img/6f8bba1594defe06545d793e4eb3f841_62.png)

2.  **死亡交叉（死叉） - 卖点**
    当短期均线从上方**向下穿越**长期均线时，形成死亡交叉。这通常意味着短期下跌动能开始显现，预示着价格可能开始下跌，是一个潜在的**卖出信号**。

![](img/6f8bba1594defe06545d793e4eb3f841_64.png)

![](img/6f8bba1594defe06545d793e4eb3f841_65.png)

![](img/6f8bba1594defe06545d793e4eb3f841_66.png)

![](img/6f8bba1594defe06545d793e4eb3f841_67.png)

![](img/6f8bba1594defe06545d793e4eb3f841_68.png)

## 策略思路与应用

![](img/6f8bba1594defe06545d793e4eb3f841_69.png)

![](img/6f8bba1594defe06545d793e4eb3f841_70.png)

理解了买点（金叉）和卖点（死叉）后，我们就可以设计一个简单的交易策略：在出现**金叉**时买入股票，在出现**死叉**时卖出股票。

![](img/6f8bba1594defe06545d793e4eb3f841_72.png)

如果这个策略能准确识别趋势，理论上可以实现“低买高卖”，从而获得收益。本节课后续将使用Python代码演示如何计算双均线，并识别这些交叉点，从而构建一个基础的双均线交易策略。

![](img/6f8bba1594defe06545d793e4eb3f841_73.png)

![](img/6f8bba1594defe06545d793e4eb3f841_74.png)

![](img/6f8bba1594defe06545d793e4eb3f841_75.png)

![](img/6f8bba1594defe06545d793e4eb3f841_76.png)

![](img/6f8bba1594defe06545d793e4eb3f841_77.png)

## 总结

![](img/6f8bba1594defe06545d793e4eb3f841_78.png)

![](img/6f8bba1594defe06545d793e4eb3f841_79.png)

![](img/6f8bba1594defe06545d793e4eb3f841_80.png)

![](img/6f8bba1594defe06545d793e4eb3f841_81.png)

![](img/6f8bba1594defe06545d793e4eb3f841_82.png)

本节课我们一起学习了双均线策略的基础知识。我们明确了**短期均线**和**长期均线**的定义与区别，并重点掌握了两个核心买卖信号：**黄金交叉（金叉）** 和 **死亡交叉（死叉）**。金叉作为买入信号，提示上涨可能开始；死叉作为卖出信号，提示下跌可能开始。这是构建自动化交易策略的重要第一步。