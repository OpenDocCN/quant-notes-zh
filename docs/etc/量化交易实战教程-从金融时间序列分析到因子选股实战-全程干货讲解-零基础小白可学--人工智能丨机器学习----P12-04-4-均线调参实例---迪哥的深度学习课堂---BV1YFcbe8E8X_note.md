# Python金融分析与量化交易实战：P12：04-4-均线调参实例 📈

![](img/9dc24a499db6f354d815fa46759d15f4_0.png)

![](img/9dc24a499db6f354d815fa46759d15f4_2.png)

![](img/9dc24a499db6f354d815fa46759d15f4_4.png)

在本节课中，我们将学习如何对双均线交易策略进行参数调优。我们将通过遍历不同的短期和长期均线参数组合，来寻找可能表现更优的策略配置。

上一节我们介绍了双均线策略的回测方法，本节中我们来看看如何通过调整参数来优化策略表现。

![](img/9dc24a499db6f354d815fa46759d15f4_6.png)

## 参数调优的必要性

![](img/9dc24a499db6f354d815fa46759d15f4_8.png)

策略的最终表现与许多因素有关，其中均线窗口长度的选择至关重要。短期均线（SMA1）和长期均线（SMA2）的取值不同，会导致不同的交易信号和最终收益。因此，我们需要系统地测试不同的参数组合，以寻找更优的配置。

![](img/9dc24a499db6f354d815fa46759d15f4_10.png)

![](img/9dc24a499db6f354d815fa46759d15f4_12.png)

![](img/9dc24a499db6f354d815fa46759d15f4_14.png)

![](img/9dc24a499db6f354d815fa46759d15f4_16.png)

## 使用 `itertools.product` 遍历参数组合

![](img/9dc24a499db6f354d815fa46759d15f4_18.png)

![](img/9dc24a499db6f354d815fa46759d15f4_20.png)

![](img/9dc24a499db6f354d815fa46759d15f4_22.png)

在Python中，我们可以借助 `itertools.product` 函数轻松地遍历所有可能的参数组合。这个函数可以生成多个可迭代对象的笛卡尔积，即所有可能的组合。

![](img/9dc24a499db6f354d815fa46759d15f4_24.png)

![](img/9dc24a499db6f354d815fa46759d15f4_26.png)

![](img/9dc24a499db6f354d815fa46759d15f4_28.png)

以下是导入和使用 `product` 的代码：

![](img/9dc24a499db6f354d815fa46759d15f4_30.png)

![](img/9dc24a499db6f354d815fa46759d15f4_32.png)

![](img/9dc24a499db6f354d815fa46759d15f4_34.png)

```python
from itertools import product
```

![](img/9dc24a499db6f354d815fa46759d15f4_36.png)

接下来，我们需要定义短期均线（SMA1）和长期均线（SMA2）的参数空间。例如，我们可以测试短期窗口从20天到60天（步长为4），长期窗口从180天到280天（步长为10）。

![](img/9dc24a499db6f354d815fa46759d15f4_38.png)

![](img/9dc24a499db6f354d815fa46759d15f4_40.png)

![](img/9dc24a499db6f354d815fa46759d15f4_42.png)

以下是定义参数空间的代码：

![](img/9dc24a499db6f354d815fa46759d15f4_44.png)

![](img/9dc24a499db6f354d815fa46759d15f4_46.png)

```python
# 定义短期均线（SMA1）的参数空间
sma1_range = range(20, 61, 4)  # 20, 24, 28, ..., 60
# 定义长期均线（SMA2）的参数空间
sma2_range = range(180, 281, 10)  # 180, 190, 200, ..., 280
```

![](img/9dc24a499db6f354d815fa46759d15f4_48.png)

![](img/9dc24a499db6f354d815fa46759d15f4_50.png)

`product(sma1_range, sma2_range)` 将生成所有可能的组合，例如 (20, 180), (20, 190), (20, 200) ... (24, 180), (24, 190) ... 等等。

![](img/9dc24a499db6f354d815fa46759d15f4_52.png)

## 构建参数调优循环

我们将在一个循环中遍历所有参数组合，对每一组参数都执行一次完整的双均线策略回测，并记录结果。

![](img/9dc24a499db6f354d815fa46759d15f4_54.png)

![](img/9dc24a499db6f354d815fa46759d15f4_56.png)

以下是参数调优循环的框架代码：

![](img/9dc24a499db6f354d815fa46759d15f4_58.png)

![](img/9dc24a499db6f354d815fa46759d15f4_60.png)

