# Python金融分析与量化交易实战：03-3：连续指标变化情况分析 📈

![](img/eea29a05c6dda28dbadf7f8ca0c90a7b_0.png)

![](img/eea29a05c6dda28dbadf7f8ca0c90a7b_2.png)

在本节课中，我们将学习如何分析金融时间序列的连续变化情况。我们将探讨为什么简单的增长率累加会失效，并引入对数变换来解决这个问题，最终计算出“初始投资相当于未来某一天的价值”这一核心指标。

---

上一节我们介绍了如何计算简单的差异值和增长率。本节中，我们来看看如何分析一个连续时间序列的累积变化情况。

## 为什么增长率不能直接累加？🤔

对于连续时间的增长率，我们能否将它们直接相加，作为总体的增长率呢？答案是否定的。

![](img/eea29a05c6dda28dbadf7f8ca0c90a7b_4.png)

让我们通过一个例子来说明。假设有一只股票：
*   第一天价格：`100`
*   第二天价格：`50`
*   第三天价格：`75`

以下是计算过程：
*   第二天的增长率 = `(50 - 100) / 100 = -0.5`
*   第三天的增长率 = `(75 - 50) / 50 = 0.5`

如果我们将这两个增长率相加：`-0.5 + 0.5 = 0`。结果为零，似乎意味着股票从第一天到第三天既没涨也没跌。

但观察原始数据：第一天`100`，第三天`75`，股价实际上是下跌的。这说明，**增长率不能直接进行加法运算**，因为它无法反映从起点到终点的整体变化。

![](img/eea29a05c6dda28dbadf7f8ca0c90a7b_6.png)

## 解决方案：引入对数变换 📊

![](img/eea29a05c6dda28dbadf7f8ca0c90a7b_8.png)

为了正确计算连续时间的累积变化，我们需要借助数学中的对数公式。

核心思想是：将对数形式的日收益率进行累加，其效果等同于计算最终价格与初始价格的比值。

![](img/eea29a05c6dda28dbadf7f8ca0c90a7b_10.png)

**计算公式如下：**
```
总累积变化 = log(价格_t / 价格_{t-1}) + log(价格_{t+1} / 价格_t) + ...
          = log( (价格_t / 价格_{t-1}) * (价格_{t+1} / 价格_t) * ... )
          = log(最终价格 / 初始价格)
```

这个公式的意义在于，无论中间过程如何涨跌，将所有对数收益率累加后，得到的结果直接反映了**初始价格（如1元钱）相当于最终值多少钱**。

## 在Python中实现连续变化分析 💻

![](img/eea29a05c6dda28dbadf7f8ca0c90a7b_12.png)

![](img/eea29a05c6dda28dbadf7f8ca0c90a7b_14.png)

接下来，我们使用Pandas和NumPy库来实现上述分析。

![](img/eea29a05c6dda28dbadf7f8ca0c90a7b_16.png)

![](img/eea29a05c6dda28dbadf7f8ca0c90a7b_18.png)

### 步骤1：计算对数收益率

![](img/eea29a05c6dda28dbadf7f8ca0c90a7b_20.png)

首先，我们需要计算每一天相对于前一天的对数收益率。

![](img/eea29a05c6dda28dbadf7f8ca0c90a7b_22.png)

![](img/eea29a05c6dda28dbadf7f8ca0c90a7b_24.png)

```python
import numpy as np
import pandas as pd

![](img/eea29a05c6dda28dbadf7f8ca0c90a7b_26.png)

![](img/eea29a05c6dda28dbadf7f8ca0c90a7b_28.png)

# 假设 `data` 是一个包含股价的Pandas Series或DataFrame
# 使用 shift(1) 将数据向上移动一格，得到“前一天”的价格
previous_day_price = data.shift(1)

![](img/eea29a05c6dda28dbadf7f8ca0c90a7b_30.png)

# 计算对数收益率：log(当天价格 / 前一天价格)
log_returns = np.log(data / previous_day_price)

# 查看结果
print(log_returns.head())
```

![](img/eea29a05c6dda28dbadf7f8ca0c90a7b_32.png)

