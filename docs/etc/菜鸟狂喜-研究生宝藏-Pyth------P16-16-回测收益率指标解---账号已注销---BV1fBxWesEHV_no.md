# Python金融量化分析：P16：回测收益率指标解读 📈

在本节课中，我们将学习如何解读策略回测的核心评估指标。我们将从最基础的“回测收益率”开始，理解其含义、计算公式，并通过Python代码进行实际计算和可视化。

![](img/7ebf9671efc17becbabb45ed9573e60f_1.png)

## 概述

当我们完成一个交易策略后，需要在历史数据中进行测试，以评估其表现。评估不仅仅是判断策略是盈利还是亏损，更需要量化其具体表现，例如盈利多少、相比市场基准有何优势、承担了何种风险以及与其他策略相比的优劣。这些都需要通过具体的评估指标来衡量。

以下是我们将重点介绍的核心评估指标之一。

![](img/7ebf9671efc17becbabb45ed9573e60f_3.png)

## 回测收益率

![](img/7ebf9671efc17becbabb45ed9573e60f_5.png)

![](img/7ebf9671efc17becbabb45ed9573e60f_7.png)

回测收益率是最基本、最常用的策略评估指标。它衡量了策略在整个回测周期内，相对于初始本金的总体盈利或亏损比例。

![](img/7ebf9671efc17becbabb45ed9573e60f_9.png)

其计算公式如下：

**回测收益率 = (期末资产总值 - 期初资产总值) / 期初资产总值**

![](img/7ebf9671efc17becbabb45ed9573e60f_11.png)

![](img/7ebf9671efc17becbabb45ed9573e60f_13.png)

或者等价于：

![](img/7ebf9671efc17becbabb45ed9573e60f_15.png)

**回测收益率 = (卖出价格 Pt - 买入价格 P0) / 买入价格 P0**

![](img/7ebf9671efc17becbabb45ed9573e60f_17.png)

![](img/7ebf9671efc17becbabb45ed9573e60f_19.png)

这个指标直观地告诉我们，执行该策略后，我们的本金最终增长（或缩水）了多少百分比。

![](img/7ebf9671efc17becbabb45ed9573e60f_21.png)

上一节我们介绍了评估指标的重要性，本节中我们来看看如何具体计算回测收益率。

## 实战计算：用Python计算回测收益率

![](img/7ebf9671efc17becbabb45ed9573e60f_23.png)

我们将通过一个简单的Python示例，计算并可视化一项资产（或策略净值）随时间变化的收益率情况。首先，我们需要导入必要的工具包并加载数据。

![](img/7ebf9671efc17becbabb45ed9573e60f_25.png)

```python
import pandas as pd
import matplotlib.pyplot as plt
# 设置中文字体，方便后续图表显示中文
plt.rcParams['font.sans-serif'] = ['SimHei']
plt.rcParams['axes.unicode_minus'] = False

![](img/7ebf9671efc17becbabb45ed9573e60f_27.png)

# 读取数据，假设第一列是日期
data = pd.read_csv('your_data.csv', index_col=0, parse_dates=True)
# 查看数据前几行
print(data.head())
```

假设我们的数据 `data` 中包含策略每日的净值或股票价格。我们将基于此计算每日相对于期初的累积收益率。

以下是计算步骤：

1.  计算每日净值与期初净值的比值。
2.  将此比值减去1，得到每日的累积收益率。
3.  最终的回测收益率即为最后一个交易日的累积收益率。

```python
# 1. 计算每日净值与期初净值的比值
cumulative_return_ratio = data / data.iloc[0]

![](img/7ebf9671efc17becbabb45ed9573e60f_29.png)

# 可视化累积净值变化
cumulative_return_ratio.plot(figsize=(12, 6))
plt.title('策略累积净值走势 (相对于期初)')
plt.xlabel('日期')
plt.ylabel('净值比率')
plt.grid(True)
plt.show()

![](img/7ebf9671efc17becbabb45ed9573e60f_31.png)

# 2. 计算回测收益率 (最终累积收益率)
final_return = cumulative_return_ratio.iloc[-1] - 1

![](img/7ebf9671efc17becbabb45ed9573e60f_33.png)

![](img/7ebf9671efc17becbabb45ed9573e60f_35.png)

# 将结果整理成DataFrame以便查看
result_df = pd.DataFrame({
    '回测收益率': final_return.values
}, index=final_return.index)

![](img/7ebf9671efc17becbabb45ed9573e60f_37.png)

print(result_df)
```

![](img/7ebf9671efc17becbabb45ed9573e60f_39.png)

执行以上代码后，我们将得到两个输出：
*   一张图表，展示了策略净值相对于期初本金的增长曲线。
*   一个表格，显示了最终的回测收益率数值。

![](img/7ebf9671efc17becbabb45ed9573e60f_41.png)

通过图表，我们可以直观看到策略在整个回测期间的表现是平稳增长、波动剧烈还是持续下跌。而 `result_df` 中的数值则给出了确切的盈利或亏损百分比。

![](img/7ebf9671efc17becbabb45ed9573e60f_43.png)

![](img/7ebf9671efc17becbabb45ed9573e60f_45.png)

## 总结

![](img/7ebf9671efc17becbabb45ed9573e60f_47.png)

本节课中我们一起学习了策略回测的核心评估指标——回测收益率。我们理解了它衡量的是策略相对于初始本金的总体收益比例，并掌握了其计算公式 **回测收益率 = (期末价值 - 期初价值) / 期初价值**。最后，我们通过Python代码实战，完成了从数据加载、比率计算、可视化到最终结果输出的全过程。

![](img/7ebf9671efc17becbabb45ed9573e60f_49.png)

理解回测收益率是评估策略表现的第一步。在后续的课程中，我们将在此基础上，引入更多维度的评估指标，如年化收益率、夏普比率、最大回撤等，以更全面、更专业地衡量一个交易策略的优劣。