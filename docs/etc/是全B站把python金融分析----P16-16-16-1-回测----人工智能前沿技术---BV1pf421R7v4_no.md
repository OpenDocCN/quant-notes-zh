# Python金融量化与股票交易：P16：回测收益率指标解读 📈

## 概述
在本节课中，我们将学习策略评估的核心指标之一——回测收益率。我们将了解它的含义、计算公式，并通过Python代码演示如何计算和可视化它。理解这个指标是评估任何量化交易策略表现的基础。

## 策略评估指标简介
上一节我们介绍了策略回测的基本概念。本节中，我们来看看如何具体评估一个策略的好坏。当我们完成一个交易策略的构建后，必须在历史数据中进行测试，以判断策略的盈亏情况。然而，仅仅知道“赚了”或“赔了”是不够的。

![](img/e39179c56bb28d5beb14edc292db02cc_1.png)

我们需要更精确的指标来回答一系列问题：具体赚了多少？与大盘相比，我们的策略有何优势？执行该策略会面临哪些风险？这些风险是否值得承担？以及我们的策略与其他策略相比有何优劣？这些问题的答案，就构成了策略评估指标。

以下是后续课程中会使用到的一个策略回测平台的部分结果截图。我们暂时不深入介绍这个平台，而是先聚焦于理解图中展示的这些核心评估指标。

![](img/e39179c56bb28d5beb14edc292db02cc_1.png)

我们只需要关注图中列出的几个常见指标。接下来，我将在Notebook中逐一讲解这些基本评估指标的含义、计算公式，并辅以通俗的解释。大家无需死记硬背公式，重点是理解每个指标所表达的意义，以便能看懂分析报告中的各项结果。

![](img/e39179c56bb28d5beb14edc292db02cc_3.png)

![](img/e39179c56bb28d5beb14edc292db02cc_3.png)

![](img/e39179c56bb28d5beb14edc292db02cc_5.png)

## 核心指标：回测收益率
我们先从最重要、最基础的指标——回测收益率开始。

![](img/e39179c56bb28d5beb14edc292db02cc_7.png)

![](img/e39179c56bb28d5beb14edc292db02cc_5.png)

![](img/e39179c56bb28d5beb14edc292db02cc_9.png)

### 指标含义
回测收益率衡量的是，在回测周期内，执行某个策略所获得的总收益相对于初始本金的比率。例如，你基于2015年至2020年的某只股票数据设计了一个策略，规定了买入和卖出的时机。最终，这个策略带来的盈利（或亏损）金额，除以初始投入的本金，就得到了回测收益率。它直观地告诉你，折腾了这么多年，你的本金最终增长（或缩水）了多少百分比。

![](img/e39179c56bb28d5beb14edc292db02cc_7.png)

![](img/e39179c56bb28d5beb14edc292db02cc_11.png)

### 计算公式
回测收益率的计算公式如下：

![](img/e39179c56bb28d5beb14edc292db02cc_13.png)

**回测收益率 = (期末资产总值 / 期初资产总值) - 1**

![](img/e39179c56bb28d5beb14edc292db02cc_15.png)

用符号表示为：
`Return = (P_t / P_0) - 1`
其中，`P_t` 代表回测结束时的资产总值（卖出所有头寸后的总金额），`P_0` 代表回测开始时的初始本金。

![](img/e39179c56bb28d5beb14edc292db02cc_17.png)

![](img/e39179c56bb28d5beb14edc292db02cc_19.png)

这个公式的核心思想是：计算你最终赚到的钱（分子 `P_t - P_0` 的简化形式）占你起初投入的钱（分母 `P_0`）的比例。

![](img/e39179c56bb28d5beb14edc292db02cc_21.png)

![](img/e39179c56bb28d5beb14edc292db02cc_23.png)

回测收益率是评估策略表现时最常用的指标，它直接反映了策略的盈利能力。

### 代码实现与可视化
现在，让我们通过Python代码来计算并可视化回测收益率。首先，我们导入必要的工具包并加载示例数据。

![](img/e39179c56bb28d5beb14edc292db02cc_23.png)

```python
# 导入必要的库
import pandas as pd
import matplotlib.pyplot as plt
# 设置中文字体（如果需显示中文）
plt.rcParams['font.sans-serif'] = ['SimHei']
plt.rcParams['axes.unicode_minus'] = False

# 读取数据，假设第一列是日期
data = pd.read_csv('your_stock_data.csv', index_col=0, parse_dates=True)
# 查看数据前几行
print(data.head())
```

