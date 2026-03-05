# 量化交易完全可自学教程：P72：10. 9.11.4-均线调参实例P11 📈

![](img/cb7ab47a2df46b9983de4a49396a2543_0.png)

![](img/cb7ab47a2df46b9983de4a49396a2543_2.png)

![](img/cb7ab47a2df46b9983de4a49396a2543_4.png)

在本节课中，我们将要学习如何对双均线交易策略进行参数调优。我们将通过遍历不同的短期和长期均线参数组合，来评估哪种组合能带来更好的交易结果。

![](img/cb7ab47a2df46b9983de4a49396a2543_6.png)

---

![](img/cb7ab47a2df46b9983de4a49396a2543_8.png)

![](img/cb7ab47a2df46b9983de4a49396a2543_10.png)

![](img/cb7ab47a2df46b9983de4a49396a2543_12.png)

![](img/cb7ab47a2df46b9983de4a49396a2543_14.png)

![](img/cb7ab47a2df46b9983de4a49396a2543_16.png)

上一节我们介绍了事件策略，本节中我们来看看第二个问题：如何评估和优化双均线策略的参数。

![](img/cb7ab47a2df46b9983de4a49396a2543_18.png)

![](img/cb7ab47a2df46b9983de4a49396a2543_20.png)

![](img/cb7ab47a2df46b9983de4a49396a2543_22.png)

![](img/cb7ab47a2df46b9983de4a49396a2543_24.png)

策略的最终结果与许多因素有关，其中短期均线（SMA1）和长期均线（SMA2）的窗口长度是关键参数。我们需要探索不同的参数组合，例如短期是5天合适还是10天合适，长期是20天合适还是40天合适。

![](img/cb7ab47a2df46b9983de4a49396a2543_26.png)

![](img/cb7ab47a2df46b9983de4a49396a2543_28.png)

![](img/cb7ab47a2df46b9983de4a49396a2543_30.png)

![](img/cb7ab47a2df46b9983de4a49396a2543_32.png)

![](img/cb7ab47a2df46b9983de4a49396a2543_34.png)

为了系统地探索这些组合，我们将使用Python的`itertools.product`工具来生成所有可能的参数组合并进行遍历。

![](img/cb7ab47a2df46b9983de4a49396a2543_36.png)

![](img/cb7ab47a2df46b9983de4a49396a2543_38.png)

![](img/cb7ab47a2df46b9983de4a49396a2543_40.png)

以下是引入迭代工具和定义参数空间的步骤：
```python
from itertools import product

![](img/cb7ab47a2df46b9983de4a49396a2543_42.png)

![](img/cb7ab47a2df46b9983de4a49396a2543_44.png)

![](img/cb7ab47a2df46b9983de4a49396a2543_46.png)

# 定义短期均线（SMA1）的参数空间：从20到60，步长为4
sma1_range = range(20, 61, 4)
# 定义长期均线（SMA2）的参数空间：从180到280，步长为10
sma2_range = range(180, 281, 10)
```

![](img/cb7ab47a2df46b9983de4a49396a2543_48.png)

![](img/cb7ab47a2df46b9983de4a49396a2543_50.png)

![](img/cb7ab47a2df46b9983de4a49396a2543_52.png)

`product`函数能帮助我们计算两个参数空间的所有组合。例如，它会生成 `(20, 180)`, `(20, 190)`, `(24, 180)` 等组合，实现对所有可能性的遍历。

接下来，我们将在循环中遍历这些参数组合，并对每一组参数重新计算策略结果。

![](img/cb7ab47a2df46b9983de4a49396a2543_54.png)

![](img/cb7ab47a2df46b9983de4a49396a2543_56.png)

![](img/cb7ab47a2df46b9983de4a49396a2543_58.png)

