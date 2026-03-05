# Python量化交易：P12：双均线策略与金叉死叉介绍 📈

![](img/a59a5ff6ae71b607cf6bc065855e193c_0.png)

![](img/a59a5ff6ae71b607cf6bc065855e193c_2.png)

![](img/a59a5ff6ae71b607cf6bc065855e193c_3.png)

在本节课中，我们将要学习量化交易中的一个基础且重要的概念：双均线策略。我们将重点理解什么是移动平均线，以及如何利用短期均线与长期均线的交叉点（金叉与死叉）来制定买卖策略。

![](img/a59a5ff6ae71b607cf6bc065855e193c_4.png)

![](img/a59a5ff6ae71b607cf6bc065855e193c_5.png)

![](img/a59a5ff6ae71b607cf6bc065855e193c_6.png)

![](img/a59a5ff6ae71b607cf6bc065855e193c_7.png)

![](img/a59a5ff6ae71b607cf6bc065855e193c_8.png)

![](img/a59a5ff6ae71b607cf6bc065855e193c_9.png)

## 理解双均线策略

![](img/a59a5ff6ae71b607cf6bc065855e193c_10.png)

![](img/a59a5ff6ae71b607cf6bc065855e193c_11.png)

![](img/a59a5ff6ae71b607cf6bc065855e193c_12.png)

![](img/a59a5ff6ae71b607cf6bc065855e193c_13.png)

在交易数据中，双均线策略是一种常用的技术分析方法。首先，我们需要解释什么是“双均线”。

![](img/a59a5ff6ae71b607cf6bc065855e193c_15.png)

![](img/a59a5ff6ae71b607cf6bc065855e193c_16.png)

以股票数据为例，比如苹果公司的股票。在每个交易日，我们都能获得一个收盘价。如果只看每一天的收盘价，我们只能看到价格的单点波动，很难从整体上把握其变化趋势。

![](img/a59a5ff6ae71b607cf6bc065855e193c_17.png)

![](img/a59a5ff6ae71b607cf6bc065855e193c_18.png)

![](img/a59a5ff6ae71b607cf6bc065855e193c_19.png)

![](img/a59a5ff6ae71b607cf6bc065855e193c_20.png)

![](img/a59a5ff6ae71b607cf6bc065855e193c_21.png)

![](img/a59a5ff6ae71b607cf6bc065855e193c_22.png)

为了解决这个问题，我们引入移动平均线的概念。移动平均线通过计算一段时间内价格的平均值，来平滑价格波动，从而更清晰地展示趋势。“双均线”指的是我们同时计算两条不同周期的移动平均线。

![](img/a59a5ff6ae71b607cf6bc065855e193c_23.png)

![](img/a59a5ff6ae71b607cf6bc065855e193c_24.png)

![](img/a59a5ff6ae71b607cf6bc065855e193c_25.png)

![](img/a59a5ff6ae71b607cf6bc065855e193c_26.png)

![](img/a59a5ff6ae71b607cf6bc065855e193c_28.png)

## 短期均线与长期均线

以下是两条均线的定义：

![](img/a59a5ff6ae71b607cf6bc065855e193c_30.png)

![](img/a59a5ff6ae71b607cf6bc065855e193c_31.png)

![](img/a59a5ff6ae71b607cf6bc065855e193c_32.png)

![](img/a59a5ff6ae71b607cf6bc065855e193c_33.png)

![](img/a59a5ff6ae71b607cf6bc065855e193c_34.png)

![](img/a59a5ff6ae71b607cf6bc065855e193c_35.png)

*   **短期均线**：计算周期较短，例如5天或10天。它反应了价格的近期变化趋势，对价格波动更敏感。
    其计算公式可以表示为：
    `短期均线值 = (过去N个交易日的收盘价之和) / N`
    其中，N代表周期，例如5。

![](img/a59a5ff6ae71b607cf6bc065855e193c_36.png)

![](img/a59a5ff6ae71b607cf6bc065855e193c_37.png)

![](img/a59a5ff6ae71b607cf6bc065855e193c_38.png)

![](img/a59a5ff6ae71b607cf6bc065855e193c_39.png)

![](img/a59a5ff6ae71b607cf6bc065855e193c_40.png)

![](img/a59a5ff6ae71b607cf6bc065855e193c_42.png)

*   **长期均线**：计算周期较长，例如20天、40天或60天。它反应了价格的中长期趋势，走势更为平缓。
    其计算公式与短期均线相同，只是周期M更大：
    `长期均线值 = (过去M个交易日的收盘价之和) / M`

