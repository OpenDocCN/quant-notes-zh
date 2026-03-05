# Python金融分析与量化交易实战：P12：04-4-均线调参实例 📈

![](img/b9618d018c5193e78e6dc651a218eece_0.png)

![](img/b9618d018c5193e78e6dc651a218eece_2.png)

![](img/b9618d018c5193e78e6dc651a218eece_4.png)

在本节课中，我们将学习如何对双均线交易策略进行参数调优。我们将通过遍历不同的短期和长期均线参数组合，来寻找可能表现更优的策略配置。

上一节我们介绍了双均线策略的基本原理和回测方法。本节中我们来看看如何系统地测试不同的参数组合，以优化策略表现。

![](img/b9618d018c5193e78e6dc651a218eece_6.png)

![](img/b9618d018c5193e78e6dc651a218eece_8.png)

![](img/b9618d018c5193e78e6dc651a218eece_10.png)

## 参数调优的必要性

![](img/b9618d018c5193e78e6dc651a218eece_12.png)

![](img/b9618d018c5193e78e6dc651a218eece_14.png)

双均线策略的结果与短期均线（SMA1）和长期均线（SMA2）的窗口大小密切相关。不同的参数组合可能导致完全不同的收益结果。因此，我们需要系统地测试多种参数组合，以找到在当前数据上表现较好的配置。

![](img/b9618d018c5193e78e6dc651a218eece_16.png)

![](img/b9618d018c5193e78e6dc651a218eece_18.png)

![](img/b9618d018c5193e78e6dc651a218eece_20.png)

![](img/b9618d018c5193e78e6dc651a218eece_22.png)

## 使用 `itertools.product` 进行参数遍历

![](img/b9618d018c5193e78e6dc651a218eece_24.png)

![](img/b9618d018c5193e78e6dc651a218eece_26.png)

![](img/b9618d018c5193e78e6dc651a218eece_28.png)

![](img/b9618d018c5193e78e6dc651a218eece_30.png)

在Python中，我们可以借助 `itertools` 模块中的 `product` 函数，轻松地生成所有可能的参数组合。

![](img/b9618d018c5193e78e6dc651a218eece_32.png)

![](img/b9618d018c5193e78e6dc651a218eece_34.png)

![](img/b9618d018c5193e78e6dc651a218eece_36.png)

以下是导入该工具的代码：
```python
from itertools import product
```
`product` 函数的作用是计算多个可迭代对象的笛卡尔积。例如，给定两个参数空间 `[20, 24]` 和 `[180, 190]`，`product` 会生成所有组合：`(20, 180)`, `(20, 190)`, `(24, 180)`, `(24, 190)`。

![](img/b9618d018c5193e78e6dc651a218eece_38.png)

![](img/b9618d018c5193e78e6dc651a218eece_40.png)

## 定义参数空间

![](img/b9618d018c5193e78e6dc651a218eece_42.png)

![](img/b9618d018c5193e78e6dc651a218eece_44.png)

![](img/b9618d018c5193e78e6dc651a218eece_46.png)

我们需要为短期均线（SMA1）和长期均线（SMA2）分别定义可能的取值范围。

![](img/b9618d018c5193e78e6dc651a218eece_48.png)

![](img/b9618d018c5193e78e6dc651a218eece_50.png)

以下是定义参数空间的示例代码：
```python
# 短期均线参数空间：从20到60，步长为4
short_range = range(20, 61, 4)
# 长期均线参数空间：从180到280，步长为10
long_range = range(180, 281, 10)
```

![](img/b9618d018c5193e78e6dc651a218eece_52.png)

![](img/b9618d018c5193e78e6dc651a218eece_54.png)

## 构建参数调优循环

接下来，我们将在一个循环中遍历所有参数组合，对每个组合执行完整的策略计算和回测。

![](img/b9618d018c5193e78e6dc651a218eece_56.png)

![](img/b9618d018c5193e78e6dc651a218eece_58.png)

以下是构建循环的步骤介绍：
1.  重新加载并预处理股票价格数据。
2.  在循环中，为每一组 `(SMA1, SMA2)` 参数计算收益率、均线值和交易信号。
3.  计算策略的累积收益，并与基准（买入并持有）收益进行比较。
4.  将每次循环的结果（参数和收益）保存到一个表格中。

![](img/b9618d018c5193e78e6dc651a218eece_60.png)

![](img/b9618d018c5193e78e6dc651a218eece_62.png)

