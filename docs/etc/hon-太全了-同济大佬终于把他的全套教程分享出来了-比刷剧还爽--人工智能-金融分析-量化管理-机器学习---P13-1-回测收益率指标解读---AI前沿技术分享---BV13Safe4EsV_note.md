# 量化投资基础：P13：1-回测收益率指标解读 📈

在本节课中，我们将要学习量化投资策略评估中最基础、最核心的指标——回测收益率。我们将理解它的含义、计算公式，并通过Python代码演示如何计算和可视化它。

![](img/4bee4345bc4d02d8475e0159f81eb605_1.png)

当我们完成一个投资策略的构建后，需要在历史数据中进行测试，以判断策略的盈利或亏损情况。然而，“盈利”或“亏损”是模糊的描述。我们需要更精确的指标来回答：具体赚了多少？相比市场基准有何优势？承担了哪些风险？这些风险是否值得？以及我们的策略相比其他策略有何特点？这些问题的答案，就构成了策略的评估指标体系。

以下是本节课将要介绍的核心评估指标之一。

## 回测收益率

![](img/4bee4345bc4d02d8475e0159f81eb605_3.png)

![](img/4bee4345bc4d02d8475e0159f81eb605_5.png)

![](img/4bee4345bc4d02d8475e0159f81eb605_7.png)

回测收益率是最基本、最常用的策略评估指标。它衡量的是，在回测周期内，策略最终获得的收益相对于初始本金的增长比例。例如，一个4%的回测收益率意味着，经过策略操作后，你的总资产相比初始本金增长了4%。

![](img/4bee4345bc4d02d8475e0159f81eb605_9.png)

它的计算公式如下：

**回测收益率 = (期末资产总值 - 期初资产总值) / 期初资产总值**

![](img/4bee4345bc4d02d8475e0159f81eb605_11.png)

![](img/4bee4345bc4d02d8475e0159f81eb605_13.png)

用符号表示为：
**Return = (P_t - P_0) / P_0**

![](img/4bee4345bc4d02d8475e0159f81eb605_15.png)

![](img/4bee4345bc4d02d8475e0159f81eb605_17.png)

其中：
*   **P_t** 代表策略在回测期末的资产总值（卖出所有头寸后的总现金）。
*   **P_0** 代表策略在回测期初投入的初始本金。

![](img/4bee4345bc4d02d8475e0159f81eb605_19.png)

![](img/4bee4345bc4d02d8475e0159f81eb605_21.png)

这个公式的本质是计算资产增长的**相对比例**，而非绝对的金额，这使得不同资金规模的策略之间可以进行比较。

上一节我们介绍了回测收益率的概念和公式，本节中我们来看看如何用Python代码进行计算和展示。

![](img/4bee4345bc4d02d8475e0159f81eb605_23.png)

我们将使用一份示例股票数据（假设它是应用了某个策略后的每日资产净值序列），来计算其回测收益率。

![](img/4bee4345bc4d02d8475e0159f81eb605_25.png)

首先，进行环境准备和数据加载。

![](img/4bee4345bc4d02d8475e0159f81eb605_27.png)

```python
# 导入必要的工具包
import pandas as pd
import matplotlib.pyplot as plt

# 设置中文字体，方便后续图表显示中文
plt.rcParams['font.sans-serif'] = ['SimHei']
plt.rcParams['axes.unicode_minus'] = False

# 读取数据，假设数据文件为‘strategy_value.csv’，第一列是日期
data = pd.read_csv('strategy_value.csv', index_col=0)
# 查看数据前几行
print(data.head())
```

假设我们的数据 `data[‘value’]` 列代表应用策略后每天的资产净值。接下来，我们基于这份数据计算回测收益率。

计算步骤如下：
1.  计算每日资产净值相对于期初净值的比值。
2.  根据公式，用期末的比值减去1，得到最终的回测收益率。

![](img/4bee4345bc4d02d8475e0159f81eb605_29.png)

![](img/4bee4345bc4d02d8475e0159f81eb605_31.png)

以下是具体的代码实现：

![](img/4bee4345bc4d02d8475e0159f81eb605_33.png)

![](img/4bee4345bc4d02d8475e0159f81eb605_35.png)

```python
# 1. 计算每日资产相对于期初的比值
# 期初净值为 data['value'].iloc[0]
relative_value = data['value'] / data['value'].iloc[0]

![](img/4bee4345bc4d02d8475e0159f81eb605_37.png)

![](img/4bee4345bc4d02d8475e0159f81eb605_39.png)

# 可视化资产相对价值的变化
plt.figure(figsize=(10, 6))
relative_value.plot()
plt.title('策略资产相对价值变化曲线')
plt.xlabel('日期')
plt.ylabel('相对价值 (期初=1)')
plt.grid(True)
plt.show()

# 2. 计算最终的回测收益率
# 期末相对价值为 relative_value.iloc[-1]
backtest_return = relative_value.iloc[-1] - 1

![](img/4bee4345bc4d02d8475e0159f81eb605_41.png)

![](img/4bee4345bc4d02d8475e0159f81eb605_43.png)

# 将结果整理成DataFrame以便清晰展示
result_df = pd.DataFrame({
    ‘回测收益率’: [backtest_return]
}, index=[‘最终结果’])
print(result_df)
```

![](img/4bee4345bc4d02d8475e0159f81eb605_45.png)

![](img/4bee4345bc4d02d8475e0159f81eb605_47.png)

执行以上代码后，你会看到一张图表，展示了资产净值随时间增长（或下跌）的相对轨迹。同时，会输出一个表格，其中包含了计算出的最终回测收益率数值。

![](img/4bee4345bc4d02d8475e0159f81eb605_49.png)

本节课中我们一起学习了量化策略评估的基石——回测收益率。我们明确了它的定义是衡量策略相对于初始本金的最终收益比例，掌握了其计算公式 **Return = (P_t - P_0) / P_0**，并亲手使用Python代码从历史数据中计算出了这个指标。理解回测收益率是读懂任何策略回测报告的第一步。在接下来的课程中，我们将继续学习其他重要的评估指标，以更全面地衡量策略的表现。