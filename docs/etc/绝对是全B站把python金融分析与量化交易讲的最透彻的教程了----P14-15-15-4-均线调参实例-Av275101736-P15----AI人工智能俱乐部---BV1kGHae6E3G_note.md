# Python金融量化+股票交易：P14：15.15.4-均线调参实例 📈

![](img/3829f24c07679475a675cd229b786283_0.png)

![](img/3829f24c07679475a675cd229b786283_2.png)

![](img/3829f24c07679475a675cd229b786283_4.png)

在本节课中，我们将学习如何对双均线交易策略进行参数调优。我们将通过遍历不同的短期和长期均线参数组合，来寻找可能表现更优的策略配置。

![](img/3829f24c07679475a675cd229b786283_6.png)

![](img/3829f24c07679475a675cd229b786283_7.png)

## 概述

上一节我们介绍了双均线策略的基本原理和实现。本节中我们来看看如何通过调整策略中的参数（如短期和长期均线的计算窗口）来优化策略表现。我们将使用Python的`itertools`工具包来遍历所有可能的参数组合，并评估每个组合的收益。

![](img/3829f24c07679475a675cd229b786283_9.png)

![](img/3829f24c07679475a675cd229b786283_10.png)

![](img/3829f24c07679475a675cd229b786283_11.png)

## 参数空间与迭代工具

![](img/3829f24c07679475a675cd229b786283_13.png)

![](img/3829f24c07679475a675cd229b786283_14.png)

为了测试不同参数组合的效果，我们需要定义参数空间并进行遍历。以下是实现步骤：

![](img/3829f24c07679475a675cd229b786283_15.png)

![](img/3829f24c07679475a675cd229b786283_16.png)

首先，导入必要的迭代工具。

![](img/3829f24c07679475a675cd229b786283_18.png)

![](img/3829f24c07679475a675cd229b786283_19.png)

![](img/3829f24c07679475a675cd229b786283_20.png)

```python
from itertools import product
```

![](img/3829f24c07679475a675cd229b786283_21.png)

![](img/3829f24c07679475a675cd229b786283_22.png)

![](img/3829f24c07679475a675cd229b786283_23.png)

![](img/3829f24c07679475a675cd229b786283_24.png)

![](img/3829f24c07679475a675cd229b786283_25.png)

`product`函数可以帮助我们生成两个或多个序列的笛卡尔积，即所有可能的组合。

![](img/3829f24c07679475a675cd229b786283_26.png)

![](img/3829f24c07679475a675cd229b786283_27.png)

![](img/3829f24c07679475a675cd229b786283_29.png)

接下来，定义短期均线（SMA1）和长期均线（SMA2）的参数空间。

```python
# 定义短期均线参数空间，例如从20天到60天，步长为4
short_window_range = range(20, 61, 4)
# 定义长期均线参数空间，例如从180天到280天，步长为10
long_window_range = range(180, 281, 10)
```

![](img/3829f24c07679475a675cd229b786283_31.png)

![](img/3829f24c07679475a675cd229b786283_32.png)

![](img/3829f24c07679475a675cd229b786283_33.png)

![](img/3829f24c07679475a675cd229b786283_34.png)

![](img/3829f24c07679475a675cd229b786283_35.png)

## 构建参数遍历与策略评估循环

![](img/3829f24c07679475a675cd229b786283_37.png)

![](img/3829f24c07679475a675cd229b786283_39.png)

我们将把上一节的策略代码整合到一个循环中，对每一组参数进行计算。

![](img/3829f24c07679475a675cd229b786283_40.png)

以下是核心循环结构的步骤介绍：

![](img/3829f24c07679475a675cd229b786283_42.png)

1.  **重新加载数据并预处理**：为确保数据干净，每次循环都重新加载苹果公司股价数据，并处理缺失值。
2.  **计算指标**：根据当前循环的`short_window`和`long_window`参数，计算收益率、短期均线和长期均线。
3.  **生成交易信号**：根据双均线交叉规则，生成持仓信号（`position`）。
4.  **计算策略收益**：根据交易信号计算策略的累计收益。
5.  **记录结果**：将当前参数组合、基准收益、策略收益以及超额收益（策略收益 - 基准收益）记录到一个表格中。

以下是整合后的代码框架：

![](img/3829f24c07679475a675cd229b786283_44.png)

![](img/3829f24c07679475a675cd229b786283_45.png)

![](img/3829f24c07679475a675cd229b786283_46.png)

![](img/3829f24c07679475a675cd229b786283_47.png)

