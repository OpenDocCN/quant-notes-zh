# 机器学习与量化交易：P12：金叉与死叉介绍 📈

![](img/950260a5fa489d429f8adbba67cff51b_0.png)

![](img/950260a5fa489d429f8adbba67cff51b_2.png)

![](img/950260a5fa489d429f8adbba67cff51b_4.png)

![](img/950260a5fa489d429f8adbba67cff51b_6.png)

![](img/950260a5fa489d429f8adbba67cff51b_8.png)

![](img/950260a5fa489d429f8adbba67cff51b_10.png)

在本节课中，我们将要学习量化交易中的一个基础且重要的概念：双均线策略。我们将重点理解什么是金叉与死叉，以及它们如何作为买卖信号来指导交易决策。

![](img/950260a5fa489d429f8adbba67cff51b_12.png)

![](img/950260a5fa489d429f8adbba67cff51b_14.png)

![](img/950260a5fa489d429f8adbba67cff51b_16.png)

![](img/950260a5fa489d429f8adbba67cff51b_18.png)

## 双均线策略概述

![](img/950260a5fa489d429f8adbba67cff51b_20.png)

![](img/950260a5fa489d429f8adbba67cff51b_22.png)

![](img/950260a5fa489d429f8adbba67cff51b_24.png)

上一节我们介绍了交易数据的基本概念，本节中我们来看看如何利用均线来分析数据趋势。双均线策略的核心是使用两条不同周期的移动平均线来捕捉价格趋势的变化。

![](img/950260a5fa489d429f8adbba67cff51b_26.png)

![](img/950260a5fa489d429f8adbba67cff51b_28.png)

![](img/950260a5fa489d429f8adbba67cff51b_30.png)

![](img/950260a5fa489d429f8adbba67cff51b_32.png)

## 什么是双均线

![](img/950260a5fa489d429f8adbba67cff51b_34.png)

![](img/950260a5fa489d429f8adbba67cff51b_36.png)

![](img/950260a5fa489d429f8adbba67cff51b_38.png)

在股票数据中，我们每天都能得到一个收盘价。如果只看单个价格点的浮动，很难把握整体的趋势。为了更好地描述趋势，我们引入移动平均线的概念。

![](img/950260a5fa489d429f8adbba67cff51b_40.png)

![](img/950260a5fa489d429f8adbba67cff51b_42.png)

![](img/950260a5fa489d429f8adbba67cff51b_44.png)

“双均线”中的“双”指的是两条线：一条是短期移动平均线（短均线），另一条是长期移动平均线（长均线）。

![](img/950260a5fa489d429f8adbba67cff51b_46.png)

![](img/950260a5fa489d429f8adbba67cff51b_48.png)

![](img/950260a5fa489d429f8adbba67cff51b_50.png)

![](img/950260a5fa489d429f8adbba67cff51b_52.png)

## 短均线与长均线的计算

![](img/950260a5fa489d429f8adbba67cff51b_54.png)

![](img/950260a5fa489d429f8adbba67cff51b_56.png)

![](img/950260a5fa489d429f8adbba67cff51b_58.png)

我们先解释短均线。例如，我们可以选择5个交易日（即一周）作为一个计算周期。对于一系列收盘价数据，我们取连续5天的价格计算其平均值，并以此作为新的数据点。通过滑动这个5天的窗口，我们可以计算出一系列平均值，从而形成一条平滑的曲线。这条曲线能更好地反映短期内的价格变化趋势。

![](img/950260a5fa489d429f8adbba67cff51b_60.png)

![](img/950260a5fa489d429f8adbba67cff51b_62.png)

![](img/950260a5fa489d429f8adbba67cff51b_64.png)

![](img/950260a5fa489d429f8adbba67cff51b_66.png)

**公式表示**：`短均线值 = (P1 + P2 + P3 + P4 + P5) / 5`，其中 P 代表每日收盘价。

![](img/950260a5fa489d429f8adbba67cff51b_68.png)

![](img/950260a5fa489d429f8adbba67cff51b_70.png)

![](img/950260a5fa489d429f8adbba67cff51b_72.png)

长均线的原理完全相同，唯一的区别在于计算周期更长。常见的周期有20天（约一个月）、40天或60天等。长均线反映了更长期的价格趋势。