![](img/e39179c56bb28d5beb14edc292db02cc_25.png)

![](img/e39179c56bb28d5beb14edc292db02cc_11.png)

![](img/e39179c56bb28d5beb14edc292db02cc_27.png)

假设我们读取的数据中，`‘close’` 列代表每日的收盘价，我们将以此模拟策略的资产净值走势。

![](img/e39179c56bb28d5beb14edc292db02cc_13.png)

为了计算回测收益率随时间的变化，我们需要计算每个时间点的资产净值与初始净值的比值。这相当于绘制一条“净值曲线”，其起点标准化为1。

```python
# 计算每日净值相对于初始净值的比值
# 即公式中的 (P_t / P_0)
relative_value = data['close'] / data['close'].iloc[0]

# 将结果转换为DataFrame便于后续处理
relative_value_df = pd.DataFrame(relative_value, columns=['relative_value'])

# 绘制净值曲线
relative_value_df.plot(figsize=(12, 6))
plt.title('资产净值相对变化曲线')
plt.ylabel('净值倍数 (初始值=1)')
plt.xlabel('日期')
plt.grid(True)
plt.show()
```

![](img/e39179c56bb28d5beb14edc292db02cc_29.png)

上图展示了从期初开始，你的资产（在此用股价模拟）相对于初始本金的变化过程。曲线高于1表示盈利，低于1表示亏损。

![](img/e39179c56bb28d5beb14edc292db02cc_29.png)

接下来，我们计算整个回测周期的最终回测收益率。根据公式，我们需要取最后一个时间点的净值比值，然后减去1。

![](img/e39179c56bb28d5beb14edc292db02cc_31.png)

```python
# 计算最终的回测收益率
# 即 (P_t / P_0) - 1
final_return = relative_value_df.iloc[-1, 0] - 1

![](img/e39179c56bb28d5beb14edc292db02cc_33.png)

![](img/e39179c56bb28d5beb14edc292db02cc_35.png)

# 为了展示更清晰，我们也可以计算每日的回测收益率（即每日的盈亏比例）
daily_return_series = relative_value_df['relative_value'] - 1
# 将其转换为DataFrame
result_df = pd.DataFrame(daily_return_series.values, index=daily_return_series.index, columns=['回测收益率'])

![](img/e39179c56bb28d5beb14edc292db02cc_37.png)

print("整个回测周期的最终收益率为：{:.2%}".format(final_return))
print("\n每日回测收益率预览：")
print(result_df.head())
```

![](img/e39179c56bb28d5beb14edc292db02cc_39.png)

![](img/e39179c56bb28d5beb14edc292db02cc_43.png)

通过以上代码，我们成功计算出了策略的回测收益率。这个值可能是正的（盈利），也可能是负的（亏损）。在我们的示例中，数据是股票的真实价格走势，并未应用策略。在实际操作中，`‘close’` 列应替换为你的策略在每日收盘后计算出的实际资产净值。

![](img/e39179c56bb28d5beb14edc292db02cc_41.png)

![](img/e39179c56bb28d5beb14edc292db02cc_47.png)

![](img/e39179c56bb28d5beb14edc292db02cc_43.png)

## 总结
本节课中，我们一起学习了量化策略评估的第一个核心指标——回测收益率。

![](img/e39179c56bb28d5beb14edc292db02cc_45.png)

我们首先明确了策略评估需要一系列量化指标，而不仅仅是定性的盈亏判断。接着，我们深入探讨了回测收益率的定义：它表示策略在回测期间获得的总收益与初始本金的比例。

![](img/e39179c56bb28d5beb14edc292db02cc_47.png)

我们掌握了其计算公式 **`Return = (P_t / P_0) - 1`**，并通过Python代码实践了如何从价格数据中计算每日净值相对变化以及最终的回测收益率，同时还将其可视化，以便直观观察资产的增长过程。

![](img/e39179c56bb28d5beb14edc292db02cc_49.png)

理解回测收益率是读懂任何策略回测报告的第一步。在接下来的课程中，我们将继续学习其他重要的评估指标，如年化收益率、夏普比率、最大回撤等，以构建一个完整的策略评估体系。