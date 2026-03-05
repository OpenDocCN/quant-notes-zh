# Python量化分析06：P1：多资产策略测试与优化 🚀

![](img/e7d9ff2f0687fd315d4dddb5c7c17ac3_1.png)

![](img/e7d9ff2f0687fd315d4dddb5c7c17ac3_2.png)

在本节课中，我们将学习如何在多种资产上测试和优化交易策略，汇总结果以提高总胜率和总回报。我们将使用一个结合移动平均线、布林带和RSI的简单策略，并在多种外汇和黄金资产上进行回测，同时实现并优化追踪止损功能。

![](img/e7d9ff2f0687fd315d4dddb5c7c17ac3_4.png)

![](img/e7d9ff2f0687fd315d4dddb5c7c17ac3_5.png)

## 概述

交易中，使用高选择性指标的策略通常胜率很高，但交易信号稀少，导致自动化系统长时间闲置。本节课的目标是展示如何通过在多资产上应用同一策略来增加交易机会，从而提高资金利用效率和整体回报。我们将使用Python编写策略，对数据文件夹中的所有CSV文件进行批量回测，并优化关键参数。

![](img/e7d9ff2f0687fd315d4dddb5c7c17ac3_7.png)

## 策略逻辑与指标

![](img/e7d9ff2f0687fd315d4dddb5c7c17ac3_9.png)

上一节我们介绍了课程目标，本节中我们来看看具体的交易策略逻辑。

我们的入场策略基于三个核心条件：趋势判断、动量确认和布林带突破。

![](img/e7d9ff2f0687fd315d4dddb5c7c17ac3_11.png)

1.  **趋势判断**：使用快速和慢速移动平均线（MA）。我们检查一个后视窗口内的蜡烛，如果快速MA始终低于慢速MA，则判定为下降趋势；反之则为上升趋势。
    *   **公式**：对于窗口内的每个蜡烛 `i`，检查 `fast_ma[i] < slow_ma[i]`（看跌）或 `fast_ma[i] > slow_ma[i]`（看涨）是否恒成立。

![](img/e7d9ff2f0687fd315d4dddb5c7c17ac3_13.png)

2.  **动量确认**：在同样的后视窗口内，检查所有蜡烛的收盘价是否都低于（对于下降趋势）或高于（对于上升趋势）快速移动平均线，以确认趋势强度。

![](img/e7d9ff2f0687fd315d4dddb5c7c17ac3_15.png)

3.  **布林带突破**：在下降趋势中，等待蜡烛收盘价**高于**布林带上轨，预期价格将向中轨回归，从而开立空头仓位。在上升趋势中，则等待蜡烛收盘价**低于**布林带下轨以开立多头仓位。同时，我们设置一个布林带宽度（Band Width）阈值，以避免在波动性极低时产生无效信号。

![](img/e7d9ff2f0687fd315d4dddb5c7c17ac3_17.png)

4.  **RSI过滤**：仅在开立多头仓位时，要求后视窗口内的RSI值均**高于30**，以确认上涨动能，避免在动能不足时入场。空头仓位则使用对称逻辑。

## 代码实现：数据读取与信号生成

以下是实现策略所需的核心函数。我们首先从指定文件夹读取所有资产的CSV数据。

```python
import pandas as pd
import os

def read_csv_to_dataframe(filepath):
    """读取CSV文件到DataFrame，并清理时间列。"""
    df = pd.read_csv(filepath)
    df['time'] = pd.to_datetime(df['time'], utc=True)
    # ... 其他数据清理步骤
    return df

def read_data_folder(folder_path):
    """读取文件夹内所有CSV文件，返回DataFrame列表和对应的文件名列表。"""
    dataframes = []
    filenames = []
    for file in os.listdir(folder_path):
        if file.endswith('.csv'):
            path = os.path.join(folder_path, file)
            df = read_csv_to_dataframe(path)
            dataframes.append(df)
            filenames.append(file.replace('.csv', ''))
    return dataframes, filenames
```

接下来，我们定义函数来计算技术指标和生成交易信号。

```python
def apply_technical_indicators(df):
    """在DataFrame上计算策略所需的技术指标（如MA, 布林带, RSI）。"""
    df['fast_ma'] = df['close'].rolling(window=10).mean()
    df['slow_ma'] = df['close'].rolling(window=30).mean()
    # 计算布林带和RSI...
    return df

def ma_signal(df, current_idx, lookback):
    """根据移动平均线判断趋势。返回 1(看跌), 2(看涨), 0(无趋势)。"""
    # ... 逻辑实现
    pass

def update_ma_signal(df, lookback=5):
    """为整个DataFrame添加移动平均线信号列。"""
    df['ma_signal'] = df.apply(lambda row: ma_signal(df, row.name, lookback), axis=1)
    return df

def total_signal(row, lookback=5, bb_width_threshold=0.01):
    """综合布林带、宽度和RSI条件，生成初始总信号。"""
    # ... 逻辑实现
    pass

def add_total_signal(df):
    """为DataFrame添加综合信号列。"""
    df['total_signal'] = df.apply(total_signal, axis=1)
    return df
```

