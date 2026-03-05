# 量化投资基础：P51：4-因子打分与排序

![](img/424395dfd44de9c0f23d2113abe659f5_0.png)

![](img/424395dfd44de9c0f23d2113abe659f5_2.png)

在本节课中，我们将学习如何对多个选股因子进行打分与排序。我们将处理两类因子：**越高越好**的因子和**越低越好**的因子，通过遍历数据框、为每个因子排序、分配分数，最终合并结果以计算每只股票的总分。

![](img/424395dfd44de9c0f23d2113abe659f5_4.png)

---

## 遍历因子并排序

上一节我们准备好了两个数据框，分别包含“越高越好”和“越低越好”的因子。本节中，我们将分别遍历这两个数据框，对每个因子进行排序。

以下是遍历“越高越好”因子数据框的步骤：

1.  获取数据框的列名列表，以遍历每一个因子。
2.  对当前因子列的数据进行排序。
3.  使用 `inplace=True` 参数，使排序结果直接作用于原数据框，无需重新赋值。

```python
# 假设 fundamental_up 是“越高越好”因子的数据框
for factor in fundamental_up.columns:
    fundamental_up.sort_values(by=factor, inplace=True)
```

---

## 为排序后的因子分配分数

![](img/424395dfd44de9c0f23d2113abe659f5_6.png)

排序完成后，我们需要为每只股票在该因子上的表现打分。为简化操作，我们直接根据排名顺序分配分数：排名第一得最高分，排名最后得最低分。

我们将使用 NumPy 的 `linspace` 函数来生成一个等差数列作为分数。该函数的三个关键参数是：起始值、结束值和元素数量。

![](img/424395dfd44de9c0f23d2113abe659f5_8.png)

![](img/424395dfd44de9c0f23d2113abe659f5_10.png)

以下是 `linspace` 函数的使用示例：

![](img/424395dfd44de9c0f23d2113abe659f5_12.png)

```python
import numpy as np
# 生成从1到300的300个等间隔数字
scores = np.linspace(1, 300, 300)
print(scores) # 输出: [  1.   2.   3. ... 298. 299. 300.]
```

![](img/424395dfd44de9c0f23d2113abe659f5_14.png)

![](img/424395dfd44de9c0f23d2113abe659f5_16.png)

对于“越高越好”的因子，排名第一（值最大）的股票应得最高分。因此，我们使用 `np.linspace(1, total_stocks, total_stocks)` 来生成分数。

![](img/424395dfd44de9c0f23d2113abe659f5_18.png)

对于“越低越好”的因子，排名第一（值最小）的股票应得最高分。因此，我们需要生成一个从高到低的分数序列，即 `np.linspace(total_stocks, 1, total_stocks)`。

![](img/424395dfd44de9c0f23d2113abe659f5_20.png)

以下是完整的打分代码：

![](img/424395dfd44de9c0f23d2113abe659f5_22.png)

![](img/424395dfd44de9c0f23d2113abe659f5_24.png)

```python
import numpy as np
import pandas as pd

![](img/424395dfd44de9c0f23d2113abe659f5_26.png)

# 假设 fundamental_up 和 fundamental_down 是两个因子数据框，total_stocks 是股票总数
total_stocks = len(fundamental_up)

![](img/424395dfd44de9c0f23d2113abe659f5_28.png)

# 为“越高越好”的因子打分
for factor in fundamental_up.columns:
    fundamental_up.sort_values(by=factor, inplace=True)
    fundamental_up[factor] = np.linspace(1, total_stocks, total_stocks)

# 为“越低越好”的因子打分
for factor in fundamental_down.columns:
    fundamental_down.sort_values(by=factor, inplace=True)
    fundamental_down[factor] = np.linspace(total_stocks, 1, total_stocks)
```

![](img/424395dfd44de9c0f23d2113abe659f5_30.png)

---

## 合并数据框并计算总分

现在，我们得到了两个包含各因子分数的数据框。为了计算每只股票的综合得分，需要将这两个数据框合并。

合并后，将所有因子的分数相加，即可得到每只股票的总分。这可以通过 Pandas 的 `sum` 方法按行（`axis=1`）轻松实现。

```python
# 合并两个分数数据框
all_scores = pd.concat([fundamental_up, fundamental_down], axis=1)

# 计算每只股票的总分
total_score = all_scores.sum(axis=1)
```

---

![](img/424395dfd44de9c0f23d2113abe659f5_32.png)

本节课中我们一起学习了量化因子处理的完整流程：首先区分因子类型并分别排序，然后利用 `np.linspace` 函数根据排名分配分数，最后合并所有因子分数并计算总分。这套方法是构建多因子选股模型的基础步骤。