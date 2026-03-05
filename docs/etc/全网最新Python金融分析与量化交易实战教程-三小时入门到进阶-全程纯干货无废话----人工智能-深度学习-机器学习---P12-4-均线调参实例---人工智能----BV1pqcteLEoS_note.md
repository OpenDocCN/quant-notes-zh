# Python金融分析与量化交易实战：P12：4-均线调参实例 📈

![](img/e503b88754494bf66aed08b282213c81_0.png)

![](img/e503b88754494bf66aed08b282213c81_2.png)

![](img/e503b88754494bf66aed08b282213c81_4.png)

在本节课中，我们将学习如何对双均线交易策略进行参数调优。我们将通过遍历不同的短期和长期均线参数组合，来寻找可能表现更优的策略配置。

## 概述：参数调优的必要性

上一节我们介绍了双均线策略的基本实现与回测。本节中我们来看看如何优化这个策略。策略的最终表现与许多因素有关，其中短期均线（SMA1）和长期均线（SMA2）的窗口长度是关键参数。我们需要找到更合适的参数组合。

![](img/e503b88754494bf66aed08b282213c81_6.png)

## 核心步骤：使用 `itertools.product` 遍历参数空间

![](img/e503b88754494bf66aed08b282213c81_8.png)

![](img/e503b88754494bf66aed08b282213c81_10.png)

为了系统地测试不同参数组合，我们将使用Python的`itertools.product`函数。它能生成多个可迭代对象的笛卡尔积，帮助我们遍历所有可能的参数组合。

![](img/e503b88754494bf66aed08b282213c81_12.png)

![](img/e503b88754494bf66aed08b282213c81_14.png)

![](img/e503b88754494bf66aed08b282213c81_16.png)

以下是导入该工具和定义参数空间的代码：

![](img/e503b88754494bf66aed08b282213c81_18.png)

![](img/e503b88754494bf66aed08b282213c81_20.png)

```python
from itertools import product

![](img/e503b88754494bf66aed08b282213c81_22.png)

![](img/e503b88754494bf66aed08b282213c81_24.png)

![](img/e503b88754494bf66aed08b282213c81_26.png)

# 定义短期均线（SMA1）的参数空间：从20到60，步长为4
sma1_range = range(20, 61, 4)
# 定义长期均线（SMA2）的参数空间：从180到280，步长为10
sma2_range = range(180, 281, 10)
```

![](img/e503b88754494bf66aed08b282213c81_28.png)

![](img/e503b88754494bf66aed08b282213c81_30.png)

`product(sma1_range, sma2_range)`将生成所有可能的（SMA1， SMA2）组合，例如（20， 180）、（20， 190）、（24， 180）等。

![](img/e503b88754494bf66aed08b282213c81_32.png)

![](img/e503b88754494bf66aed08b282213c81_34.png)

## 构建参数遍历与回测循环

![](img/e503b88754494bf66aed08b282213c81_36.png)

![](img/e503b88754494bf66aed08b282213c81_38.png)

接下来，我们将在一个循环中遍历所有参数组合，并对每一种组合执行完整的策略回测流程。

![](img/e503b88754494bf66aed08b282213c81_40.png)

![](img/e503b88754494bf66aed08b282213c81_42.png)

以下是循环和数据处理的核心结构：

![](img/e503b88754494bf66aed08b282213c81_44.png)

![](img/e503b88754494bf66aed08b282213c81_46.png)

```python
import pandas as pd

![](img/e503b88754494bf66aed08b282213c81_48.png)

# 初始化一个空的DataFrame来存储所有结果
results = pd.DataFrame()

![](img/e503b88754494bf66aed08b282213c81_50.png)

# 遍历所有参数组合
for sma1, sma2 in product(sma1_range, sma2_range):
    # 1. 重新加载数据（确保每次循环数据是干净的）
    data = pd.DataFrame(your_data['AAPL.Close']) # 请替换‘your_data’为你的实际数据源
    data.columns = ['price']
    
    # 2. 处理缺失值
    data = data.dropna()
    
    # 3. 计算收益率和均线
    data['returns'] = np.log(data['price'] / data['price'].shift(1))
    data['SMA1'] = data['price'].rolling(window=sma1).mean()
    data['SMA2'] = data['price'].rolling(window=sma2).mean()
    
    # 4. 再次处理因计算产生的缺失值
    data = data.dropna()
    
    # 5. 生成交易信号
    data['position'] = np.where(data['SMA1'] > data['SMA2'], 1, -1)
    
    # 6. 计算策略收益率
    data['strategy'] = data['position'].shift(1) * data['returns']
    
    # 7. 计算累计收益（简单求和，实际中可能使用复利计算）
    total_return = data['returns'].sum()
    total_strategy_return = data['strategy'].sum()
    outperformance = total_strategy_return - total_return
    
    # 8. 将本次循环的结果保存到汇总表中
    result_dict = {
        'SMA1': sma1,
        'SMA2': sma2,
        'Buy&Hold_Return': total_return,
        'Strategy_Return': total_strategy_return,
        'Outperformance': outperformance
    }
    # 将单次结果添加到总结果DataFrame中
    results = results.append(result_dict, ignore_index=True)
```