最后，我们使用蜡烛收盘价与移动平均线的相对位置进行最终信号确认。

```python
def candle_trend_signal(df, current_idx, lookback):
    """根据蜡烛与快速MA的位置进行趋势确认。"""
    # ... 逻辑实现
    pass

def update_final_signal(df, lookback=5):
    """用蜡烛趋势信号确认或拒绝初始总信号，生成最终信号列。"""
    df['final_signal'] = 0
    for i in range(len(df)):
        ma_sig = df.iloc[i]['ma_signal']
        candle_sig = candle_trend_signal(df, i, lookback)
        total_sig = df.iloc[i]['total_signal']
        # 仅当移动平均线信号与蜡烛趋势信号一致，且与总信号方向匹配时确认
        if ma_sig == candle_sig and ma_sig == total_sig:
            df.at[i, 'final_signal'] = total_sig
    return df
```

## 多资产回测与参数优化

上一节我们完成了信号生成，本节中我们来看看如何进行批量回测和参数优化。

我们使用一个回测引擎，并对所有资产循环应用策略。核心优化参数是追踪止损的系数，该系数乘以平均真实波幅（ATR）来确定动态止损距离。

以下是回溯测试和优化循环的关键部分：

```python
# 假设 backtest_engine 是一个已定义的回测类
results_summary = []
all_drawdowns = []

for df in dataframes:
    asset_results = []
    # 优化止损系数
    for stop_coef in [0.8, 1.0, 1.2, 1.5, 2.0, 3.0]:
        bt = backtest_engine(df, stop_loss_coefficient=stop_coef)
        stats = bt.run_backtest()
        asset_results.append((stop_coef, stats))
    # 为当前资产选择最佳系数对应的结果
    best_result = max(asset_results, key=lambda x: x[1]['total_return'])
    results_summary.append(best_result[1])
    all_drawdowns.append(best_result[1]['max_drawdown'])

# 汇总所有资产的结果
total_return = sum([r['total_return'] for r in results_summary])
total_trades = sum([r['number_of_trades'] for r in results_summary])
avg_drawdown = sum(all_drawdowns) / len(all_drawdowns)
# ... 计算其他汇总指标
```

## 结果分析与策略评估

完成回测后，我们需要分析策略在不同资产上的表现。

![](img/e7d9ff2f0687fd315d4dddb5c7c17ac3_19.png)

以下是关键结果的汇总与比较方法：

![](img/e7d9ff2f0687fd315d4dddb5c7c17ac3_21.png)

1.  **打印总体绩效**：输出所有资产合并后的总回报率、交易次数、最大回撤、平均回撤和胜率。
2.  **比较资产间表现**：绘制或列出每种资产单独的回测收益曲线和关键指标（如夏普比率、总回报）。
3.  **筛选优质资产**：根据表现（例如总回报 > 5%，胜率 > 40%）筛选出适合该策略的资产。放弃那些表现不佳或无法覆盖交易成本的资产。

![](img/e7d9ff2f0687fd315d4dddb5c7c17ac3_23.png)

通过分析，我们可能发现该策略在欧元/美元、英镑/美元和黄金/美元上表现良好（例如获得10%-14%的回报），而在其他资产上效果平平。这意味着我们可以将策略重点应用在这些优质资产上。

## 总结

![](img/e7d9ff2f0687fd315d4dddb5c7c17ac3_25.png)

![](img/e7d9ff2f0687fd315d4dddb5c7c17ac3_27.png)

本节课中我们一起学习了多资产量化策略的完整流程。

![](img/e7d9ff2f0687fd315d4dddb5c7c17ac3_29.png)

1.  **策略设计**：我们构建了一个基于移动平均线趋势、布林带突破和RSI过滤的入场策略。
2.  **代码实现**：我们编写了函数来读取多资产数据、计算技术指标、生成并确认交易信号。
3.  **批量回测**：我们使用循环结构对数据文件夹中的所有资产自动进行策略回测。
4.  **参数优化**：我们以追踪止损系数为例，展示了如何通过遍历参数来优化策略绩效。
5.  **结果汇总与筛选**：我们汇总了所有资产的回测结果，并学习了如何通过比较绩效来筛选出最适合当前策略的资产。

![](img/e7d9ff2f0687fd315d4dddb5c7c17ac3_31.png)

这种方法的核心优势在于，它通过增加交易机会（分散于多种资产）来提高资金利用率和策略稳定性，而非依赖单一资产上的少数高胜率信号。你可以将此框架应用于任何策略，通过扩展资产池和持续优化来提升整体交易系统的表现。