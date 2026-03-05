# 金融量化分析：P15：4-均线调参实例 📈

![](img/32f784f1c1bc0bb0db3c05ba1d15ea01_0.png)

![](img/32f784f1c1bc0bb0db3c05ba1d15ea01_2.png)

![](img/32f784f1c1bc0bb0db3c05ba1d15ea01_4.png)

![](img/32f784f1c1bc0bb0db3c05ba1d15ea01_6.png)

![](img/32f784f1c1bc0bb0db3c05ba1d15ea01_8.png)

![](img/32f784f1c1bc0bb0db3c05ba1d15ea01_10.png)

![](img/32f784f1c1bc0bb0db3c05ba1d15ea01_12.png)

![](img/32f784f1c1bc0bb0db3c05ba1d15ea01_14.png)

![](img/32f784f1c1bc0bb0db3c05ba1d15ea01_16.png)

在本节课中，我们将学习如何对双均线交易策略进行参数调优。我们将通过遍历不同的短期和长期均线参数组合，来评估哪种组合能带来更好的投资回报。

![](img/32f784f1c1bc0bb0db3c05ba1d15ea01_18.png)

![](img/32f784f1c1bc0bb0db3c05ba1d15ea01_20.png)

![](img/32f784f1c1bc0bb0db3c05ba1d15ea01_22.png)

![](img/32f784f1c1bc0bb0db3c05ba1d15ea01_24.png)

![](img/32f784f1c1bc0bb0db3c05ba1d15ea01_26.png)

![](img/32f784f1c1bc0bb0db3c05ba1d15ea01_28.png)

![](img/32f784f1c1bc0bb0db3c05ba1d15ea01_30.png)

![](img/32f784f1c1bc0bb0db3c05ba1d15ea01_32.png)

---

![](img/32f784f1c1bc0bb0db3c05ba1d15ea01_34.png)

![](img/32f784f1c1bc0bb0db3c05ba1d15ea01_36.png)

![](img/32f784f1c1bc0bb0db3c05ba1d15ea01_38.png)

![](img/32f784f1c1bc0bb0db3c05ba1d15ea01_40.png)

![](img/32f784f1c1bc0bb0db3c05ba1d15ea01_42.png)

![](img/32f784f1c1bc0bb0db3c05ba1d15ea01_44.png)

![](img/32f784f1c1bc0bb0db3c05ba1d15ea01_46.png)

上一节我们介绍了双均线策略的基本原理和回测方法。本节中，我们来看看如何通过调整策略参数来优化其表现。策略的最终收益与短期均线（SMA1）和长期均线（SMA2）的窗口大小密切相关。我们需要找到最优的参数组合。

![](img/32f784f1c1bc0bb0db3c05ba1d15ea01_48.png)

![](img/32f784f1c1bc0bb0db3c05ba1d15ea01_50.png)

![](img/32f784f1c1bc0bb0db3c05ba1d15ea01_52.png)

![](img/32f784f1c1bc0bb0db3c05ba1d15ea01_54.png)

![](img/32f784f1c1bc0bb0db3c05ba1d15ea01_56.png)

![](img/32f784f1c1bc0bb0db3c05ba1d15ea01_58.png)

![](img/32f784f1c1bc0bb0db3c05ba1d15ea01_60.png)

![](img/32f784f1c1bc0bb0db3c05ba1d15ea01_62.png)

以下是实现参数遍历调优的步骤：

![](img/32f784f1c1bc0bb0db3c05ba1d15ea01_64.png)

![](img/32f784f1c1bc0bb0db3c05ba1d15ea01_66.png)

1.  **导入必要工具**：首先，我们需要导入用于生成参数组合的工具。
    ```python
    from itertools import product
    ```

![](img/32f784f1c1bc0bb0db3c05ba1d15ea01_68.png)

![](img/32f784f1c1bc0bb0db3c05ba1d15ea01_70.png)

![](img/32f784f1c1bc0bb0db3c05ba1d15ea01_72.png)

![](img/32f784f1c1bc0bb0db3c05ba1d15ea01_74.png)

![](img/32f784f1c1bc0bb0db3c05ba1d15ea01_76.png)

![](img/32f784f1c1bc0bb0db3c05ba1d15ea01_78.png)

2.  **定义参数空间**：接着，我们定义短期均线和长期均线的候选参数范围。
    ```python
    # 短期均线参数范围：20到60，步长为4
    sma1_range = range(20, 61, 4)
    # 长期均线参数范围：180到280，步长为10
    sma2_range = range(180, 281, 10)
    ```

