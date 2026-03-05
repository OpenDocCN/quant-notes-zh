# Python量化64：最实用的策略 - P1 - LuQuant

![](img/31173f1a387bcbff420ebf7f89624c97_0.png)

## 📊 概述

在本节课中，我们将学习如何分析一个已部署的自动化交易策略在近一个月内的实际表现。我们将从经纪商平台下载交易数据，使用Python进行数据清洗、可视化，并通过统计分析来评估策略的性能，同时探讨潜在的改进方向。

![](img/31173f1a387bcbff420ebf7f89624c97_2.png)

---

![](img/31173f1a387bcbff420ebf7f89624c97_3.png)

## 📈 1：数据获取与初步观察

上一节我们介绍了策略的基本构成和部署过程。本节中，我们来看看如何获取和分析实盘交易数据。

我们的交易数据来自经纪商平台，以CSV文件形式提供。该文件包含了交易分析所需的所有详细信息，例如交易日期、类型、资产、价格、数量、方向、佣金以及最终的盈亏。

![](img/31173f1a387bcbff420ebf7f89624c97_5.png)

以下是加载和查看数据的代码示例：

```python
import pandas as pd

![](img/31173f1a387bcbff420ebf7f89624c97_7.png)

# 加载交易数据
df = pd.read_csv('trading_data.csv')
print(df.head())
```

数据加载后，我们首先将`交易日期和时间`列转换为标准的日期时间格式，并从中分离出单独的`交易日期`和`交易时间`列，以便后续按日期和时间进行分析。

```python
# 转换日期时间格式并拆分
df['交易日期时间'] = pd.to_datetime(df['交易日期和时间'])
df['交易日期'] = df['交易日期时间'].dt.date
df['交易时间'] = df['交易日期时间'].dt.time
```

![](img/31173f1a387bcbff420ebf7f89624c97_9.png)

---

## 🧹 2：数据清洗与权益曲线绘制

在开始深入分析前，我们需要对数据进行清洗。由于每笔交易在数据中可能对应多行记录（如开仓、修改、平仓），我们只关心最终产生盈亏的那一行。

以下是筛选出有效盈亏记录并绘制账户权益曲线的步骤：

![](img/31173f1a387bcbff420ebf7f89624c97_11.png)

```python
# 1. 清理盈亏列为空的行
df_clean = df[df['利润/损失'].notna()].copy()

![](img/31173f1a387bcbff420ebf7f89624c97_13.png)

# 2. 绘制权益曲线
# 假设‘余额’列记录了每次交易后的账户净值
import matplotlib.pyplot as plt
plt.figure(figsize=(12,6))
plt.plot(df_clean['交易日期'], df_clean['余额'])
plt.title('账户权益曲线')
plt.xlabel('日期')
plt.ylabel('余额（美元）')
plt.grid(True)
plt.show()
```

从绘制的权益曲线可以看到，初始资金为2500美元，在一个月后下降至约2200美元，回撤幅度约为12%。这与该策略在历史回测中经历的漫长下跌期特征相符。

---

## 📊 3：按日盈亏分析

为了更细致地了解策略表现，我们接下来分析每天的净盈亏情况。

以下是计算并绘制每日盈亏的代码：

```python
# 按交易日期汇总每日盈亏
daily_pnl = df_clean.groupby('交易日期')['利润/损失'].sum()

plt.figure(figsize=(12,6))
plt.bar(daily_pnl.index, daily_pnl.values)
plt.axhline(y=0, color='r', linestyle='-', alpha=0.3) # 添加零线
plt.title('每日盈亏')
plt.xlabel('日期')
plt.ylabel('盈亏（美元）')
plt.xticks(rotation=45)
plt.grid(True)
plt.show()
```

![](img/31173f1a387bcbff420ebf7f89624c97_15.png)

通过图表可以发现，虽然存在盈利日，但亏损日的数量更多，且单日亏损幅度更大。这提示我们需要重点关注那些造成大幅亏损的交易，分析其成因，例如是否由特定的市场模式或信号错误导致。

---

## ⏰ 4：交易时间与盈亏的关系分析

![](img/31173f1a387bcbff420ebf7f89624c97_17.png)

一个自然的疑问是：策略的表现是否与交易时间有关？例如，是否在特定时段表现更好或更差？为此，我们分析盈利交易和亏损交易的发生时间分布。

以下是分析步骤：

首先，我们需要将交易时间字符串转换为可用于分析和统计的数值格式（例如小时数）。

![](img/31173f1a387bcbff420ebf7f89624c97_19.png)

