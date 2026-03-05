# Python金融分析与量化交易实战课程：P12：金叉与死叉介绍 📈

![](img/78216acd16fb51ff63f9ec942b9d4bfa_0.png)

![](img/78216acd16fb51ff63f9ec942b9d4bfa_2.png)

![](img/78216acd16fb51ff63f9ec942b9d4bfa_4.png)

![](img/78216acd16fb51ff63f9ec942b9d4bfa_6.png)

![](img/78216acd16fb51ff63f9ec942b9d4bfa_8.png)

![](img/78216acd16fb51ff63f9ec942b9d4bfa_10.png)

在本节课中，我们将要学习量化交易中的一个基础且重要的概念——双均线策略，并重点理解其核心信号：**金叉**与**死叉**。我们将探讨如何通过计算不同周期的移动平均线来识别市场趋势，并据此制定简单的买卖策略。

![](img/78216acd16fb51ff63f9ec942b9d4bfa_12.png)

![](img/78216acd16fb51ff63f9ec942b9d4bfa_14.png)

![](img/78216acd16fb51ff63f9ec942b9d4bfa_16.png)

![](img/78216acd16fb51ff63f9ec942b9d4bfa_18.png)

## 双均线策略的基本概念

![](img/78216acd16fb51ff63f9ec942b9d4bfa_20.png)

![](img/78216acd16fb51ff63f9ec942b9d4bfa_22.png)

![](img/78216acd16fb51ff63f9ec942b9d4bfa_24.png)

![](img/78216acd16fb51ff63f9ec942b9d4bfa_26.png)

上一节我们介绍了交易数据分析的基础，本节中我们来看看如何利用均线来观察趋势。在股票交易中，我们每天都能获得一个收盘价。然而，仅观察每日价格的浮动，很难从整体上把握其变化趋势。

![](img/78216acd16fb51ff63f9ec942b9d4bfa_28.png)

![](img/78216acd16fb51ff63f9ec942b9d4bfa_30.png)

![](img/78216acd16fb51ff63f9ec942b9d4bfa_32.png)

为了解决这个问题，我们引入**移动平均线**的概念。它通过计算一段时间内价格的平均值，来平滑短期波动，从而更清晰地显示价格趋势。“双均线”指的是同时使用两条不同周期的移动平均线。

![](img/78216acd16fb51ff63f9ec942b9d4bfa_34.png)

![](img/78216acd16fb51ff63f9ec942b9d4bfa_36.png)

![](img/78216acd16fb51ff63f9ec942b9d4bfa_38.png)

![](img/78216acd16fb51ff63f9ec942b9d4bfa_40.png)

## 短均线与长均线

![](img/78216acd16fb51ff63f9ec942b9d4bfa_42.png)

![](img/78216acd16fb51ff63f9ec942b9d4bfa_44.png)

![](img/78216acd16fb51ff63f9ec942b9d4bfa_46.png)

![](img/78216acd16fb51ff63f9ec942b9d4bfa_48.png)

以下是两条均线的定义：

![](img/78216acd16fb51ff63f9ec942b9d4bfa_50.png)

![](img/78216acd16fb51ff63f9ec942b9d4bfa_52.png)

![](img/78216acd16fb51ff63f9ec942b9d4bfa_54.png)

*   **短均线**：计算周期较短的移动平均线，例如5日（一周）或10日均线。它反应了价格的短期趋势。
*   **长均线**：计算周期较长的移动平均线，例如20日（一月）或60日均线。它反应了价格的中长期趋势。

![](img/78216acd16fb51ff63f9ec942b9d4bfa_56.png)

![](img/78216acd16fb51ff63f9ec942b9d4bfa_58.png)

![](img/78216acd16fb51ff63f9ec942b9d4bfa_60.png)

![](img/78216acd16fb51ff63f9ec942b9d4bfa_62.png)

它们的核心区别在于计算时所选取的**时间窗口**大小不同。短均线窗口小，对价格变化更敏感；长均线窗口大，趋势更平滑稳定。

![](img/78216acd16fb51ff63f9ec942b9d4bfa_64.png)

![](img/78216acd16fb51ff63f9ec942b9d4bfa_66.png)

![](img/78216acd16fb51ff63f9ec942b9d4bfa_68.png)

![](img/78216acd16fb51ff63f9ec942b9d4bfa_70.png)

计算N日简单移动平均线（SMA）的公式为：
**SMA = (P1 + P2 + ... + PN) / N**
其中，P1 到 PN 是过去N个交易日的收盘价。

![](img/78216acd16fb51ff63f9ec942b9d4bfa_72.png)

