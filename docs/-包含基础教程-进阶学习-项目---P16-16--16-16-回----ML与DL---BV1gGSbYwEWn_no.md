# 量化交易教程：P16：回测收益率指标解读 📈

在本节课中，我们将学习量化交易策略评估中最基础且重要的指标——回测收益率。我们将了解其定义、计算公式，并通过Python代码演示如何计算和可视化它。

![](img/03ce023eb847b8d2472ff12b30f9f923_1.png)

当我们完成一个交易策略后，需要在历史数据中进行测试，以判断策略的盈亏情况。盈亏本身是一个定性描述，我们需要更精确的定量指标来评估策略表现，例如赚了多少、相比大盘有何优势、面临哪些风险以及与其他策略相比的优劣。这些都需要通过一系列策略评估指标来完成。

以下是本节课将要介绍的核心评估指标之一。

![](img/03ce023eb847b8d2472ff12b30f9f923_3.png)

![](img/03ce023eb847b8d2472ff12b30f9f923_5.png)

## 回测收益率

![](img/03ce023eb847b8d2472ff12b30f9f923_7.png)

![](img/03ce023eb847b8d2472ff12b30f9f923_9.png)

回测收益率是最基本、最常用的策略评估指标。它衡量了在回测周期内，策略最终获得的收益相对于初始本金的增长比例。例如，一个4%的回测收益率意味着，经过一段时间的交易，你的总资产相比初始本金增长了4%。

其计算公式如下：

![](img/03ce023eb847b8d2472ff12b30f9f923_11.png)

![](img/03ce023eb847b8d2472ff12b30f9f923_13.png)

**公式：**
`回测收益率 = (期末资产价值 - 期初资产价值) / 期初资产价值`

![](img/03ce023eb847b8d2472ff12b30f9f923_15.png)

![](img/03ce023eb847b8d2472ff12b30f9f923_17.png)

![](img/03ce023eb847b8d2472ff12b30f9f923_19.png)

或者等价于：
`回测收益率 = (Pt / P0) - 1`
其中，`P0`代表期初资产价值，`Pt`代表期末资产价值。

![](img/03ce023eb847b8d2472ff12b30f9f923_21.png)

上一节我们介绍了回测收益率的概念，本节中我们来看看如何用Python代码进行计算和展示。

![](img/03ce023eb847b8d2472ff12b30f9f923_23.png)

首先，我们需要导入必要的工具包并加载数据。

```python
import pandas as pd
import matplotlib.pyplot as plt

![](img/03ce023eb847b8d2472ff12b30f9f923_25.png)

![](img/03ce023eb847b8d2472ff12b30f9f923_27.png)

# 设置中文字体，方便后续作图显示中文
plt.rcParams['font.sans-serif'] = ['SimHei']
plt.rcParams['axes.unicode_minus'] = False

# 读取数据，假设第一列是日期，将其设为索引
data = pd.read_csv('your_stock_data.csv', index_col=0)
print(data.head())
```

![](img/03ce023eb847b8d2472ff12b30f9f923_29.png)

假设我们得到了一份股票的历史数据。接下来，我们将基于这份数据模拟一个策略的资产净值走势，并计算其回测收益率。

以下是计算回测收益率并绘制其走势图的步骤：

![](img/03ce023eb847b8d2472ff12b30f9f923_31.png)

1.  计算每日资产净值相对于期初净值的比值。
2.  根据比值计算整个回测周期的最终收益率。
3.  将结果可视化。

![](img/03ce023eb847b8d2472ff12b30f9f923_33.png)

```python
# 1. 计算每日净值相对于期初净值的比值
# 假设‘close’列代表每日策略的资产净值
initial_value = data['close'].iloc[0]
relative_value_df = data['close'] / initial_value

![](img/03ce023eb847b8d2472ff12b30f9f923_35.png)

![](img/03ce023eb847b8d2472ff12b30f9f923_37.png)

# 2. 绘制净值走势图
plt.figure(figsize=(12, 6))
plt.plot(relative_value_df.index, relative_value_df.values)
plt.title('策略净值相对走势图')
plt.xlabel('日期')
plt.ylabel('相对净值 (相对于期初)')
plt.grid(True)
plt.show()

![](img/03ce023eb847b8d2472ff12b30f9f923_39.png)

![](img/03ce023eb847b8d2472ff12b30f9f923_41.png)

# 3. 计算最终的回测收益率
final_relative_value = relative_value_df.iloc[-1]
backtest_return = final_relative_value - 1

![](img/03ce023eb847b8d2472ff12b30f9f923_43.png)

![](img/03ce023eb847b8d2472ff12b30f9f923_45.png)

# 4. 将结果整理成DataFrame展示
result_df = pd.DataFrame({
    '回测收益率': [backtest_return]
}, index=['结果'])
print(result_df)
```

![](img/03ce023eb847b8d2472ff12b30f9f923_47.png)

![](img/03ce023eb847b8d2472ff12b30f9f923_49.png)

执行以上代码后，你将看到策略净值随时间变化的曲线图，并在控制台输出最终的回测收益率数值。正值代表盈利，负值代表亏损。

![](img/03ce023eb847b8d2472ff12b30f9f923_51.png)

本节课中我们一起学习了回测收益率这一核心评估指标。我们明确了它的定义是衡量策略最终收益相对于本金的比例，掌握了其计算公式 `(Pt / P0) - 1`，并实践了如何使用Python计算和展示该指标。理解回测收益率是读懂策略回测报告的第一步，它为后续学习更复杂的风险评估与对比指标奠定了基础。