# Python金融量化交易：16：回测收益率指标解读 📈

在本节课中，我们将学习量化交易策略评估中最基础且核心的指标——回测收益率。我们将了解它的含义、计算公式，并通过Python代码演示如何计算和可视化它。

![](img/9d4acd42e6b8869c17162ccacd3a6447_1.png)

## 概述

当我们完成一个交易策略后，需要在历史数据中进行测试，以评估策略的盈利能力。评估不能仅用“赚了”或“赔了”来描述，需要具体的指标来衡量。这些指标能告诉我们赚了多少、相比市场基准有何优势、承担了哪些风险，以及与其他策略相比的优劣。本节课，我们将聚焦于第一个关键指标：回测收益率。

## 回测收益率详解

![](img/9d4acd42e6b8869c17162ccacd3a6447_3.png)

![](img/9d4acd42e6b8869c17162ccacd3a6447_5.png)

![](img/9d4acd42e6b8869c17162ccacd3a6447_7.png)

回测收益率是评估策略表现最常用的指标，它表示策略在整个回测周期内，相对于初始本金的盈利或亏损比例。

![](img/9d4acd42e6b8869c17162ccacd3a6447_9.png)

其计算公式如下：

**回测收益率 = (期末资产总值 - 期初资产总值) / 期初资产总值**

![](img/9d4acd42e6b8869c17162ccacd3a6447_11.png)

![](img/9d4acd42e6b8869c17162ccacd3a6447_13.png)

或者等价于：

![](img/9d4acd42e6b8869c17162ccacd3a6447_15.png)

![](img/9d4acd42e6b8869c17162ccacd3a6447_17.png)

**回测收益率 = (期末资产总值 / 期初资产总值) - 1**

![](img/9d4acd42e6b8869c17162ccacd3a6447_19.png)

![](img/9d4acd42e6b8869c17162ccacd3a6447_21.png)

其中，`期末资产总值` 对应卖出所有资产后的总金额，`期初资产总值` 对应初始投入的本金。

## 代码实践：计算回测收益率

![](img/9d4acd42e6b8869c17162ccacd3a6447_23.png)

接下来，我们将通过Python代码，基于一份股票历史数据，计算并可视化其回测收益率。首先，我们需要导入必要的工具包并加载数据。

![](img/9d4acd42e6b8869c17162ccacd3a6447_25.png)

```python
import pandas as pd
import matplotlib.pyplot as plt

![](img/9d4acd42e6b8869c17162ccacd3a6447_27.png)

# 设置中文字体，方便后续图表显示
plt.rcParams['font.sans-serif'] = ['SimHei']
plt.rcParams['axes.unicode_minus'] = False

# 读取数据，假设第一列是日期
data = pd.read_csv('your_stock_data.csv', index_col=0)
print(data.head())
```

假设我们的数据包含了股票每日的收盘价。我们将每日收盘价视为策略每日的资产净值，来计算每日相对于初始本金的累计收益率。

```python
# 计算每日资产净值相对于期初的比值
cumulative_return_ratio = data / data.iloc[0]

# 绘制累计收益率曲线
cumulative_return_ratio.plot(figsize=(12, 6))
plt.title('累计收益率变化曲线')
plt.xlabel('日期')
plt.ylabel('累计收益率倍数')
plt.grid(True)
plt.show()
```

![](img/9d4acd42e6b8869c17162ccacd3a6447_29.png)

![](img/9d4acd42e6b8869c17162ccacd3a6447_31.png)

上图展示了资产净值随时间的变化。曲线上的点表示，相对于期初的1单位本金，当前资产净值是多少倍。

![](img/9d4acd42e6b8869c17162ccacd3a6447_33.png)

![](img/9d4acd42e6b8869c17162ccacd3a6447_35.png)

现在，我们来计算整个回测周期的最终收益率。

![](img/9d4acd42e6b8869c17162ccacd3a6447_37.png)

![](img/9d4acd42e6b8869c17162ccacd3a6447_39.png)

```python
# 计算最终的回测收益率
final_return = cumulative_return_ratio.iloc[-1] - 1

# 将结果整理成DataFrame以便查看
result_df = pd.DataFrame(final_return.values, columns=['回测收益率'], index=data.columns)
print(result_df)
```

![](img/9d4acd42e6b8869c17162ccacd3a6447_41.png)

![](img/9d4acd42e6b8869c17162ccacd3a6447_43.png)

执行以上代码，我们将得到一个表格，其中列出了基于该数据（或策略）计算出的最终回测收益率。正值代表盈利，负值代表亏损。

![](img/9d4acd42e6b8869c17162ccacd3a6447_45.png)

![](img/9d4acd42e6b8869c17162ccacd3a6447_47.png)

## 总结

![](img/9d4acd42e6b8869c17162ccacd3a6447_49.png)

本节课中，我们一起学习了量化策略评估的第一个核心指标——回测收益率。我们理解了它的定义是衡量策略相对于初始本金的总体盈利比例，并掌握了其计算公式。最后，我们通过Python代码实践了如何从历史价格数据中计算并可视化这一指标。理解回测收益率是读懂更复杂评估报告的基础。在下一节中，我们将继续学习其他重要的策略评估指标。