![](img/32f784f1c1bc0bb0db3c05ba1d15ea01_80.png)

![](img/32f784f1c1bc0bb0db3c05ba1d15ea01_82.png)

![](img/32f784f1c1bc0bb0db3c05ba1d15ea01_84.png)

![](img/32f784f1c1bc0bb0db3c05ba1d15ea01_86.png)

![](img/32f784f1c1bc0bb0db3c05ba1d15ea01_88.png)

![](img/32f784f1c1bc0bb0db3c05ba1d15ea01_90.png)

3.  **遍历参数组合**：使用 `product` 函数生成所有可能的参数组合，并在循环中对每一组参数进行策略回测。
    ```python
    import pandas as pd
    # 初始化一个空的DataFrame来存储结果
    results = pd.DataFrame()
    
    for sma1, sma2 in product(sma1_range, sma2_range):
        # 1. 重新加载并预处理数据
        data = pd.read_csv('apple_stock_price.csv', index_col=0, parse_dates=True)
        data = data.dropna()
        
        # 2. 计算收益率和均线
        data['returns'] = data['price'].pct_change()
        data['SMA1'] = data['price'].rolling(window=sma1).mean()
        data['SMA2'] = data['price'].rolling(window=sma2).mean()
        data = data.dropna()
        
        # 3. 生成交易信号
        data['position'] = 0
        data.loc[data['SMA1'] > data['SMA2'], 'position'] = 1
        data.loc[data['SMA1'] < data['SMA2'], 'position'] = -1
        
        # 4. 计算策略收益
        data['strategy'] = data['position'].shift(1) * data['returns']
        data = data.dropna()
        
        # 5. 汇总本次回测结果
        result_table = pd.DataFrame({
            'SMA1': [sma1],
            'SMA2': [sma2],
            'Market_Return': [data['returns'].sum()],
            'Strategy_Return': [data['strategy'].sum()],
            'Outperformance': [data['strategy'].sum() - data['returns'].sum()]
        }, index=[0])
        
        # 6. 将本次结果追加到总结果表中
        results = pd.concat([results, result_table], ignore_index=True)
    ```

![](img/32f784f1c1bc0bb0db3c05ba1d15ea01_92.png)

![](img/32f784f1c1bc0bb0db3c05ba1d15ea01_94.png)

![](img/32f784f1c1bc0bb0db3c05ba1d15ea01_96.png)

![](img/32f784f1c1bc0bb0db3c05ba1d15ea01_98.png)

![](img/32f784f1c1bc0bb0db3c05ba1d15ea01_100.png)

![](img/32f784f1c1bc0bb0db3c05ba1d15ea01_102.png)

![](img/32f784f1c1bc0bb0db3c05ba1d15ea01_104.png)

![](img/32f784f1c1bc0bb0db3c05ba1d15ea01_106.png)

![](img/32f784f1c1bc0bb0db3c05ba1d15ea01_108.png)

4.  **分析结果**：运行完成后，我们可以查看 `results` 表格，其中包含了所有参数组合下的市场收益、策略收益以及策略的超额收益（Outperformance）。通过排序，我们可以轻松找出表现最好的参数组合。
    ```python
    # 按超额收益降序排列，找到最佳参数
    best_params = results.sort_values('Outperformance', ascending=False).head()
    print(best_params)
    ```

![](img/32f784f1c1bc0bb0db3c05ba1d15ea01_110.png)

![](img/32f784f1c1bc0bb0db3c05ba1d15ea01_112.png)

![](img/32f784f1c1bc0bb0db3c05ba1d15ea01_114.png)

![](img/32f784f1c1bc0bb0db3c05ba1d15ea01_116.png)

---

![](img/32f784f1c1bc0bb0db3c05ba1d15ea01_118.png)

![](img/32f784f1c1bc0bb0db3c05ba1d15ea01_120.png)

![](img/32f784f1c1bc0bb0db3c05ba1d15ea01_122.png)

![](img/32f784f1c1bc0bb0db3c05ba1d15ea01_124.png)

![](img/32f784f1c1bc0bb0db3c05ba1d15ea01_126.png)

![](img/32f784f1c1bc0bb0db3c05ba1d15ea01_128.png)

本节课中我们一起学习了如何对双均线策略进行参数调优。核心方法是利用 `itertools.product` 遍历所有可能的参数组合，并对每一组参数执行完整的策略回测流程，最终通过比较超额收益来筛选出较优的参数。这个过程是量化策略开发中优化模型性能的关键步骤。