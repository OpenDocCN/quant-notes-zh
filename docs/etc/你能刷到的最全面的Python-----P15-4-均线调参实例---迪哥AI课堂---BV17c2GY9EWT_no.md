# Python金融分析量化交易：P15：4-均线调参实例 📈

![](img/651ab3497767ebb1596d0471b6e5a2d0_0.png)

![](img/651ab3497767ebb1596d0471b6e5a2d0_2.png)

![](img/651ab3497767ebb1596d0471b6e5a2d0_4.png)

![](img/651ab3497767ebb1596d0471b6e5a2d0_6.png)

![](img/651ab3497767ebb1596d0471b6e5a2d0_8.png)

在本节课中，我们将学习如何对双均线交易策略进行参数调优。我们将通过遍历不同的短期和长期均线参数组合，来评估哪种组合能带来更好的投资回报。

![](img/651ab3497767ebb1596d0471b6e5a2d0_10.png)

![](img/651ab3497767ebb1596d0471b6e5a2d0_12.png)

![](img/651ab3497767ebb1596d0471b6e5a2d0_14.png)

![](img/651ab3497767ebb1596d0471b6e5a2d0_16.png)

![](img/651ab3497767ebb1596d0471b6e5a2d0_18.png)

---

![](img/651ab3497767ebb1596d0471b6e5a2d0_20.png)

![](img/651ab3497767ebb1596d0471b6e5a2d0_22.png)

![](img/651ab3497767ebb1596d0471b6e5a2d0_24.png)

![](img/651ab3497767ebb1596d0471b6e5a2d0_26.png)

![](img/651ab3497767ebb1596d0471b6e5a2d0_28.png)

![](img/651ab3497767ebb1596d0471b6e5a2d0_30.png)

![](img/651ab3497767ebb1596d0471b6e5a2d0_32.png)

上一节我们介绍了双均线策略的基本原理和实现。本节中，我们来看看如何通过调整策略参数来优化其表现。策略的最终收益与短期均线（SMA1）和长期均线（SMA2）的窗口大小密切相关。我们需要找到最优的参数组合。

![](img/651ab3497767ebb1596d0471b6e5a2d0_34.png)

![](img/651ab3497767ebb1596d0471b6e5a2d0_36.png)

![](img/651ab3497767ebb1596d0471b6e5a2d0_38.png)

![](img/651ab3497767ebb1596d0471b6e5a2d0_40.png)

![](img/651ab3497767ebb1596d0471b6e5a2d0_42.png)

![](img/651ab3497767ebb1596d0471b6e5a2d0_44.png)

以下是实现参数调优的步骤：

![](img/651ab3497767ebb1596d0471b6e5a2d0_46.png)

![](img/651ab3497767ebb1596d0471b6e5a2d0_48.png)

![](img/651ab3497767ebb1596d0471b6e5a2d0_50.png)

![](img/651ab3497767ebb1596d0471b6e5a2d0_52.png)

1.  **导入必要工具**：首先，我们需要导入用于生成参数组合的工具。
    ```python
    from itertools import product
    ```

![](img/651ab3497767ebb1596d0471b6e5a2d0_54.png)

![](img/651ab3497767ebb1596d0471b6e5a2d0_56.png)

![](img/651ab3497767ebb1596d0471b6e5a2d0_58.png)

![](img/651ab3497767ebb1596d0471b6e5a2d0_60.png)

![](img/651ab3497767ebb1596d0471b6e5a2d0_62.png)

2.  **定义参数空间**：接下来，我们定义短期和长期均线可能的参数范围。例如，短期均线窗口从20到60，步长为4；长期均线窗口从180到280，步长为10。
    ```python
    short_windows = range(20, 61, 4)
    long_windows = range(180, 281, 10)
    ```

![](img/651ab3497767ebb1596d0471b6e5a2d0_64.png)

![](img/651ab3497767ebb1596d0471b6e5a2d0_66.png)

3.  **遍历参数组合**：使用 `product` 函数生成所有可能的参数组合，并进行遍历计算。
    ```python
    for sma1, sma2 in product(short_windows, long_windows):
        # 在此处计算每个参数组合下的策略表现
    ```

![](img/651ab3497767ebb1596d0471b6e5a2d0_68.png)

![](img/651ab3497767ebb1596d0471b6e5a2d0_70.png)

![](img/651ab3497767ebb1596d0471b6e5a2d0_72.png)

