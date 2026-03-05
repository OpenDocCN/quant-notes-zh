# Python金融分析与量化交易实战：P13：04-4-均线调参实例 📈

![](img/53460bfe0b23d2cd6201573c9c9e2108_0.png)

![](img/53460bfe0b23d2cd6201573c9c9e2108_2.png)

![](img/53460bfe0b23d2cd6201573c9c9e2108_4.png)

在本节课中，我们将学习如何对双均线交易策略进行参数调优。我们将通过遍历不同的短期和长期均线参数组合，来寻找可能表现更优的策略配置。

---

![](img/53460bfe0b23d2cd6201573c9c9e2108_6.png)

![](img/53460bfe0b23d2cd6201573c9c9e2108_8.png)

上一节我们介绍了双均线策略的基本实现与回测。本节中我们来看看如何通过调整策略参数来优化其表现。

![](img/53460bfe0b23d2cd6201573c9c9e2108_10.png)

![](img/53460bfe0b23d2cd6201573c9c9e2108_12.png)

![](img/53460bfe0b23d2cd6201573c9c9e2108_14.png)

策略的最终收益与许多因素有关，其中短期均线（SMA1）和长期均线（SMA2）的窗口长度是关键参数。我们需要探索不同的参数组合，例如短期窗口是5天合适还是10天合适，长期窗口是20天合适还是40天合适。

![](img/53460bfe0b23d2cd6201573c9c9e2108_16.png)

![](img/53460bfe0b23d2cd6201573c9c9e2108_18.png)

![](img/53460bfe0b23d2cd6201573c9c9e2108_20.png)

![](img/53460bfe0b23d2cd6201573c9c9e2108_22.png)

以下是实现参数遍历的核心步骤：

![](img/53460bfe0b23d2cd6201573c9c9e2108_24.png)

![](img/53460bfe0b23d2cd6201573c9c9e2108_26.png)

![](img/53460bfe0b23d2cd6201573c9c9e2108_28.png)

![](img/53460bfe0b23d2cd6201573c9c9e2108_30.png)

1.  **导入必要工具**：使用 `itertools.product` 函数来生成所有可能的参数组合。
    ```python
    from itertools import product
    ```

![](img/53460bfe0b23d2cd6201573c9c9e2108_32.png)

![](img/53460bfe0b23d2cd6201573c9c9e2108_34.png)

![](img/53460bfe0b23d2cd6201573c9c9e2108_36.png)

2.  **定义参数空间**：分别指定短期均线和长期均线的候选值范围。
    ```python
    # 短期均线参数空间：从20到60，步长为4
    sma1_range = range(20, 61, 4)
    # 长期均线参数空间：从180到280，步长为10
    sma2_range = range(180, 281, 10)
    ```

![](img/53460bfe0b23d2cd6201573c9c9e2108_38.png)

![](img/53460bfe0b23d2cd6201573c9c9e2108_40.png)

3.  **遍历参数组合**：使用循环遍历每一组参数，并计算对应的策略收益。
    ```python
    for sma1, sma2 in product(sma1_range, sma2_range):
        # 在此处计算使用 sma1 和 sma2 参数时的策略表现
    ```

![](img/53460bfe0b23d2cd6201573c9c9e2108_42.png)

![](img/53460bfe0b23d2cd6201573c9c9e2108_44.png)

![](img/53460bfe0b23d2cd6201573c9c9e2108_46.png)

---

![](img/53460bfe0b23d2cd6201573c9c9e2108_48.png)

![](img/53460bfe0b23d2cd6201573c9c9e2108_50.png)

接下来，我们需要在循环内部，为每一组参数重新计算策略并评估其表现。以下是每个循环内需要执行的操作：

![](img/53460bfe0b23d2cd6201573c9c9e2108_52.png)

1.  **重新加载并预处理数据**：确保每次计算都从干净的数据开始。
    ```python
    data = df[‘AAPL.Close’].copy()
    data = data.dropna()
    ```

2.  **计算收益率和均线**：根据当前参数 `sma1` 和 `sma2` 计算短期、长期均线以及股价的日收益率。
    ```python
    data[‘returns’] = np.log(data / data.shift(1))
    data[‘SMA1’] = data[‘AAPL.Close’].rolling(sma1).mean()
    data[‘SMA2’] = data[‘AAPL.Close’].rolling(sma2).mean()
    data = data.dropna()
    ```

