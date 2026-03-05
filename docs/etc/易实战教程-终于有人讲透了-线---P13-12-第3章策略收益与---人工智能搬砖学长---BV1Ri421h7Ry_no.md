# Python金融时间序列分析与量化交易实战教程：P13：12.第3章策略收益与风险评估指标解析：回测收益率指标解读 📈

![](img/9b2255492d970dcb7ed0f623acb6c955_0.png)

在本节课中，我们将学习量化交易策略评估中最基础且核心的指标——回测收益率。我们将理解其含义、计算公式，并通过Python代码演示如何从原始数据中计算并可视化它。

![](img/9b2255492d970dcb7ed0f623acb6c955_2.png)

## 概述

当我们完成一个交易策略的构建后，必须在历史数据中进行测试，以评估其表现。评估不仅仅是判断“赚”或“赔”，而是需要一系列量化指标来衡量收益的多少、与市场基准相比的优势、以及所承担的风险是否值得。这些指标共同构成了策略的评估体系。

## 回测收益率

![](img/9b2255492d970dcb7ed0f623acb6c955_4.png)

![](img/9b2255492d970dcb7ed0f623acb6c955_6.png)

![](img/9b2255492d970dcb7ed0f623acb6c955_8.png)

回测收益率是评估策略表现最直接的指标。它衡量了策略在整个回测周期内，相对于初始本金所获得的总收益百分比。

其计算公式如下：

![](img/9b2255492d970dcb7ed0f623acb6c955_10.png)

![](img/9b2255492d970dcb7ed0f623acb6c955_12.png)

**回测收益率 = (最终资产价值 / 初始资产价值) - 1**

![](img/9b2255492d970dcb7ed0f623acb6c955_14.png)

![](img/9b2255492d970dcb7ed0f623acb6c955_16.png)

用符号表示为：
`R_total = (P_T / P_0) - 1`
其中，`P_T` 代表回测结束时的资产总值，`P_0` 代表回测开始时的初始本金。

![](img/9b2255492d970dcb7ed0f623acb6c955_18.png)

![](img/9b2255492d970dcb7ed0f623acb6c955_20.png)

![](img/9b2255492d970dcb7ed0f623acb6c955_22.png)

这个指标直观地告诉我们，经过一段时间的操作，我们的本金增长（或亏损）了多少比例。

接下来，我们将通过Python代码，基于一份股票历史数据，实际计算并观察其回测收益率的变化过程。

## 代码实战：计算与可视化

![](img/9b2255492d970dcb7ed0f623acb6c955_24.png)

首先，我们需要导入必要的工具包并加载数据。

![](img/9b2255492d970dcb7ed0f623acb6c955_26.png)

![](img/9b2255492d970dcb7ed0f623acb6c955_28.png)

```python
import pandas as pd
import matplotlib.pyplot as plt

# 设置中文字体，方便后续图表显示
plt.rcParams['font.sans-serif'] = ['SimHei']
plt.rcParams['axes.unicode_minus'] = False

![](img/9b2255492d970dcb7ed0f623acb6c955_30.png)

![](img/9b2255492d970dcb7ed0f623acb6c955_32.png)

# 读取数据，指定第一列为日期索引
data = pd.read_csv('your_stock_data.csv', index_col=0)
print(data.head())
```

![](img/9b2255492d970dcb7ed0f623acb6c955_34.png)

假设我们的 `data` 数据框中包含股票每日的收盘价。我们可以将其视为策略每日的资产净值。

![](img/9b2255492d970dcb7ed0f623acb6c955_36.png)

![](img/9b2255492d970dcb7ed0f623acb6c955_38.png)

为了计算每日相对于初始本金的累计收益变化，我们进行以下操作：

![](img/9b2255492d970dcb7ed0f623acb6c955_40.png)

![](img/9b2255492d970dcb7ed0f623acb6c955_42.png)

```python
# 计算每日资产净值与初始净值的比值
cumulative_return_ratio = data / data.iloc[0]

![](img/9b2255492d970dcb7ed0f623acb6c955_44.png)

![](img/9b2255492d970dcb7ed0f623acb6c955_46.png)

# 绘制累计收益变化曲线
plt.figure(figsize=(12, 6))
cumulative_return_ratio.plot()
plt.title('累计收益变化曲线')
plt.xlabel('日期')
plt.ylabel('累计收益倍数')
plt.grid(True)
plt.show()
```

![](img/9b2255492d970dcb7ed0f623acb6c955_48.png)

![](img/9b2255492d970dcb7ed0f623acb6c955_50.png)

这张图展示了从回测开始起，每一时刻的资产相对于初始本金的变化情况。数值大于1表示盈利，小于1表示亏损。

![](img/9b2255492d970dcb7ed0f623acb6c955_52.png)

![](img/9b2255492d970dcb7ed0f623acb6c955_54.png)

最后，我们计算整个回测周期的总收益率：

![](img/9b2255492d970dcb7ed0f623acb6c955_56.png)

![](img/9b2255492d970dcb7ed0f623acb6c955_58.png)

![](img/9b2255492d970dcb7ed0f623acb6c955_60.png)

```python
# 计算最终的回测总收益率
final_return = cumulative_return_ratio.iloc[-1] - 1

# 将结果整理成清晰的DataFrame格式展示
result_df = pd.DataFrame(final_return.values, columns=['回测收益率'], index=data.columns)
print(result_df)
```

![](img/9b2255492d970dcb7ed0f623acb6c955_62.png)

![](img/9b2255492d970dcb7ed0f623acb6c955_64.png)

![](img/9b2255492d970dcb7ed0f623acb6c955_66.png)

执行以上代码，我们将得到一个表格，清晰地列出基于该数据（或策略）所获得的最终回测收益率。

![](img/9b2255492d970dcb7ed0f623acb6c955_68.png)

![](img/9b2255492d970dcb7ed0f623acb6c955_70.png)

## 总结

![](img/9b2255492d970dcb7ed0f623acb6c955_72.png)

本节课我们一起学习了策略评估的第一个核心指标——回测收益率。我们理解了它衡量的是策略在整个测试周期内的总收益能力，并掌握了其计算公式 `(P_T / P_0) - 1`。通过Python实战，我们学会了如何从时间序列数据中计算每日的累计收益变化，并最终得到总的回测收益率。这是读懂任何策略回测报告的第一步，后续我们将在此基础上，引入更多衡量风险和相对表现的指标。