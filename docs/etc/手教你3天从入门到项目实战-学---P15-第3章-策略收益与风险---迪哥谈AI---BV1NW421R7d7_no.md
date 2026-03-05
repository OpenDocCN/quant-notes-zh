# 金融量化分析：P15：第3章：策略收益与风险评估指标解析 📊

在本节课中，我们将学习如何评估一个量化交易策略的优劣。策略评估不仅仅是看“赚了还是赔了”，而是需要通过一系列标准化的指标来衡量其收益、风险以及与市场基准的对比情况。我们将逐一解析这些核心指标的含义、计算方法，并通过Python代码进行演示，帮助你理解如何在实际中应用它们。

## 概述

当我们完成一个交易策略的构建后，必须在历史数据中进行回测，以检验其有效性。策略评估指标的作用是量化地告诉我们：策略赚了多少、相比市场基准有何优势、承担了哪些风险，以及与其他策略相比的竞争力。本节课将重点介绍几个最常用的评估指标。

## 核心指标解析

上一节我们介绍了策略评估的重要性，本节中我们来看看具体的评估指标。以下是几个最核心的指标，我们将逐一进行讲解。

![](img/de0417fffbbdd939de49cb1dbe4ae504_1.png)

### 1. 回测收益率

回测收益率是最基本、最直观的指标，它衡量了策略在整个回测周期内，相对于初始本金的总体盈利或亏损情况。它不是一个具体的金额，而是一个百分比，表示最终资产净值相对于初始资产净值的增长比率。

其计算公式如下：

**公式：**
`回测收益率 = (期末资产净值 - 期初资产净值) / 期初资产净值`

也可以等价地写作：
`回测收益率 = (P_T / P_0) - 1`
其中，`P_T` 是期末的资产净值，`P_0` 是期初的资产净值。

![](img/de0417fffbbdd939de49cb1dbe4ae504_3.png)

下面，我们通过Python代码来计算并可视化回测收益率的变化过程。

![](img/de0417fffbbdd939de49cb1dbe4ae504_4.png)

首先，导入必要的工具包并加载数据。

```python
import pandas as pd
import matplotlib.pyplot as plt

# 设置中文字体，方便绘图显示
plt.rcParams['font.sans-serif'] = ['SimHei']
plt.rcParams['axes.unicode_minus'] = False

# 读取数据，假设第一列是日期，将其设为索引
data = pd.read_csv('your_stock_data.csv', index_col=0)
data.index = pd.to_datetime(data.index) # 确保索引是日期时间格式
print(data.head())
```

![](img/de0417fffbbdd939de49cb1dbe4ae504_6.png)

![](img/de0417fffbbdd939de49cb1dbe4ae504_7.png)

假设我们的数据 `data` 中有一列 `‘close’` 代表每日的收盘价（或策略的每日资产净值）。我们将其视为策略每日的价值。

```python
# 计算每日净值相对于期初净值的比值
cumulative_return = data['close'] / data['close'].iloc[0]

# 绘制净值增长曲线
plt.figure(figsize=(12, 6))
cumulative_return.plot()
plt.title('策略净值增长曲线 (相对于期初)')
plt.ylabel('净值比值')
plt.xlabel('日期')
plt.grid(True)
plt.show()
```

接下来，计算整个回测周期的总收益率。

![](img/de0417fffbbdd939de49cb1dbe4ae504_9.png)

```python
# 计算总回测收益率
total_return = cumulative_return.iloc[-1] - 1

![](img/de0417fffbbdd939de49cb1dbe4ae504_10.png)

![](img/de0417fffbbdd939de49cb1dbe4ae504_11.png)

# 将结果整理成DataFrame展示
result_df = pd.DataFrame({
    ‘回测收益率’: [total_return]
}, index=[‘策略表现’])
print(result_df)
```

![](img/de0417fffbbdd939de49cb1dbe4ae504_12.png)

![](img/de0417fffbbdd939de49cb1dbe4ae504_13.png)

执行以上代码，我们就可以得到策略在整个回测期间的总收益率。这个值如果是正数，代表盈利；如果是负数，代表亏损。

![](img/de0417fffbbdd939de49cb1dbe4ae504_14.png)

### 2. 年化收益率

仅仅看总收益率可能不够，因为不同策略的运行时间长度不同。年化收益率将总收益率转化为一个标准化的年度收益率，便于不同周期策略之间的比较。

其计算公式基于复利思想：

**公式：**
`年化收益率 = (1 + 总收益率)^(252 / 回测交易日总数) - 1`
其中，252是一年中大约的交易天数。

![](img/de0417fffbbdd939de49cb1dbe4ae504_16.png)

```python
# 假设回测天数为总数据长度
total_days = len(data)
annual_return = (1 + total_return) ** (252 / total_days) - 1
print(f“年化收益率：{annual_return:.2%}”)
```

### 3. 最大回撤

