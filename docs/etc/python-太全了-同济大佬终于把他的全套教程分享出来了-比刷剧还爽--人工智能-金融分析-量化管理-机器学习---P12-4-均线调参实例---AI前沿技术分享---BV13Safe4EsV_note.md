# 量化投资基础：P12：4-均线调参实例 📈

![](img/d7edf5a36ea232b97b165b18accbca0b_0.png)

![](img/d7edf5a36ea232b97b165b18accbca0b_2.png)

![](img/d7edf5a36ea232b97b165b18accbca0b_4.png)

在本节课中，我们将学习如何通过调整移动平均线的参数来优化双均线交易策略。我们将使用Python的`itertools`工具包来遍历不同的参数组合，并评估每种组合的表现，从而找到更优的策略参数。

---

![](img/d7edf5a36ea232b97b165b18accbca0b_6.png)

上一节我们介绍了双均线策略的基本原理和实现。本节中，我们来看看如何通过调整短期和长期移动平均线的参数来优化策略效果。

![](img/d7edf5a36ea232b97b165b18accbca0b_8.png)

![](img/d7edf5a36ea232b97b165b18accbca0b_10.png)

策略的最终收益与许多因素有关，其中短期和长期移动平均线的窗口大小是关键参数。我们需要探索不同的参数组合，例如短期均线是5天合适还是10天合适，长期均线是20天合适还是40天合适。

![](img/d7edf5a36ea232b97b165b18accbca0b_12.png)

![](img/d7edf5a36ea232b97b165b18accbca0b_14.png)

![](img/d7edf5a36ea232b97b165b18accbca0b_16.png)

在Python中，我们可以借助`itertools.product`函数轻松地遍历所有可能的参数组合。

![](img/d7edf5a36ea232b97b165b18accbca0b_18.png)

![](img/d7edf5a36ea232b97b165b18accbca0b_20.png)

![](img/d7edf5a36ea232b97b165b18accbca0b_22.png)

以下是实现参数遍历的步骤：

![](img/d7edf5a36ea232b97b165b18accbca0b_24.png)

![](img/d7edf5a36ea232b97b165b18accbca0b_26.png)

![](img/d7edf5a36ea232b97b165b18accbca0b_28.png)

1.  **导入必要的工具包**：首先，我们需要导入用于生成参数组合的工具。
    ```python
    from itertools import product
    ```

![](img/d7edf5a36ea232b97b165b18accbca0b_30.png)

![](img/d7edf5a36ea232b97b165b18accbca0b_32.png)

![](img/d7edf5a36ea232b97b165b18accbca0b_34.png)

2.  **定义参数空间**：我们需要分别指定短期均线（SMA1）和长期均线（SMA2）的参数取值范围。
    ```python
    # 定义短期均线参数空间，例如从20到60，步长为4
    sma1_range = range(20, 61, 4)
    # 定义长期均线参数空间，例如从180到280，步长为10
    sma2_range = range(180, 281, 10)
    ```

![](img/d7edf5a36ea232b97b165b18accbca0b_36.png)

3.  **遍历所有参数组合**：使用`product`函数生成所有可能的（SMA1， SMA2）组合，并进行遍历计算。
    ```python
    for sma1, sma2 in product(sma1_range, sma2_range):
        # 在此处计算每个参数组合下的策略表现
        print(f"正在计算 SMA1={sma1}, SMA2={sma2}")
    ```
    `product`函数会帮我们计算出所有组合，例如(20,180)、(20,190)、(24,180)、(24,190)等，并逐一进行遍历。

![](img/d7edf5a36ea232b97b165b18accbca0b_38.png)

![](img/d7edf5a36ea232b97b165b18accbca0b_40.png)

![](img/d7edf5a36ea232b97b165b18accbca0b_42.png)

---

![](img/d7edf5a36ea232b97b165b18accbca0b_44.png)

![](img/d7edf5a36ea232b97b165b18accbca0b_46.png)

在遍历循环内部，我们需要为每一组参数重新计算策略并评估其表现。以下是循环内部的核心计算流程：

![](img/d7edf5a36ea232b97b165b18accbca0b_48.png)

![](img/d7edf5a36ea232b97b165b18accbca0b_50.png)

1.  **重新加载并处理数据**：为了确保每次计算独立，我们重新加载股价数据并处理缺失值。
    ```python
    # 假设`data`是包含苹果股价的原始DataFrame
    data = raw_data[['AAPL']].copy()
    data = data.dropna()  # 去除缺失值
    ```

![](img/d7edf5a36ea232b97b165b18accbca0b_52.png)

2.  **计算收益率和移动平均线**：根据当前遍历得到的`sma1`和`sma2`参数，计算收益率、短期均线和长期均线。
    ```python
    data['returns'] = np.log(data['AAPL'] / data['AAPL'].shift(1))
    data['SMA1'] = data['AAPL'].rolling(window=sma1).mean()
    data['SMA2'] = data['AAPL'].rolling(window=sma2).mean()
    data = data.dropna()  # 滚动计算会产生新的缺失值，需要再次去除
    ```