![](img/a59a5ff6ae71b607cf6bc065855e193c_44.png)

![](img/a59a5ff6ae71b607cf6bc065855e193c_45.png)

![](img/a59a5ff6ae71b607cf6bc065855e193c_46.png)

通过计算，我们可以得到两条曲线：一条波动较大的短期均线，和一条相对平滑的长期均线。将这两条线绘制在价格走势图上，就构成了双均线系统。

![](img/a59a5ff6ae71b607cf6bc065855e193c_48.png)

![](img/a59a5ff6ae71b607cf6bc065855e193c_49.png)

![](img/a59a5ff6ae71b607cf6bc065855e193c_51.png)

## 金叉与死叉：买卖信号

![](img/a59a5ff6ae71b607cf6bc065855e193c_52.png)

![](img/a59a5ff6ae71b607cf6bc065855e193c_53.png)

![](img/a59a5ff6ae71b607cf6bc065855e193c_54.png)

![](img/a59a5ff6ae71b607cf6bc065855e193c_55.png)

![](img/a59a5ff6ae71b607cf6bc065855e193c_56.png)

上一节我们介绍了如何计算两条均线，本节中我们来看看如何利用它们的交叉点来判断买卖时机。核心在于观察短期均线与长期均线的相对位置变化。

![](img/a59a5ff6ae71b607cf6bc065855e193c_58.png)

![](img/a59a5ff6ae71b607cf6bc065855e193c_59.png)

![](img/a59a5ff6ae71b607cf6bc065855e193c_60.png)

![](img/a59a5ff6ae71b607cf6bc065855e193c_61.png)

以下是两种关键的交叉信号：

![](img/a59a5ff6ae71b607cf6bc065855e193c_62.png)

![](img/a59a5ff6ae71b607cf6bc065855e193c_64.png)

*   **黄金交叉（金叉）**：当短期均线从下方**向上**穿越长期均线时，形成金叉。这通常被视为**买入信号**。它表明近期价格上涨动力增强，可能开启一段上升趋势。
    ![](img/a59a5ff6ae71b607cf6bc065855e193c_48.png)

![](img/a59a5ff6ae71b607cf6bc065855e193c_65.png)

![](img/a59a5ff6ae71b607cf6bc065855e193c_66.png)

![](img/a59a5ff6ae71b607cf6bc065855e193c_67.png)

![](img/a59a5ff6ae71b607cf6bc065855e193c_68.png)

![](img/a59a5ff6ae71b607cf6bc065855e193c_69.png)

![](img/a59a5ff6ae71b607cf6bc065855e193c_70.png)

*   **死亡交叉（死叉）**：当短期均线从上方**向下**穿越长期均线时，形成死叉。这通常被视为**卖出信号**。它表明近期价格下跌压力增大，可能开启一段下降趋势。
    ![](img/a59a5ff6ae71b607cf6bc065855e193c_64.png)

基于这个原理，我们可以设计一个简单的交易策略：在金叉出现时买入股票，在死叉出现时卖出股票。理论上，如果能够准确识别这些交叉点并执行交易，就能实现低买高卖，获取收益。

![](img/a59a5ff6ae71b607cf6bc065855e193c_72.png)

![](img/a59a5ff6ae71b607cf6bc065855e193c_73.png)

![](img/a59a5ff6ae71b607cf6bc065855e193c_74.png)

![](img/a59a5ff6ae71b607cf6bc065855e193c_75.png)

![](img/a59a5ff6ae71b607cf6bc065855e193c_76.png)

## 总结

![](img/a59a5ff6ae71b607cf6bc065855e193c_77.png)

![](img/a59a5ff6ae71b607cf6bc065855e193c_78.png)

![](img/a59a5ff6ae71b607cf6bc065855e193c_79.png)

![](img/a59a5ff6ae71b607cf6bc065855e193c_80.png)

![](img/a59a5ff6ae71b607cf6bc065855e193c_81.png)

![](img/a59a5ff6ae71b607cf6bc065855e193c_82.png)

本节课中我们一起学习了双均线策略的核心概念。我们首先解释了移动平均线如何帮助观察趋势，然后定义了短期均线和长期均线。最后，我们重点学习了**金叉**和**死叉**这两个关键的交易信号，它们分别对应着潜在的买入和卖出时机。理解这一基础策略，是后续使用Python进行量化交易分析和回测的重要第一步。