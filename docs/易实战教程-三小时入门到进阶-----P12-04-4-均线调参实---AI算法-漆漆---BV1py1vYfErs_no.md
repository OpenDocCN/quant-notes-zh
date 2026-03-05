# Python金融分析与量化交易实战：P12：04-4-均线调参实例 📈

![](img/c5f0cc0d3e959bb864437f5a1b3dd449_0.png)

![](img/c5f0cc0d3e959bb864437f5a1b3dd449_2.png)

![](img/c5f0cc0d3e959bb864437f5a1b3dd449_4.png)

在本节课中，我们将学习如何对双均线交易策略进行参数调优。我们将通过遍历不同的短期和长期均线参数组合，来寻找能带来最佳收益的参数设置。

![](img/c5f0cc0d3e959bb864437f5a1b3dd449_6.png)

---

![](img/c5f0cc0d3e959bb864437f5a1b3dd449_8.png)

![](img/c5f0cc0d3e959bb864437f5a1b3dd449_10.png)

![](img/c5f0cc0d3e959bb864437f5a1b3dd449_12.png)

上一节我们介绍了双均线策略的基本实现和回测方法。本节中，我们来看看如何通过调整策略参数来优化其表现。

![](img/c5f0cc0d3e959bb864437f5a1b3dd449_14.png)

![](img/c5f0cc0d3e959bb864437f5a1b3dd449_16.png)

![](img/c5f0cc0d3e959bb864437f5a1b3dd449_18.png)

![](img/c5f0cc0d3e959bb864437f5a1b3dd449_20.png)

![](img/c5f0cc0d3e959bb864437f5a1b3dd449_22.png)

策略的最终收益与许多因素有关，其中短期均线（SMA1）和长期均线（SMA2）的窗口长度是关键参数。我们需要探索不同的参数组合，例如短期窗口是5天合适还是10天合适，长期窗口是20天合适还是40天合适。

![](img/c5f0cc0d3e959bb864437f5a1b3dd449_24.png)

![](img/c5f0cc0d3e959bb864437f5a1b3dd449_26.png)

![](img/c5f0cc0d3e959bb864437f5a1b3dd449_28.png)

![](img/c5f0cc0d3e959bb864437f5a1b3dd449_30.png)

以下是实现参数遍历调优的步骤：

![](img/c5f0cc0d3e959bb864437f5a1b3dd449_32.png)

![](img/c5f0cc0d3e959bb864437f5a1b3dd449_34.png)

![](img/c5f0cc0d3e959bb864437f5a1b3dd449_36.png)

1.  **导入必要工具**：我们将使用 `itertools` 库中的 `product` 函数来生成所有可能的参数组合。
    ```python
    from itertools import product
    ```

![](img/c5f0cc0d3e959bb864437f5a1b3dd449_38.png)

![](img/c5f0cc0d3e959bb864437f5a1b3dd449_40.png)

![](img/c5f0cc0d3e959bb864437f5a1b3dd449_42.png)

2.  **定义参数空间**：为短期均线和长期均线分别指定一个可能的取值范围。
    ```python
    # 短期均线参数空间：从20到60，步长为4
    sma1_range = range(20, 61, 4)
    # 长期均线参数空间：从180到280，步长为10
    sma2_range = range(180, 281, 10)
    ```

![](img/c5f0cc0d3e959bb864437f5a1b3dd449_44.png)

![](img/c5f0cc0d3e959bb864437f5a1b3dd449_46.png)

![](img/c5f0cc0d3e959bb864437f5a1b3dd449_48.png)

3.  **遍历参数组合**：使用 `product` 函数遍历所有 `(sma1, sma2)` 的组合。
    ```python
    for sma1, sma2 in product(sma1_range, sma2_range):
        # 在此循环内对每一组参数执行策略回测
    ```

![](img/c5f0cc0d3e959bb864437f5a1b3dd449_50.png)

在循环内部，我们需要为每一组参数重新计算策略并评估其表现。以下是循环内的核心操作：

![](img/c5f0cc0d3e959bb864437f5a1b3dd449_52.png)

1.  **重新加载并预处理数据**：确保每次循环都从干净的数据开始。
    ```python
    # 假设 `data` 是包含苹果股价的原始DataFrame
    price = data[‘AAPL’].copy()
    price = price.dropna()  # 去除缺失值
    ```

![](img/c5f0cc0d3e959bb864437f5a1b3dd449_54.png)

![](img/c5f0cc0d3e959bb864437f5a1b3dd449_56.png)

