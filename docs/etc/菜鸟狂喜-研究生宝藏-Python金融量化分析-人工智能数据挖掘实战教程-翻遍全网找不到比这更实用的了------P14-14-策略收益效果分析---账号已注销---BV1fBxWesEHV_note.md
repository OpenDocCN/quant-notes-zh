# Python金融量化分析：P14：策略收益效果分析 📈

在本节课中，我们将学习如何计算和比较交易策略的收益。我们将通过一个具体的例子，分析“双均线策略”相对于“什么都不做”的原始投资，其收益效果如何。核心在于理解如何计算每日收益率，并应用策略信号来调整这些收益率，最终通过累加和对数还原得到总收益。

![](img/7b93b7e3eda0cfc16617f1c9ad700b8d_1.png)

![](img/7b93b7e3eda0cfc16617f1c9ad700b8d_3.png)

## 计算原始收益率

上一节我们介绍了如何获取和处理股价数据，本节中我们来看看如何计算每日的收益率。收益率是衡量投资表现的基础，它表示资产价格每日的相对变化。

计算每日收益率（报酬率）的公式是：`当日收盘价 / 前一日收盘价`。在代码中，我们使用 `shift(1)` 函数来获取前一日的数据。

```python
# 假设 data[‘Close’] 是包含每日收盘价的序列
returns = data[‘Close’] / data[‘Close’].shift(1)
```

![](img/7b93b7e3eda0cfc16617f1c9ad700b8d_5.png)

![](img/7b93b7e3eda0cfc16617f1c9ad700b8d_7.png)

执行这段代码后，`returns` 序列就包含了每日的增长率。序列的第一个值会是 `NaN`（缺失值），因为第一天没有前一日的数据可供比较。

![](img/7b93b7e3eda0cfc16617f1c9ad700b8d_9.png)

## 应用策略信号

我们已经有了市场的原始收益率。接下来，需要将我们之前生成的交易策略信号应用上去。我们的策略信号 `position` 是一个序列，其中的值为 1 或 -1。

![](img/7b93b7e3eda0cfc16617f1c9ad700b8d_11.png)

![](img/7b93b7e3eda0cfc16617f1c9ad700b8d_13.png)

以下是 `position` 信号的含义：
*   **`position = 1`**：表示遵循原始市场走势（即持有或做多）。
*   **`position = -1`**：表示反向操作（即做空），旨在市场下跌时获利。

![](img/7b93b7e3eda0cfc16617f1c9ad700b8d_15.png)

因此，策略的每日收益率应该是原始收益率与策略信号的乘积。

![](img/7b93b7e3eda0cfc16617f1c9ad700b8d_17.png)

```python
# 应用策略信号，同样需要进行 shift(1) 操作以对齐日期
strategy_returns = position.shift(1) * returns
```

这段代码意味着：当信号为 1 时，策略收益等于市场收益；当信号为 -1 时，策略收益等于市场收益的相反数，从而实现“低买高卖”或“高卖低买”的反转逻辑。

![](img/7b93b7e3eda0cfc16617f1c9ad700b8d_19.png)

## 计算并比较总收益

![](img/7b93b7e3eda0cfc16617f1c9ad700b8d_21.png)

现在我们有了两个收益率序列：原始收益率 `returns` 和策略收益率 `strategy_returns`。要比较长期收益，我们需要将它们从每日增长率还原为累计的总收益。

![](img/7b93b7e3eda0cfc16617f1c9ad700b8d_23.png)

![](img/7b93b7e3eda0cfc16617f1c9ad700b8d_25.png)

这个过程分为两步：
1.  **累加对数收益率**：首先对收益率序列求和。由于我们之前计算的是价格比值，对其取对数后求和，在数学上等同于计算连续复合增长率。
2.  **指数还原**：将对数收益率之和通过指数函数 `np.exp()` 还原为最终的总资产倍数。

![](img/7b93b7e3eda0cfc16617f1c9ad700b8d_27.png)

以下是计算并比较总收益的完整代码步骤：

![](img/7b93b7e3eda0cfc16617f1c9ad700b8d_29.png)

```python
# 1. 删除收益率序列中的缺失值（NaN）
returns_clean = returns.dropna()
strategy_returns_clean = strategy_returns.dropna()

# 2. 计算对数收益率之和，并通过指数函数还原
final_return = np.exp(returns_clean.sum())
final_strategy_return = np.exp(strategy_returns_clean.sum())

![](img/7b93b7e3eda0cfc16617f1c9ad700b8d_31.png)

print(f“什么都不做，1元最终变为：{final_return:.2f} 元”)
print(f“使用双均线策略，1元最终变为：{final_strategy_return:.2f} 元”)
```

![](img/7b93b7e3eda0cfc16617f1c9ad700b8d_33.png)

![](img/7b93b7e3eda0cfc16617f1c9ad700b8d_35.png)

## 结果分析

![](img/7b93b7e3eda0cfc16617f1c9ad700b8d_37.png)

执行上述计算后，我们可以得到类似以下的结果：
*   原始投资总收益：例如，1 元变成了 4 元。
*   策略投资总收益：例如，1 元变成了 5.8 元。

这个对比清晰地展示了，在我们构建的这个“双均线策略”下，投资收益得到了显著的提升（从4元提升至5.8元）。这验证了策略在历史数据上的有效性。

![](img/7b93b7e3eda0cfc16617f1c9ad700b8d_39.png)

---

![](img/7b93b7e3eda0cfc16617f1c9ad700b8d_41.png)

![](img/7b93b7e3eda0cfc16617f1c9ad700b8d_43.png)

本节课中我们一起学习了金融量化分析中策略收益评估的全过程。我们首先计算了基础的每日收益率，然后通过乘以策略信号（1或-1）来得到策略调整后的每日收益率。最后，我们通过对收益率序列求和并指数还原，计算出了“买入持有”和“使用策略”两种情况下投资的最终总收益，并通过对比验证了策略的增效作用。这是量化策略回测中评估性能的关键一步。