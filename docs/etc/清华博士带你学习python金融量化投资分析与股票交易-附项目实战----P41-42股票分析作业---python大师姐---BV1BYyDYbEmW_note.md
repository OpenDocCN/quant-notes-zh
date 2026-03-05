# Python金融量化投资分析：P41：股票分析作业 📈

![](img/f0e612a29aadef836d037b9bc8b72f1f_1.png)

![](img/f0e612a29aadef836d037b9bc8b72f1f_3.png)

![](img/f0e612a29aadef836d037b9bc8b72f1f_5.png)

![](img/f0e612a29aadef836d037b9bc8b72f1f_7.png)

在本节课中，我们将学习如何利用Python进行基础的股票数据分析。我们将通过一个具体的作业案例，从获取股票数据开始，逐步分析特定条件下的交易日期，并最终模拟一个简单的长期交易策略来计算收益。

![](img/f0e612a29aadef836d037b9bc8b72f1f_9.png)

![](img/f0e612a29aadef836d037b9bc8b72f1f_11.png)

![](img/f0e612a29aadef836d037b9bc8b72f1f_13.png)

---

## 第一步：导入必要的库 📦

![](img/f0e612a29aadef836d037b9bc8b72f1f_15.png)

![](img/f0e612a29aadef836d037b9bc8b72f1f_17.png)

首先，我们需要导入进行数据分析所需的Python库。这些库提供了数据处理、数值计算和金融数据获取等功能。

以下是需要导入的库：
*   `pandas`：用于数据处理和分析。
*   `numpy`：用于数值计算。
*   `tushare`：用于获取金融数据。
*   `matplotlib`：用于数据可视化（本练习中未使用，但通常需要）。
*   `jupyter`：用于交互式编程环境。

```python
import pandas as pd
import numpy as np
import tushare as ts
import matplotlib.pyplot as plt
```

---

![](img/f0e612a29aadef836d037b9bc8b72f1f_19.png)

![](img/f0e612a29aadef836d037b9bc8b72f1f_21.png)

## 第二步：获取并保存股票数据 💾

上一节我们导入了必要的工具，本节中我们来看看如何获取目标股票的历史数据。

使用 `tushare` 包获取某只股票（例如贵州茅台，代码 `600519`）的历史行情数据，并将数据保存到本地CSV文件，方便后续重复使用。

```python
# 获取贵州茅台（600519）从2001年至今的历史数据
df = ts.get_k_data('600519', start='2001-01-01')
# 将数据保存到本地文件
df.to_csv('600519.csv', index=False)
```

---

## 第三步：加载并预处理数据 🔧

![](img/f0e612a29aadef836d037b9bc8b72f1f_23.png)

![](img/f0e612a29aadef836d037b9bc8b72f1f_25.png)

数据已经保存到本地，接下来我们需要加载它并进行初步的清理，以便于后续分析。

![](img/f0e612a29aadef836d037b9bc8b72f1f_27.png)

从保存的CSV文件中读取数据，将日期列设置为索引，并转换为时间序列对象。同时，我们只保留开盘价(`open`)、收盘价(`close`)、最高价(`high`)、最低价(`low`)这四列核心价格数据。

```python
# 从文件加载数据，并指定日期列为索引
df = pd.read_csv('600519.csv', index_col='date', parse_dates=['date'])
# 只保留‘open’， ‘close’， ‘high’， ‘low’四列
df = df[['open', 'close', 'high', 'low']]
```

---

## 第四步：分析上涨行情日期 📅

数据准备就绪后，我们可以开始进行具体的条件分析了。首先，我们来找出所有收盘价比开盘价上涨超过3%的交易日。

逻辑是计算每日的涨幅率 `(close - open) / open`，然后筛选出涨幅大于等于3%的日期。

```python
# 计算每日涨幅，并筛选涨幅 >= 3% 的日期
rise_dates = df[(df['close'] - df['open']) / df['open'] >= 0.03].index
print("收盘比开盘上涨3%以上的日期：")
print(rise_dates)
```

