# 量化交易教程：P16：回测收益率指标解读 📈

## 概述
在本节课中，我们将要学习量化交易策略评估中最基础且核心的指标——回测收益率。我们将理解它的含义、计算公式，并通过Python代码演示如何从历史数据中计算它。

上一节我们介绍了策略评估指标的重要性，本节中我们来看看第一个具体指标。

![](img/28e3c3e3cb4e7f2e4b1d9ee1dbe12329_1.png)

## 什么是回测收益率？
当我们完成一个交易策略的构建后，需要在历史数据中进行测试，以判断策略的盈亏情况。但“赚了”或“赔了”是模糊的描述，我们需要一个量化的指标来精确衡量策略的盈利能力。回测收益率就是这样一个指标，它衡量的是策略在整个回测周期内，相对于初始本金所获得的总收益比例。

## 回测收益率的计算公式
回测收益率的计算公式非常简单，其核心思想是计算最终资产相对于初始资产的增长比例。

![](img/28e3c3e3cb4e7f2e4b1d9ee1dbe12329_3.png)

**公式**如下：
`回测收益率 = (最终资产价值 - 初始资产价值) / 初始资产价值`

![](img/28e3c3e3cb4e7f2e4b1d9ee1dbe12329_4.png)

也可以表示为：
`回测收益率 = (P_T / P_0) - 1`

其中：
*   **`P_T`** 代表回测结束时的资产总价值（卖出所有头寸后的总资金）。
*   **`P_0`** 代表回测开始时的初始本金。

![](img/28e3c3e3cb4e7f2e4b1d9ee1dbe12329_6.png)

![](img/28e3c3e3cb4e7f2e4b1d9ee1dbe12329_7.png)

这个指标直接回答了“我投入的钱最终变成了多少”这个问题。例如，一个40%的回测收益率意味着你的本金增长了40%。

## 使用Python计算回测收益率
以下是使用Python和Pandas库，基于股价时间序列数据计算回测收益率的步骤。我们将股价的收盘价序列视为资产净值的变化来模拟这个过程。

![](img/28e3c3e3cb4e7f2e4b1d9ee1dbe12329_9.png)

![](img/28e3c3e3cb4e7f2e4b1d9ee1dbe12329_10.png)

![](img/28e3c3e3cb4e7f2e4b1d9ee1dbe12329_11.png)

![](img/28e3c3e3cb4e7f2e4b1d9ee1dbe12329_12.png)

首先，导入必要的库并加载数据。

![](img/28e3c3e3cb4e7f2e4b1d9ee1dbe12329_13.png)

![](img/28e3c3e3cb4e7f2e4b1d9ee1dbe12329_14.png)

```python
import pandas as pd
import matplotlib.pyplot as plt

# 设置中文字体（可选，用于图表标签）
plt.rcParams['font.sans-serif'] = ['SimHei']
plt.rcParams['axes.unicode_minus'] = False

![](img/28e3c3e3cb4e7f2e4b1d9ee1dbe12329_16.png)

# 读取数据，假设第一列是日期
data = pd.read_csv('your_stock_data.csv', index_col=0)
print(data.head())
```

假设我们的`data` DataFrame中有一列`‘Close’`代表每日收盘价。我们将基于此计算每日相对于第一天的净值变化，并最终得到总回测收益率。

![](img/28e3c3e3cb4e7f2e4b1d9ee1dbe12329_18.png)

![](img/28e3c3e3cb4e7f2e4b1d9ee1dbe12329_19.png)

第一步，计算每个时间点资产相对于初始点的净值比。

```python
# 计算每日净值相对于初始净值的比值
cumulative_return = data['Close'] / data['Close'].iloc[0]

# 绘制净值曲线
plt.figure(figsize=(12, 6))
cumulative_return.plot()
plt.title('资产净值变化曲线 (基准: 初始值=1)')
plt.ylabel('净值比')
plt.xlabel('日期')
plt.grid(True)
plt.show()
```

这段代码计算了每个交易日收盘价与第一天收盘价的比值，并绘制出其变化曲线。这条曲线展示了资产净值随时间增长（或下跌）的过程。

第二步，根据净值比计算最终的回测收益率。

![](img/28e3c3e3cb4e7f2e4b1d9ee1dbe12329_21.png)

```python
# 计算最终的回测收益率： (最终净值 / 初始净值) - 1
final_return = cumulative_return.iloc[-1] - 1

![](img/28e3c3e3cb4e7f2e4b1d9ee1dbe12329_23.png)

![](img/28e3c3e3cb4e7f2e4b1d9ee1dbe12329_24.png)

# 将结果整理成表格形式展示
result_df = pd.DataFrame({
    '回测收益率': [final_return]
}, index=['策略结果'])

print(result_df)
```

![](img/28e3c3e3cb4e7f2e4b1d9ee1dbe12329_26.png)

![](img/28e3c3e3cb4e7f2e4b1d9ee1dbe12329_27.png)

![](img/28e3c3e3cb4e7f2e4b1d9ee1dbe12329_28.png)

执行以上代码，`result_df` 将显示策略在整个回测周期内的总收益率。例如，输出`0.45`表示获得了45%的收益。

## 核心要点总结
以下是关于回测收益率需要记住的几个关键点：

![](img/28e3c3e3cb4e7f2e4b1d9ee1dbe12329_30.png)

*   **核心定义**：回测收益率衡量策略在历史测试中获得的**总收益百分比**。
*   **计算公式**：`(最终价值 / 初始价值) - 1`。这是评估策略盈利能力的**最直接指标**。
*   **计算过程**：通常先计算整个周期的**资产净值曲线**，再取曲线终值进行计算。
*   **注意事项**：它是一个**事后汇总指标**，只告诉最终结果，不反映收益过程中的波动和风险。高收益率可能伴随着高风险，需要结合其他指标（如下节课将介绍的夏普比率、最大回撤等）综合评估。

![](img/28e3c3e3cb4e7f2e4b1d9ee1dbe12329_32.png)

## 总结
本节课中我们一起学习了量化策略评估的基石——**回测收益率**。我们理解了它作为衡量策略绝对收益能力的角色，掌握了其计算公式 `(P_T / P_0) - 1`，并亲手使用Python代码从股价数据中计算出了这个指标。记住，回测收益率是评价策略好坏的起点，但绝非终点。

![](img/28e3c3e3cb4e7f2e4b1d9ee1dbe12329_33.png)

![](img/28e3c3e3cb4e7f2e4b1d9ee1dbe12329_34.png)

![](img/28e3c3e3cb4e7f2e4b1d9ee1dbe12329_35.png)

下一节，我们将在此基础上，学习如何评估这些收益背后所承担的风险，引入更全面的评估指标。