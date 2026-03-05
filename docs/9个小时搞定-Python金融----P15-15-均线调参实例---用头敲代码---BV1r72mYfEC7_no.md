# Python金融量化分析：P15：均线调参实例 📈

![](img/e340b357d1fdf4f65e3115aaf2218f52_0.png)

![](img/e340b357d1fdf4f65e3115aaf2218f52_2.png)

![](img/e340b357d1fdf4f65e3115aaf2218f52_4.png)

![](img/e340b357d1fdf4f65e3115aaf2218f52_6.png)

![](img/e340b357d1fdf4f65e3115aaf2218f52_8.png)

![](img/e340b357d1fdf4f65e3115aaf2218f52_10.png)

![](img/e340b357d1fdf4f65e3115aaf2218f52_12.png)

![](img/e340b357d1fdf4f65e3115aaf2218f52_14.png)

在本节课中，我们将学习如何对双均线交易策略进行参数调优。我们将通过遍历不同的短期和长期均线参数组合，来寻找可能表现更优的策略配置。

![](img/e340b357d1fdf4f65e3115aaf2218f52_16.png)

![](img/e340b357d1fdf4f65e3115aaf2218f52_18.png)

![](img/e340b357d1fdf4f65e3115aaf2218f52_20.png)

![](img/e340b357d1fdf4f65e3115aaf2218f52_22.png)

![](img/e340b357d1fdf4f65e3115aaf2218f52_24.png)

---

![](img/e340b357d1fdf4f65e3115aaf2218f52_26.png)

![](img/e340b357d1fdf4f65e3115aaf2218f52_28.png)

![](img/e340b357d1fdf4f65e3115aaf2218f52_30.png)

![](img/e340b357d1fdf4f65e3115aaf2218f52_32.png)

![](img/e340b357d1fdf4f65e3115aaf2218f52_34.png)

![](img/e340b357d1fdf4f65e3115aaf2218f52_36.png)

![](img/e340b357d1fdf4f65e3115aaf2218f52_38.png)

![](img/e340b357d1fdf4f65e3115aaf2218f52_40.png)

上一节我们介绍了双均线策略的基本原理和实现。本节中，我们来看看如何通过调整策略参数来优化其表现。

![](img/e340b357d1fdf4f65e3115aaf2218f52_42.png)

![](img/e340b357d1fdf4f65e3115aaf2218f52_44.png)

![](img/e340b357d1fdf4f65e3115aaf2218f52_46.png)

![](img/e340b357d1fdf4f65e3115aaf2218f52_48.png)

![](img/e340b357d1fdf4f65e3115aaf2218f52_50.png)

现在有一个问题：策略的最终收益与哪些因素有关？显然，与短期均线（SMA1）和长期均线（SMA2）的窗口大小选择密切相关。短期窗口是5天合适还是10天合适？长期窗口是20天合适还是40天合适？我们需要通过计算不同参数组合的表现来寻找答案。

![](img/e340b357d1fdf4f65e3115aaf2218f52_52.png)

![](img/e340b357d1fdf4f65e3115aaf2218f52_54.png)

![](img/e340b357d1fdf4f65e3115aaf2218f52_56.png)

![](img/e340b357d1fdf4f65e3115aaf2218f52_58.png)

![](img/e340b357d1fdf4f65e3115aaf2218f52_60.png)

![](img/e340b357d1fdf4f65e3115aaf2218f52_62.png)

在Python中，我们可以轻松地使用迭代工具来完成这个任务。

![](img/e340b357d1fdf4f65e3115aaf2218f52_64.png)

![](img/e340b357d1fdf4f65e3115aaf2218f52_66.png)

以下是实现参数遍历的步骤：

![](img/e340b357d1fdf4f65e3115aaf2218f52_68.png)

![](img/e340b357d1fdf4f65e3115aaf2218f52_70.png)

![](img/e340b357d1fdf4f65e3115aaf2218f52_72.png)

![](img/e340b357d1fdf4f65e3115aaf2218f52_74.png)

1.  **导入迭代工具**：首先，我们需要导入`itertools`模块中的`product`函数，它能帮助我们生成所有可能的参数组合。
    ```python
    from itertools import product
    ```

![](img/e340b357d1fdf4f65e3115aaf2218f52_76.png)

![](img/e340b357d1fdf4f65e3115aaf2218f52_78.png)

![](img/e340b357d1fdf4f65e3115aaf2218f52_80.png)

![](img/e340b357d1fdf4f65e3115aaf2218f52_82.png)

![](img/e340b357d1fdf4f65e3115aaf2218f52_84.png)

![](img/e340b357d1fdf4f65e3115aaf2218f52_86.png)

2.  **定义参数空间**：接着，我们定义短期均线（SMA1）和长期均线（SMA2）的参数取值范围。
    ```python
    # 定义短期均线参数空间，例如从20到60，步长为4
    sma1_range = range(20, 61, 4)
    # 定义长期均线参数空间，例如从180到280，步长为10
    sma2_range = range(180, 281, 10)
    ```
    `product`函数会生成这两个范围的所有组合，例如 `(20, 180)`, `(20, 190)`, `(24, 180)`, `(24, 190)` 等等。

