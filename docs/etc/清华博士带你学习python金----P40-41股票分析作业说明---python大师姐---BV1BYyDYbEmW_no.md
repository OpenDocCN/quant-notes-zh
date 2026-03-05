# Python金融量化投资分析：P40：股票分析作业说明 📊

在本节课中，我们将通过一个综合练习来应用之前学到的知识，完成一次简单的股票数据分析与策略模拟。这个练习将涵盖数据获取、条件筛选、以及一个基础的投资策略回测。

![](img/21b58d8cd9668c54ca5fe72956785de4_1.png)

## 概述

本次作业的目标是使用 `tushare` 包获取股票数据，并进行一系列分析。我们将完成四个任务：1）获取并保存股票历史数据；2）筛选单日涨幅较大的日期；3）筛选开盘跳空下跌的日期；4）模拟一个简单的定期投资策略并计算收益。

---

## 第一步：获取并保存股票历史数据 📥

![](img/21b58d8cd9668c54ca5fe72956785de4_3.png)

首先，我们需要获取某只股票的历史行情数据。为了避免每次分析都重复调用接口，我们将数据保存到本地的CSV文件中。

以下是具体步骤：
1.  使用 `tushare` 包获取股票数据。
2.  可以选取任意一只股票，数据时间范围可以从其上市日开始，或选择一个近似的起始点（例如2008年或2010年）。
3.  将获取到的数据保存为CSV文件。

```python
import tushare as ts
import pandas as pd

![](img/21b58d8cd9668c54ca5fe72956785de4_5.png)

# 示例：获取贵州茅台（股票代码：600519）从2010年至今的数据
df = ts.get_k_data('600519', start='2010-01-01')
# 将数据保存到CSV文件
df.to_csv('stock_data.csv', index=False)
```

---

![](img/21b58d8cd9668c54ca5fe72956785de4_7.png)

## 第二步：筛选单日涨幅较大的日期 📈

上一节我们介绍了如何获取和保存数据，本节中我们来看看如何对数据进行条件筛选。第二个任务是：找出所有“收盘价比开盘价上涨超过3%”的交易日。

![](img/21b58d8cd9668c54ca5fe72956785de4_9.png)

以下是实现方法：
*   计算每日的涨跌幅：`(收盘价 - 开盘价) / 开盘价 * 100%`。
*   使用布尔型索引筛选出涨幅大于3%的数据行。
*   提取这些行对应的日期。

```python
# 读取之前保存的数据
df = pd.read_csv('stock_data.csv')
# 计算单日涨幅（百分比）
df['pct_change'] = (df['close'] - df['open']) / df['open'] * 100
# 使用布尔索引筛选涨幅>3%的日期
up_dates = df[df['pct_change'] > 3]['date'].tolist()
print("单日涨幅超过3%的日期：", up_dates[:10]) # 打印前10个结果
```

---

## 第三步：筛选开盘跳空下跌的日期 📉

![](img/21b58d8cd9668c54ca5fe72956785de4_11.png)

在分析了单日内部涨跌后，我们来看看跨交易日的情况。第三个任务是：找出所有“开盘价比前一日收盘价跌幅超过2%”的日期。这需要比较当前交易日的数据与前一交易日的数据。

以下是实现步骤：
*   使用Pandas的 `shift()` 函数将收盘价列向下移动一行，从而让前一日的收盘价与当日的开盘价对齐。
*   计算开盘价相对于前收盘的跌幅：`(开盘价 - 前收盘价) / 前收盘价 * 100%`。
*   使用布尔型索引筛选出跌幅小于-2%的日期。

![](img/21b58d8cd9668c54ca5fe72956785de4_13.png)

```python
# 计算前一日收盘价
df['pre_close'] = df['close'].shift(1)
# 计算开盘相对于前收盘的涨跌幅（百分比）
df['gap_pct'] = (df['open'] - df['pre_close']) / df['pre_close'] * 100
# 筛选开盘跌幅超过2%的日期
gap_down_dates = df[df['gap_pct'] < -2]['date'].tolist()
print("开盘较前日收盘跌超2%的日期：", gap_down_dates[:10])
```

![](img/21b58d8cd9668c54ca5fe72956785de4_15.png)

---

## 第四步：模拟定期投资策略并计算收益 💰

最后，我们将模拟一个简单的投资策略，并计算其收益。这个策略是：从2010年1月1日开始，在每月第一个交易日买入100股（一手），然后在每年最后一个交易日卖出全部持仓。

以下是策略模拟的核心逻辑：
1.  **初始化**：设定起始现金（例如1亿元）、持仓股数为0、并确定策略的起止日期。
2.  **交易模拟**：
    *   遍历每个月的第一个交易日，执行买入操作（现金减少，持仓增加）。
    *   遍历每年的最后一个交易日，执行卖出操作（持仓清零，现金增加）。
3.  **计算最终收益**：
    *   最终资产 = 期末现金 + 期末持仓数量 * 期末股票价格（可用最后一天的收盘价或开盘价计算）。
    *   收益 = 最终资产 - 初始现金。

```python
# 策略模拟示例代码框架
initial_cash = 1e8  # 初始资金1亿元
cash = initial_cash
holdings = 0  # 持有股数
buy_volume = 100  # 每次买入100股

# 确保日期为datetime格式，并设置为索引
df['date'] = pd.to_datetime(df['date'])
df.set_index('date', inplace=True)

# 获取每月第一个交易日和每年最后一个交易日
month_first = df.resample('MS').first().index  # 每月第一天（若交易）
year_last = df.resample('Y').last().index     # 每年最后一天（若交易）

# 简化模拟：遍历所有交易日，在指定日期执行买卖（实际需处理非交易日情况）
for date_idx in df.index:
    # 如果是每月首个交易日，则买入
    if date_idx in month_first:
        price = df.loc[date_idx, 'open']
        cost = price * buy_volume
        if cash >= cost:
            cash -= cost
            holdings += buy_volume
    # 如果是每年最后一个交易日，则卖出全部
    if date_idx in year_last:
        price = df.loc[date_idx, 'open']
        cash += price * holdings
        holdings = 0

# 计算最终收益（假设最后一天以收盘价清仓）
final_price = df.iloc[-1]['close']
final_asset = cash + holdings * final_price
total_profit = final_asset - initial_cash

print(f"策略最终收益为：{total_profit:.2f} 元")
```

---

## 总结

本节课中我们一起完成了一个完整的股票分析小项目。我们学习了：
1.  使用 `tushare` 获取并本地化存储股票数据。
2.  利用Pandas的布尔索引进行数据筛选，找出特定涨跌幅的交易日。
3.  运用 `shift()` 函数进行跨行数据比较。
4.  模拟了一个简单的定期定额投资策略，并计算了其绝对收益。

这个练习涵盖了数据分析的基本流程，为后续学习更复杂的金融指标和量化策略打下了基础。