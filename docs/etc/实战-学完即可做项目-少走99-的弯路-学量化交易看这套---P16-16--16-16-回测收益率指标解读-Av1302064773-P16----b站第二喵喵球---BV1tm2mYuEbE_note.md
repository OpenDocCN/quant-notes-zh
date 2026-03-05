# 量化交易实战课程：P16：回测收益率指标解读 📈

在本节课中，我们将要学习量化策略评估中最基础且核心的指标——回测收益率。我们将理解其含义、计算公式，并通过Python代码演示如何从历史数据中计算它。

当我们完成一个交易策略后，必须在历史数据中进行测试，以判断策略的盈亏情况。但“赚了”或“赔了”是模糊的描述。我们需要更精确的指标来回答：赚了多少？相比市场基准有何优势？承担了哪些风险？与其他策略相比有何特点？这些问题的答案就是策略评估指标。

![](img/42739e3c4dd7e058e485bd50a72cf3b0_1.png)

以下是后续课程中会使用的一个策略回测平台的结果示例，其中包含了多个核心评估指标。本节课，我们将聚焦于第一个指标。

![](img/42739e3c4dd7e058e485bd50a72cf3b0_1.png)

我们暂时不深入介绍这个平台，只需关注图中列出的各项指标。本节课的目标是理解这些基本指标的含义和计算方法。公式可能看起来复杂，但重点是掌握其通俗理解，以便能读懂任何回测报告中的结果。

![](img/42739e3c4dd7e058e485bd50a72cf3b0_3.png)

![](img/42739e3c4dd7e058e485bd50a72cf3b0_3.png)

接下来，我们将逐一讲解这些指标。首先从“回测收益率”开始。

![](img/42739e3c4dd7e058e485bd50a72cf3b0_5.png)

![](img/42739e3c4dd7e058e485bd50a72cf3b0_5.png)

![](img/42739e3c4dd7e058e485bd50a72cf3b0_7.png)

## 回测收益率详解

![](img/42739e3c4dd7e058e485bd50a72cf3b0_9.png)

回测收益率是最基本、最重要的指标。它衡量了在回测周期内，策略最终获得的收益相对于初始本金的增长比例。

例如，你基于2015年至2020年的某只股票数据设计了一个策略，规定了买入和卖出时机。回测收益率告诉你，在回测结束时，你的总资产相比最初投入的本金增加了多少百分比。图中显示约为4%，这意味着多年操作后，你的资金相对于本金增长了约4%。

![](img/42739e3c4dd7e058e485bd50a72cf3b0_11.png)

![](img/42739e3c4dd7e058e485bd50a72cf3b0_23.png)

![](img/42739e3c4dd7e058e485bd50a72cf3b0_13.png)

### 计算公式

![](img/42739e3c4dd7e058e485bd50a72cf3b0_15.png)

![](img/42739e3c4dd7e058e485bd50a72cf3b0_17.png)

回测收益率的计算公式如下：

![](img/42739e3c4dd7e058e485bd50a72cf3b0_19.png)

**回测收益率 = (期末资产总值 - 期初资产总值) / 期初资产总值**

![](img/42739e3c4dd7e058e485bd50a72cf3b0_21.png)

用符号表示为：
`Return = (P_t - P_0) / P_0`

其中：
*   `P_t` 代表回测结束时的资产总值（卖出所有头寸后的总现金）。
*   `P_0` 代表回测开始时的初始本金。

这个指标直观地反映了策略的绝对盈利能力，是评估策略表现的首要依据。

![](img/42739e3c4dd7e058e485bd50a72cf3b0_23.png)

![](img/42739e3c4dd7e058e485bd50a72cf3b0_25.png)

## 使用Python计算与可视化

![](img/42739e3c4dd7e058e485bd50a72cf3b0_25.png)

![](img/42739e3c4dd7e058e485bd50a72cf3b0_27.png)

理解了概念后，我们通过Python代码实际计算一下。我们将使用一份示例股票数据，将其模拟为策略回测后的每日资产净值序列，并计算其回测收益率。