以下是遍历参数组合并计算策略的核心代码框架：
```python
# 初始化一个空的DataFrame来存储所有结果
results = pd.DataFrame()

![](img/cb7ab47a2df46b9983de4a49396a2543_60.png)

![](img/cb7ab47a2df46b9983de4a49396a2543_62.png)

![](img/cb7ab47a2df46b9983de4a49396a2543_64.png)

![](img/cb7ab47a2df46b9983de4a49396a2543_66.png)

# 遍历所有参数组合
for sma1, sma2 in product(sma1_range, sma2_range):
    # 1. 重新加载并预处理数据（例如苹果股价）
    data = ... # 加载数据
    data = data.dropna() # 去除缺失值

    # 2. 计算收益率和均线
    data['returns'] = ... # 计算收益率
    data['SMA1'] = data['Close'].rolling(window=sma1).mean() # 计算短期均线
    data['SMA2'] = data['Close'].rolling(window=sma2).mean() # 计算长期均线
    data = data.dropna() # 再次去除因滚动计算产生的缺失值

    # 3. 生成交易信号
    data['position'] = np.where(data['SMA1'] > data['SMA2'], 1, -1)

    # 4. 计算策略收益
    data['strategy'] = data['position'].shift(1) * data['returns']
    data = data.dropna() # 去除信号滞后产生的缺失值

    # 5. 汇总本次参数组合的结果
    # 计算策略相对于简单持有（基准收益）的超额收益
    outperformance = data['strategy'].sum() - data['returns'].sum()

    # 6. 将本次结果添加到总结果表中
    result_table = pd.DataFrame({
        'SMA1': [sma1],
        'SMA2': [sma2],
        'Returns': [data['returns'].sum()],
        'Strategy': [data['strategy'].sum()],
        'Outperformance': [outperformance]
    })
    # 使用ignore_index确保索引连续
    results = pd.concat([results, result_table], ignore_index=True)
```

![](img/cb7ab47a2df46b9983de4a49396a2543_68.png)

![](img/cb7ab47a2df46b9983de4a49396a2543_70.png)

在实现过程中，可能会遇到数据格式或索引问题。例如，从数据源取出的单列数据可能不是DataFrame格式，导致后续操作出错。解决方法是用`pd.DataFrame()`将其显式转换为DataFrame。

![](img/cb7ab47a2df46b9983de4a49396a2543_72.png)

![](img/cb7ab47a2df46b9983de4a49396a2543_74.png)

![](img/cb7ab47a2df46b9983de4a49396a2543_76.png)

![](img/cb7ab47a2df46b9983de4a49396a2543_78.png)

另一个常见问题是在合并结果时索引重复。使用`pd.concat()`函数时，设置参数`ignore_index=True`可以重置索引，生成一个整洁的、索引连续的结果表格。

![](img/cb7ab47a2df46b9983de4a49396a2543_80.png)

![](img/cb7ab47a2df46b9983de4a49396a2543_82.png)

![](img/cb7ab47a2df46b9983de4a49396a2543_84.png)

执行完代码后，我们可以查看`results`表格。表格中会包含每一组参数（SMA1, SMA2）对应的基准收益（Returns）、策略收益（Strategy）以及策略超额收益（Outperformance）。

![](img/cb7ab47a2df46b9983de4a49396a2543_86.png)

通过观察`Outperformance`列，我们可以评估参数效果：
*   如果`Outperformance`为负值，说明该参数组合下的策略表现还不如简单持有（买入并持有）策略。
*   如果`Outperformance`为正值，说明策略产生了超额收益。
*   通过比较不同组合的`Outperformance`，我们可以初步筛选出表现较好的参数区间。

![](img/cb7ab47a2df46b9983de4a49396a2543_88.png)

![](img/cb7ab47a2df46b9983de4a49396a2543_90.png)

![](img/cb7ab47a2df46b9983de4a49396a2543_92.png)

---

![](img/cb7ab47a2df46b9983de4a49396a2543_94.png)

![](img/cb7ab47a2df46b9983de4a49396a2543_96.png)

![](img/cb7ab47a2df46b9983de4a49396a2543_98.png)

![](img/cb7ab47a2df46b9983de4a49396a2543_100.png)

本节课中我们一起学习了如何对双均线策略进行参数调优。我们使用`itertools.product`遍历了不同的短期和长期均线参数组合，对每一组参数重新计算了策略收益，并通过对比策略收益与基准收益，评估了参数的有效性。这个过程是量化策略开发中优化和验证的关键步骤，帮助我们寻找更优的策略参数。