---

## 第五步：分析跳空低开日期 📉

![](img/f0e612a29aadef836d037b9bc8b72f1f_29.png)

![](img/f0e612a29aadef836d037b9bc8b72f1f_31.png)

在分析了上涨日期后，我们再来看看另一种市场情况：开盘价相对于前一日收盘价大幅低开。

这里的关键是让当日的开盘价与前一天的收盘价进行比较。我们使用 `shift()` 函数将收盘价列向下移动一行，从而实现数据的对齐。

```python
# 计算开盘价相对于前日收盘价的跌幅，并筛选跌幅超过2%的日期
gap_down_dates = df[(df['open'] - df['close'].shift(1)) / df['close'].shift(1) <= -0.02].index
print("开盘比前日收盘跌幅超过2%的日期：")
print(gap_down_dates)
```

---

## 第六步：模拟长期交易策略并计算收益 💰

最后，也是最复杂的一部分，我们将模拟一个简单的长期交易策略，并计算其最终收益。

策略规则如下：
1.  从2010年1月1日开始。
2.  每月第一个交易日买入一手（100股）。
3.  每年最后一个交易日卖出所有持仓的股票。
4.  计算截至2017年底的总收益。

![](img/f0e612a29aadef836d037b9bc8b72f1f_33.png)

![](img/f0e612a29aadef836d037b9bc8b72f1f_35.png)

以下是实现该策略的步骤和代码：

```python
# 1. 截取2010-01-01至2017-12-31期间的数据
df = df['2010-01-01':'2017-12-31']

# 2. 获取每月第一个交易日的开盘价数据
df_monthly = df.resample('MS').first()  # ‘MS’ 表示月初

# 3. 获取每年最后一个交易日的开盘价数据
df_yearly = df.resample('A').last()     # ‘A’ 表示年末
# 剔除最后一年的数据，因为我们的策略在每年末卖出，而最后一年末未到卖出时机
df_yearly = df_yearly.iloc[:-1]

# 4. 初始化变量：总花费（负值表示投入）和持有股数
cost_money = 0
hold_stocks = 0

# 5. 模拟2010年至2017年的交易
for year in range(2010, 2018):
    # 每年每月买入
    monthly_cost = df_monthly[str(year)]['open'].sum() * 100  # 每月买一手
    cost_money -= monthly_cost  # 花费资金，总资金减少
    hold_stocks += len(df_monthly[str(year)]) * 100  # 增加持有股数

    # 如果不是2017年，则在年末卖出
    if year != 2017:
        sell_price = df_yearly[str(year)]['open'].iloc[0]  # 当年最后一个交易日的开盘价
        sell_money = sell_price * hold_stocks  # 卖出所得金额
        cost_money += sell_money  # 资金回流
        hold_stocks = 0  # 清空持仓

![](img/f0e612a29aadef836d037b9bc8b72f1f_37.png)

# 6. 计算最终收益（2017年末未卖出，按当日股价计算持仓价值）
last_price = df['open'].iloc[-1]  # 数据集中最后一个交易日的开盘价
final_value = hold_stocks * last_price  # 最终持仓市值
total_profit = cost_money + final_value  # 总盈利 = 现金流 + 持仓市值

![](img/f0e612a29aadef836d037b9bc8b72f1f_39.png)

print(f"模拟策略总收益为：{total_profit:.2f} 元")
```

![](img/f0e612a29aadef836d037b9bc8b72f1f_41.png)

---

![](img/f0e612a29aadef836d037b9bc8b72f1f_43.png)

本节课中我们一起学习了如何用Python完成一次完整的股票数据分析作业。我们从数据获取与预处理开始，实践了基于条件的日期筛选，并最终实现并模拟了一个定期投资策略的收益计算。这个过程涵盖了金融数据分析的常见步骤，是量化投资入门的重要实践。在下一个练习中，我们将探索更复杂的“双均线交易策略”。