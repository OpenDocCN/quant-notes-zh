# Python金融量化：15：均线调参实例 📈

![](img/bdd6622f487188abbde7d90f2e5645a4_0.png)

![](img/bdd6622f487188abbde7d90f2e5645a4_2.png)

![](img/bdd6622f487188abbde7d90f2e5645a4_4.png)

![](img/bdd6622f487188abbde7d90f2e5645a4_6.png)

![](img/bdd6622f487188abbde7d90f2e5645a4_8.png)

![](img/bdd6622f487188abbde7d90f2e5645a4_10.png)

![](img/bdd6622f487188abbde7d90f2e5645a4_12.png)

![](img/bdd6622f487188abbde7d90f2e5645a4_14.png)

![](img/bdd6622f487188abbde7d90f2e5645a4_16.png)

在本节课中，我们将学习如何对双均线策略进行参数调优。我们将通过遍历不同的短期和长期均线参数组合，来评估哪种参数设置能带来更好的投资回报。

![](img/bdd6622f487188abbde7d90f2e5645a4_18.png)

![](img/bdd6622f487188abbde7d90f2e5645a4_20.png)

![](img/bdd6622f487188abbde7d90f2e5645a4_22.png)

![](img/bdd6622f487188abbde7d90f2e5645a4_24.png)

![](img/bdd6622f487188abbde7d90f2e5645a4_26.png)

![](img/bdd6622f487188abbde7d90f2e5645a4_28.png)

![](img/bdd6622f487188abbde7d90f2e5645a4_30.png)

![](img/bdd6622f487188abbde7d90f2e5645a4_32.png)

---

![](img/bdd6622f487188abbde7d90f2e5645a4_34.png)

![](img/bdd6622f487188abbde7d90f2e5645a4_36.png)

![](img/bdd6622f487188abbde7d90f2e5645a4_38.png)

![](img/bdd6622f487188abbde7d90f2e5645a4_40.png)

![](img/bdd6622f487188abbde7d90f2e5645a4_42.png)

![](img/bdd6622f487188abbde7d90f2e5645a4_44.png)

![](img/bdd6622f487188abbde7d90f2e5645a4_46.png)

上一节我们介绍了双均线策略的基本原理和实现。本节中，我们来看看如何通过调整策略中的参数来优化其表现。策略的最终收益与短期均线（SMA1）和长期均线（SMA2）的窗口大小密切相关。我们需要找到最优的参数组合。

![](img/bdd6622f487188abbde7d90f2e5645a4_48.png)

![](img/bdd6622f487188abbde7d90f2e5645a4_50.png)

![](img/bdd6622f487188abbde7d90f2e5645a4_52.png)

![](img/bdd6622f487188abbde7d90f2e5645a4_54.png)

![](img/bdd6622f487188abbde7d90f2e5645a4_56.png)

![](img/bdd6622f487188abbde7d90f2e5645a4_58.png)

![](img/bdd6622f487188abbde7d90f2e5645a4_60.png)

![](img/bdd6622f487188abbde7d90f2e5645a4_62.png)

以下是实现参数调优的步骤：

![](img/bdd6622f487188abbde7d90f2e5645a4_64.png)

![](img/bdd6622f487188abbde7d90f2e5645a4_66.png)

1.  **导入必要的工具**：首先，我们需要导入用于生成参数组合的工具。
    ```python
    from itertools import product
    ```

![](img/bdd6622f487188abbde7d90f2e5645a4_68.png)

![](img/bdd6622f487188abbde7d90f2e5645a4_70.png)

![](img/bdd6622f487188abbde7d90f2e5645a4_72.png)

![](img/bdd6622f487188abbde7d90f2e5645a4_74.png)

![](img/bdd6622f487188abbde7d90f2e5645a4_76.png)

![](img/bdd6622f487188abbde7d90f2e5645a4_78.png)

2.  **定义参数空间**：接下来，我们定义短期均线和长期均线的可能取值范围。
    ```python
    # 短期均线参数空间：从20到60，步长为4
    sma1_range = range(20, 61, 4)
    # 长期均线参数空间：从180到280，步长为10
    sma2_range = range(180, 281, 10)
    ```

