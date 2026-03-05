# 机器学习与量化交易：P16：回测收益率指标解读 📈

在本节课中，我们将学习量化交易策略评估中最基础且核心的指标——回测收益率。我们将理解其含义、计算公式，并通过Python代码演示如何计算和可视化它。

当我们完成一个交易策略后，必须在历史数据中进行测试，以判断策略是盈利还是亏损。但“盈利”或“亏损”本身是模糊的。我们需要更具体的指标来回答：策略具体赚了多少？相比市场基准有何优势？策略承担了哪些风险？与其他策略相比有何特点？这些问题的答案就构成了策略评估指标。

![](img/9acf772e77c5e9dfeba35adc7ac6ce7d_1.png)

以下是后续课程中会使用的一个策略回测平台示例。我们暂时不深入介绍平台本身，而是先关注它生成的评估结果中包含哪些关键指标。

![](img/9acf772e77c5e9dfeba35adc7ac6ce7d_1.png)

图中展示了一系列最常用的评估指标。本节课，我们将在Jupyter Notebook中逐一讲解这些指标的基本含义和计算公式。大家无需死记硬背公式，重点是理解每个指标所表达的核心概念，以便能看懂回测分析报告中的各项结果。

![](img/9acf772e77c5e9dfeba35adc7ac6ce7d_3.png)

![](img/9acf772e77c5e9dfeba35adc7ac6ce7d_3.png)

接下来，我们开始介绍第一个，也是最重要的指标。

![](img/9acf772e77c5e9dfeba35adc7ac6ce7d_5.png)

![](img/9acf772e77c5e9dfeba35adc7ac6ce7d_7.png)

![](img/9acf772e77c5e9dfeba35adc7ac6ce7d_5.png)

![](img/9acf772e77c5e9dfeba35adc7ac6ce7d_9.png)

## 回测收益率

回测收益率是最基本的一个指标。它衡量的是，在回测周期内，执行某个策略所获得的总收益相对于初始本金的比率。

例如，你基于2015年至2020年的某只股票数据设计了一个策略，规定了买入和卖出的时机。回测收益率就是最终告诉你，相对于最初投入的本金，你赚了（或亏了）百分之多少。图中显示约4%，意味着经过这些年的模拟交易，你的总资产相对于本金增长了约4%。

![](img/9acf772e77c5e9dfeba35adc7ac6ce7d_11.png)

![](img/9acf772e77c5e9dfeba35adc7ac6ce7d_13.png)

![](img/9acf772e77c5e9dfeba35adc7ac6ce7d_23.png)

![](img/9acf772e77c5e9dfeba35adc7ac6ce7d_15.png)

它的计算公式如下：

![](img/9acf772e77c5e9dfeba35adc7ac6ce7d_17.png)

**公式**：`回测收益率 = (期末资产总值 / 期初资产总值) - 1`

![](img/9acf772e77c5e9dfeba35adc7ac6ce7d_19.png)

![](img/9acf772e77c5e9dfeba35adc7ac6ce7d_21.png)

其中，`期末资产总值`可以理解为策略结束时的卖出总所得（包含本金和利润），`期初资产总值`就是最初投入的本金。这个指标直观地反映了策略的整体盈利能力。

![](img/9acf772e77c5e9dfeba35adc7ac6ce7d_25.png)

理解了概念后，我们通过Python代码来实际计算和可视化回测收益率。首先，导入必要的工具包并加载数据。

![](img/9acf772e77c5e9dfeba35adc7ac6ce7d_23.png)

![](img/9acf772e77c5e9dfeba35adc7ac6ce7d_9.png)

```python
# 导入必要的库
import pandas as pd
import matplotlib.pyplot as plt
# 设置中文字体（如果需显示中文）
# plt.rcParams[‘font.sans-serif‘] = [‘SimHei‘]

![](img/9acf772e77c5e9dfeba35adc7ac6ce7d_25.png)

# 读取数据，指定第一列为日期索引
data = pd.read_csv(‘your_data.csv‘, index_col=0)
# 查看数据前几行
print(data.head())
```