## 代码调试与优化

在编写上述循环时，可能会遇到数据格式或索引错误。一个常见的错误是，从原始数据中提取一列后，它可能变成一个Series而非DataFrame，导致后续操作失败。

![](img/e503b88754494bf66aed08b282213c81_52.png)

**解决方案**：使用`pd.DataFrame()`显式地将数据列转换为DataFrame，并指定列名。

![](img/e503b88754494bf66aed08b282213c81_54.png)

![](img/e503b88754494bf66aed08b282213c81_56.png)

![](img/e503b88754494bf66aed08b282213c81_58.png)

```python
# 错误示例：可能得到一个没有列名的Series
data = your_data['AAPL.Close']

![](img/e503b88754494bf66aed08b282213c81_60.png)

# 正确示例：转换为DataFrame并命名列
data = pd.DataFrame(your_data['AAPL.Close'])
data.columns = ['price']
```

![](img/e503b88754494bf66aed08b282213c81_62.png)

![](img/e503b88754494bf66aed08b282213c81_64.png)

另一个常见错误是在构建结果表时，`pd.DataFrame.append()`方法要求忽略索引或正确指定索引。

![](img/e503b88754494bf66aed08b282213c81_66.png)

![](img/e503b88754494bf66aed08b282213c81_68.png)

**解决方案**：在`append`方法中使用`ignore_index=True`参数。

![](img/e503b88754494bf66aed08b282213c81_70.png)

![](img/e503b88754494bf66aed08b282213c81_72.png)

![](img/e503b88754494bf66aed08b282213c81_74.png)

```python
results = results.append(result_dict, ignore_index=True)
```

![](img/e503b88754494bf66aed08b282213c81_76.png)

![](img/e503b88754494bf66aed08b282213c81_78.png)

## 结果分析与解读

![](img/e503b88754494bf66aed08b282213c81_80.png)

![](img/e503b88754494bf66aed08b282213c81_82.png)

执行完循环后，我们可以查看`results`这个DataFrame。它包含了所有参数组合的回测结果。

以下是分析结果的要点：

![](img/e503b88754494bf66aed08b282213c81_84.png)

![](img/e503b88754494bf66aed08b282213c81_86.png)

*   **`Outperformance`列**：表示策略收益超过简单买入持有（Buy & Hold）收益的部分。正值表示策略更优，负值表示不如买入持有。
*   **参数影响**：观察不同（SMA1， SMA2）组合对应的`Outperformance`。我们可以：
    *   找出表现最好的前N个参数组合。
    *   观察参数与表现之间是否存在某种规律（例如，特定的差值区间表现更稳定）。
    *   注意到某些参数组合下策略可能失效（`Outperformance`为负），这说明了参数选择的重要性。

![](img/e503b88754494bf66aed08b282213c81_88.png)

```python
# 按‘Outperformance’降序排列，查看表现最好的组合
top_results = results.sort_values(by='Outperformance', ascending=False)
print(top_results.head(10))
```

![](img/e503b88754494bf66aed08b282213c81_90.png)

![](img/e503b88754494bf66aed08b282213c81_92.png)

## 总结

![](img/e503b88754494bf66aed08b282213c81_94.png)

![](img/e503b88754494bf66aed08b282213c81_96.png)

![](img/e503b88754494bf66aed08b282213c81_98.png)

本节课中我们一起学习了双均线策略的参数调优实战。我们首先理解了遍历参数空间的必要性，然后使用`itertools.product`工具高效地定义了参数组合。接着，我们构建了一个完整的循环回测框架，将数据加载、指标计算、信号生成和绩效评估整合在一起。在编码过程中，我们探讨了可能遇到的数据格式错误及其解决方法。最后，通过对结果数据的分析，我们可以量化评估不同参数对策略表现的影响，从而为选择更优的策略参数提供了数据支持。这个过程是量化策略开发中至关重要的一步——从固定策略走向参数优化。