![](img/78216acd16fb51ff63f9ec942b9d4bfa_74.png)

![](img/78216acd16fb51ff63f9ec942b9d4bfa_76.png)

## 金叉与死叉：买卖信号

![](img/78216acd16fb51ff63f9ec942b9d4bfa_78.png)

![](img/78216acd16fb51ff63f9ec942b9d4bfa_80.png)

![](img/78216acd16fb51ff63f9ec942b9d4bfa_82.png)

![](img/78216acd16fb51ff63f9ec942b9d4bfa_84.png)

当我们把短均线和长均线绘制在同一张图上时，它们的交叉点就构成了交易信号。

![](img/78216acd16fb51ff63f9ec942b9d4bfa_86.png)

![](img/78216acd16fb51ff63f9ec942b9d4bfa_88.png)

![](img/78216acd16fb51ff63f9ec942b9d4bfa_90.png)

![](img/78216acd16fb51ff63f9ec942b9d4bfa_92.png)

![](img/78216acd16fb51ff63f9ec942b9d4bfa_76.png)

![](img/78216acd16fb51ff63f9ec942b9d4bfa_94.png)

![](img/78216acd16fb51ff63f9ec942b9d4bfa_96.png)

![](img/78216acd16fb51ff63f9ec942b9d4bfa_98.png)

![](img/78216acd16fb51ff63f9ec942b9d4bfa_100.png)

观察上图，我们可以识别出两个关键信号点：

![](img/78216acd16fb51ff63f9ec942b9d4bfa_102.png)

![](img/78216acd16fb51ff63f9ec942b9d4bfa_104.png)

![](img/78216acd16fb51ff63f9ec942b9d4bfa_106.png)

1.  **买点（金叉）**：当**短均线从下方向上穿越长均线**时，形成**黄金交叉（Golden Cross）**。这通常意味着短期趋势开始走强，并可能带动长期趋势向上，被视为一个**买入信号**。
2.  **卖点（死叉）**：当**短均线从上方向下穿越长均线**时，形成**死亡交叉（Death Cross）**。这通常意味着短期趋势开始走弱，并可能带动长期趋势向下，被视为一个**卖出信号**。

![](img/78216acd16fb51ff63f9ec942b9d4bfa_108.png)

![](img/78216acd16fb51ff63f9ec942b9d4bfa_110.png)

![](img/78216acd16fb51ff63f9ec942b9d4bfa_112.png)

![](img/78216acd16fb51ff63f9ec942b9d4bfa_114.png)

## 策略逻辑与应用

![](img/78216acd16fb51ff63f9ec942b9d4bfa_116.png)

![](img/78216acd16fb51ff63f9ec942b9d4bfa_118.png)

![](img/78216acd16fb51ff63f9ec942b9d4bfa_120.png)

![](img/78216acd16fb51ff63f9ec942b9d4bfa_122.png)

基于金叉和死叉，我们可以构建一个简单的交易策略：在出现**金叉**时买入股票，在出现**死叉**时卖出股票。理论上，如果每次都能正确识别这些信号并执行交易，就能实现“低买高卖”，从而获得收益。

![](img/78216acd16fb51ff63f9ec942b9d4bfa_124.png)

![](img/78216acd16fb51ff63f9ec942b9d4bfa_126.png)

![](img/78216acd16fb51ff63f9ec942b9d4bfa_128.png)

这个策略的核心思想是：利用短期均线对趋势的快速反应和长期均线对趋势的稳定确认，来捕捉市场的主要波动。在接下来的课程中，我们将使用Python来实际计算移动平均线，并自动化地寻找金叉与死叉信号，从而将这一策略付诸实践。

![](img/78216acd16fb51ff63f9ec942b9d4bfa_130.png)

![](img/78216acd16fb51ff63f9ec942b9d4bfa_132.png)

![](img/78216acd16fb51ff63f9ec942b9d4bfa_134.png)

![](img/78216acd16fb51ff63f9ec942b9d4bfa_136.png)

## 总结

![](img/78216acd16fb51ff63f9ec942b9d4bfa_138.png)

![](img/78216acd16fb51ff63f9ec942b9d4bfa_140.png)

![](img/78216acd16fb51ff63f9ec942b9d4bfa_142.png)

本节课中我们一起学习了双均线策略的基础。我们了解了如何通过计算**短均线**和**长均线**来观察股票价格趋势，并掌握了两个关键的交易信号：**金叉（买入信号）** 和 **死叉（卖出信号）**。记住这个简单的策略逻辑：金叉时买入，死叉时卖出。这是许多复杂量化策略的基石。