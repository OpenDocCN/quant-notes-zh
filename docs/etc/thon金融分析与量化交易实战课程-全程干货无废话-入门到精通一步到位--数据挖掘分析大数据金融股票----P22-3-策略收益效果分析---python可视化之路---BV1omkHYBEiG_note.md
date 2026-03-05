# 金融数据分析：P22：3-策略收益效果分析 📈

![](img/04616268e5cd4c8b5b2c0f97aeaee4ef_1.png)

![](img/04616268e5cd4c8b5b2c0f97aeaee4ef_3.png)

在本节课中，我们将学习如何计算和比较量化交易策略的收益。我们将通过一个具体的例子，分析使用双均线策略与不采取任何策略（即简单持有）两种情况下，投资回报的差异。核心在于理解如何从股价数据计算日收益率，并应用策略信号来调整这些收益率，最终通过累加和对数还原得到总收益。

## 计算基础收益率

上一节我们介绍了如何生成交易信号，本节中我们来看看如何基于这些信号计算策略收益。首先，我们需要计算股票的基础日收益率。

![](img/04616268e5cd4c8b5b2c0f97aeaee4ef_5.png)

![](img/04616268e5cd4c8b5b2c0f97aeaee4ef_7.png)

以下是计算日收益率的步骤：
1.  获取股价数据。
2.  计算每日的股价变化率，即当日收盘价除以前一日收盘价。
3.  在Python的pandas库中，可以使用`.shift(1)`方法获取前一日的数据。

![](img/04616268e5cd4c8b5b2c0f97aeaee4ef_9.png)

对应的核心公式和代码如下：
*   **收益率公式**：`return_t = price_t / price_{t-1}`
*   **代码实现**：
    ```python
    # 假设‘data’是一个包含股价‘Close’列的DataFrame
    data[‘returns’] = data[‘Close’] / data[‘Close’].shift(1)
    ```
执行这段代码后，我们得到了一个名为`returns`的列，它代表了股价每日的增长倍数。

![](img/04616268e5cd4c8b5b2c0f97aeaee4ef_11.png)

![](img/04616268e5cd4c8b5b2c0f97aeaee4ef_13.png)

## 应用策略计算策略收益率

![](img/04616268e5cd4c8b5b2c0f97aeaee4ef_15.png)

![](img/04616268e5cd4c8b5b2c0f97aeaee4ef_17.png)

在计算出基础收益率后，我们需要根据之前生成的交易信号来调整收益。我们的策略逻辑是：在特定的市场区间（由`position`信号为-1标识），我们采取与市场走势相反的操作。

![](img/04616268e5cd4c8b5b2c0f97aeaee4ef_19.png)

以下是应用策略计算收益的步骤：
1.  回顾`position`列：其值为1代表跟随市场（持有），为-1代表反向市场（例如，做空或卖出）。
2.  将`position`信号（同样需要做`.shift(1)`处理以对齐日期）与基础收益率`returns`相乘。
3.  这样，在`position`为-1的区间，收益率会被反转，实现策略意图。

![](img/04616268e5cd4c8b5b2c0f97aeaee4ef_21.png)

对应的核心公式和代码如下：
*   **策略收益率公式**：`strategy_returns_t = position_{t-1} * returns_t`
*   **代码实现**：
    ```python
    # 假设‘position’列已存在于data中
    data[‘strategy’] = data[‘position’].shift(1) * data[‘returns’]
    ```
完成这一步后，我们得到了两列收益率数据：`returns`（原始市场收益）和`strategy`（策略调整后的收益）。

![](img/04616268e5cd4c8b5b2c0f97aeaee4ef_23.png)

![](img/04616268e5cd4c8b5b2c0f97aeaee4ef_25.png)

![](img/04616268e5cd4c8b5b2c0f97aeaee4ef_27.png)

## 计算并比较总收益

![](img/04616268e5cd4c8b5b2c0f97aeaee4ef_29.png)

现在，我们有了每日的收益率序列。为了比较长期的投资效果，我们需要将这些每日的收益累积起来，计算出从期初投入1元钱到期末的总价值。

![](img/04616268e5cd4c8b5b2c0f97aeaee4ef_31.png)

以下是计算和比较总收益的步骤：
1.  **处理缺失值**：由于使用了`.shift()`操作，数据首行会产生缺失值（NaN），需要使用`.dropna()`方法将其清除。
2.  **累积收益**：将每日的收益率连乘，即可得到总收益倍数。在数学上，连乘可以通过对对数收益率求和再取指数来实现，但这里我们直接对调整后的收益率序列进行累乘。
3.  **执行计算**：对清理后的`returns`列和`strategy`列分别进行累乘操作。

![](img/04616268e5cd4c8b5b2c0f97aeaee4ef_33.png)

![](img/04616268e5cd4c8b5b2c0f97aeaee4ef_35.png)

对应的核心公式和代码如下：
*   **总收益公式**：`total_return = (returns * strategy_returns).prod()`
*   **代码实现**：
    ```python
    # 删除含有缺失值的行
    data = data.dropna()
    
    # 计算总收益：从1开始，连乘每日的收益倍数
    total_return_buy_hold = data[‘returns’].prod()
    total_return_strategy = data[‘strategy’].prod()
    
    print(f“简单持有总收益：{total_return_buy_hold:.2f}“)
    print(f“双均线策略总收益：{total_return_strategy:.2f}“)
    ```
通过比较这两个数值，我们可以直观地看到策略是否有效。例如，如果简单持有收益为4.0，而策略收益为5.8，则表明该策略在此期间带来了额外的超额收益。

![](img/04616268e5cd4c8b5b2c0f97aeaee4ef_37.png)

## 总结

![](img/04616268e5cd4c8b5b2c0f97aeaee4ef_39.png)

本节课中我们一起学习了量化策略收益分析的全过程。我们首先从股价计算出基础的日收益率，然后利用预先定义好的交易信号（`position`）对收益率进行调整，得到策略收益率序列。最后，通过累乘计算总收益，并对比了“简单持有”和“使用双均线策略”两种方案的投资结果。这个过程是量化交易中评估策略有效性的基础环节。