![](img/53460bfe0b23d2cd6201573c9c9e2108_54.png)

3.  **生成交易信号**：当短期均线上穿长期均线时，产生买入信号（1），否则为0。
    ```python
    data[‘position’] = np.where(data[‘SMA1’] > data[‘SMA2’], 1, 0)
    ```

![](img/53460bfe0b23d2cd6201573c9c9e2108_56.png)

![](img/53460bfe0b23d2cd6201573c9c9e2108_58.png)

![](img/53460bfe0b23d2cd6201573c9c9e2108_60.png)

![](img/53460bfe0b23d2cd6201573c9c9e2108_62.png)

4.  **计算策略收益**：根据交易信号计算策略的累计收益，并与基准（单纯持有）收益进行比较。
    ```python
    data[‘strategy’] = data[‘position’].shift(1) * data[‘returns’]
    data[[‘returns’, ‘strategy’]].cumsum().apply(np.exp).plot(figsize=(10, 6))
    outperformance = data[‘strategy’].sum() - data[‘returns’].sum()
    ```

![](img/53460bfe0b23d2cd6201573c9c9e2108_64.png)

![](img/53460bfe0b23d2cd6201573c9c9e2108_66.png)

---

![](img/53460bfe0b23d2cd6201573c9c9e2108_68.png)

![](img/53460bfe0b23d2cd6201573c9c9e2108_70.png)

![](img/53460bfe0b23d2cd6201573c9c9e2108_72.png)

为了系统地比较所有参数组合的结果，我们需要将每次循环的计算结果保存下来。以下是构建结果汇总表的方法：

![](img/53460bfe0b23d2cd6201573c9c9e2108_74.png)

![](img/53460bfe0b23d2cd6201573c9c9e2108_76.png)

![](img/53460bfe0b23d2cd6201573c9c9e2108_78.png)

![](img/53460bfe0b23d2cd6201573c9c9e2108_80.png)

1.  **初始化结果表**：在循环开始前，创建一个空的DataFrame来存储结果。
    ```python
    results = pd.DataFrame()
    ```

![](img/53460bfe0b23d2cd6201573c9c9e2108_82.png)

![](img/53460bfe0b23d2cd6201573c9c9e2108_84.png)

![](img/53460bfe0b23d2cd6201573c9c9e2108_86.png)

2.  **在循环中收集结果**：每次计算完成后，将参数和对应的收益指标打包成一个字典，并添加到结果表中。
    ```python
    result = pd.DataFrame({
        ‘SMA1’: sma1,
        ‘SMA2’: sma2,
        ‘MARKET_RET’: data[‘returns’].sum(),
        ‘STRAT_RET’: data[‘strategy’].sum(),
        ‘OUTPERF’: outperformance
    }, index=[0]) # 需要指定一个索引
    results = results.append(result, ignore_index=True)
    ```

3.  **查看与分析结果**：运行完成后，可以查看 `results` 表格，其中 `OUTPERF` 列（策略超额收益）可以帮助我们判断哪些参数组合更有效。
    ```python
    print(results.head(10))
    ```

![](img/53460bfe0b23d2cd6201573c9c9e2108_88.png)

![](img/53460bfe0b23d2cd6201573c9c9e2108_90.png)

![](img/53460bfe0b23d2cd6201573c9c9e2108_92.png)

---

![](img/53460bfe0b23d2cd6201573c9c9e2108_94.png)

![](img/53460bfe0b23d2cd6201573c9c9e2108_96.png)

从结果表中我们可以观察到，并非所有参数组合都能带来正收益。有些组合的表现甚至不如单纯持有股票（基准收益）。这说明了参数选择对策略有效性的重要影响。通过这种系统性的遍历，我们可以初步筛选出表现较好的参数区间，为更精细的优化打下基础。

![](img/53460bfe0b23d2cd6201573c9c9e2108_98.png)

![](img/53460bfe0b23d2cd6201573c9c9e2108_100.png)

![](img/53460bfe0b23d2cd6201573c9c9e2108_102.png)

![](img/53460bfe0b23d2cd6201573c9c9e2108_104.png)

本节课中我们一起学习了如何对双均线策略进行参数调优。我们利用 `itertools.product` 遍历了不同的均线参数组合，并通过回测计算了每个组合的策略表现，最终将结果汇总成表以便分析。这个过程是量化策略开发中优化环节的基础实践。