3.  **生成交易信号**：根据双均线策略生成持仓信号（position）。当短期均线上穿长期均线时，做多（设为1）；反之，平仓（设为0）。
    ```python
    data['position'] = np.where(data['SMA1'] > data['SMA2'], 1, 0)
    ```

![](img/d7edf5a36ea232b97b165b18accbca0b_54.png)

4.  **计算策略收益**：根据持仓信号计算策略的每日收益，并累积得到策略总收益。同时，计算基准（什么都不做，即一直持有）的收益。
    ```python
    data['strategy'] = data['position'].shift(1) * data['returns']
    # 计算策略累积收益和基准累积收益
    strategy_return = data['strategy'].sum()
    baseline_return = data['returns'].sum()
    # 计算策略相对于基准的超额收益
    outperformance = strategy_return - baseline_return
    ```

![](img/d7edf5a36ea232b97b165b18accbca0b_56.png)

![](img/d7edf5a36ea232b97b165b18accbca0b_58.png)

---

![](img/d7edf5a36ea232b97b165b18accbca0b_60.png)

![](img/d7edf5a36ea232b97b165b18accbca0b_62.png)

![](img/d7edf5a36ea232b97b165b18accbca0b_64.png)

为了系统地比较所有参数组合的结果，我们需要将每次遍历的结果保存下来。以下是结果收集的方法：

![](img/d7edf5a36ea232b97b165b18accbca0b_66.png)

1.  **初始化结果表**：在遍历开始前，创建一个空的DataFrame来存储所有结果。
    ```python
    import pandas as pd
    results = pd.DataFrame()
    ```

![](img/d7edf5a36ea232b97b165b18accbca0b_68.png)

![](img/d7edf5a36ea232b97b165b18accbca0b_70.png)

![](img/d7edf5a36ea232b97b165b18accbca0b_72.png)

2.  **在循环中收集结果**：每次计算完成后，将当前参数组合及其对应的收益结果整理成一行数据，并添加到结果表中。
    ```python
    for sma1, sma2 in product(sma1_range, sma2_range):
        # ... (上述计算流程)
        # 将结果组织成字典
        result_row = pd.DataFrame({
            'SMA1': [sma1],
            'SMA2': [sma2],
            'Baseline_Return': [baseline_return],
            'Strategy_Return': [strategy_return],
            'Outperformance': [outperformance]
        })
        # 将当前结果行追加到总结果表中，忽略其自身的索引
        results = pd.concat([results, result_row], ignore_index=True)
    ```

![](img/d7edf5a36ea232b97b165b18accbca0b_74.png)

![](img/d7edf5a36ea232b97b165b18accbca0b_76.png)

![](img/d7edf5a36ea232b97b165b18accbca0b_78.png)

3.  **查看与分析结果**：计算完成后，我们可以查看结果表，并根据`Outperformance`（超额收益）等指标对结果进行排序，从而找出表现最优的参数组合。
    ```python
    # 查看结果的前几行
    print(results.head(10))
    # 按超额收益降序排列，找到最佳参数
    best_params = results.sort_values('Outperformance', ascending=False).iloc[0]
    print(f"最佳参数组合: SMA1={best_params['SMA1']}, SMA2={best_params['SMA2']}")
    ```

![](img/d7edf5a36ea232b97b165b18accbca0b_80.png)

![](img/d7edf5a36ea232b97b165b18accbca0b_82.png)

![](img/d7edf5a36ea232b97b165b18accbca0b_84.png)

---

![](img/d7edf5a36ea232b97b165b18accbca0b_86.png)

在代码实现过程中，可能会遇到一些错误。例如，从DataFrame中单独取出一列数据后，它可能变成一个Series而丢失列名，导致后续操作报错。此时，需要用`pd.DataFrame()`将其重新包装成DataFrame。

![](img/d7edf5a36ea232b97b165b18accbca0b_88.png)

另一个常见错误是在构建DataFrame时未正确指定索引。`pd.DataFrame()`要求提供`index`参数，我们可以传入一个简单的列表（如`[0]`），然后在用`pd.concat`合并时使用`ignore_index=True`来重置索引，保持结果表的整洁。

![](img/d7edf5a36ea232b97b165b18accbca0b_90.png)

![](img/d7edf5a36ea232b97b165b18accbca0b_92.png)

通过观察结果表可以发现，并非所有参数组合都能带来正收益。有些参数组合下，策略收益甚至不如基准（什么都不做）。这正说明了参数优化的重要性。大部分情况下，经过优化的参数组合能带来比基准更好的收益。

![](img/d7edf5a36ea232b97b165b18accbca0b_94.png)

![](img/d7edf5a36ea232b97b165b18accbca0b_96.png)

---

![](img/d7edf5a36ea232b97b165b18accbca0b_98.png)

![](img/d7edf5a36ea232b97b165b18accbca0b_100.png)

![](img/d7edf5a36ea232b97b165b18accbca0b_102.png)

本节课中我们一起学习了如何对双均线策略进行参数优化。我们使用`itertools.product`遍历了不同的短期和长期移动平均线窗口组合，为每一组参数重新计算策略收益，并通过对比策略收益与基准收益，评估了参数的有效性。这个过程是量化策略开发中至关重要的一步，它帮助我们理解策略表现对参数的敏感性，并找到更稳健、更有效的参数设置。