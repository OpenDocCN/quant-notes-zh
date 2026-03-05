# Python金融量化分析：P15：均线调参实例 📈

![](img/4de3fc311c5a9abba2493f5e7d32f068_0.png)

![](img/4de3fc311c5a9abba2493f5e7d32f068_2.png)

![](img/4de3fc311c5a9abba2493f5e7d32f068_4.png)

![](img/4de3fc311c5a9abba2493f5e7d32f068_6.png)

![](img/4de3fc311c5a9abba2493f5e7d32f068_8.png)

![](img/4de3fc311c5a9abba2493f5e7d32f068_10.png)

![](img/4de3fc311c5a9abba2493f5e7d32f068_12.png)

![](img/4de3fc311c5a9abba2493f5e7d32f068_14.png)

![](img/4de3fc311c5a9abba2493f5e7d32f068_16.png)

在本节课中，我们将学习如何对双均线交易策略进行参数调优。我们将通过遍历不同的短期和长期移动平均线参数组合，来评估哪种组合能带来更好的策略表现。

![](img/4de3fc311c5a9abba2493f5e7d32f068_18.png)

![](img/4de3fc311c5a9abba2493f5e7d32f068_20.png)

![](img/4de3fc311c5a9abba2493f5e7d32f068_22.png)

![](img/4de3fc311c5a9abba2493f5e7d32f068_24.png)

![](img/4de3fc311c5a9abba2493f5e7d32f068_26.png)

![](img/4de3fc311c5a9abba2493f5e7d32f068_28.png)

![](img/4de3fc311c5a9abba2493f5e7d32f068_30.png)

![](img/4de3fc311c5a9abba2493f5e7d32f068_32.png)

![](img/4de3fc311c5a9abba2493f5e7d32f068_34.png)

---

![](img/4de3fc311c5a9abba2493f5e7d32f068_36.png)

![](img/4de3fc311c5a9abba2493f5e7d32f068_38.png)

![](img/4de3fc311c5a9abba2493f5e7d32f068_40.png)

![](img/4de3fc311c5a9abba2493f5e7d32f068_42.png)

![](img/4de3fc311c5a9abba2493f5e7d32f068_44.png)

![](img/4de3fc311c5a9abba2493f5e7d32f068_46.png)

![](img/4de3fc311c5a9abba2493f5e7d32f068_48.png)

上一节我们介绍了双均线策略的基本原理和回测方法。本节中，我们来看看如何通过调整策略参数来优化其表现。策略的最终收益与短期均线（SMA1）和长期均线（SMA2）的窗口大小密切相关。我们需要找到最优的参数组合。

![](img/4de3fc311c5a9abba2493f5e7d32f068_50.png)

![](img/4de3fc311c5a9abba2493f5e7d32f068_52.png)

![](img/4de3fc311c5a9abba2493f5e7d32f068_54.png)

![](img/4de3fc311c5a9abba2493f5e7d32f068_56.png)

![](img/4de3fc311c5a9abba2493f5e7d32f068_58.png)

![](img/4de3fc311c5a9abba2493f5e7d32f068_60.png)

![](img/4de3fc311c5a9abba2493f5e7d32f068_62.png)

![](img/4de3fc311c5a9abba2493f5e7d32f068_64.png)

以下是实现参数遍历调优的步骤：

![](img/4de3fc311c5a9abba2493f5e7d32f068_66.png)

![](img/4de3fc311c5a9abba2493f5e7d32f068_68.png)

1.  **导入必要工具**
    首先，我们需要导入用于生成参数组合的工具 `product`。
    ```python
    from itertools import product
    ```

![](img/4de3fc311c5a9abba2493f5e7d32f068_70.png)

![](img/4de3fc311c5a9abba2493f5e7d32f068_72.png)

![](img/4de3fc311c5a9abba2493f5e7d32f068_74.png)

![](img/4de3fc311c5a9abba2493f5e7d32f068_76.png)

![](img/4de3fc311c5a9abba2493f5e7d32f068_78.png)

![](img/4de3fc311c5a9abba2493f5e7d32f068_80.png)

2.  **定义参数空间**
    我们需要为短期均线（SMA1）和长期均线（SMA2）分别定义一个可能的参数范围。
    ```python
    # 定义短期均线参数范围：从20到60，步长为4
    sma1_range = range(20, 61, 4)
    # 定义长期均线参数范围：从180到280，步长为10
    sma2_range = range(180, 281, 10)
    ```

