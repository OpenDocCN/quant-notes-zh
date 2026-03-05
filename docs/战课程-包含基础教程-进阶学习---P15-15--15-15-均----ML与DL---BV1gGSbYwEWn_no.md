# 量化交易教程：P15：均线调参实例 📈

![](img/9a61cc31869951f298a95b625c2d4fdc_0.png)

![](img/9a61cc31869951f298a95b625c2d4fdc_2.png)

![](img/9a61cc31869951f298a95b625c2d4fdc_4.png)

![](img/9a61cc31869951f298a95b625c2d4fdc_6.png)

![](img/9a61cc31869951f298a95b625c2d4fdc_8.png)

![](img/9a61cc31869951f298a95b625c2d4fdc_10.png)

![](img/9a61cc31869951f298a95b625c2d4fdc_12.png)

![](img/9a61cc31869951f298a95b625c2d4fdc_14.png)

在本节课中，我们将学习如何对双均线交易策略进行参数调优。我们将通过遍历不同的短期和长期移动平均线参数组合，来评估哪种组合能带来更好的策略表现。

![](img/9a61cc31869951f298a95b625c2d4fdc_16.png)

![](img/9a61cc31869951f298a95b625c2d4fdc_18.png)

![](img/9a61cc31869951f298a95b625c2d4fdc_20.png)

![](img/9a61cc31869951f298a95b625c2d4fdc_22.png)

![](img/9a61cc31869951f298a95b625c2d4fdc_24.png)

![](img/9a61cc31869951f298a95b625c2d4fdc_26.png)

![](img/9a61cc31869951f298a95b625c2d4fdc_28.png)

![](img/9a61cc31869951f298a95b625c2d4fdc_30.png)

![](img/9a61cc31869951f298a95b625c2d4fdc_32.png)

---

![](img/9a61cc31869951f298a95b625c2d4fdc_34.png)

![](img/9a61cc31869951f298a95b625c2d4fdc_36.png)

![](img/9a61cc31869951f298a95b625c2d4fdc_38.png)

![](img/9a61cc31869951f298a95b625c2d4fdc_40.png)

![](img/9a61cc31869951f298a95b625c2d4fdc_42.png)

![](img/9a61cc31869951f298a95b625c2d4fdc_44.png)

![](img/9a61cc31869951f298a95b625c2d4fdc_46.png)

上一节我们介绍了双均线策略的回测方法。本节中，我们来看看如何通过调整策略参数来优化其表现。策略的最终收益与短期和长期移动平均线的窗口大小密切相关。因此，我们需要系统地测试不同的参数组合。

![](img/9a61cc31869951f298a95b625c2d4fdc_48.png)

![](img/9a61cc31869951f298a95b625c2d4fdc_50.png)

![](img/9a61cc31869951f298a95b625c2d4fdc_52.png)

![](img/9a61cc31869951f298a95b625c2d4fdc_54.png)

![](img/9a61cc31869951f298a95b625c2d4fdc_56.png)

![](img/9a61cc31869951f298a95b625c2d4fdc_58.png)

![](img/9a61cc31869951f298a95b625c2d4fdc_60.png)

![](img/9a61cc31869951f298a95b625c2d4fdc_62.png)

以下是实现参数调优的步骤：

![](img/9a61cc31869951f298a95b625c2d4fdc_64.png)

![](img/9a61cc31869951f298a95b625c2d4fdc_66.png)

1.  **导入必要工具**：首先，我们需要导入用于生成参数组合的工具。
    ```python
    from itertools import product
    ```
    `product` 函数可以帮助我们生成两个参数空间的所有可能组合。

![](img/9a61cc31869951f298a95b625c2d4fdc_68.png)

![](img/9a61cc31869951f298a95b625c2d4fdc_70.png)

![](img/9a61cc31869951f298a95b625c2d4fdc_72.png)

![](img/9a61cc31869951f298a95b625c2d4fdc_74.png)

![](img/9a61cc31869951f298a95b625c2d4fdc_76.png)

![](img/9a61cc31869951f298a95b625c2d4fdc_78.png)

2.  **定义参数空间**：接下来，我们定义短期均线（SMA1）和长期均线（SMA2）的参数取值范围。
    ```python
    sma1_range = range(20, 61, 4)   # 短期均线参数：20, 24, 28, ..., 60
    sma2_range = range(180, 281, 10) # 长期均线参数：180, 190, 200, ..., 280
    ```

