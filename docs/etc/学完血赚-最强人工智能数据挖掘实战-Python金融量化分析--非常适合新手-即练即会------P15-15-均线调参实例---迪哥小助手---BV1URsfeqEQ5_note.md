# Python金融量化分析：P15：均线调参实例 📈

![](img/14039c08d9fe78b301fd065723bb6bd7_0.png)

![](img/14039c08d9fe78b301fd065723bb6bd7_2.png)

![](img/14039c08d9fe78b301fd065723bb6bd7_4.png)

在本节课中，我们将学习如何对双均线交易策略进行参数调优。我们将通过遍历不同的短期和长期均线参数组合，来寻找能带来更好收益的参数设置。

![](img/14039c08d9fe78b301fd065723bb6bd7_6.png)

![](img/14039c08d9fe78b301fd065723bb6bd7_7.png)

上一节我们介绍了双均线策略的基本原理和实现，本节中我们来看看如何通过调整参数来优化策略表现。

![](img/14039c08d9fe78b301fd065723bb6bd7_9.png)

## 参数调优的必要性 🔍

![](img/14039c08d9fe78b301fd065723bb6bd7_10.png)

![](img/14039c08d9fe78b301fd065723bb6bd7_11.png)

策略的最终收益结果与很多因素有关。其中，短期均线（SMA1）和长期均线（SMA2）的窗口大小是核心参数。我们需要探索不同的参数组合，例如短期窗口是5天合适还是10天合适，长期窗口是20天合适还是40天合适。

![](img/14039c08d9fe78b301fd065723bb6bd7_13.png)

![](img/14039c08d9fe78b301fd065723bb6bd7_15.png)

![](img/14039c08d9fe78b301fd065723bb6bd7_16.png)

![](img/14039c08d9fe78b301fd065723bb6bd7_17.png)

在Python中，我们可以轻松地通过迭代工具来完成这种参数组合的遍历。

![](img/14039c08d9fe78b301fd065723bb6bd7_18.png)

## 导入迭代工具

![](img/14039c08d9fe78b301fd065723bb6bd7_20.png)

![](img/14039c08d9fe78b301fd065723bb6bd7_21.png)

![](img/14039c08d9fe78b301fd065723bb6bd7_22.png)

首先，我们需要导入用于生成参数组合的工具。

![](img/14039c08d9fe78b301fd065723bb6bd7_23.png)

![](img/14039c08d9fe78b301fd065723bb6bd7_24.png)

![](img/14039c08d9fe78b301fd065723bb6bd7_25.png)

![](img/14039c08d9fe78b301fd065723bb6bd7_26.png)

![](img/14039c08d9fe78b301fd065723bb6bd7_27.png)

![](img/14039c08d9fe78b301fd065723bb6bd7_28.png)

```python
from itertools import product
```

![](img/14039c08d9fe78b301fd065723bb6bd7_29.png)

![](img/14039c08d9fe78b301fd065723bb6bd7_31.png)

`product` 函数可以帮助我们生成两个参数空间的所有可能组合。

![](img/14039c08d9fe78b301fd065723bb6bd7_33.png)

## 定义参数空间

![](img/14039c08d9fe78b301fd065723bb6bd7_35.png)

![](img/14039c08d9fe78b301fd065723bb6bd7_36.png)

![](img/14039c08d9fe78b301fd065723bb6bd7_37.png)

![](img/14039c08d9fe78b301fd065723bb6bd7_38.png)

接下来，我们需要定义短期均线和长期均线的参数取值范围。

![](img/14039c08d9fe78b301fd065723bb6bd7_39.png)

![](img/14039c08d9fe78b301fd065723bb6bd7_40.png)

![](img/14039c08d9fe78b301fd065723bb6bd7_42.png)

以下是参数空间定义的示例：
*   **短期均线（SMA1）**：从20到60（步长为4），即 `range(20, 61, 4)`。
*   **长期均线（SMA2）**：从180到280（步长为10），即 `range(180, 281, 10)`。

![](img/14039c08d9fe78b301fd065723bb6bd7_44.png)

![](img/14039c08d9fe78b301fd065723bb6bd7_45.png)

`product` 函数会生成所有这些值的组合，例如 (20, 180), (20, 190), (24, 180), (24, 190) 等，帮助我们进行完整的遍历。

![](img/14039c08d9fe78b301fd065723bb6bd7_47.png)

## 构建参数遍历函数

我们将之前实现的双均线策略代码封装到一个函数中，以便对每一组参数进行计算。

![](img/14039c08d9fe78b301fd065723bb6bd7_49.png)

![](img/14039c08d9fe78b301fd065723bb6bd7_50.png)

![](img/14039c08d9fe78b301fd065723bb6bd7_51.png)

以下是构建遍历函数的核心步骤：
1.  **重新加载数据**：为确保数据干净，每次迭代重新加载苹果公司股价数据。
2.  **数据预处理**：处理缺失值。
3.  **计算指标**：根据传入的 `sma1` 和 `sma2` 参数，计算收益率、短期均线和长期均线。
4.  **生成交易信号**：根据双均线交叉规则生成持仓信号。
5.  **计算策略收益**：根据信号计算策略的累计收益，并与基准（买入持有）收益进行比较。
6.  **记录结果**：将每组参数及其对应的收益结果保存下来。