首先，导入必要的工具包并加载数据。

```python
import pandas as pd
import matplotlib.pyplot as plt
# 设置中文字体（如果需显示中文标签）
plt.rcParams['font.sans-serif'] = ['SimHei']
plt.rcParams['axes.unicode_minus'] = False

# 读取数据，假设第一列是日期
data = pd.read_csv('your_stock_data.csv', index_col=0, parse_dates=True)
# 假设‘close’列是我们策略回测后的每日资产净值
portfolio_value = data['close']
print(portfolio_value.head())
```

![](img/42739e3c4dd7e058e485bd50a72cf3b0_29.png)

![](img/42739e3c4dd7e058e485bd50a72cf3b0_13.png)

数据加载后，我们开始计算。首先，我们计算相对于期初净值的每日累积收益比率，这可以帮助我们观察资金曲线的变化。

```python
# 计算每日净值相对于期初净值（第0天）的比值
cumulative_return_ratio = portfolio_value / portfolio_value.iloc[0]
# 绘制资金曲线
plt.figure(figsize=(10, 6))
plt.plot(cumulative_return_ratio.index, cumulative_return_ratio.values)
plt.title('策略累积收益曲线（相对于期初）')
plt.xlabel('日期')
plt.ylabel('净值比率')
plt.grid(True)
plt.show()
```

![](img/42739e3c4dd7e058e485bd50a72cf3b0_33.png)

![](img/42739e3c4dd7e058e485bd50a72cf3b0_31.png)

上图展示了从期初开始，资产净值随时间的变化过程。现在，我们来计算整个回测周期的最终收益率。

![](img/42739e3c4dd7e058e485bd50a72cf3b0_33.png)

根据公式，最终的回测收益率就是期末净值相对于期初净值的增长率。

![](img/42739e3c4dd7e058e485bd50a72cf3b0_35.png)

```python
# 计算最终回测收益率
final_return = (portfolio_value.iloc[-1] - portfolio_value.iloc[0]) / portfolio_value.iloc[0]
# 等价于 cumulative_return_ratio.iloc[-1] - 1

![](img/42739e3c4dd7e058e485bd50a72cf3b0_37.png)

# 将结果组织成DataFrame以便清晰展示
result_df = pd.DataFrame({
    ‘回测收益率’: [final_return]
}, index=[‘最终结果’])
print(result_df)
```

![](img/42739e3c4dd7e058e485bd50a72cf3b0_39.png)

![](img/42739e3c4dd7e058e485bd50a72cf3b0_41.png)

![](img/42739e3c4dd7e058e485bd50a72cf3b0_49.png)

执行代码后，我们便得到了该策略在回测数据时间区间内的总收益率。这个值可能是正数（盈利），也可能是负数（亏损）。在本示例中，我们只是用股价数据模拟了净值，实际策略的净值曲线会根据买卖信号发生变化。

![](img/42739e3c4dd7e058e485bd50a72cf3b0_43.png)

## 总结

![](img/42739e3c4dd7e058e485bd50a72cf3b0_45.png)

本节课中，我们一起学习了量化策略回测的核心评估指标——**回测收益率**。

![](img/42739e3c4dd7e058e485bd50a72cf3b0_47.png)

我们明确了它的定义：策略在回测周期内获得的**总收益**与**初始本金**的比率。它是最直观的策略盈利能力的体现。

![](img/42739e3c4dd7e058e485bd50a72cf3b0_49.png)

我们掌握了其计算公式：`(期末资产 - 期初本金) / 期初本金`。

![](img/42739e3c4dd7e058e485bd50a72cf3b0_51.png)

最后，我们使用Python演示了如何从一串代表每日资产净值的数据中，计算出最终的策略回测收益率，并绘制了累积收益曲线进行可视化观察。理解这个指标是读懂复杂回测报告、评估策略有效性的第一步。在接下来的课程中，我们将继续学习其他重要的风险评估和收益质量指标。