```python
import pandas as pd
# 假设已导入数据并命名为`data`
# data = ... (加载苹果股价数据)

![](img/3829f24c07679475a675cd229b786283_49.png)

![](img/3829f24c07679475a675cd229b786283_50.png)

results = pd.DataFrame() # 创建一个空DataFrame存储所有结果

![](img/3829f24c07679475a675cd229b786283_52.png)

![](img/3829f24c07679475a675cd229b786283_53.png)

![](img/3829f24c07679475a675cd229b786283_54.png)

# 遍历所有参数组合
for SMA1, SMA2 in product(short_window_range, long_window_range):
    # 1. 数据预处理
    data_clean = data.dropna()

    # 2. 计算收益率和均线
    data_clean['returns'] = np.log(data_clean['Close'] / data_clean['Close'].shift(1))
    data_clean['SMA1'] = data_clean['Close'].rolling(window=SMA1).mean()
    data_clean['SMA2'] = data_clean['Close'].rolling(window=SMA2).mean()

    # 3. 生成交易信号
    data_clean['position'] = np.where(data_clean['SMA1'] > data_clean['SMA2'], 1, 0)

    # 4. 计算策略收益
    data_clean['strategy'] = data_clean['position'].shift(1) * data_clean['returns']
    data_clean.dropna(inplace=True)

    # 5. 计算累计收益
    baseline_return = data_clean['returns'].cumsum().iloc[-1]
    strategy_return = data_clean['strategy'].cumsum().iloc[-1]
    outperformance = strategy_return - baseline_return

    # 6. 将结果保存为一行数据
    temp_df = pd.DataFrame({
        'SMA1': [SMA1],
        'SMA2': [SMA2],
        'Baseline_Return': [baseline_return],
        'Strategy_Return': [strategy_return],
        'Outperformance': [outperformance]
    })

    # 7. 将当前结果追加到总表中
    results = pd.concat([results, temp_df], ignore_index=True)
```

![](img/3829f24c07679475a675cd229b786283_56.png)

![](img/3829f24c07679475a675cd229b786283_57.png)

## 结果分析与解读

![](img/3829f24c07679475a675cd229b786283_58.png)

![](img/3829f24c07679475a675cd229b786283_60.png)

代码执行完毕后，`results` DataFrame 将包含所有参数组合的回测结果。

![](img/3829f24c07679475a675cd229b786283_61.png)

![](img/3829f24c07679475a675cd229b786283_62.png)

![](img/3829f24c07679475a675cd229b786283_63.png)

![](img/3829f24c07679475a675cd229b786283_64.png)

![](img/3829f24c07679475a675cd229b786283_65.png)

我们可以查看结果的前几行：

![](img/3829f24c07679475a675cd229b786283_67.png)

![](img/3829f24c07679475a675cd229b786283_68.png)

```python
print(results.head(10))
```

![](img/3829f24c07679475a675cd229b786283_70.png)

![](img/3829f24c07679475a675cd229b786283_71.png)

![](img/3829f24c07679475a675cd229b786283_72.png)

输出结果可能类似以下格式：

| SMA1 | SMA2 | Baseline_Return | Strategy_Return | Outperformance |
| :--- | :--- | :-------------- | :-------------- | :------------- |
| 20   | 180  | 0.85            | 0.82            | -0.03          |
| 20   | 190  | 0.85            | 0.88            | 0.03           |
| 24   | 180  | 0.85            | 0.90            | 0.05           |

![](img/3829f24c07679475a675cd229b786283_74.png)

![](img/3829f24c07679475a675cd229b786283_75.png)

![](img/3829f24c07679475a675cd229b786283_76.png)

**结果解读**：
*   `Outperformance`列表示策略收益相对于简单持有（基准收益）的超额收益。
*   正值表示该参数组合下的策略表现优于简单持有。
*   负值表示该参数组合下的策略表现不如简单持有。
*   通过排序（例如 `results.sort_values(by='Outperformance', ascending=False)`），我们可以快速找出历史回测中表现最好的参数组合。

## 总结

![](img/3829f24c07679475a675cd229b786283_78.png)

![](img/3829f24c07679475a675cd229b786283_79.png)

![](img/3829f24c07679475a675cd229b786283_80.png)

![](img/3829f24c07679475a675cd229b786283_81.png)

![](img/3829f24c07679475a675cd229b786283_82.png)

本节课中我们一起学习了如何对双均线交易策略进行参数调优。我们利用`itertools.product`函数遍历了短期和长期均线的不同窗口组合，并通过一个循环结构对每个组合进行了完整的策略回测和收益计算。最后，通过分析结果表格，我们可以评估不同参数的效果，并寻找历史数据上表现较优的策略配置。这是量化策略开发中优化和验证环节的重要一步。