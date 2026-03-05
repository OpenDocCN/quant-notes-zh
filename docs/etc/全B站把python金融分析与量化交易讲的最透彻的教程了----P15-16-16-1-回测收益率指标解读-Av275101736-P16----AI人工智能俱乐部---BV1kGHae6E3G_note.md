# Python金融量化与股票交易：P15：16.16.1-回测收益率指标解读 📈

## 概述
在本节课中，我们将学习量化交易策略评估的核心指标之一：回测收益率。理解这个指标是评估一个策略是否盈利以及盈利多少的基础。

## 策略评估指标简介
上一节我们介绍了策略评估的重要性。当我们完成一个交易策略后，必须在历史数据中进行测试。测试的目的不仅仅是知道策略是赚是赔，还需要量化具体的收益，并与市场基准进行比较，同时评估策略所承担的风险。这些都需要通过具体的评估指标来完成。

![](img/408b0bc9f726fb5f6dfe5ac253075ce7_1.png)

以下是策略评估中常用的几个核心指标，本节课我们将重点讲解第一个。

*   回测收益率
*   年化收益率
*   夏普比率
*   最大回撤
*   阿尔法与贝塔

![](img/408b0bc9f726fb5f6dfe5ac253075ce7_3.png)

## 回测收益率详解
本节中我们来看看最基础也是最重要的指标：回测收益率。它衡量的是策略在整个回测周期内的总收益情况。

![](img/408b0bc9f726fb5f6dfe5ac253075ce7_4.png)

回测收益率描述的是：在完成一个策略回测后，最终获得的收益相对于初始本金的增长比例。例如，一个结果为40%的回测收益率意味着，相对于初始投入的本金，策略最终赚取了40%的利润。

![](img/408b0bc9f726fb5f6dfe5ac253075ce7_6.png)

其计算公式如下：

![](img/408b0bc9f726fb5f6dfe5ac253075ce7_7.png)

**回测收益率 = (最终资产价值 - 初始资产价值) / 初始资产价值**

![](img/408b0bc9f726fb5f6dfe5ac253075ce7_9.png)

也可以等价地写作：

![](img/408b0bc9f726fb5f6dfe5ac253075ce7_10.png)

![](img/408b0bc9f726fb5f6dfe5ac253075ce7_11.png)

![](img/408b0bc9f726fb5f6dfe5ac253075ce7_12.png)

**回测收益率 = (P_T / P_0) - 1**

![](img/408b0bc9f726fb5f6dfe5ac253075ce7_13.png)

![](img/408b0bc9f726fb5f6dfe5ac253075ce7_14.png)

其中：
*   `P_T` 代表回测结束时的资产价值（卖出价格）。
*   `P_0` 代表回测开始时的资产价值（买入价格）。

这个公式的核心是计算资产价值的相对增长。

![](img/408b0bc9f726fb5f6dfe5ac253075ce7_16.png)

## 使用Python计算回测收益率
接下来，我们通过Python代码来演示如何计算回测收益率。我们将使用一份示例股票数据，假设其收盘价序列就是我们策略回测后的每日资产净值。

首先，导入必要的工具包并加载数据。

![](img/408b0bc9f726fb5f6dfe5ac253075ce7_18.png)

![](img/408b0bc9f726fb5f6dfe5ac253075ce7_19.png)

```python
import pandas as pd
import matplotlib.pyplot as plt

# 设置中文字体，方便绘图显示
plt.rcParams['font.sans-serif'] = ['SimHei']
plt.rcParams['axes.unicode_minus'] = False

# 读取数据，假设第一列是日期
data = pd.read_csv('your_stock_data.csv', index_col=0)
print(data.head())
```

假设数据中的 `close` 列代表每日策略的资产净值。我们首先计算每个时间点资产相对于初始资产的比例。

```python
# 计算每日资产净值相对于初始净值的比值
cumulative_return = data['close'] / data['close'].iloc[0]

![](img/408b0bc9f726fb5f6dfe5ac253075ce7_21.png)

# 绘制资产净值变化曲线
plt.figure(figsize=(12, 6))
cumulative_return.plot()
plt.title('资产净值相对变化曲线')
plt.xlabel('日期')
plt.ylabel('净值比例 (相对于期初)')
plt.grid(True)
plt.show()
```

![](img/408b0bc9f726fb5f6dfe5ac253075ce7_23.png)

![](img/408b0bc9f726fb5f6dfe5ac253075ce7_24.png)

这张图展示了从期初开始，资产价值随时间的变化情况。1.0的水平线代表初始本金。曲线在1.0以上表示盈利，以下表示亏损。

最后，我们计算整个回测周期的总收益率。

![](img/408b0bc9f726fb5f6dfe5ac253075ce7_26.png)

![](img/408b0bc9f726fb5f6dfe5ac253075ce7_27.png)

![](img/408b0bc9f726fb5f6dfe5ac253075ce7_28.png)

```python
# 计算最终的回测收益率： (最终净值 / 初始净值) - 1
total_return = cumulative_return.iloc[-1] - 1

# 将结果整理成DataFrame以便查看
result_df = pd.DataFrame({
    '回测收益率': [total_return]
}, index=['策略'])

![](img/408b0bc9f726fb5f6dfe5ac253075ce7_30.png)

print(result_df)
```

![](img/408b0bc9f726fb5f6dfe5ac253075ce7_32.png)

执行以上代码，我们便得到了该策略在回测周期内的总收益率。这个数值直观地告诉我们策略的整体表现是盈利还是亏损，以及盈利或亏损的幅度。

![](img/408b0bc9f726fb5f6dfe5ac253075ce7_33.png)

![](img/408b0bc9f726fb5f6dfe5ac253075ce7_34.png)

![](img/408b0bc9f726fb5f6dfe5ac253075ce7_35.png)

## 总结
本节课中我们一起学习了量化策略评估的第一个核心指标——回测收益率。我们了解了它的定义，即策略最终收益相对于初始本金的比例。我们学习了其计算公式 **`(P_T / P_0) - 1`**，并通过Python代码演示了如何基于资产净值序列计算该指标。理解回测收益率是读懂任何策略回测报告的第一步，它给出了策略盈利能力的总体概览。在接下来的课程中，我们将继续深入其他更复杂的评估指标。