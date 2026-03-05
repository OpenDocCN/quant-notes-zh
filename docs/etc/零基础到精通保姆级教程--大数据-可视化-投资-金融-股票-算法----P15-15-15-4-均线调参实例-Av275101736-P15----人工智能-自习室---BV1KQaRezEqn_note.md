# Python金融量化交易：15：均线调参实例 📈

![](img/f3dc872b4345d5d7b3a2a25b225552b1_0.png)

![](img/f3dc872b4345d5d7b3a2a25b225552b1_2.png)

![](img/f3dc872b4345d5d7b3a2a25b225552b1_4.png)

在本节课中，我们将学习如何通过调整双均线策略的参数来优化交易策略。我们将使用Python的`itertools`工具来遍历不同的短期和长期均线组合，并评估每种组合的表现，从而找到更优的参数设置。

![](img/f3dc872b4345d5d7b3a2a25b225552b1_6.png)

![](img/f3dc872b4345d5d7b3a2a25b225552b1_8.png)

---

上一节我们介绍了双均线策略的基本实现和回测。本节中，我们来看看如何通过调整均线的参数来优化策略效果。

![](img/f3dc872b4345d5d7b3a2a25b225552b1_10.png)

![](img/f3dc872b4345d5d7b3a2a25b225552b1_12.png)

![](img/f3dc872b4345d5d7b3a2a25b225552b1_14.png)

## 参数空间与迭代工具

![](img/f3dc872b4345d5d7b3a2a25b225552b1_16.png)

![](img/f3dc872b4345d5d7b3a2a25b225552b1_18.png)

策略的表现与短期均线（SMA1）和长期均线（SMA2）的窗口大小密切相关。为了找到最佳参数组合，我们需要测试不同的值。例如，短期窗口是5天合适还是10天合适？长期窗口是20天合适还是40天合适？

![](img/f3dc872b4345d5d7b3a2a25b225552b1_20.png)

![](img/f3dc872b4345d5d7b3a2a25b225552b1_22.png)

![](img/f3dc872b4345d5d7b3a2a25b225552b1_24.png)

在Python中，我们可以使用`itertools.product`函数轻松地遍历所有可能的参数组合。以下是引入该工具的方法：

![](img/f3dc872b4345d5d7b3a2a25b225552b1_26.png)

![](img/f3dc872b4345d5d7b3a2a25b225552b1_28.png)

![](img/f3dc872b4345d5d7b3a2a25b225552b1_30.png)

![](img/f3dc872b4345d5d7b3a2a25b225552b1_32.png)

```python
from itertools import product
```

![](img/f3dc872b4345d5d7b3a2a25b225552b1_34.png)

![](img/f3dc872b4345d5d7b3a2a25b225552b1_36.png)

![](img/f3dc872b4345d5d7b3a2a25b225552b1_38.png)

`itertools.product`函数能够生成多个可迭代对象的笛卡尔积。这意味着，给定两个参数范围，它可以帮我们计算出所有可能的组合。

![](img/f3dc872b4345d5d7b3a2a25b225552b1_40.png)

![](img/f3dc872b4345d5d7b3a2a25b225552b1_42.png)

![](img/f3dc872b4345d5d7b3a2a25b225552b1_44.png)

## 定义参数范围并进行遍历

![](img/f3dc872b4345d5d7b3a2a25b225552b1_46.png)

![](img/f3dc872b4345d5d7b3a2a25b225552b1_48.png)

接下来，我们需要定义短期和长期均线的参数空间，并使用循环遍历所有组合。

![](img/f3dc872b4345d5d7b3a2a25b225552b1_50.png)

![](img/f3dc872b4345d5d7b3a2a25b225552b1_52.png)

![](img/f3dc872b4345d5d7b3a2a25b225552b1_54.png)

以下是定义参数空间并进行遍历的代码示例：

![](img/f3dc872b4345d5d7b3a2a25b225552b1_56.png)

![](img/f3dc872b4345d5d7b3a2a25b225552b1_58.png)

```python
# 定义短期均线（SMA1）的参数范围：从20到60，步长为4
sma1_range = range(20, 61, 4)
# 定义长期均线（SMA2）的参数范围：从180到280，步长为10
sma2_range = range(180, 281, 10)

![](img/f3dc872b4345d5d7b3a2a25b225552b1_60.png)

![](img/f3dc872b4345d5d7b3a2a25b225552b1_62.png)

![](img/f3dc872b4345d5d7b3a2a25b225552b1_64.png)

# 遍历所有参数组合
for sma1, sma2 in product(sma1_range, sma2_range):
    # 在这里执行策略计算和评估
    pass
```

![](img/f3dc872b4345d5d7b3a2a25b225552b1_66.png)

通过上述循环，我们可以依次测试如(20, 180)、(20, 190)、(24, 180)等所有参数组合。

![](img/f3dc872b4345d5d7b3a2a25b225552b1_68.png)

## 重构策略计算流程

为了对每个参数组合进行评估，我们需要将之前的策略计算流程整合到一个循环中。这包括数据加载、缺失值处理、收益率计算、均线计算以及策略信号生成。

![](img/f3dc872b4345d5d7b3a2a25b225552b1_70.png)

![](img/f3dc872b4345d5d7b3a2a25b225552b1_72.png)

以下是整合后的核心计算步骤：

![](img/f3dc872b4345d5d7b3a2a25b225552b1_74.png)

