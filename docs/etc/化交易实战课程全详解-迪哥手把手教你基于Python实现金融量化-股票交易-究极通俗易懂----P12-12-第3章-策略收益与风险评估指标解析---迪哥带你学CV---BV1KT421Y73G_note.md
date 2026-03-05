# Python金融分析与量化交易实战课程：P12：第3章：策略收益与风险评估指标解析 📊

![](img/6cf72234eac1004fea08b6710b50f00a_1.png)

在本节课中，我们将学习如何评估一个量化交易策略的优劣。当我们完成一个策略设计后，需要在历史数据上进行测试，以判断其表现。评估不仅仅是看“赚”或“赔”，而是需要一系列具体的指标来衡量收益、风险以及与市场基准的对比。本节课将逐一解析这些核心评估指标，帮助你读懂策略分析报告。

上一节我们介绍了策略回测的基本概念，本节中我们来看看如何具体衡量一个策略的表现。

## 回测收益率 📈

![](img/6cf72234eac1004fea08b6710b50f00a_3.png)

![](img/6cf72234eac1004fea08b6710b50f00a_5.png)

![](img/6cf72234eac1004fea08b6710b50f00a_7.png)

回测收益率是最基本、最常用的评估指标。它衡量的是在整个回测周期内，策略相对于初始本金的盈利或亏损比例。例如，一个4%的回测收益率意味着，相对于初始投入，策略最终带来了4%的收益。

![](img/6cf72234eac1004fea08b6710b50f00a_9.png)

其计算公式如下：

![](img/6cf72234eac1004fea08b6710b50f00a_11.png)

![](img/6cf72234eac1004fea08b6710b50f00a_13.png)

**公式：**
`回测收益率 = (期末资产总值 - 期初资产总值) / 期初资产总值`

![](img/6cf72234eac1004fea08b6710b50f00a_15.png)

![](img/6cf72234eac1004fea08b6710b50f00a_17.png)

或者等价于：
`回测收益率 = (卖出价格 Pt - 买入价格 P0) / 买入价格 P0`

![](img/6cf72234eac1004fea08b6710b50f00a_19.png)

![](img/6cf72234eac1004fea08b6710b50f00a_21.png)

接下来，我们通过代码来演示如何计算并可视化回测收益率的变化过程。

![](img/6cf72234eac1004fea08b6710b50f00a_23.png)

首先，导入必要的工具包并加载数据。

```python
import pandas as pd
import matplotlib.pyplot as plt

![](img/6cf72234eac1004fea08b6710b50f00a_25.png)

![](img/6cf72234eac1004fea08b6710b50f00a_27.png)

# 设置中文字体，方便后续绘图显示中文
plt.rcParams['font.sans-serif'] = ['SimHei']
plt.rcParams['axes.unicode_minus'] = False

# 读取数据，假设第一列是日期，将其设为索引
data = pd.read_csv('your_stock_data.csv', index_col=0)
print(data.head())
```

假设我们有一份股票的历史数据，其中包含每日的收盘价。我们将这个收盘价序列视为策略执行后的每日资产净值。

以下是计算每日相对收益和最终回测收益率的步骤：

![](img/6cf72234eac1004fea08b6710b50f00a_29.png)

![](img/6cf72234eac1004fea08b6710b50f00a_31.png)

```python
# 1. 计算每日资产净值相对于期初的比值
# 即：每日收盘价 / 第一期收盘价
relative_value = data['close'] / data['close'].iloc[0]

![](img/6cf72234eac1004fea08b6710b50f00a_33.png)

![](img/6cf72234eac1004fea08b6710b50f00a_35.png)

# 2. 绘制相对价值变化曲线
plt.figure(figsize=(12, 6))
relative_value.plot()
plt.title('资产净值相对变化曲线')
plt.xlabel('日期')
plt.ylabel('相对价值 (基准=1)')
plt.grid(True)
plt.show()

![](img/6cf72234eac1004fea08b6710b50f00a_37.png)

![](img/6cf72234eac1004fea08b6710b50f00a_39.png)

# 3. 计算最终的回测收益率
# 公式：(期末价值 / 期初价值) - 1
final_return = (data['close'].iloc[-1] / data['close'].iloc[0]) - 1

![](img/6cf72234eac1004fea08b6710b50f00a_41.png)

# 4. 将结果整理成DataFrame展示
result_df = pd.DataFrame({
    '回测收益率': [final_return]
}, index=['最终结果'])
print(result_df)
```

![](img/6cf72234eac1004fea08b6710b50f00a_43.png)

![](img/6cf72234eac1004fea08b6710b50f00a_45.png)

通过以上代码，我们可以得到策略在整个回测周期内的最终收益率，并看到资产净值随时间变化的趋势图。

![](img/6cf72234eac1004fea08b6710b50f00a_47.png)

![](img/6cf72234eac1004fea08b6710b50f00a_49.png)

本节课中我们一起学习了量化策略评估的第一个核心指标——回测收益率。它直观地反映了策略的绝对盈利能力。然而，仅看收益是不够的，我们还需要考虑风险。在接下来的章节中，我们将继续学习其他重要的风险评估指标。