最大回撤衡量的是策略在历史上从任一峰值点下跌到随后最低点的最大幅度，是评估策略下行风险的关键指标。回撤越小，说明策略在逆境中的抗风险能力越强。

计算最大回撤需要先计算策略的累计净值曲线，然后找出历史上最大的跌幅。

![](img/de0417fffbbdd939de49cb1dbe4ae504_18.png)

```python
# 计算累计最大净值（到当前日期为止的历史最高点）
cumulative_max = cumulative_return.cummax()

![](img/de0417fffbbdd939de49cb1dbe4ae504_19.png)

# 计算每日的回撤（当前净值距离历史最高点的跌幅）
daily_drawdown = (cumulative_return - cumulative_max) / cumulative_max

# 计算最大回撤（所有每日回撤中的最小值）
max_drawdown = daily_drawdown.min()
print(f“最大回撤：{max_drawdown:.2%}”)
```

### 4. 夏普比率

夏普比率衡量的是策略每承担一单位总风险（波动率），所获得的超额回报（超过无风险利率的收益）。夏普比率越高，说明策略的风险调整后收益越好。

**公式：**
`夏普比率 = (年化收益率 - 无风险利率) / 年化波动率`

假设无风险利率为0（简化计算），年化波动率是每日收益率标准差的年化值。

```python
# 计算每日收益率
daily_returns = data[‘close’].pct_change().dropna()

# 计算年化波动率
annual_volatility = daily_returns.std() * (252 ** 0.5)

# 计算夏普比率 (假设无风险利率为0)
sharpe_ratio = annual_return / annual_volatility
print(f“夏普比率：{sharpe_ratio:.2f}”)
```

### 5. 索提诺比率

![](img/de0417fffbbdd939de49cb1dbe4ae504_21.png)

索提诺比率与夏普比率类似，但它只考虑下行风险（即不好的波动），而不惩罚上行波动（即好的收益），对于注重下行保护的投资者更具参考价值。

**公式：**
`索提诺比率 = (年化收益率 - 无风险利率) / 下行标准差`

![](img/de0417fffbbdd939de49cb1dbe4ae504_23.png)

```python
# 计算下行收益率（只取负收益）
downside_returns = daily_returns[daily_returns < 0]

![](img/de0417fffbbdd939de49cb1dbe4ae504_24.png)

# 计算下行标准差并年化
downside_std = downside_returns.std() * (252 ** 0.5)

# 计算索提诺比率 (假设无风险利率为0)
sortino_ratio = annual_return / downside_std
print(f“索提诺比率：{sortino_ratio:.2f}”)
```

### 6. 信息比率

![](img/de0417fffbbdd939de49cb1dbe4ae504_26.png)

![](img/de0417fffbbdd939de49cb1dbe4ae504_27.png)

信息比率衡量的是策略相对于某个基准（如沪深300指数）的超额收益的稳定性。比率越高，说明策略持续战胜基准的能力越强。

![](img/de0417fffbbdd939de49cb1dbe4ae504_28.png)

**公式：**
`信息比率 = (策略年化收益率 - 基准年化收益率) / 跟踪误差`
其中，跟踪误差是策略与基准收益率差值的年化标准差。

```python
# 假设我们有一个基准数据 benchmark_returns (例如沪深300的日收益率序列)
# 这里用随机数据示例，实际中需加载真实基准数据
benchmark_returns = pd.Series(np.random.randn(len(daily_returns)) * 0.0005, index=daily_returns.index)

![](img/de0417fffbbdd939de49cb1dbe4ae504_30.png)

# 计算超额收益
excess_returns = daily_returns - benchmark_returns

# 计算信息比率
information_ratio = excess_returns.mean() * 252 / (excess_returns.std() * (252 ** 0.5))
print(f“信息比率：{information_ratio:.2f}”)
```

## 总结

本节课中我们一起学习了量化策略的核心评估指标。我们从最基础的回测收益率开始，逐步深入到年化收益率、最大回撤、夏普比率、索提诺比率和信息比率。每个指标都从不同角度揭示了策略的盈利能力、风险水平和稳定性。

![](img/de0417fffbbdd939de49cb1dbe4ae504_32.png)

理解这些指标的含义至关重要，它们能帮助你在回测平台生成的复杂报告（如下图示例）中，快速抓住策略表现的核心，判断一个策略是否真正具有投资价值，而不仅仅是“感觉上”不错。

![](img/de0417fffbbdd939de49cb1dbe4ae504_33.png)

![](img/de0417fffbbdd939de49cb1dbe4ae504_34.png)

![](img/de0417fffbbdd939de49cb1dbe4ae504_1.png)

![](img/de0417fffbbdd939de49cb1dbe4ae504_35.png)

记住，在实际应用中，不必死记硬背公式，但必须清楚每个指标所反映的策略特性。后续课程中，我们将直接使用专业的回测平台，这些指标都会自动计算并呈现，而本节课的知识将成为你分析和解读那些结果的坚实基础。