# Python金融分析与量化交易实战课程：P11：11.4-均线调参实例 📈

![](img/9568fa3ef4f9e46f195f3c3a2a3a6e9f_0.png)

![](img/9568fa3ef4f9e46f195f3c3a2a3a6e9f_2.png)

![](img/9568fa3ef4f9e46f195f3c3a2a3a6e9f_4.png)

在本节课中，我们将学习如何对双均线交易策略进行参数调优。我们将通过遍历不同的短期和长期均线参数组合，来寻找可能带来更高收益的参数设置。

---

![](img/9568fa3ef4f9e46f195f3c3a2a3a6e9f_6.png)

![](img/9568fa3ef4f9e46f195f3c3a2a3a6e9f_8.png)

上一节我们介绍了双均线策略的基本原理和实现。本节中，我们来看看如何通过调整参数来优化策略效果。

![](img/9568fa3ef4f9e46f195f3c3a2a3a6e9f_10.png)

![](img/9568fa3ef4f9e46f195f3c3a2a3a6e9f_12.png)

![](img/9568fa3ef4f9e46f195f3c3a2a3a6e9f_14.png)

![](img/9568fa3ef4f9e46f195f3c3a2a3a6e9f_16.png)

策略的最终收益与很多因素有关，其中短期均线（SMA1）和长期均线（SMA2）的窗口大小是关键参数。我们需要探索不同的参数组合，例如短期是5天合适还是10天合适，长期是20天合适还是40天合适。

![](img/9568fa3ef4f9e46f195f3c3a2a3a6e9f_18.png)

![](img/9568fa3ef4f9e46f195f3c3a2a3a6e9f_20.png)

![](img/9568fa3ef4f9e46f195f3c3a2a3a6e9f_22.png)

![](img/9568fa3ef4f9e46f195f3c3a2a3a6e9f_24.png)

在Python中，我们可以借助`itertools`模块轻松实现对所有参数组合的遍历。

![](img/9568fa3ef4f9e46f195f3c3a2a3a6e9f_26.png)

![](img/9568fa3ef4f9e46f195f3c3a2a3a6e9f_28.png)

![](img/9568fa3ef4f9e46f195f3c3a2a3a6e9f_30.png)

以下是导入必要工具和定义参数空间的步骤：

![](img/9568fa3ef4f9e46f195f3c3a2a3a6e9f_32.png)

![](img/9568fa3ef4f9e46f195f3c3a2a3a6e9f_34.png)

![](img/9568fa3ef4f9e46f195f3c3a2a3a6e9f_36.png)

```python
from itertools import product

![](img/9568fa3ef4f9e46f195f3c3a2a3a6e9f_38.png)

![](img/9568fa3ef4f9e46f195f3c3a2a3a6e9f_40.png)

![](img/9568fa3ef4f9e46f195f3c3a2a3a6e9f_42.png)

# 定义短期均线（SMA1）和长期均线（SMA2）的参数空间
sma1_range = range(20, 61, 4)   # 从20到60，步长为4
sma2_range = range(180, 281, 10) # 从180到280，步长为10
```

![](img/9568fa3ef4f9e46f195f3c3a2a3a6e9f_44.png)

![](img/9568fa3ef4f9e46f195f3c3a2a3a6e9f_46.png)

`product`函数能帮助我们生成所有可能的参数组合，例如(20, 180)、(20, 190)、(24, 180)等。接下来，我们将遍历这些组合，为每一组参数计算策略收益。

![](img/9568fa3ef4f9e46f195f3c3a2a3a6e9f_48.png)

![](img/9568fa3ef4f9e46f195f3c3a2a3a6e9f_50.png)

以下是遍历所有参数组合并计算策略收益的核心循环结构：

