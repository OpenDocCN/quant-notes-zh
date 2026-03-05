# Python金融分析量化交易：P16：1-回测收益率指标解读 📈

![](img/45b8e2ef425af2a00b6d1ea7974c28f6_1.png)

在本节课中，我们将学习如何评估一个量化交易策略。策略评估的核心是理解一系列关键指标，它们能告诉我们策略在历史数据上的表现如何，包括赚了多少钱、承担了哪些风险，以及与其他策略相比的优势。本节课我们将重点解读最基础的指标——回测收益率。

上一节我们介绍了策略评估的重要性，本节中我们来看看第一个核心指标：回测收益率。

## 回测收益率

![](img/45b8e2ef425af2a00b6d1ea7974c28f6_3.png)

回测收益率是评估策略表现最直接、最基础的指标。它衡量的是，在回测周期结束时，你的策略相对于初始本金获得了多少百分比的总收益。

![](img/45b8e2ef425af2a00b6d1ea7974c28f6_5.png)

![](img/45b8e2ef425af2a00b6d1ea7974c28f6_7.png)

其计算公式如下：

![](img/45b8e2ef425af2a00b6d1ea7974c28f6_9.png)

**公式：**
`回测收益率 = (期末资产总值 - 期初本金) / 期初本金`

![](img/45b8e2ef425af2a00b6d1ea7974c28f6_11.png)

![](img/45b8e2ef425af2a00b6d1ea7974c28f6_13.png)

或者等价于：
`回测收益率 = (期末价格 Pt / 期初价格 P0) - 1`

![](img/45b8e2ef425af2a00b6d1ea7974c28f6_15.png)

![](img/45b8e2ef425af2a00b6d1ea7974c28f6_17.png)

这个指标直观地告诉我们，经过一段时间的交易，我们的本金是增长了（正收益）还是缩水了（负收益）。

![](img/45b8e2ef425af2a00b6d1ea7974c28f6_19.png)

![](img/45b8e2ef425af2a00b6d1ea7974c28f6_21.png)

接下来，我们将通过Python代码，基于一份股票历史数据，实际计算并可视化回测收益率。

## 实战计算回测收益率

![](img/45b8e2ef425af2a00b6d1ea7974c28f6_23.png)

首先，我们需要导入必要的工具包并加载数据。

![](img/45b8e2ef425af2a00b6d1ea7974c28f6_25.png)

```python
import pandas as pd
import matplotlib.pyplot as plt

![](img/45b8e2ef425af2a00b6d1ea7974c28f6_27.png)

# 设置中文字体，方便后续图表显示
plt.rcParams['font.sans-serif'] = ['SimHei']
plt.rcParams['axes.unicode_minus'] = False

# 读取数据，假设第一列是日期
data = pd.read_csv('your_stock_data.csv', index_col=0)
print(data.head())
```

数据加载后，我们假设`data`中的某一列（例如收盘价）代表了我们策略在每一天的资产净值。

以下是计算回测收益率并绘制其变化曲线的步骤：

![](img/45b8e2ef425af2a00b6d1ea7974c28f6_29.png)

1.  **计算每日相对净值**：将每一天的净值与初始净值相除，得到相对于初始本金的累积倍数。
2.  **计算最终回测收益率**：取最后一个交易日的相对净值，减去1，即得到总的回测收益率。
3.  **可视化过程**：绘制相对净值随时间变化的曲线。

![](img/45b8e2ef425af2a00b6d1ea7974c28f6_31.png)

![](img/45b8e2ef425af2a00b6d1ea7974c28f6_33.png)

```python
# 1. 计算每日相对净值（假设‘close’列代表每日策略净值）
initial_value = data.iloc[0]  # 获取期初净值
relative_value = data / initial_value  # 计算每日相对净值

![](img/45b8e2ef425af2a00b6d1ea7974c28f6_35.png)

![](img/45b8e2ef425af2a00b6d1ea7974c28f6_37.png)

# 2. 绘制相对净值变化曲线
plt.figure(figsize=(12, 6))
relative_value.plot()
plt.title('策略净值相对变化曲线')
plt.xlabel('日期')
plt.ylabel('相对净值 (倍数)')
plt.grid(True)
plt.show()

![](img/45b8e2ef425af2a00b6d1ea7974c28f6_39.png)

# 3. 计算最终的回测收益率
final_relative_value = relative_value.iloc[-1]  # 获取最后一个交易日的相对净值
backtest_return = final_relative_value - 1  # 根据公式计算收益率

![](img/45b8e2ef425af2a00b6d1ea7974c28f6_41.png)

![](img/45b8e2ef425af2a00b6d1ea7974c28f6_43.png)

# 4. 以表格形式展示结果
result_df = pd.DataFrame(backtest_return.values, columns=['回测收益率'], index=[data.index[-1]])
print(result_df)
```

![](img/45b8e2ef425af2a00b6d1ea7974c28f6_45.png)

![](img/45b8e2ef425af2a00b6d1ea7974c28f6_47.png)

执行以上代码，你将看到一条展示资产相对初始本金增长情况的曲线，以及一个显示最终回测收益率的表格。如果收益率为正，说明策略在回测期内盈利；如果为负，则说明亏损。

![](img/45b8e2ef425af2a00b6d1ea7974c28f6_49.png)

本节课中我们一起学习了量化策略评估的第一个核心指标——回测收益率。我们理解了它的定义和计算公式，并通过Python代码实战，计算了策略在整个回测周期内的总收益表现。这个指标为我们判断策略“赚了还是赔了”提供了最直接的依据。在接下来的课程中，我们将继续学习其他重要的评估指标，以更全面地衡量策略的性能。