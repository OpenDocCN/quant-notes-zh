# Python金融分析量化交易：P51：4-因子打分与排序

![](img/499c18abb72cf4cc7a94ab6c933a239e_0.png)

![](img/499c18abb72cf4cc7a94ab6c933a239e_2.png)

![](img/499c18abb72cf4cc7a94ab6c933a239e_0.png)

![](img/499c18abb72cf4cc7a94ab6c933a239e_4.png)

在本节课程中，我们将学习如何对筛选出的因子进行排序和打分。这是构建多因子模型的关键步骤，我们将分别处理“越高越好”和“越低越好”两类因子，并为它们分配相应的分数。

上一节我们介绍了如何根据因子特性将数据分为“越高越好”和“越低越好”两个DataFrame。本节中，我们将遍历这些DataFrame，对每个因子进行排序并打分。

![](img/499c18abb72cf4cc7a94ab6c933a239e_6.png)

## 遍历因子并排序

首先，我们需要遍历每个DataFrame中的因子列。以下是具体步骤。

我们需要获取DataFrame的列名，然后遍历每一列（即每一个因子）。对于每一列，我们根据其数值进行排序。

![](img/499c18abb72cf4cc7a94ab6c933a239e_8.png)

以下是实现排序的代码示例：

```python
# 假设 fundamental_up 是“越高越好”的因子DataFrame
for factor in fundamental_up.columns:
    # 对当前因子列进行排序，inplace=True表示直接修改原DataFrame
    fundamental_up.sort_values(by=factor, inplace=True)
```

![](img/499c18abb72cf4cc7a94ab6c933a239e_10.png)

在这段代码中，`sort_values`方法用于排序，`by`参数指定了排序依据的列。设置`inplace=True`可以避免额外的赋值操作，直接修改原始DataFrame。

## 为排序后的因子打分

![](img/499c18abb72cf4cc7a94ab6c933a239e_12.png)

排序完成后，我们需要为每个股票根据其因子排名进行打分。简单起见，我们采用线性打分法：排名第一的股票得分最高，排名最后的股票得分最低。

![](img/499c18abb72cf4cc7a94ab6c933a239e_14.png)

![](img/499c18abb72cf4cc7a94ab6c933a239e_16.png)

我们将使用NumPy的`linspace`函数来生成分数序列。这个函数可以生成指定范围内均匀间隔的数字。

![](img/499c18abb72cf4cc7a94ab6c933a239e_18.png)

![](img/499c18abb72cf4cc7a94ab6c933a239e_20.png)

以下是`numpy.linspace`函数的参数说明和示例：

![](img/499c18abb72cf4cc7a94ab6c933a239e_22.png)

```python
import numpy as np
# numpy.linspace(start, stop, num)
# start: 序列的起始值
# stop: 序列的结束值
# num: 要生成的样本数量
scores = np.linspace(1, 300, 300)  # 生成从1到300的300个数字
print(scores)  # 输出: [  1.   2.   3. ... 298. 299. 300.]
```

![](img/499c18abb72cf4cc7a94ab6c933a239e_24.png)

对于“越高越好”的因子，排名越高（数值越大）的股票应获得越高分数，因此我们使用`np.linspace(1, num_stocks, num_stocks)`。对于“越低越好”的因子，排名越低（数值越小）的股票应获得越高分数，因此我们使用`np.linspace(num_stocks, 1, num_stocks)`。

![](img/499c18abb72cf4cc7a94ab6c933a239e_26.png)

![](img/499c18abb72cf4cc7a94ab6c933a239e_28.png)

以下是完整的打分代码，需要先导入必要的库：

![](img/499c18abb72cf4cc7a94ab6c933a239e_30.png)

```python
import numpy as np
import pandas as pd

![](img/499c18abb72cf4cc7a94ab6c933a239e_32.png)

![](img/499c18abb72cf4cc7a94ab6c933a239e_34.png)

# 假设 fundamental_up 是“越高越好”的DataFrame， fundamental_down 是“越低越好”的DataFrame
num_stocks = len(fundamental_up)  # 获取股票数量，例如300

![](img/499c18abb72cf4cc7a94ab6c933a239e_36.png)

# 对“越高越好”的因子进行打分
for factor in fundamental_up.columns:
    fundamental_up.sort_values(by=factor, inplace=True)
    # 生成从1到num_stocks的分数，分配给排序后的股票
    fundamental_up[factor] = np.linspace(1, num_stocks, num_stocks)

![](img/499c18abb72cf4cc7a94ab6c933a239e_38.png)

# 对“越低越好”的因子进行打分
for factor in fundamental_down.columns:
    fundamental_down.sort_values(by=factor, inplace=True)
    # 生成从num_stocks到1的分数，分配给排序后的股票
    fundamental_down[factor] = np.linspace(num_stocks, 1, num_stocks)
```

## 合并数据并计算总分

完成两个DataFrame的因子打分后，我们得到了两个独立的评分表。为了计算每个股票的综合得分，我们需要将这两个表合并。

![](img/499c18abb72cf4cc7a94ab6c933a239e_40.png)

接下来，我们将两个DataFrame按行合并，然后对每个股票的所有因子得分进行求和，得到最终的总分。

![](img/499c18abb72cf4cc7a94ab6c933a239e_42.png)

```python
# 将两个DataFrame合并
total_fundamental = pd.concat([fundamental_up, fundamental_down], axis=1)

# 计算每个股票的总分（横向求和）
total_fundamental[‘总分‘] = total_fundamental.sum(axis=1)

![](img/499c18abb72cf4cc7a94ab6c933a239e_44.png)

# 展示结果
print(total_fundamental.head())
```

![](img/499c18abb72cf4cc7a94ab6c933a239e_46.png)

本节课中我们一起学习了因子打分与排序的核心流程。我们首先遍历了“越高越好”和“越低越好”两类因子数据框，对每个因子进行排序。然后，我们利用`numpy.linspace`函数根据排名生成了对应的分数。最后，我们将所有因子的评分合并，并计算出了每个股票的综合得分，为后续的选股策略奠定了基础。