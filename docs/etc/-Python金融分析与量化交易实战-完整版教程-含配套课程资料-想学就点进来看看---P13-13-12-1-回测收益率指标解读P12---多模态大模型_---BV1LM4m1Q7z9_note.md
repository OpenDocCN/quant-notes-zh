# Python金融分析与量化交易实战：13.12.1：回测收益率指标解读 📈

在本节课中，我们将要学习量化交易策略评估中最基础且重要的一个指标：**回测收益率**。我们将了解它的含义、计算公式，并通过Python代码演示如何从历史数据中计算它。

![](img/4bdd6bbef04d64a0a0d77893ec0f000a_1.png)

当我们完成一个交易策略的构建后，需要在历史数据中进行测试，以评估其表现。评估不能仅用“赚钱”或“赔钱”来概括，我们需要更精确的指标来回答：赚了多少？相比市场基准有何优势？承担了哪些风险？与其他策略相比表现如何？这些问题的答案就构成了策略的评估指标体系。

以下是本节课将要介绍的核心指标列表，我们将从第一个指标开始详细讲解。

*   **回测收益率**
*   （后续课程将介绍其他指标，如夏普比率、最大回撤等）

![](img/4bdd6bbef04d64a0a0d77893ec0f000a_3.png)

---

![](img/4bdd6bbef04d64a0a0d77893ec0f000a_5.png)

![](img/4bdd6bbef04d64a0a0d77893ec0f000a_7.png)

## 回测收益率的概念

**回测收益率**是最基本的策略评估指标。它衡量的是，在整个回测周期结束后，你的策略带来的总收益相对于初始本金的增长比例。

![](img/4bdd6bbef04d64a0a0d77893ec0f000a_9.png)

例如，你使用2015年至2020年的股票数据测试了一个策略，该策略规定了买入和卖出的时机。最终，这个策略带来的总收益，相对于你最初投入的本金，增长了4.2%。这个4.2%就是你的回测收益率。

![](img/4bdd6bbef04d64a0a0d77893ec0f000a_11.png)

![](img/4bdd6bbef04d64a0a0d77893ec0f000a_13.png)

它的核心思想是：**（最终资产价值 - 初始本金）/ 初始本金**。

![](img/4bdd6bbef04d64a0a0d77893ec0f000a_15.png)

![](img/4bdd6bbef04d64a0a0d77893ec0f000a_17.png)

## 回测收益率的计算公式

回测收益率的计算公式可以表示为：

**回测收益率 = (P_T / P_0) - 1**

其中：
*   **P_T** 代表回测周期结束时的资产总价值（卖出所有头寸后的总金额）。
*   **P_0** 代表回测周期开始时的初始本金。

![](img/4bdd6bbef04d64a0a0d77893ec0f000a_19.png)

![](img/4bdd6bbef04d64a0a0d77893ec0f000a_21.png)

这个公式计算的是资产在整个周期内的**相对增长率**。

## 使用Python计算回测收益率

![](img/4bdd6bbef04d64a0a0d77893ec0f000a_23.png)

上一节我们介绍了回测收益率的概念和公式，本节中我们来看看如何用Python代码实现计算。我们将使用一个示例数据集来模拟策略回测后的每日资产净值走势。

![](img/4bdd6bbef04d64a0a0d77893ec0f000a_25.png)

首先，导入必要的工具包并加载数据。

![](img/4bdd6bbef04d64a0a0d77893ec0f000a_27.png)

```python
import pandas as pd
import matplotlib.pyplot as plt
# 设置中文字体，方便后续图表显示中文
plt.rcParams['font.sans-serif'] = ['SimHei']
plt.rcParams['axes.unicode_minus'] = False

# 读取数据，假设第一列是日期
data = pd.read_csv('your_strategy_result.csv', index_col=0)
print(data.head())
```

![](img/4bdd6bbef04d64a0a0d77893ec0f000a_29.png)

假设我们的 `data` 数据框中有一列 `‘net_value’` 记录了策略每日的资产净值。初始本金 `P_0` 就是第一个交易日的净值。

![](img/4bdd6bbef04d64a0a0d77893ec0f000a_31.png)

![](img/4bdd6bbef04d64a0a0d77893ec0f000a_33.png)

计算回测收益率的第一步，是计算每个交易日相对于初始本金的累计收益曲线。这可以通过将每日净值除以初始净值得到。

![](img/4bdd6bbef04d64a0a0d77893ec0f000a_35.png)

```python
# 计算每日净值相对于初始净值的变化比例
cumulative_return_ratio = data['net_value'] / data['net_value'].iloc[0]
# 绘制累计收益曲线
cumulative_return_ratio.plot(figsize=(12, 6))
plt.title('策略累计收益曲线 (相对于初始本金)')
plt.ylabel('净值比例')
plt.xlabel('日期')
plt.grid(True)
plt.show()
```

![](img/4bdd6bbef04d64a0a0d77893ec0f000a_37.png)

![](img/4bdd6bbef04d64a0a0d77893ec0f000a_39.png)

这张图展示了从初始值1开始，你的策略资产随时间变化的相对情况。

![](img/4bdd6bbef04d64a0a0d77893ec0f000a_41.png)

![](img/4bdd6bbef04d64a0a0d77893ec0f000a_43.png)

接下来，计算最终的回测收益率。根据公式，我们需要取最后一个交易日的净值比例，然后减去1。

![](img/4bdd6bbef04d64a0a0d77893ec0f000a_45.png)

```python
# 计算最终的回测收益率
final_return = cumulative_return_ratio.iloc[-1] - 1

![](img/4bdd6bbef04d64a0a0d77893ec0f000a_47.png)

# 将结果整理成表格形式展示
result_df = pd.DataFrame({
    ‘回测收益率’: [final_return]
}, index=[‘最终结果’])
print(result_df)
```

![](img/4bdd6bbef04d64a0a0d77893ec0f000a_49.png)

![](img/4bdd6bbef04d64a0a0d77893ec0f000a_51.png)

执行以上代码，我们就可以得到该策略在回测周期内的总收益率。这个值可能是正数（盈利），也可能是负数（亏损）。

![](img/4bdd6bbef04d64a0a0d77893ec0f000a_53.png)

---

![](img/4bdd6bbef04d64a0a0d77893ec0f000a_55.png)

本节课中我们一起学习了**回测收益率**这个核心评估指标。我们理解了它代表策略带来的总收益增长比例，掌握了其计算公式 **`(P_T / P_0) - 1`**，并实践了如何使用Python从历史净值数据中计算出该指标。这是评估任何交易策略表现的第一步，为我们后续学习更复杂的风险调整后收益指标（如夏普比率）奠定了基础。