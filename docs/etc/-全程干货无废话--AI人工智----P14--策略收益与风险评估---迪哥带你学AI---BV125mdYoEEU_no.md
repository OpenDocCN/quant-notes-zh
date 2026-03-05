# Python金融分析与量化交易实战教程：P14：策略收益与风险评估指标解析

## 概述
在本节课中，我们将学习量化交易策略评估的核心指标。当我们完成一个交易策略后，需要在历史数据上进行测试，以判断其表现。评估指标能告诉我们策略赚了多少、相比市场基准有何优势、面临哪些风险，以及与其他策略相比的优劣。本节课将重点解析第一个核心指标——回测收益率。

![](img/2b874493e4fb7c11c9f57ebfe14ceba2_1.png)

上一节我们介绍了策略评估的重要性，本节中我们来看看如何具体计算和解读回测收益率。

## 回测收益率指标解读

![](img/2b874493e4fb7c11c9f57ebfe14ceba2_3.png)

![](img/2b874493e4fb7c11c9f57ebfe14ceba2_5.png)

回测收益率是最基本、最常用的策略评估指标。它衡量了策略在回测期间的总收益，即相对于初始本金，策略最终赚了多少钱。例如，一个4%的回测收益率意味着，相对于初始投入的本金，策略最终赚取了4%的利润。

![](img/2b874493e4fb7c11c9f57ebfe14ceba2_7.png)

![](img/2b874493e4fb7c11c9f57ebfe14ceba2_9.png)

其计算公式如下：

**回测收益率 = (最终资产价值 / 初始资产价值) - 1**

![](img/2b874493e4fb7c11c9f57ebfe14ceba2_11.png)

![](img/2b874493e4fb7c11c9f57ebfe14ceba2_13.png)

用公式表示为：
`R_total = (P_T / P_0) - 1`
其中，`P_T` 代表回测结束时的资产价值（卖出价格），`P_0` 代表回测开始时的初始资产价值（买入成本）。

![](img/2b874493e4fb7c11c9f57ebfe14ceba2_15.png)

![](img/2b874493e4fb7c11c9f57ebfe14ceba2_17.png)

![](img/2b874493e4fb7c11c9f57ebfe14ceba2_19.png)

下面，我们通过Python代码来演示如何计算和可视化回测收益率。

![](img/2b874493e4fb7c11c9f57ebfe14ceba2_21.png)

首先，导入必要的工具包并读取数据。

```python
import pandas as pd
import matplotlib.pyplot as plt

![](img/2b874493e4fb7c11c9f57ebfe14ceba2_23.png)

# 设置中文字体，方便后续作图显示中文
plt.rcParams['font.sans-serif'] = ['SimHei']
plt.rcParams['axes.unicode_minus'] = False

![](img/2b874493e4fb7c11c9f57ebfe14ceba2_25.png)

![](img/2b874493e4fb7c11c9f57ebfe14ceba2_27.png)

# 读取数据，指定日期列为索引
data = pd.read_csv('your_stock_data.csv', index_col=0)
print(data.head())
```

数据读取后，我们得到了股票的历史行情数据。接下来，我们基于这些数据模拟一个策略的回测过程。为简化演示，我们直接使用收盘价序列作为策略的资产价值走势。

计算回测收益率的第一步，是计算每个时间点资产相对于初始时刻的比值。

```python
# 计算每个时间点的资产价值相对于初始时刻的比值
relative_value = data['close'] / data['close'].iloc[0]

![](img/2b874493e4fb7c11c9f57ebfe14ceba2_29.png)

# 绘制资产相对价值变化图
plt.figure(figsize=(10, 6))
relative_value.plot()
plt.title('资产相对价值变化曲线')
plt.xlabel('日期')
plt.ylabel('相对价值 (初始值=1)')
plt.grid(True)
plt.show()
```

![](img/2b874493e4fb7c11c9f57ebfe14ceba2_31.png)

![](img/2b874493e4fb7c11c9f57ebfe14ceba2_33.png)

这张图展示了从回测开始到结束，资产价值相对于初始本金（设为1）的变化过程。曲线上升代表盈利，下降代表亏损。

![](img/2b874493e4fb7c11c9f57ebfe14ceba2_35.png)

![](img/2b874493e4fb7c11c9f57ebfe14ceba2_37.png)

最后，我们根据公式计算整个回测期间的总收益率。

![](img/2b874493e4fb7c11c9f57ebfe14ceba2_39.png)

```python
# 计算总回测收益率
total_return = (data['close'].iloc[-1] / data['close'].iloc[0]) - 1

![](img/2b874493e4fb7c11c9f57ebfe14ceba2_41.png)

![](img/2b874493e4fb7c11c9f57ebfe14ceba2_43.png)

# 将结果整理成DataFrame，便于查看
result_df = pd.DataFrame({
    '回测收益率': [total_return]
}, index=['策略表现'])
print(result_df)
```

![](img/2b874493e4fb7c11c9f57ebfe14ceba2_45.png)

执行以上代码，我们便得到了该策略在选定回测周期内的总收益率。这个数值直观地告诉我们策略的最终盈亏情况。

![](img/2b874493e4fb7c11c9f57ebfe14ceba2_47.png)

![](img/2b874493e4fb7c11c9f57ebfe14ceba2_49.png)

## 总结
本节课中我们一起学习了量化策略评估的第一个核心指标——回测收益率。我们理解了它的定义，即策略在回测期间的总收益相对于初始本金的比例。通过公式 `R_total = (P_T / P_0) - 1` 和实际的Python代码演示，我们掌握了如何从历史数据中计算并可视化这一指标。回测收益率是评估策略盈利能力的起点，在后续课程中，我们将在此基础上，引入更多维度（如风险、与基准对比等）的评估指标，以全面衡量策略的优劣。