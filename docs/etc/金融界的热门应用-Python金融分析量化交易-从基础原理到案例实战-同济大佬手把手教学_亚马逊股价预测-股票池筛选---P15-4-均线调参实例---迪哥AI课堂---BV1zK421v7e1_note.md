# Python金融分析量化交易：P15：4-均线调参实例 📈

![](img/f0a882343b922625cf5df9c31b153adf_0.png)

![](img/f0a882343b922625cf5df9c31b153adf_2.png)

![](img/f0a882343b922625cf5df9c31b153adf_4.png)

![](img/f0a882343b922625cf5df9c31b153adf_6.png)

![](img/f0a882343b922625cf5df9c31b153adf_8.png)

在本节课中，我们将学习如何对双均线策略中的参数进行调优。上一节我们介绍了双均线策略的基本原理和实现，本节中我们来看看如何通过遍历不同的参数组合，找到表现更优的短期和长期移动平均线窗口。

![](img/f0a882343b922625cf5df9c31b153adf_10.png)

## 概述

![](img/f0a882343b922625cf5df9c31b153adf_12.png)

![](img/f0a882343b922625cf5df9c31b153adf_14.png)

![](img/f0a882343b922625cf5df9c31b153adf_16.png)

双均线策略的表现与短期和长期移动平均线的窗口大小密切相关。为了找到最佳参数组合，我们将使用Python的`itertools.product`工具遍历一系列预设的窗口值，计算每种组合下的策略收益，并与基准收益进行对比。

![](img/f0a882343b922625cf5df9c31b153adf_18.png)

![](img/f0a882343b922625cf5df9c31b153adf_20.png)

![](img/f0a882343b922625cf5df9c31b153adf_22.png)

![](img/f0a882343b922625cf5df9c31b153adf_24.png)

## 导入迭代工具

![](img/f0a882343b922625cf5df9c31b153adf_26.png)

![](img/f0a882343b922625cf5df9c31b153adf_28.png)

![](img/f0a882343b922625cf5df9c31b153adf_30.png)

![](img/f0a882343b922625cf5df9c31b153adf_32.png)

首先，我们需要导入用于生成参数组合的工具。

![](img/f0a882343b922625cf5df9c31b153adf_34.png)

![](img/f0a882343b922625cf5df9c31b153adf_36.png)

![](img/f0a882343b922625cf5df9c31b153adf_38.png)

![](img/f0a882343b922625cf5df9c31b153adf_40.png)

```python
from itertools import product
```

![](img/f0a882343b922625cf5df9c31b153adf_42.png)

![](img/f0a882343b922625cf5df9c31b153adf_44.png)

![](img/f0a882343b922625cf5df9c31b153adf_46.png)

`itertools.product`函数可以帮助我们计算多个序列的笛卡尔积，即生成所有可能的参数组合。

![](img/f0a882343b922625cf5df9c31b153adf_48.png)

![](img/f0a882343b922625cf5df9c31b153adf_50.png)

![](img/f0a882343b922625cf5df9c31b153adf_52.png)

![](img/f0a882343b922625cf5df9c31b153adf_54.png)

## 定义参数空间

![](img/f0a882343b922625cf5df9c31b153adf_56.png)

![](img/f0a882343b922625cf5df9c31b153adf_58.png)

![](img/f0a882343b922625cf5df9c31b153adf_60.png)

![](img/f0a882343b922625cf5df9c31b153adf_62.png)

接下来，我们需要定义短期和长期移动平均线的参数搜索空间。

![](img/f0a882343b922625cf5df9c31b153adf_64.png)

![](img/f0a882343b922625cf5df9c31b153adf_66.png)

以下是定义参数空间的示例代码：
```python
# 定义短期均线（SMA1）的参数空间，例如从20到60，步长为4
sma1_range = range(20, 61, 4)

# 定义长期均线（SMA2）的参数空间，例如从180到280，步长为10
sma2_range = range(180, 281, 10)
```
这段代码创建了两个范围，`sma1_range`和`sma2_range`，`product`函数将遍历这两个范围中的所有组合。

![](img/f0a882343b922625cf5df9c31b153adf_68.png)

![](img/f0a882343b922625cf5df9c31b153adf_70.png)

## 遍历参数与计算策略

![](img/f0a882343b922625cf5df9c31b153adf_72.png)

![](img/f0a882343b922625cf5df9c31b153adf_74.png)

![](img/f0a882343b922625cf5df9c31b153adf_76.png)

![](img/f0a882343b922625cf5df9c31b153adf_78.png)