![](img/bdd6622f487188abbde7d90f2e5645a4_80.png)

![](img/bdd6622f487188abbde7d90f2e5645a4_82.png)

![](img/bdd6622f487188abbde7d90f2e5645a4_84.png)

![](img/bdd6622f487188abbde7d90f2e5645a4_86.png)

![](img/bdd6622f487188abbde7d90f2e5645a4_88.png)

![](img/bdd6622f487188abbde7d90f2e5645a4_90.png)

![](img/bdd6622f487188abbde7d90f2e5645a4_92.png)

3.  **遍历参数组合**：使用 `product` 函数生成所有可能的参数组合，并对每一组参数执行策略计算。
    ```python
    import pandas as pd
    # 初始化一个空的DataFrame来存储所有结果
    results = pd.DataFrame()
    
    # 遍历所有参数组合
    for sma1, sma2 in product(sma1_range, sma2_range):
        # 1. 加载并预处理数据（例如苹果股价）
        data = pd.DataFrame(your_data['AAPL'])
        data = data.dropna()
        
        # 2. 计算收益率和均线
        data['returns'] = np.log(data['AAPL'] / data['AAPL'].shift(1))
        data['SMA1'] = data['AAPL'].rolling(window=sma1).mean()
        data['SMA2'] = data['AAPL'].rolling(window=sma2).mean()
        data = data.dropna()
        
        # 3. 生成交易信号
        data['position'] = np.where(data['SMA1'] > data['SMA2'], 1, -1)
        
        # 4. 计算策略收益
        data['strategy'] = data['position'].shift(1) * data['returns']
        data = data.dropna()
        
        # 5. 汇总本次参数下的结果
        perf = data[['returns', 'strategy']].sum().apply(np.exp)
        out_perf = perf['strategy'] - perf['returns']
        
        # 6. 将结果添加到总表中
        results = results.append({
            'SMA1': sma1,
            'SMA2': sma2,
            'Returns': perf['returns'],
            'Strategy': perf['strategy'],
            'Outperformance': out_perf
        }, ignore_index=True)
    ```

![](img/bdd6622f487188abbde7d90f2e5645a4_94.png)

![](img/bdd6622f487188abbde7d90f2e5645a4_96.png)

![](img/bdd6622f487188abbde7d90f2e5645a4_98.png)

![](img/bdd6622f487188abbde7d90f2e5645a4_100.png)

![](img/bdd6622f487188abbde7d90f2e5645a4_102.png)

![](img/bdd6622f487188abbde7d90f2e5645a4_104.png)

![](img/bdd6622f487188abbde7d90f2e5645a4_106.png)

![](img/bdd6622f487188abbde7d90f2e5645a4_108.png)

4.  **分析与展示结果**：计算完成后，我们可以查看结果表格，找出表现最佳（即 `Outperformance` 值最高）的参数组合。
    ```python
    # 按超额收益降序排列，查看最佳参数
    best_params = results.sort_values('Outperformance', ascending=False)
    print(best_params.head(10))
    ```

![](img/bdd6622f487188abbde7d90f2e5645a4_110.png)

![](img/bdd6622f487188abbde7d90f2e5645a4_112.png)

![](img/bdd6622f487188abbde7d90f2e5645a4_114.png)

![](img/bdd6622f487188abbde7d90f2e5645a4_116.png)

---

![](img/bdd6622f487188abbde7d90f2e5645a4_118.png)

![](img/bdd6622f487188abbde7d90f2e5645a4_120.png)

![](img/bdd6622f487188abbde7d90f2e5645a4_122.png)

![](img/bdd6622f487188abbde7d90f2e5645a4_124.png)

![](img/bdd6622f487188abbde7d90f2e5645a4_126.png)

![](img/bdd6622f487188abbde7d90f2e5645a4_128.png)

![](img/bdd6622f487188abbde7d90f2e5645a4_130.png)

在本节课中，我们一起学习了如何对双均线策略进行参数调优。通过系统地遍历不同的短期和长期窗口组合，我们能够量化评估每种参数设置的表现，并从中选择能带来最高超额收益的参数。这个过程是量化策略开发中优化和验证的关键步骤。