![](img/950260a5fa489d429f8adbba67cff51b_74.png)

![](img/950260a5fa489d429f8adbba67cff51b_76.png)

![](img/950260a5fa489d429f8adbba67cff51b_78.png)

![](img/950260a5fa489d429f8adbba67cff51b_80.png)

**公式表示**：`长均线值 = (P1 + P2 + ... + Pn) / n`，其中 n 是长期周期（如20）。

![](img/950260a5fa489d429f8adbba67cff51b_82.png)

![](img/950260a5fa489d429f8adbba67cff51b_84.png)

![](img/950260a5fa489d429f8adbba67cff51b_86.png)

![](img/950260a5fa489d429f8adbba67cff51b_88.png)

## 金叉与死叉：买卖信号

![](img/950260a5fa489d429f8adbba67cff51b_90.png)

![](img/950260a5fa489d429f8adbba67cff51b_92.png)

![](img/950260a5fa489d429f8adbba67cff51b_94.png)

绘制出短均线和长均线后，它们有什么用呢？关键在于观察两条线的交叉点。

![](img/950260a5fa489d429f8adbba67cff51b_96.png)

![](img/950260a5fa489d429f8adbba67cff51b_98.png)

![](img/950260a5fa489d429f8adbba67cff51b_100.png)

![](img/950260a5fa489d429f8adbba67cff51b_102.png)

以下是两种关键的交叉情况：

![](img/950260a5fa489d429f8adbba67cff51b_104.png)

![](img/950260a5fa489d429f8adbba67cff51b_106.png)

![](img/950260a5fa489d429f8adbba67cff51b_108.png)

*   **金叉（黄金交叉）**：当短均线从下方**向上**穿越长均线时，形成金叉。这通常被视为一个**买入信号**。它意味着短期趋势开始转强，并可能引领价格向上突破长期的盘整或下跌趋势。
*   **死叉（死亡交叉）**：当短均线从上方**向下**穿越长均线时，形成死叉。这通常被视为一个**卖出信号**。它意味着短期趋势开始走弱，价格可能即将进入下跌阶段。

![](img/950260a5fa489d429f8adbba67cff51b_110.png)

![](img/950260a5fa489d429f8adbba67cff51b_112.png)

![](img/950260a5fa489d429f8adbba67cff51b_114.png)

![](img/950260a5fa489d429f8adbba67cff51b_116.png)

## 策略构建思路

![](img/950260a5fa489d429f8adbba67cff51b_118.png)

![](img/950260a5fa489d429f8adbba67cff51b_120.png)

![](img/950260a5fa489d429f8adbba67cff51b_122.png)

理解了金叉和死叉的含义后，我们就可以构建一个简单的交易策略：在金叉出现时买入股票，在死叉出现时卖出股票。理论上，如果能够准确识别这些信号并执行交易，就能实现“低买高卖”，从而获得收益。

![](img/950260a5fa489d429f8adbba67cff51b_124.png)

![](img/950260a5fa489d429f8adbba67cff51b_126.png)

![](img/950260a5fa489d429f8adbba67cff51b_128.png)

![](img/950260a5fa489d429f8adbba67cff51b_130.png)

当然，实际市场更为复杂，这个基础策略需要结合其他指标和风险管理手段进行优化。在接下来的课程中，我们将使用Python代码来实际演示如何计算均线、识别金叉死叉并构建这个双均线策略。

![](img/950260a5fa489d429f8adbba67cff51b_132.png)

![](img/950260a5fa489d429f8adbba67cff51b_134.png)

![](img/950260a5fa489d429f8adbba67cff51b_136.png)

## 总结

![](img/950260a5fa489d429f8adbba67cff51b_138.png)

![](img/950260a5fa489d429f8adbba67cff51b_140.png)

![](img/950260a5fa489d429f8adbba67cff51b_142.png)

本节课中我们一起学习了双均线策略的核心概念。我们明白了短均线和长均线如何通过平滑价格数据来揭示趋势，并重点掌握了**金叉**和**死叉**这两个关键的买卖信号。金叉代表短线上穿长线的买入时机，而死叉代表短线下穿长线的卖出时机。这是构建趋势跟踪策略的重要基石。