4.  **数据准备与计算**：在循环内部，我们需要为每一组参数重新加载数据、计算收益率和均线。
    ```python
    # 重新加载苹果股价数据
    data = pd.DataFrame(yf.download('AAPL', start='2020-01-01', end='2023-01-01')['Adj Close'])
    data.dropna(inplace=True) # 去除缺失值

    # 计算收益率和均线
    data['returns'] = np.log(data['Adj Close'] / data['Adj Close'].shift(1))
    data['SMA1'] = data['Adj Close'].rolling(window=sma1).mean()
    data['SMA2'] = data['Adj Close'].rolling(window=sma2).mean()
    data.dropna(inplace=True) # 再次去除因计算产生的缺失值
    ```

![](img/651ab3497767ebb1596d0471b6e5a2d0_74.png)

![](img/651ab3497767ebb1596d0471b6e5a2d0_76.png)

![](img/651ab3497767ebb1596d0471b6e5a2d0_78.png)

![](img/651ab3497767ebb1596d0471b6e5a2d0_80.png)

![](img/651ab3497767ebb1596d0471b6e5a2d0_82.png)

5.  **生成交易信号**：根据双均线交叉规则生成持仓信号。
    ```python
    data['position'] = np.where(data['SMA1'] > data['SMA2'], 1, -1)
    ```

![](img/651ab3497767ebb1596d0471b6e5a2d0_84.png)

![](img/651ab3497767ebb1596d0471b6e5a2d0_86.png)

![](img/651ab3497767ebb1596d0471b6e5a2d0_88.png)

![](img/651ab3497767ebb1596d0471b6e5a2d0_90.png)

![](img/651ab3497767ebb1596d0471b6e5a2d0_92.png)

6.  **计算策略收益**：根据持仓信号计算策略的每日收益率，并与基准（买入并持有）收益率进行比较。
    ```python
    data['strategy'] = data['position'].shift(1) * data['returns']
    data.dropna(inplace=True)
    # 计算策略相对于基准的超额收益
    outperformance = data['strategy'].sum() - data['returns'].sum()
    ```

![](img/651ab3497767ebb1596d0471b6e5a2d0_94.png)

![](img/651ab3497767ebb1596d0471b6e5a2d0_96.png)

![](img/651ab3497767ebb1596d0471b6e5a2d0_98.png)

![](img/651ab3497767ebb1596d0471b6e5a2d0_100.png)

![](img/651ab3497767ebb1596d0471b6e5a2d0_102.png)

![](img/651ab3497767ebb1596d0471b6e5a2d0_104.png)

7.  **汇总结果**：将每组参数及其对应的策略表现（如基准收益、策略收益、超额收益）保存到一个结果表格中。
    ```python
    result = pd.DataFrame({
        'SMA1': [sma1],
        'SMA2': [sma2],
        'Benchmark_Return': [data['returns'].sum()],
        'Strategy_Return': [data['strategy'].sum()],
        'Outperformance': [outperformance]
    })
    # 将本次循环的结果追加到总结果表中
    results = pd.concat([results, result], ignore_index=True)
    ```

![](img/651ab3497767ebb1596d0471b6e5a2d0_106.png)

![](img/651ab3497767ebb1596d0471b6e5a2d0_108.png)

![](img/651ab3497767ebb1596d0471b6e5a2d0_110.png)

![](img/651ab3497767ebb1596d0471b6e5a2d0_112.png)

完成所有循环后，我们可以查看 `results` 表格。通过分析表格中的 `Outperformance`（超额收益）列，可以找出哪些参数组合使策略表现优于简单的买入持有策略，哪些组合反而更差。这帮助我们理解参数选择对策略有效性的关键影响。

![](img/651ab3497767ebb1596d0471b6e5a2d0_114.png)

![](img/651ab3497767ebb1596d0471b6e5a2d0_116.png)

![](img/651ab3497767ebb1596d0471b6e5a2d0_118.png)

---

![](img/651ab3497767ebb1596d0471b6e5a2d0_120.png)

![](img/651ab3497767ebb1596d0471b6e5a2d0_122.png)

![](img/651ab3497767ebb1596d0471b6e5a2d0_124.png)

![](img/651ab3497767ebb1596d0471b6e5a2d0_126.png)

![](img/651ab3497767ebb1596d0471b6e5a2d0_128.png)

本节课中我们一起学习了如何对双均线策略进行系统的参数调优。通过遍历不同的参数组合并计算其表现，我们能够更科学地评估和选择策略参数，这是量化交易中优化模型性能的重要一步。