![](img/eea29a05c6dda28dbadf7f8ca0c90a7b_34.png)

### 步骤2：计算累积对数收益率

![](img/eea29a05c6dda28dbadf7f8ca0c90a7b_36.png)

得到每日的对数收益率后，我们需要对其进行累加，得到从起始日到每一天的累积变化。

```python
# 使用 cumsum() 函数进行累加
cumulative_log_returns = log_returns.cumsum()

![](img/eea29a05c6dda28dbadf7f8ca0c90a7b_38.png)

![](img/eea29a05c6dda28dbadf7f8ca0c90a7b_40.png)

# 查看结果
print(cumulative_log_returns.head())
```

![](img/eea29a05c6dda28dbadf7f8ca0c90a7b_42.png)

### 步骤3：还原为实际价值倍数

![](img/eea29a05c6dda28dbadf7f8ca0c90a7b_44.png)

累积对数收益率表示的是“初始1单位相当于多少倍”。为了得到更直观的价值倍数，我们需要通过指数函数将其还原。

![](img/eea29a05c6dda28dbadf7f8ca0c90a7b_46.png)

```python
# 使用 exp() 函数进行还原：e^(累积对数收益率)
value_multiple = np.exp(cumulative_log_returns)

![](img/eea29a05c6dda28dbadf7f8ca0c90a7b_48.png)

# 查看结果：例如，第N天的值表示“初始的1元钱在第N天相当于多少元”
print(value_multiple.head())
```

![](img/eea29a05c6dda28dbadf7f8ca0c90a7b_50.png)

### 步骤4：可视化结果

![](img/eea29a05c6dda28dbadf7f8ca0c90a7b_52.png)

最后，我们可以将计算出的价值倍数绘制成图，直观地观察各股票从起始点到不同时间点的价值增长情况。

![](img/eea29a05c6dda28dbadf7f8ca0c90a7b_54.png)

```python
import matplotlib.pyplot as plt

![](img/eea29a05c6dda28dbadf7f8ca0c90a7b_56.png)

# 绘制价值倍数曲线
value_multiple.plot(figsize=(12, 6))
plt.title('初始投资价值增长曲线 (以起始日为1单位)')
plt.ylabel('价值倍数')
plt.xlabel('日期')
plt.legend(loc='best')
plt.grid(True)
plt.show()
```

![](img/eea29a05c6dda28dbadf7f8ca0c90a7b_58.png)

执行以上代码后，你将得到一张图表。图中每条曲线都从1开始，展示了如果初始投资为1个单位（例如1元），在后续每个交易日它相当于多少单位。曲线上升代表增值，下降代表贬值。

![](img/eea29a05c6dda28dbadf7f8ca0c90a7b_60.png)

---

## 本节总结 📝

![](img/eea29a05c6dda28dbadf7f8ca0c90a7b_62.png)

![](img/eea29a05c6dda28dbadf7f8ca0c90a7b_64.png)

本节课中我们一起学习了金融时间序列中连续变化情况的分析方法：

![](img/eea29a05c6dda28dbadf7f8ca0c90a7b_66.png)

![](img/eea29a05c6dda28dbadf7f8ca0c90a7b_68.png)

1.  **识别问题**：我们首先认识到简单的增长率不能直接累加以反映整体变化。
2.  **引入对数**：通过引入对数变换，我们将乘法关系（价格比值）转化为加法关系（对数收益率），从而使得累加操作具有了实际意义——反映从起点到终点的整体倍数关系。
3.  **实践操作**：我们使用 `shift()` 函数获取滞后数据，用 `np.log()` 计算对数收益率，用 `cumsum()` 进行累加，最后用 `np.exp()` 还原为直观的价值倍数。
4.  **核心指标**：我们最终得到了一个重要的分析指标——**“初始的1单位投资，在时间序列的任一时点相当于多少单位”**，并通过图表将其可视化。

![](img/eea29a05c6dda28dbadf7f8ca0c90a7b_70.png)

![](img/eea29a05c6dda28dbadf7f8ca0c90a7b_72.png)

这种方法在量化金融中非常常用，是分析资产价格长期趋势和计算复合收益的基础。