![](img/14039c08d9fe78b301fd065723bb6bd7_52.png)

![](img/14039c08d9fe78b301fd065723bb6bd7_54.png)

![](img/14039c08d9fe78b301fd065723bb6bd7_55.png)

```python
import pandas as pd

![](img/14039c08d9fe78b301fd065723bb6bd7_57.png)

![](img/14039c08d9fe78b301fd065723bb6bd7_58.png)

![](img/14039c08d9fe78b301fd065723bb6bd7_59.png)

# 定义参数空间
sma1_range = range(20, 61, 4)
sma2_range = range(180, 281, 10)

![](img/14039c08d9fe78b301fd065723bb6bd7_61.png)

# 初始化结果表格
results = pd.DataFrame()

![](img/14039c08d9fe78b301fd065723bb6bd7_62.png)

![](img/14039c08d9fe78b301fd065723bb6bd7_63.png)

![](img/14039c08d9fe78b301fd065723bb6bd7_65.png)

# 遍历所有参数组合
for sma1, sma2 in product(sma1_range, sma2_range):
    # 1. 加载数据
    data = pd.read_csv(‘your_data.csv‘, index_col=0, parse_dates=True)[‘AAPL‘]
    data = pd.DataFrame(data) # 确保是DataFrame格式
    data.dropna(inplace=True)

    # 2. 计算收益率和均线
    data[‘returns‘] = np.log(data[‘AAPL‘] / data[‘AAPL‘].shift(1))
    data[‘SMA1‘] = data[‘AAPL‘].rolling(window=sma1).mean()
    data[‘SMA2‘] = data[‘AAPL‘].rolling(window=sma2).mean()
    data.dropna(inplace=True)

    # 3. 生成交易信号
    data[‘position‘] = np.where(data[‘SMA1‘] > data[‘SMA2‘], 1, -1)

    # 4. 计算策略收益
    data[‘strategy‘] = data[‘position‘].shift(1) * data[‘returns‘]
    data.dropna(inplace=True)

    # 5. 计算累计收益并比较
    baseline_return = data[‘returns‘].cumsum().iloc[-1]
    strategy_return = data[‘strategy‘].cumsum().iloc[-1]
    outperformance = strategy_return - baseline_return

    # 6. 保存结果
    result_table = pd.DataFrame({
        ‘SMA1‘: sma1,
        ‘SMA2‘: sma2,
        ‘returns‘: baseline_return,
        ‘strategy‘: strategy_return,
        ‘outperformance‘: outperformance
    }, index=[0]) # 指定索引

    # 将当前结果追加到总表中
    results = pd.concat([results, result_table], ignore_index=True)
```

![](img/14039c08d9fe78b301fd065723bb6bd7_66.png)

![](img/14039c08d9fe78b301fd065723bb6bd7_67.png)

![](img/14039c08d9fe78b301fd065723bb6bd7_68.png)

![](img/14039c08d9fe78b301fd065723bb6bd7_69.png)

![](img/14039c08d9fe78b301fd065723bb6bd7_70.png)

## 结果分析与解读

![](img/14039c08d9fe78b301fd065723bb6bd7_72.png)

![](img/14039c08d9fe78b301fd065723bb6bd7_73.png)

运行上述代码后，我们可以查看 `results` 表格。

![](img/14039c08d9fe78b301fd065723bb6bd7_75.png)

![](img/14039c08d9fe78b301fd065723bb6bd7_76.png)

![](img/14039c08d9fe78b301fd065723bb6bd7_77.png)

```python
print(results.head(10))
```

表格中包含了每组参数下的基准收益、策略收益以及策略相对于基准的超额收益（`outperformance`）。

![](img/14039c08d9fe78b301fd065723bb6bd7_79.png)

![](img/14039c08d9fe78b301fd065723bb6bd7_80.png)

![](img/14039c08d9fe78b301fd065723bb6bd7_81.png)

通过观察结果，我们可以发现：
*   如果参数选择不当（例如 `outperformance` 为负值），策略表现可能还不如简单的买入持有策略。
*   大部分参数组合下，策略能产生正向的超额收益。
*   有些组合可能表现持平。

这直观地展示了参数选择对策略效果的显著影响，并帮助我们筛选出表现更优的参数组合。

![](img/14039c08d9fe78b301fd065723bb6bd7_83.png)

![](img/14039c08d9fe78b301fd065723bb6bd7_84.png)

![](img/14039c08d9fe78b301fd065723bb6bd7_85.png)

![](img/14039c08d9fe78b301fd065723bb6bd7_86.png)

![](img/14039c08d9fe78b301fd065723bb6bd7_87.png)

本节课中我们一起学习了如何对双均线交易策略进行参数调优。我们利用 `itertools.product` 工具遍历了不同的均线参数组合，并通过函数封装计算了每种组合的策略表现。最后，通过分析结果表格，我们能够评估不同参数的效果并选择更优的设置。这个过程是量化策略开发中至关重要的一步。