![](img/9a61cc31869951f298a95b625c2d4fdc_80.png)

![](img/9a61cc31869951f298a95b625c2d4fdc_82.png)

![](img/9a61cc31869951f298a95b625c2d4fdc_84.png)

![](img/9a61cc31869951f298a95b625c2d4fdc_86.png)

![](img/9a61cc31869951f298a95b625c2d4fdc_88.png)

![](img/9a61cc31869951f298a95b625c2d4fdc_90.png)

![](img/9a61cc31869951f298a95b625c2d4fdc_92.png)

3.  **遍历参数组合**：使用 `product` 函数遍历所有参数组合，并对每一组参数执行完整的策略回测流程。
    ```python
    results = pd.DataFrame() # 创建一个空的DataFrame来存储结果

    for sma1, sma2 in product(sma1_range, sma2_range):
        # 1. 重新加载并预处理数据
        data = pd.DataFrame(raw_data['AAPL.Close']) # 假设raw_data是原始数据
        data = data.dropna()

        # 2. 计算收益率和移动平均线
        data['returns'] = np.log(data['AAPL.Close'] / data['AAPL.Close'].shift(1))
        data['SMA1'] = data['AAPL.Close'].rolling(window=sma1).mean()
        data['SMA2'] = data['AAPL.Close'].rolling(window=sma2).mean()
        data = data.dropna()

        # 3. 生成交易信号
        data['position'] = np.where(data['SMA1'] > data['SMA2'], 1, -1)

        # 4. 计算策略收益
        data['strategy'] = data['position'].shift(1) * data['returns']
        data = data.dropna()

        # 5. 汇总本次回测结果
        result_table = pd.DataFrame({
            'SMA1': [sma1],
            'SMA2': [sma2],
            'Market Returns': [data['returns'].cumsum().iloc[-1]],
            'Strategy Returns': [data['strategy'].cumsum().iloc[-1]],
            'Outperformance': [data['strategy'].cumsum().iloc[-1] - data['returns'].cumsum().iloc[-1]]
        }, index=[0])

        # 6. 将本次结果添加到总表中
        results = pd.concat([results, result_table], ignore_index=True)
    ```

![](img/9a61cc31869951f298a95b625c2d4fdc_94.png)

![](img/9a61cc31869951f298a95b625c2d4fdc_96.png)

![](img/9a61cc31869951f298a95b625c2d4fdc_98.png)

![](img/9a61cc31869951f298a95b625c2d4fdc_100.png)

![](img/9a61cc31869951f298a95b625c2d4fdc_102.png)

![](img/9a61cc31869951f298a95b625c2d4fdc_104.png)

![](img/9a61cc31869951f298a95b625c2d4fdc_106.png)

![](img/9a61cc31869951f298a95b625c2d4fdc_108.png)

4.  **分析结果**：执行完循环后，`results` DataFrame 中将包含所有参数组合的回测结果。我们可以通过排序等方式找出表现最佳的参数组合。
    ```python
    # 按策略超额收益（Outperformance）降序排列
    best_results = results.sort_values('Outperformance', ascending=False)
    print(best_results.head(10))
    ```
    从结果中可以看到，有些参数组合下策略表现优于单纯持有（市场收益），而有些组合则可能表现更差。这说明了参数选择对策略有效性的关键影响。

![](img/9a61cc31869951f298a95b625c2d4fdc_110.png)

![](img/9a61cc31869951f298a95b625c2d4fdc_112.png)

![](img/9a61cc31869951f298a95b625c2d4fdc_114.png)

![](img/9a61cc31869951f298a95b625c2d4fdc_116.png)

---

![](img/9a61cc31869951f298a95b625c2d4fdc_118.png)

![](img/9a61cc31869951f298a95b625c2d4fdc_120.png)

![](img/9a61cc31869951f298a95b625c2d4fdc_122.png)

![](img/9a61cc31869951f298a95b625c2d4fdc_124.png)

![](img/9a61cc31869951f298a95b625c2d4fdc_126.png)

![](img/9a61cc31869951f298a95b625c2d4fdc_128.png)

![](img/9a61cc31869951f298a95b625c2d4fdc_130.png)

本节课中我们一起学习了如何对双均线策略进行参数调优。我们利用 `itertools.product` 函数遍历了不同的短期和长期移动平均线窗口组合，并通过回测计算了每种组合的策略表现。这个过程清晰地展示了在量化交易中，通过系统性的参数测试来寻找较优策略配置的方法。