在循环中，我们将对每一组参数执行完整的双均线策略计算流程。

![](img/f0a882343b922625cf5df9c31b153adf_80.png)

![](img/f0a882343b922625cf5df9c31b153adf_82.png)

![](img/f0a882343b922625cf5df9c31b153adf_84.png)

![](img/f0a882343b922625cf5df9c31b153adf_86.png)

以下是核心计算循环的框架：
```python
# 初始化一个空的DataFrame来存储所有结果
results = pd.DataFrame()

![](img/f0a882343b922625cf5df9c31b153adf_88.png)

![](img/f0a882343b922625cf5df9c31b153adf_90.png)

# 遍历所有参数组合
for sma1, sma2 in product(sma1_range, sma2_range):
    # 1. 重新加载或准备股票价格数据（例如苹果股价）
    data = ... # 加载数据代码

    # 2. 处理缺失值
    data = data.dropna()

    # 3. 计算收益率、短期均线和长期均线
    data[‘returns’] = ... # 计算收益率
    data[‘SMA1’] = ... # 计算短期均线，窗口为sma1
    data[‘SMA2’] = ... # 计算长期均线，窗口为sma2

    # 4. 根据均线关系生成交易信号（Position）
    data[‘position’] = ... # 生成信号代码

    # 5. 计算策略收益率
    data[‘strategy’] = ... # 计算策略收益率代码

    # 6. 再次处理计算过程中产生的缺失值
    data = data.dropna()

    # 7. 汇总本次参数组合下的关键指标
    # 例如：年化收益率、夏普比率等，这里简化为最终策略净值
    perf = data[[‘returns’, ‘strategy’]].iloc[-1] # 假设取最后一期的累计收益

    # 8. 将结果保存到results DataFrame中
    result_row = pd.DataFrame({
        ‘SMA1’: [sma1],
        ‘SMA2’: [sma2],
        ‘Returns’: [perf[‘returns’]],
        ‘Strategy’: [perf[‘strategy’]],
        ‘Outperformance’: [perf[‘strategy’] - perf[‘returns’]] # 策略超额收益
    })
    results = pd.concat([results, result_row], ignore_index=True)
```
**注意**：在实际代码中，步骤1到6的具体实现需要替换为之前课程中讲解过的完整、正确的代码块。上述框架展示了逻辑流程。

![](img/f0a882343b922625cf5df9c31b153adf_92.png)

![](img/f0a882343b922625cf5df9c31b153adf_94.png)

![](img/f0a882343b922625cf5df9c31b153adf_96.png)

![](img/f0a882343b922625cf5df9c31b153adf_98.png)

![](img/f0a882343b922625cf5df9c31b153adf_100.png)

## 结果分析

![](img/f0a882343b922625cf5df9c31b153adf_102.png)

![](img/f0a882343b922625cf5df9c31b153adf_104.png)

![](img/f0a882343b922625cf5df9c31b153adf_106.png)

![](img/f0a882343b922625cf5df9c31b153adf_108.png)

计算完成后，我们可以查看`results`表格，分析不同参数组合的表现。

![](img/f0a882343b922625cf5df9c31b153adf_110.png)

![](img/f0a882343b922625cf5df9c31b153adf_112.png)

以下是如何查看结果的示例：
```python
# 查看结果的前10行
print(results.head(10))
```
通过对比`Outperformance`（超额收益）列，我们可以判断哪些参数组合使得策略表现优于单纯持有资产（基准收益）。有些参数组合可能带来负的超额收益，而好的组合则能产生正向增益。

![](img/f0a882343b922625cf5df9c31b153adf_114.png)

![](img/f0a882343b922625cf5df9c31b153adf_116.png)

## 总结

![](img/f0a882343b922625cf5df9c31b153adf_118.png)

![](img/f0a882343b922625cf5df9c31b153adf_120.png)

本节课中我们一起学习了双均线策略的参数调优方法。我们通过以下步骤实现：
1.  使用`itertools.product`遍历预设的短期和长期均线参数空间。
2.  对每一组参数，执行完整的双均线策略回测流程。
3.  汇总并对比不同参数组合下的策略收益与基准收益。

![](img/f0a882343b922625cf5df9c31b153adf_122.png)

![](img/f0a882343b922625cf5df9c31b153adf_124.png)

![](img/f0a882343b922625cf5df9c31b153adf_126.png)

![](img/f0a882343b922625cf5df9c31b153adf_128.png)

通过这种方法，我们可以系统地寻找表现更优的策略参数，这是量化交易策略开发中至关重要的一步。