![](img/9acf772e77c5e9dfeba35adc7ac6ce7d_27.png)

![](img/9acf772e77c5e9dfeba35adc7ac6ce7d_11.png)

数据加载后，我们假设其中的价格序列就是我们策略回测后的每日资产净值走势。我们的目标是计算每一天相对于起始日的净值比，并最终得到总的回测收益率。

![](img/9acf772e77c5e9dfeba35adc7ac6ce7d_15.png)

计算步骤如下：
1.  计算每日资产净值与初始净值的比值。
2.  根据比值计算整个周期的回测收益率。
3.  可视化净值增长曲线。

以下是具体代码实现：

```python
# 1. 计算每日净值比：当前净值 / 初始净值
# 假设‘close‘列代表每日策略的资产净值
initial_value = data[‘close‘].iloc[0]  # 期初净值
value_ratio = data[‘close‘] / initial_value  # 每日净值比序列

# 2. 可视化净值增长曲线
plt.figure(figsize=(10, 6))
value_ratio.plot()
plt.title(‘策略净值增长曲线‘)
plt.ylabel(‘净值比 (相对于期初)‘)
plt.xlabel(‘日期‘)
plt.grid(True)
plt.show()
```

![](img/9acf772e77c5e9dfeba35adc7ac6ce7d_29.png)

![](img/9acf772e77c5e9dfeba35adc7ac6ce7d_29.png)

![](img/9acf772e77c5e9dfeba35adc7ac6ce7d_31.png)

上图展示了资产净值随时间的变化。比值大于1表示盈利，小于1表示亏损。接下来，我们计算整个回测周期的总收益率。

![](img/9acf772e77c5e9dfeba35adc7ac6ce7d_33.png)

```python
# 3. 计算总回测收益率
final_ratio = value_ratio.iloc[-1]  # 期末净值比
backtest_return = final_ratio - 1  # 根据公式：收益率 = 期末净值比 - 1

![](img/9acf772e77c5e9dfeba35adc7ac6ce7d_35.png)

# 将结果整理成DataFrame以便查看
result_df = pd.DataFrame({
    ‘回测收益率‘: [backtest_return]
}, index=[‘总收益率‘])
print(result_df)
```

![](img/9acf772e77c5e9dfeba35adc7ac6ce7d_37.png)

![](img/9acf772e77c5e9dfeba35adc7ac6ce7d_39.png)

![](img/9acf772e77c5e9dfeba35adc7ac6ce7d_41.png)

执行代码后，我们便得到了该策略在回测周期内的总收益率。这个值可能是正数（盈利），也可能是负数（亏损）。在本例的模拟数据中，它展示了一个正常的股价走势。

![](img/9acf772e77c5e9dfeba35adc7ac6ce7d_41.png)

![](img/9acf772e77c5e9dfeba35adc7ac6ce7d_47.png)

![](img/9acf772e77c5e9dfeba35adc7ac6ce7d_43.png)

## 总结

![](img/9acf772e77c5e9dfeba35adc7ac6ce7d_45.png)

本节课中，我们一起学习了量化策略评估的第一个核心指标——**回测收益率**。

![](img/9acf772e77c5e9dfeba35adc7ac6ce7d_47.png)

我们明确了它的定义：策略在回测期间获得的总收益与初始本金的比率。掌握了其计算公式：`(期末资产/期初资产) - 1`。并通过Python实战，演示了如何从价格数据中计算每日净值比、绘制增长曲线，并最终算出总收益率。

![](img/9acf772e77c5e9dfeba35adc7ac6ce7d_49.png)

回测收益率是衡量策略盈利能力的直接指标，但它只是一个开始。要全面评估一个策略，我们还需要结合其他指标，例如风险指标、与基准的比较等，这些将在后续课程中详细介绍。