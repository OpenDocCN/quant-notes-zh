# Python金融量化+股票交易：P15：均线调参实例 📈

![](img/1988321d055ef656859bb92fbc6ac3f5_0.png)

![](img/1988321d055ef656859bb92fbc6ac3f5_2.png)

![](img/1988321d055ef656859bb92fbc6ac3f5_4.png)

![](img/1988321d055ef656859bb92fbc6ac3f5_6.png)

![](img/1988321d055ef656859bb92fbc6ac3f5_8.png)

在本节课中，我们将学习如何对双均线交易策略进行参数调优。我们将通过遍历不同的短期和长期移动平均线参数组合，来寻找可能表现更优的策略配置。

![](img/1988321d055ef656859bb92fbc6ac3f5_10.png)

![](img/1988321d055ef656859bb92fbc6ac3f5_12.png)

![](img/1988321d055ef656859bb92fbc6ac3f5_14.png)

---

![](img/1988321d055ef656859bb92fbc6ac3f5_16.png)

![](img/1988321d055ef656859bb92fbc6ac3f5_18.png)

![](img/1988321d055ef656859bb92fbc6ac3f5_20.png)

上一节我们介绍了双均线策略的基本原理和实现。本节中，我们来看看如何通过调整策略参数来优化其表现。

![](img/1988321d055ef656859bb92fbc6ac3f5_22.png)

![](img/1988321d055ef656859bb92fbc6ac3f5_24.png)

![](img/1988321d055ef656859bb92fbc6ac3f5_26.png)

![](img/1988321d055ef656859bb92fbc6ac3f5_28.png)

![](img/1988321d055ef656859bb92fbc6ac3f5_30.png)

策略的最终收益与许多因素有关，其中短期和长期移动平均线的窗口大小是关键参数。我们需要探索不同的参数组合，例如短期窗口是5天还是10天更合适，长期窗口是20天还是40天更合适。

![](img/1988321d055ef656859bb92fbc6ac3f5_32.png)

![](img/1988321d055ef656859bb92fbc6ac3f5_34.png)

![](img/1988321d055ef656859bb92fbc6ac3f5_36.png)

![](img/1988321d055ef656859bb92fbc6ac3f5_38.png)

![](img/1988321d055ef656859bb92fbc6ac3f5_40.png)

在Python中，我们可以借助工具轻松地完成这种参数遍历。

![](img/1988321d055ef656859bb92fbc6ac3f5_42.png)

![](img/1988321d055ef656859bb92fbc6ac3f5_44.png)

![](img/1988321d055ef656859bb92fbc6ac3f5_46.png)

![](img/1988321d055ef656859bb92fbc6ac3f5_48.png)

以下是实现参数遍历的步骤：

![](img/1988321d055ef656859bb92fbc6ac3f5_50.png)

![](img/1988321d055ef656859bb92fbc6ac3f5_52.png)

![](img/1988321d055ef656859bb92fbc6ac3f5_54.png)

![](img/1988321d055ef656859bb92fbc6ac3f5_56.png)

![](img/1988321d055ef656859bb92fbc6ac3f5_58.png)

1.  **导入必要的工具**：我们将使用 `itertools` 库中的 `product` 函数来生成所有可能的参数组合。
    ```python
    from itertools import product
    ```

![](img/1988321d055ef656859bb92fbc6ac3f5_60.png)

![](img/1988321d055ef656859bb92fbc6ac3f5_62.png)

![](img/1988321d055ef656859bb92fbc6ac3f5_64.png)

![](img/1988321d055ef656859bb92fbc6ac3f5_66.png)

2.  **定义参数空间**：我们需要指定短期和长期移动平均线的候选窗口值。
    ```python
    # 定义短期均线（SMA1）的参数空间，例如从20到60，步长为4
    short_params = range(20, 61, 4)
    # 定义长期均线（SMA2）的参数空间，例如从180到280，步长为10
    long_params = range(180, 281, 10)
    ```

![](img/1988321d055ef656859bb92fbc6ac3f5_68.png)

3.  **遍历所有组合**：使用 `product` 函数生成所有 `(短期窗口, 长期窗口)` 的组合，并进行循环计算。
    ```python
    for sma1, sma2 in product(short_params, long_params):
        # 在此循环内，使用当前的 sma1 和 sma2 参数执行策略
        print(f"正在测试 SMA1={sma1}, SMA2={sma2}")
    ```

![](img/1988321d055ef656859bb92fbc6ac3f5_70.png)

接下来，我们需要在循环体内，为每一组参数重新计算策略并评估其表现。以下是循环体内的核心计算流程：

![](img/1988321d055ef656859bb92fbc6ac3f5_72.png)

![](img/1988321d055ef656859bb92fbc6ac3f5_74.png)

![](img/1988321d055ef656859bb92fbc6ac3f5_76.png)

![](img/1988321d055ef656859bb92fbc6ac3f5_78.png)