![](img/4de3fc311c5a9abba2493f5e7d32f068_82.png)

![](img/4de3fc311c5a9abba2493f5e7d32f068_84.png)

![](img/4de3fc311c5a9abba2493f5e7d32f068_86.png)

![](img/4de3fc311c5a9abba2493f5e7d32f068_88.png)

![](img/4de3fc311c5a9abba2493f5e7d32f068_90.png)

![](img/4de3fc311c5a9abba2493f5e7d32f068_92.png)

![](img/4de3fc311c5a9abba2493f5e7d32f068_94.png)

3.  **遍历参数组合**
    使用 `product` 函数生成所有可能的参数组合，并在循环中对每一组参数进行策略回测。
    ```python
    import pandas as pd
    # 初始化一个空的DataFrame来存储所有结果
    results = pd.DataFrame()
    
    # 遍历所有参数组合
    for sma1, sma2 in product(sma1_range, sma2_range):
        # 1. 重新加载并预处理数据
        data = pd.read_csv('你的股票数据文件.csv', index_col=0, parse_dates=True)
        data = data['Close'].to_frame()  # 假设我们使用收盘价
        data.dropna(inplace=True)
        
        # 2. 计算收益率和移动平均线
        data['Returns'] = data['Close'].pct_change()
        data['SMA1'] = data['Close'].rolling(window=sma1).mean()
        data['SMA2'] = data['Close'].rolling(window=sma2).mean()
        data.dropna(inplace=True)
        
        # 3. 生成交易信号
        data['Position'] = 0
        data.loc[data['SMA1'] > data['SMA2'], 'Position'] = 1
        data['Strategy'] = data['Position'].shift(1) * data['Returns']
        data.dropna(inplace=True)
        
        # 4. 计算策略表现
        strategy_return = data['Strategy'].sum()
        buy_hold_return = data['Returns'].sum()
        outperformance = strategy_return - buy_hold_return
        
        # 5. 将本次结果存入表格
        temp_df = pd.DataFrame({
            'SMA1': [sma1],
            'SMA2': [sma2],
            'Buy&Hold_Return': [buy_hold_return],
            'Strategy_Return': [strategy_return],
            'Outperformance': [outperformance]
        })
        results = pd.concat([results, temp_df], ignore_index=True)
    ```

![](img/4de3fc311c5a9abba2493f5e7d32f068_96.png)

![](img/4de3fc311c5a9abba2493f5e7d32f068_98.png)

![](img/4de3fc311c5a9abba2493f5e7d32f068_100.png)

![](img/4de3fc311c5a9abba2493f5e7d32f068_102.png)

![](img/4de3fc311c5a9abba2493f5e7d32f068_104.png)

![](img/4de3fc311c5a9abba2493f5e7d32f068_106.png)

![](img/4de3fc311c5a9abba2493f5e7d32f068_108.png)

![](img/4de3fc311c5a9abba2493f5e7d32f068_110.png)

4.  **分析结果**
    代码运行后，`results` DataFrame 将包含所有参数组合下的策略表现。我们可以通过排序来找出表现最佳（`Outperformance` 值最高）的参数组合。
    ```python
    # 按超额收益降序排列，查看最佳参数
    best_results = results.sort_values(by='Outperformance', ascending=False)
    print(best_results.head())
    ```

![](img/4de3fc311c5a9abba2493f5e7d32f068_112.png)

![](img/4de3fc311c5a9abba2493f5e7d32f068_114.png)

![](img/4de3fc311c5a9abba2493f5e7d32f068_116.png)

![](img/4de3fc311c5a9abba2493f5e7d32f068_118.png)

---

![](img/4de3fc311c5a9abba2493f5e7d32f068_120.png)

![](img/4de3fc311c5a9abba2493f5e7d32f068_122.png)

![](img/4de3fc311c5a9abba2493f5e7d32f068_124.png)

![](img/4de3fc311c5a9abba2493f5e7d32f068_126.png)

![](img/4de3fc311c5a9abba2493f5e7d32f068_128.png)

![](img/4de3fc311c5a9abba2493f5e7d32f068_130.png)

![](img/4de3fc311c5a9abba2493f5e7d32f068_132.png)

本节课中我们一起学习了如何对双均线策略进行参数调优。核心方法是使用 `itertools.product` 遍历所有可能的参数组合，并对每一组参数执行完整的策略回测流程，最后通过比较超额收益来筛选出较优的参数设置。这个过程是量化策略开发中优化模型性能的关键步骤。