![](img/f3dc872b4345d5d7b3a2a25b225552b1_76.png)

![](img/f3dc872b4345d5d7b3a2a25b225552b1_78.png)

1.  **加载数据并处理缺失值**：重新加载股票价格数据，并确保数据完整。
2.  **计算收益率和均线**：根据当前循环中的`sma1`和`sma2`参数，计算短期均线、长期均线和日收益率。
3.  **生成交易信号**：基于双均线交叉规则（短期均线上穿长期均线时买入，下穿时卖出）生成持仓信号。
4.  **计算策略收益**：根据交易信号计算策略的累积收益，并与基准（买入并持有）收益进行比较。

![](img/f3dc872b4345d5d7b3a2a25b225552b1_80.png)

![](img/f3dc872b4345d5d7b3a2a25b225552b1_82.png)

![](img/f3dc872b4345d5d7b3a2a25b225552b1_84.png)

以下是整合代码的框架：

![](img/f3dc872b4345d5d7b3a2a25b225552b1_86.png)

![](img/f3dc872b4345d5d7b3a2a25b225552b1_88.png)

```python
import pandas as pd

![](img/f3dc872b4345d5d7b3a2a25b225552b1_90.png)

![](img/f3dc872b4345d5d7b3a2a25b225552b1_92.png)

![](img/f3dc872b4345d5d7b3a2a25b225552b1_94.png)

# 假设已经加载了名为‘AAPL‘的苹果公司股价数据到变量`data`中
# data = ...

![](img/f3dc872b4345d5d7b3a2a25b225552b1_96.png)

![](img/f3dc872b4345d5d7b3a2a25b225552b1_98.png)

![](img/f3dc872b4345d5d7b3a2a25b225552b1_100.png)

results = pd.DataFrame() # 创建一个空的DataFrame来存储结果

![](img/f3dc872b4345d5d7b3a2a25b225552b1_102.png)

![](img/f3dc872b4345d5d7b3a2a25b225552b1_104.png)

![](img/f3dc872b4345d5d7b3a2a25b225552b1_106.png)

for sma1, sma2 in product(sma1_range, sma2_range):
    # 1. 复制数据并处理缺失值
    df = data.copy().dropna()
    
    # 2. 计算收益率和均线
    df[‘returns‘] = np.log(df[‘Close‘] / df[‘Close‘].shift(1))
    df[‘SMA1‘] = df[‘Close‘].rolling(window=sma1).mean()
    df[‘SMA2‘] = df[‘Close‘].rolling(window=sma2).mean()
    df = df.dropna()
    
    # 3. 生成交易信号
    df[‘position‘] = np.where(df[‘SMA1‘] > df[‘SMA2‘], 1, -1)
    
    # 4. 计算策略收益
    df[‘strategy‘] = df[‘position‘].shift(1) * df[‘returns‘]
    df = df.dropna()
    
    # 计算策略相对于基准的超额收益
    outperformance = df[‘strategy‘].sum() - df[‘returns‘].sum()
    
    # 5. 将结果保存到表格中
    result_temp = pd.DataFrame({
        ‘SMA1‘: [sma1],
        ‘SMA2‘: [sma2],
        ‘Returns‘: [df[‘returns‘].sum()],
        ‘Strategy‘: [df[‘strategy‘].sum()],
        ‘Outperformance‘: [outperformance]
    })
    
    results = pd.concat([results, result_temp], ignore_index=True)
```

![](img/f3dc872b4345d5d7b3a2a25b225552b1_108.png)

![](img/f3dc872b4345d5d7b3a2a25b225552b1_110.png)

![](img/f3dc872b4345d5d7b3a2a25b225552b1_112.png)

## 结果分析与解读

![](img/f3dc872b4345d5d7b3a2a25b225552b1_114.png)

执行完上述代码后，`results` DataFrame 将包含所有参数组合下的策略表现。我们可以通过排序来快速找到表现最佳的参数。

![](img/f3dc872b4345d5d7b3a2a25b225552b1_116.png)

```python
# 按超额收益（Outperformance）降序排列，找到最佳参数
best_results = results.sort_values(by=‘Outperformance‘, ascending=False)
print(best_results.head())
```

![](img/f3dc872b4345d5d7b3a2a25b225552b1_118.png)

![](img/f3dc872b4345d5d7b3a2a25b225552b1_120.png)

分析结果时，你可能会发现：
*   某些参数组合下，策略收益甚至低于简单的买入持有（`Outperformance`为负值），说明参数选择不当。
*   大部分参数组合能带来正向的超额收益。
*   通过排序，我们可以直观地识别出在当前历史数据上表现最优的短期和长期均线窗口。

![](img/f3dc872b4345d5d7b3a2a25b225552b1_122.png)

![](img/f3dc872b4345d5d7b3a2a25b225552b1_124.png)

---

![](img/f3dc872b4345d5d7b3a2a25b225552b1_126.png)

![](img/f3dc872b4345d5d7b3a2a25b225552b1_128.png)

![](img/f3dc872b4345d5d7b3a2a25b225552b1_130.png)

本节课中我们一起学习了如何对双均线策略进行参数优化。我们使用`itertools.product`遍历了不同的参数组合，并通过重构代码对每个组合进行了策略回测和评估。这个过程是量化交易中策略优化的重要一步，帮助你理解参数选择对策略效果的显著影响，并能够通过系统性的方法寻找更优的参数设置。