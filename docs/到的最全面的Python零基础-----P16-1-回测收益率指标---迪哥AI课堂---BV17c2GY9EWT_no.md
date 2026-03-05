# Python金融量化交易：P16：1-回测收益率指标解读 📈

在本节课中，我们将要学习策略评估中最基础且重要的一个指标——回测收益率。我们将了解它的含义、计算公式，并通过Python代码演示如何计算和可视化它。

![](img/67e67f94e4808d5c8defb441bb019cab_1.png)

## 概述

当我们完成一个交易策略后，需要在历史数据中进行测试，以评估其表现。评估不仅要知道策略是盈利还是亏损，更需要量化具体的收益情况，例如赚了多少、相比市场基准有何优势、以及承担了哪些风险。这些都需要通过一系列评估指标来完成。本节课，我们将聚焦于第一个核心指标：回测收益率。

![](img/67e67f94e4808d5c8defb441bb019cab_3.png)

## 回测收益率详解

![](img/67e67f94e4808d5c8defb441bb019cab_5.png)

![](img/67e67f94e4808d5c8defb441bb019cab_7.png)

回测收益率是评估策略表现最常用的指标，它衡量了策略在整个回测周期内，相对于初始本金的总体盈利或亏损比例。

![](img/67e67f94e4808d5c8defb441bb019cab_9.png)

其核心计算公式如下：

![](img/67e67f94e4808d5c8defb441bb019cab_11.png)

**回测收益率 = (期末资产价值 / 期初资产价值) - 1**

![](img/67e67f94e4808d5c8defb441bb019cab_13.png)

![](img/67e67f94e4808d5c8defb441bb019cab_15.png)

其中：
*   **期末资产价值**：策略在回测结束时的总资产价值（包括现金和持仓市值）。
*   **期初资产价值**：策略在回测开始时的初始本金。

![](img/67e67f94e4808d5c8defb441bb019cab_17.png)

![](img/67e67f94e4808d5c8defb441bb019cab_19.png)

这个指标直观地告诉我们，投入的初始资金经过策略运作后，最终变成了多少。例如，回测收益率为0.15表示盈利15%，为-0.05则表示亏损5%。

![](img/67e67f94e4808d5c8defb441bb019cab_21.png)

## 使用Python计算回测收益率

![](img/67e67f94e4808d5c8defb441bb019cab_23.png)

上一节我们介绍了回测收益率的概念和公式，本节中我们来看看如何用Python代码实现计算和可视化。我们将使用一个示例的股票价格数据来模拟策略的净值曲线。

首先，导入必要的工具包并加载数据。

![](img/67e67f94e4808d5c8defb441bb019cab_25.png)

![](img/67e67f94e4808d5c8defb441bb019cab_27.png)

```python
import pandas as pd
import matplotlib.pyplot as plt
# 设置中文字体（可选，用于图表标签）
plt.rcParams['font.sans-serif'] = ['SimHei']
plt.rcParams['axes.unicode_minus'] = False

# 读取数据，假设第一列是日期
data = pd.read_csv('your_stock_data.csv', index_col=0, parse_dates=True)
# 假设‘close’列代表每日的收盘价或策略净值
print(data.head())
```

数据加载后，我们假设`data[‘close’]`列代表了我们策略每日的资产净值。接下来，我们基于此计算回测收益率。

以下是计算回测收益率并绘制净值曲线的步骤：

1.  **计算累积净值曲线**：将每日净值除以初始净值，得到相对于初始本金的累积变化。
2.  **计算最终回测收益率**：根据公式，使用期末净值除以期初净值再减1。
3.  **可视化结果**：绘制累积净值曲线图。

![](img/67e67f94e4808d5c8defb441bb019cab_29.png)

![](img/67e67f94e4808d5c8defb441bb019cab_31.png)

```python
# 1. 计算累积净值曲线 (假设以第一天的收盘价为初始本金)
cumulative_return = data['close'] / data['close'].iloc[0]

![](img/67e67f94e4808d5c8defb441bb019cab_33.png)

![](img/67e67f94e4808d5c8defb441bb019cab_35.png)

# 2. 计算最终的回测收益率
final_return = cumulative_return.iloc[-1] - 1

![](img/67e67f94e4808d5c8defb441bb019cab_37.png)

![](img/67e67f94e4808d5c8defb441bb019cab_39.png)

# 3. 绘制累积净值曲线图
plt.figure(figsize=(10, 6))
cumulative_return.plot()
plt.title('策略累积净值曲线')
plt.xlabel('日期')
plt.ylabel('累积净值 (倍数)')
plt.grid(True)
plt.show()

# 4. 以表格形式展示最终的回测收益率
result_df = pd.DataFrame({
    '回测收益率': [final_return]
}, index=['最终结果'])
print(result_df)
```

![](img/67e67f94e4808d5c8defb441bb019cab_41.png)

![](img/67e67f94e4808d5c8defb441bb019cab_43.png)

执行以上代码，你将看到一条从1开始的净值曲线。如果曲线向上，代表策略盈利；向下则代表亏损。打印出的`result_df`表格会显示具体的回测收益率数值。

![](img/67e67f94e4808d5c8defb441bb019cab_45.png)

![](img/67e67f94e4808d5c8defb441bb019cab_47.png)

## 总结

![](img/67e67f94e4808d5c8defb441bb019cab_49.png)

本节课中我们一起学习了策略评估的第一个关键指标——回测收益率。我们理解了它衡量的是策略相对于初始本金的总体收益比例，并掌握了其计算公式 **`(期末价值/期初价值) - 1`**。最后，我们通过Python代码实践了如何从价格数据计算并可视化这一指标。理解回测收益率是读懂任何策略回测报告的第一步，在后续课程中，我们将以此为基础，学习更多维度的评估指标。