```python
import pandas as pd

![](img/9dc24a499db6f354d815fa46759d15f4_62.png)

![](img/9dc24a499db6f354d815fa46759d15f4_64.png)

![](img/9dc24a499db6f354d815fa46759d15f4_66.png)

# 初始化一个空的DataFrame来存储所有结果
results = pd.DataFrame()

![](img/9dc24a499db6f354d815fa46759d15f4_68.png)

![](img/9dc24a499db6f354d815fa46759d15f4_70.png)

# 遍历所有参数组合
for sma1, sma2 in product(sma1_range, sma2_range):
    # 1. 重新加载数据（确保每次循环数据是干净的）
    data = pd.DataFrame(original_data['AAPL.Close'])  # 假设原始数据已加载
    data.columns = ['price']

    # 2. 处理缺失值
    data = data.dropna()

    # 3. 计算收益率和均线
    data['returns'] = np.log(data['price'] / data['price'].shift(1))
    data['SMA1'] = data['price'].rolling(window=sma1).mean()
    data['SMA2'] = data['price'].rolling(window=sma2).mean()

    # 4. 再次处理因滚动计算产生的缺失值
    data = data.dropna()

    # 5. 生成交易信号
    data['position'] = np.where(data['SMA1'] > data['SMA2'], 1, -1)

    # 6. 计算策略收益率
    data['strategy'] = data['position'].shift(1) * data['returns']

    # 7. 再次处理缺失值并计算累计收益
    data = data.dropna()
    data['cumulative_returns'] = data['returns'].cumsum().apply(np.exp)
    data['cumulative_strategy'] = data['strategy'].cumsum().apply(np.exp)

    # 8. 计算策略相对于基准的 outperformance
    outperformance = data['cumulative_strategy'].iloc[-1] - data['cumulative_returns'].iloc[-1]

    # 9. 将本次循环的结果保存到 results DataFrame 中
    temp_df = pd.DataFrame({
        'SMA1': [sma1],
        'SMA2': [sma2],
        'Returns': [data['cumulative_returns'].iloc[-1]],
        'Strategy': [data['cumulative_strategy'].iloc[-1]],
        'Outperformance': [outperformance]
    })
    results = pd.concat([results, temp_df], ignore_index=True)
```

![](img/9dc24a499db6f354d815fa46759d15f4_72.png)

## 代码调试与结果分析

![](img/9dc24a499db6f354d815fa46759d15f4_74.png)

![](img/9dc24a499db6f354d815fa46759d15f4_76.png)

![](img/9dc24a499db6f354d815fa46759d15f4_78.png)

![](img/9dc24a499db6f354d815fa46759d15f4_80.png)

在编写代码时可能会遇到错误。例如，从原始数据中选取一列后，该列可能不再是一个具有列名的DataFrame，导致后续操作出错。解决方法是用 `pd.DataFrame()` 将其重新包装。

![](img/9dc24a499db6f354d815fa46759d15f4_82.png)

![](img/9dc24a499db6f354d815fa46759d15f4_84.png)

![](img/9dc24a499db6f354d815fa46759d15f4_86.png)

另一个常见错误是在创建DataFrame时未指定索引。我们可以通过设置 `index=[0]` 并配合 `ignore_index=True` 参数来解决。

![](img/9dc24a499db6f354d815fa46759d15f4_88.png)

![](img/9dc24a499db6f354d815fa46759d15f4_90.png)

执行完循环后，我们可以查看 `results` 表格。表格中会包含每一组参数对应的基准收益、策略收益以及策略相对于基准的超额收益（Outperformance）。

![](img/9dc24a499db6f354d815fa46759d15f4_92.png)

以下是查看结果的代码：

![](img/9dc24a499db6f354d815fa46759d15f4_94.png)

```python
print(results.head(10))
```

![](img/9dc24a499db6f354d815fa46759d15f4_96.png)

![](img/9dc24a499db6f354d815fa46759d15f4_98.png)

通过观察 `Outperformance` 列，我们可以判断哪些参数组合使策略表现优于简单持有（正数），哪些反而更差（负数）。大部分情况下，合适的参数能使策略获得正向的超额收益。

![](img/9dc24a499db6f354d815fa46759d15f4_100.png)

![](img/9dc24a499db6f354d815fa46759d15f4_102.png)

![](img/9dc24a499db6f354d815fa46759d15f4_104.png)

## 总结

![](img/9dc24a499db6f354d815fa46759d15f4_106.png)

![](img/9dc24a499db6f354d815fa46759d15f4_108.png)

![](img/9dc24a499db6f354d815fa46759d15f4_110.png)

本节课中我们一起学习了如何对双均线交易策略进行参数调优。我们使用 `itertools.product` 函数遍历了不同的短期和长期均线窗口组合，并对每一组参数进行了完整的策略回测。通过分析结果，我们可以筛选出表现更优的参数，从而优化我们的交易策略。这个过程是量化策略开发中至关重要的一步。