```python
import pandas as pd

![](img/9568fa3ef4f9e46f195f3c3a2a3a6e9f_52.png)

# 初始化一个空的DataFrame来存储所有结果
results = pd.DataFrame()

![](img/9568fa3ef4f9e46f195f3c3a2a3a6e9f_54.png)

![](img/9568fa3ef4f9e46f195f3c3a2a3a6e9f_56.png)

![](img/9568fa3ef4f9e46f195f3c3a2a3a6e9f_58.png)

# 遍历所有参数组合
for sma1, sma2 in product(sma1_range, sma2_range):
    # 1. 重新加载数据
    data = pd.DataFrame(your_data['AAPL.Close']) # 请替换为你的实际数据列
    data.columns = ['price']
    
    # 2. 处理缺失值
    data = data.dropna()
    
    # 3. 计算收益率和均线
    data['returns'] = np.log(data['price'] / data['price'].shift(1))
    data['SMA1'] = data['price'].rolling(sma1).mean()
    data['SMA2'] = data['price'].rolling(sma2).mean()
    data = data.dropna()
    
    # 4. 生成交易信号
    data['position'] = np.where(data['SMA1'] > data['SMA2'], 1, -1)
    
    # 5. 计算策略收益率
    data['strategy'] = data['position'].shift(1) * data['returns']
    data = data.dropna()
    
    # 6. 汇总本次参数下的表现
    perf = data[['returns', 'strategy']].sum()
    out = perf['strategy'] - perf['returns']
    
    # 7. 将结果添加到总表中
    results = results.append(pd.DataFrame({
                    'SMA1': sma1,
                    'SMA2': sma2,
                    'Returns': perf['returns'],
                    'Strategy': perf['strategy'],
                    'Out/Under': out}, index=[0]), ignore_index=True)
```

![](img/9568fa3ef4f9e46f195f3c3a2a3a6e9f_60.png)

![](img/9568fa3ef4f9e46f195f3c3a2a3a6e9f_62.png)

在代码执行过程中，可能会遇到一些错误，例如数据列名丢失或DataFrame索引问题。我们需要确保每一步的数据结构正确。

![](img/9568fa3ef4f9e46f195f3c3a2a3a6e9f_64.png)

![](img/9568fa3ef4f9e46f195f3c3a2a3a6e9f_66.png)

![](img/9568fa3ef4f9e46f195f3c3a2a3a6e9f_68.png)

![](img/9568fa3ef4f9e46f195f3c3a2a3a6e9f_70.png)

如果遇到“KeyError”错误，通常是因为从DataFrame中提取单列后，该列失去了列名。解决方法是用`pd.DataFrame()`重新包装数据。

![](img/9568fa3ef4f9e46f195f3c3a2a3a6e9f_72.png)

![](img/9568fa3ef4f9e46f195f3c3a2a3a6e9f_74.png)

![](img/9568fa3ef4f9e46f195f3c3a2a3a6e9f_76.png)

如果遇到“必须指定索引”的错误，在创建DataFrame时，我们需要显式提供一个索引，例如`index=[0]`。在后续使用`append`方法合并结果时，则设置`ignore_index=True`来忽略索引。

![](img/9568fa3ef4f9e46f195f3c3a2a3a6e9f_78.png)

![](img/9568fa3ef4f9e46f195f3c3a2a3a6e9f_80.png)

![](img/9568fa3ef4f9e46f195f3c3a2a3a6e9f_82.png)

![](img/9568fa3ef4f9e46f195f3c3a2a3a6e9f_84.png)

代码成功运行后，我们将得到一个包含所有参数组合及其对应收益的表格。通过观察`Out/Under`列（策略收益减去基准收益），我们可以评估策略的优劣。

![](img/9568fa3ef4f9e46f195f3c3a2a3a6e9f_86.png)

以下是结果分析示例：

![](img/9568fa3ef4f9e46f195f3c3a2a3a6e9f_88.png)

![](img/9568fa3ef4f9e46f195f3c3a2a3a6e9f_90.png)

*   当`Out/Under`为负值时，表示该参数下的策略表现还不如简单的买入持有策略。
*   当`Out/Under`为正值时，表示策略带来了超额收益。
*   通过排序，我们可以快速找出表现最好的几组参数。

![](img/9568fa3ef4f9e46f195f3c3a2a3a6e9f_92.png)

![](img/9568fa3ef4f9e46f195f3c3a2a3a6e9f_94.png)

---

![](img/9568fa3ef4f9e46f195f3c3a2a3a6e9f_96.png)

![](img/9568fa3ef4f9e46f195f3c3a2a3a6e9f_98.png)

![](img/9568fa3ef4f9e46f195f3c3a2a3a6e9f_100.png)

![](img/9568fa3ef4f9e46f195f3c3a2a3a6e9f_102.png)

本节课中我们一起学习了如何对双均线策略进行参数调优。我们使用`itertools.product`遍历了不同的短期和长期均线窗口组合，并通过完整的回测流程计算了每一组参数的表现。最后，我们学会了如何分析结果表格，以识别能够提升策略收益的参数设置。这个过程是量化交易中优化策略性能的基础步骤。