![](img/c5f0cc0d3e959bb864437f5a1b3dd449_58.png)

2.  **计算收益率和均线**：基于当前参数 `sma1` 和 `sma2` 进行计算。
    ```python
    returns = price.pct_change()  # 计算日收益率
    sma1_values = price.rolling(window=sma1).mean()  # 计算短期均线
    sma2_values = price.rolling(window=sma2).mean()  # 计算长期均线
    # 去除均线计算产生的缺失值
    sma1_values = sma1_values.dropna()
    sma2_values = sma2_values.dropna()
    ```

![](img/c5f0cc0d3e959bb864437f5a1b3dd449_60.png)

![](img/c5f0cc0d3e959bb864437f5a1b3dd449_62.png)

![](img/c5f0cc0d3e959bb864437f5a1b3dd449_64.png)

3.  **生成交易信号**：根据双均线交叉规则生成持仓信号。
    ```python
    # 当短期均线上穿长期均线时，做多（信号为1）
    # 当短期均线下穿长期均线时，平仓（信号为0）
    position = pd.Series(0, index=sma1_values.index)
    position[sma1_values > sma2_values] = 1
    position[sma2_values > sma1_values] = 0
    ```

![](img/c5f0cc0d3e959bb864437f5a1b3dd449_66.png)

![](img/c5f0cc0d3e959bb864437f5a1b3dd449_68.png)

![](img/c5f0cc0d3e959bb864437f5a1b3dd449_70.png)

4.  **计算策略收益**：将交易信号与收益率结合，计算策略的累计收益。
    ```python
    strategy_returns = position.shift(1) * returns  # 策略日收益率
    # 计算累计收益
    cum_returns = (1 + returns).cumprod()
    cum_strategy_returns = (1 + strategy_returns).cumprod()
    ```

![](img/c5f0cc0d3e959bb864437f5a1b3dd449_72.png)

![](img/c5f0cc0d3e959bb864437f5a1b3dd449_74.png)

![](img/c5f0cc0d3e959bb864437f5a1b3dd449_76.png)

![](img/c5f0cc0d3e959bb864437f5a1b3dd449_78.png)

5.  **记录结果**：将当前参数组合及其对应的策略表现保存下来。
    ```python
    # 计算策略相对于简单买入持有的超额收益
    outperformance = cum_strategy_returns.iloc[-1] - cum_returns.iloc[-1]
    
    # 将结果存入一个临时的字典
    result = {
        ‘SMA1’: sma1,
        ‘SMA2’: sma2,
        ‘Buy&Hold_Return’: cum_returns.iloc[-1],
        ‘Strategy_Return’: cum_strategy_returns.iloc[-1],
        ‘Outperformance’: outperformance
    }
    # 将结果追加到总的结果DataFrame中
    results = results.append(result, ignore_index=True)
    ```

![](img/c5f0cc0d3e959bb864437f5a1b3dd449_80.png)

![](img/c5f0cc0d3e959bb864437f5a1b3dd449_82.png)

![](img/c5f0cc0d3e959bb864437f5a1b3dd449_84.png)

完成所有参数组合的遍历后，我们可以查看 `results` 这个DataFrame。通过排序或可视化，可以轻松找出在该段历史数据上表现最佳的参数组合。例如，我们可以按 `Outperformance`（超额收益）降序排列：

![](img/c5f0cc0d3e959bb864437f5a1b3dd449_86.png)

![](img/c5f0cc0d3e959bb864437f5a1b3dd449_88.png)

```python
best_params = results.sort_values(by=‘Outperformance’, ascending=False).head()
print(best_params)
```

![](img/c5f0cc0d3e959bb864437f5a1b3dd449_90.png)

![](img/c5f0cc0d3e959bb864437f5a1b3dd449_92.png)

---

![](img/c5f0cc0d3e959bb864437f5a1b3dd449_94.png)

![](img/c5f0cc0d3e959bb864437f5a1b3dd449_96.png)

![](img/c5f0cc0d3e959bb864437f5a1b3dd449_98.png)

![](img/c5f0cc0d3e959bb864437f5a1b3dd449_100.png)

![](img/c5f0cc0d3e959bb864437f5a1b3dd449_102.png)

本节课中我们一起学习了如何对双均线交易策略进行参数调优。我们通过定义参数空间、遍历所有组合、分别回测并记录结果，最终找到了在历史数据上表现更优的参数设置。这个过程是量化策略开发中优化和验证的关键步骤。