以下是核心循环结构的代码框架：
```python
import pandas as pd

![](img/b9618d018c5193e78e6dc651a218eece_64.png)

![](img/b9618d018c5193e78e6dc651a218eece_66.png)

![](img/b9618d018c5193e78e6dc651a218eece_68.png)

# 初始化一个空的DataFrame来存储所有结果
results = pd.DataFrame()

![](img/b9618d018c5193e78e6dc651a218eece_70.png)

![](img/b9618d018c5193e78e6dc651a218eece_72.png)

# 遍历所有参数组合
for SMA1, SMA2 in product(short_range, long_range):
    # 1. 重新加载数据
    data = ... # 加载苹果股价数据
    data = data.dropna() # 处理缺失值

    # 2. 计算收益率和均线
    data['returns'] = ... # 计算日收益率
    data['SMA1'] = ... # 计算短期均线，窗口为SMA1
    data['SMA2'] = ... # 计算长期均线，窗口为SMA2
    data = data.dropna() # 再次处理因计算产生的缺失值

    # 3. 生成交易信号（双均线策略）
    data['position'] = ... # 当SMA1上穿SMA2时为1，否则为0
    data['strategy'] = ... # 根据position信号计算策略收益率
    data = data.dropna()

    # 4. 计算策略相对于基准的超额收益
    result = data[['returns', 'strategy']].iloc[-1] # 取最终累积收益
    outperformance = result['strategy'] - result['returns']

    # 5. 将本次结果添加到总表中
    result_table = pd.DataFrame({
        'SMA1': [SMA1],
        'SMA2': [SMA2],
        'returns': [result['returns']],
        'strategy': [result['strategy']],
        'outperformance': [outperformance]
    })
    results = pd.concat([results, result_table], ignore_index=True)
```

![](img/b9618d018c5193e78e6dc651a218eece_74.png)

![](img/b9618d018c5193e78e6dc651a218eece_76.png)

![](img/b9618d018c5193e78e6dc651a218eece_78.png)

![](img/b9618d018c5193e78e6dc651a218eece_80.png)

## 结果分析与解读

![](img/b9618d018c5193e78e6dc651a218eece_82.png)

![](img/b9618d018c5193e78e6dc651a218eece_84.png)

![](img/b9618d018c5193e78e6dc651a218eece_86.png)

执行完上述循环后，`results` DataFrame 中将包含所有参数组合的回测结果。

![](img/b9618d018c5193e78e6dc651a218eece_88.png)

![](img/b9618d018c5193e78e6dc651a218eece_90.png)

![](img/b9618d018c5193e78e6dc651a218eece_92.png)

以下是查看和分析结果的代码：
```python
# 查看前10行结果
print(results.head(10))
```
通过观察 `outperformance`（超额收益）列，我们可以判断策略表现：
*   **正值**：表示该参数下的双均线策略收益超过了简单的买入持有策略。
*   **负值**：表示该参数下的策略表现不如买入持有策略。
*   **零或接近零**：表示两者表现持平。

分析结果可以发现，并非所有参数组合都能带来正向收益。有些参数组合（如短期窗口过小或长期窗口不合适）可能导致策略失效甚至产生负超额收益。这正是参数调优的意义所在——帮助我们筛选出在当前市场环境下可能更有效的策略参数。

![](img/b9618d018c5193e78e6dc651a218eece_94.png)

![](img/b9618d018c5193e78e6dc651a218eece_96.png)

![](img/b9618d018c5193e78e6dc651a218eece_98.png)

## 注意事项与代码调试

![](img/b9618d018c5193e78e6dc651a218eece_100.png)

![](img/b9618d018c5193e78e6dc651a218eece_102.png)

在编写复杂的循环时，可能会遇到各种错误。视频中演示了两种常见的调试方法：
1.  **检查中间数据**：在报错位置前打印出关键变量（如 `data`），检查其结构和内容是否符合预期。
2.  **处理数据结构**：确保从DataFrame中取出的单列数据在后续操作中保持正确的数据结构（如使用 `pd.DataFrame()` 进行包装）。

![](img/b9618d018c5193e78e6dc651a218eece_104.png)

![](img/b9618d018c5193e78e6dc651a218eece_106.png)

![](img/b9618d018c5193e78e6dc651a218eece_108.png)

![](img/b9618d018c5193e78e6dc651a218eece_110.png)

本节课中我们一起学习了如何对双均线交易策略进行参数调优。我们掌握了使用 `itertools.product` 遍历参数空间的方法，构建了一个完整的回测循环来评估不同参数组合的表现，并学会了如何解读调优结果。参数调优是量化策略开发中至关重要的一步，它能帮助我们从众多可能性中找到更稳健或收益更高的策略配置。