1.  **重新加载并准备数据**：确保每次循环都使用干净、原始的数据。
    ```python
    # 假设 data 是原始的股价DataFrame，例如苹果股票数据
    data = pd.read_csv(‘apple_stock.csv‘, index_col=‘Date‘, parse_dates=True)
    # 清理缺失值
    data_clean = data.dropna()
    ```

![](img/1988321d055ef656859bb92fbc6ac3f5_80.png)

![](img/1988321d055ef656859bb92fbc6ac3f5_82.png)

![](img/1988321d055ef656859bb92fbc6ac3f5_84.png)

![](img/1988321d055ef656859bb92fbc6ac3f5_86.png)

![](img/1988321d055ef656859bb92fbc6ac3f5_88.png)

2.  **计算指标**：基于当前的 `sma1` 和 `sma2` 参数，计算收益率、短期均线和长期均线。
    ```python
    # 计算日收益率
    returns = data_clean[‘Close‘].pct_change()
    # 计算短期移动平均线
    sma1_series = data_clean[‘Close‘].rolling(window=sma1).mean()
    # 计算长期移动平均线
    sma2_series = data_clean[‘Close‘].rolling(window=sma2).mean()
    ```

![](img/1988321d055ef656859bb92fbc6ac3f5_90.png)

![](img/1988321d055ef656859bb92fbc6ac3f5_92.png)

![](img/1988321d055ef656859bb92fbc6ac3f5_94.png)

3.  **生成交易信号**：根据双均线交叉规则生成持仓信号。
    ```python
    # 当短期均线上穿长期均线时，做多（信号为1）；否则空仓（信号为0）
    data_clean[‘position‘] = np.where(sma1_series > sma2_series, 1, 0)
    ```

![](img/1988321d055ef656859bb92fbc6ac3f5_96.png)

![](img/1988321d055ef656859bb92fbc6ac3f5_98.png)

![](img/1988321d055ef656859bb92fbc6ac3f5_100.png)

![](img/1988321d055ef656859bb92fbc6ac3f5_102.png)

![](img/1988321d055ef656859bb92fbc6ac3f5_104.png)

4.  **计算策略收益**：根据持仓信号计算策略的每日收益，并与基准（简单持有）收益对比。
    ```python
    # 策略收益 = 持仓信号 * 日收益率（次日执行，这里做了移位）
    data_clean[‘strategy‘] = data_clean[‘position‘].shift(1) * returns
    # 清理计算产生的缺失值
    data_clean = data_clean.dropna()
    # 计算累积收益
    cum_returns = (1 + returns).cumprod()
    cum_strategy = (1 + data_clean[‘strategy‘]).cumprod()
    # 计算策略相对于基准的超额收益
    outperformance = cum_strategy.iloc[-1] - cum_returns.iloc[-1]
    ```

![](img/1988321d055ef656859bb92fbc6ac3f5_106.png)

![](img/1988321d055ef656859bb92fbc6ac3f5_108.png)

![](img/1988321d055ef656859bb92fbc6ac3f5_110.png)

![](img/1988321d055ef656859bb92fbc6ac3f5_112.png)

![](img/1988321d055ef656859bb92fbc6ac3f5_114.png)

5.  **保存结果**：将每组参数及其对应的策略表现保存下来，以便后续分析。
    ```python
    # 初始化一个列表或DataFrame来保存所有结果
    results = []
    # 在循环内，将结果以字典形式追加
    results.append({
        ‘SMA1‘: sma1,
        ‘SMA2‘: sma2,
        ‘Benchmark_Return‘: cum_returns.iloc[-1],
        ‘Strategy_Return‘: cum_strategy.iloc[-1],
        ‘Outperformance‘: outperformance
    })
    # 循环结束后，将结果列表转换为DataFrame
    results_df = pd.DataFrame(results)
    ```

![](img/1988321d055ef656859bb92fbc6ac3f5_116.png)

运行完所有参数组合后，我们可以查看 `results_df`。通过分析`Outperformance`（超额收益）等列，可以找出哪些参数组合在本段历史数据上表现更优。例如，有些参数组合可能产生负的超额收益，意味着策略还不如简单持有股票；而表现好的组合则能带来正向提升。

![](img/1988321d055ef656859bb92fbc6ac3f5_118.png)

![](img/1988321d055ef656859bb92fbc6ac3f5_120.png)

![](img/1988321d055ef656859bb92fbc6ac3f5_122.png)

---

![](img/1988321d055ef656859bb92fbc6ac3f5_124.png)

![](img/1988321d055ef656859bb92fbc6ac3f5_126.png)

![](img/1988321d055ef656859bb92fbc6ac3f5_128.png)

![](img/1988321d055ef656859bb92fbc6ac3f5_130.png)

![](img/1988321d055ef656859bb92fbc6ac3f5_132.png)

本节课中我们一起学习了如何对双均线策略进行参数调优。我们利用 `itertools.product` 遍历了不同的均线窗口组合，并为每一组参数完整执行了策略回测，最终通过对比超额收益来评估参数优劣。这个过程是量化交易中优化策略性能的基础步骤。