```python
# 将交易时间转换为小时数（整数）
df_clean['交易小时'] = pd.to_datetime(df_clean['交易时间'], format='%H:%M:%S').dt.hour

![](img/31173f1a387bcbff420ebf7f89624c97_21.png)

# 分离盈利和亏损交易的时间
positive_times = df_clean[df_clean['利润/损失'] > 0]['交易小时']
negative_times = df_clean[df_clean['利润/损失'] < 0]['交易小时']
```

![](img/31173f1a387bcbff420ebf7f89624c97_22.png)

接着，我们可以通过可视化直观比较：

```python
import seaborn as sns
plt.figure(figsize=(10,6))
sns.histplot(positive_times, color='g', label='盈利交易', kde=True, bins=24)
sns.histplot(negative_times, color='r', label='亏损交易', kde=True, bins=24, alpha=0.6)
plt.title('盈利与亏损交易的时间分布')
plt.xlabel('交易小时')
plt.ylabel('频次')
plt.legend()
plt.grid(True)
plt.show()
```

![](img/31173f1a387bcbff420ebf7f89624c97_24.png)

从分布图来看，盈利和亏损交易在一天中各时段的分布没有明显差异。

![](img/31173f1a387bcbff420ebf7f89624c97_26.png)

---

## 📊 5：统计检验验证

为了从统计上确认“交易时间对盈亏没有显著影响”这一观察，我们进行假设检验。我们同时使用T检验（检验均值差异）和曼-惠特尼U检验（检验中位数差异，不要求正态分布）。

![](img/31173f1a387bcbff420ebf7f89624c97_28.png)

以下是进行统计检验的代码：

![](img/31173f1a387bcbff420ebf7f89624c97_30.png)

```python
from scipy import stats

# T检验
t_stat, t_p = stats.ttest_ind(positive_times, negative_times, nan_policy='omit')
print(f"T检验 p值: {t_p:.4f}")

# 曼-惠特尼U检验
u_stat, u_p = stats.mannwhitneyu(positive_times, negative_times, alternative='two-sided')
print(f"曼-惠特尼U检验 p值: {u_p:.4f}")
```

两个检验得到的p值均远大于0.05的显著性水平。这意味着，从统计学上看，盈利交易和亏损交易的发生时间在均值或中位数上并无显著差异，从而证实了我们的直观观察。

---

## 💡 6：策略回顾与改进思路

在分析了上月实盘表现后，我们现在回顾策略并思考改进方案。我们的策略核心是**布林带（Bollinger Bands）**与**两条移动平均线（Moving Averages）**的结合来产生交易信号。

![](img/31173f1a387bcbff420ebf7f89624c97_32.png)

其入场逻辑可以用以下伪代码描述：
```
IF (价格上穿快均线 AND 价格低于布林带上轨) THEN
    生成买入信号
ELSE IF (价格下穿慢均线 AND 价格高于布林带下轨) THEN
    生成卖出信号
```

![](img/31173f1a387bcbff420ebf7f89624c97_33.png)

然而，实盘表现表明，策略的**交易管理部分（即何时平仓）**是当前的薄弱环节。我们使用了简单的固定止盈止损方法。

基于本次分析，我们提出以下几点改进思路：

以下是几个关键的改进方向：

1.  **优化交易时间窗口**：本次实盘将交易限制在上午8点至晚上7点（GMT+2）。未来应考虑全天候交易，以获取更全面的时间影响数据。
2.  **动态参数优化**：策略参数是基于两年前的历史数据优化的。应建立定期（例如每周）使用最新数据重新优化参数的自动化流程，使策略适应市场变化。
3.  **改进出场策略**：这是提升绩效的关键。需要设计更动态的止盈止损逻辑，例如基于波动率调整、移动止损、或结合其他指标确认出场信号。
4.  **扩大数据样本**：一个月的交易数据量有限，统计结论不确定性较高。需要更长时间的实盘运行来验证策略的稳定性和改进效果。

---

## 🎯 总结

![](img/31173f1a387bcbff420ebf7f89624c97_35.png)

本节课中我们一起学习了如何对实盘交易策略进行绩效分析。我们完成了从数据获取、清洗、可视化到统计分析的全过程。分析显示，该策略在实盘首月出现了预期内的回撤，且盈亏与交易时间无显著关联。核心问题在于交易管理模块较为简单。未来工作的重点将放在建立参数定期优化机制和设计更智能的动态出场策略上。

![](img/31173f1a387bcbff420ebf7f89624c97_36.png)

通过这种严谨的事后分析，我们能够发现问题、验证想法，并指导策略迭代，这是量化交易流程中至关重要的一环。