![](img/e340b357d1fdf4f65e3115aaf2218f52_88.png)

![](img/e340b357d1fdf4f65e3115aaf2218f52_90.png)

![](img/e340b357d1fdf4f65e3115aaf2218f52_92.png)

![](img/e340b357d1fdf4f65e3115aaf2218f52_94.png)

![](img/e340b357d1fdf4f65e3115aaf2218f52_96.png)

![](img/e340b357d1fdf4f65e3115aaf2218f52_98.png)

3.  **遍历参数组合并计算策略表现**：然后，我们遍历每一个参数组合，为每个组合重新计算双均线策略的收益。
    ```python
    import pandas as pd

    # 初始化一个空的DataFrame来存储所有结果
    results = pd.DataFrame()

    # 遍历所有参数组合
    for sma1, sma2 in product(sma1_range, sma2_range):
        # 1. 重新加载并预处理数据（例如苹果股价）
        data = pd.read_csv(‘apple_stock_price.csv‘, index_col=0, parse_dates=True)
        data = data.dropna()  # 去除缺失值

        # 2. 计算收益率
        data[‘returns‘] = np.log(data[‘Close‘] / data[‘Close‘].shift(1))

        # 3. 使用当前参数计算短期和长期均线
        data[‘SMA1‘] = data[‘Close‘].rolling(window=sma1).mean()
        data[‘SMA2‘] = data[‘Close‘].rolling(window=sma2).mean()

        # 4. 生成交易信号（金叉买入，死叉卖出）
        data[‘position‘] = np.where(data[‘SMA1‘] > data[‘SMA2‘], 1, -1)

        # 5. 计算策略收益率
        data[‘strategy‘] = data[‘position‘].shift(1) * data[‘returns‘]

        # 6. 去除计算产生的缺失值
        data = data.dropna()

        # 7. 计算累计收益
        cum_returns = data[[‘returns‘, ‘strategy‘]].cumsum().apply(np.exp)

        # 8. 计算策略相对于简单持有的超额收益（Outperformance）
        outperformance = cum_returns[‘strategy‘].iloc[-1] - cum_returns[‘returns‘].iloc[-1]

        # 9. 将当前参数组合的结果保存到总表中
        temp_df = pd.DataFrame({
            ‘SMA1‘: [sma1],
            ‘SMA2‘: [sma2],
            ‘Cumulative Returns‘: [cum_returns[‘returns‘].iloc[-1]],
            ‘Strategy Returns‘: [cum_returns[‘strategy‘].iloc[-1]],
            ‘Outperformance‘: [outperformance]
        })
        results = pd.concat([results, temp_df], ignore_index=True)
    ```

![](img/e340b357d1fdf4f65e3115aaf2218f52_100.png)

![](img/e340b357d1fdf4f65e3115aaf2218f52_102.png)

![](img/e340b357d1fdf4f65e3115aaf2218f52_104.png)

![](img/e340b357d1fdf4f65e3115aaf2218f52_106.png)

![](img/e340b357d1fdf4f65e3115aaf2218f52_108.png)

![](img/e340b357d1fdf4f65e3115aaf2218f52_110.png)

![](img/e340b357d1fdf4f65e3115aaf2218f52_112.png)

4.  **分析结果**：最后，我们可以查看`results`这个DataFrame，根据`Outperformance`（策略超额收益）等指标对结果进行排序，找出表现最好的参数组合。
    ```python
    # 按超额收益降序排列，查看表现最好的参数
    best_params = results.sort_values(by=‘Outperformance‘, ascending=False)
    print(best_params.head())
    ```
    从结果中可以看到，不同的参数组合会导致不同的策略表现。有些参数组合可能反而不如简单持有（`Outperformance`为负），而有些组合则能带来显著的正向超额收益。

![](img/e340b357d1fdf4f65e3115aaf2218f52_114.png)

![](img/e340b357d1fdf4f65e3115aaf2218f52_116.png)

![](img/e340b357d1fdf4f65e3115aaf2218f52_118.png)

---

![](img/e340b357d1fdf4f65e3115aaf2218f52_120.png)

![](img/e340b357d1fdf4f65e3115aaf2218f52_122.png)

![](img/e340b357d1fdf4f65e3115aaf2218f52_124.png)

![](img/e340b357d1fdf4f65e3115aaf2218f52_126.png)

![](img/e340b357d1fdf4f65e3115aaf2218f52_128.png)

本节课中我们一起学习了如何对双均线策略进行参数调优。我们利用`itertools.product`函数遍历了不同的短期和长期均线窗口组合，并通过计算每个组合的策略累计收益和超额收益，来评估和比较不同参数的效果。这个过程是量化策略开发中优化和寻找稳健参数的关键步骤。