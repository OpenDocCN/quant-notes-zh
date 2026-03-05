# Python金融分析与量化交易实战课程：P46：4-因子打分与排序 📊

![](img/2606b1a40e390daa21a5af4494205fdd_0.png)

![](img/2606b1a40e390daa21a5af4494205fdd_2.png)

在本节课中，我们将要学习如何对选出的股票因子进行排序和打分。这是构建多因子模型的关键步骤，通过为每个因子赋予合理的分数，我们可以综合评估每只股票的吸引力。

![](img/2606b1a40e390daa21a5af4494205fdd_4.png)

上一节我们介绍了如何根据因子特性（越高越好或越低越好）将数据分为两个独立的DataFrame。本节中我们来看看如何遍历这些DataFrame，对每个因子进行排序，并基于排名进行打分。

## 遍历因子并排序 🔄

首先，我们需要遍历每个DataFrame中的因子列。以下是具体步骤：

1.  获取DataFrame的列名列表，以便遍历每一个因子。
2.  对每个因子列的数据进行排序。对于“越高越好”的因子，我们按降序排列；对于“越低越好”的因子，我们按升序排列。
3.  使用`inplace=True`参数直接在原DataFrame上更新排序结果，避免创建新的变量。

以下是实现排序的代码框架：
```python
# 假设 df_up 是“越高越好”因子的DataFrame
for factor in df_up.columns.tolist():
    df_up.sort_values(by=factor, ascending=False, inplace=True)
```

## 为排序结果进行打分 🏆

![](img/2606b1a40e390daa21a5af4494205fdd_6.png)

排序完成后，我们需要为每只股票在每个因子上的表现打分。一个简单直接的方法是：排名第一的股票获得最高分，排名最后的股票获得最低分。

![](img/2606b1a40e390daa21a5af4494205fdd_8.png)

我们将使用NumPy的`linspace`函数来生成一个线性的分数序列。这个函数可以方便地创建从起点到终点、包含指定数量元素的等差数列。

![](img/2606b1a40e390daa21a5af4494205fdd_10.png)

![](img/2606b1a40e390daa21a5af4494205fdd_12.png)

`linspace`函数的基本公式为：
`numpy.linspace(start, stop, num)`
*   `start`：序列的起始值。
*   `stop`：序列的结束值。
*   `num`：要生成的样本数量。

![](img/2606b1a40e390daa21a5af4494205fdd_14.png)

![](img/2606b1a40e390daa21a5af4494205fdd_16.png)

以下是使用`linspace`进行打分的示例：
```python
import numpy as np
import pandas as pd

![](img/2606b1a40e390daa21a5af4494205fdd_18.png)

![](img/2606b1a40e390daa21a5af4494205fdd_20.png)

# 假设有300只股票
num_stocks = 300

![](img/2606b1a40e390daa21a5af4494205fdd_22.png)

![](img/2606b1a40e390daa21a5af4494205fdd_24.png)

# 为“越高越好”的因子打分：排名越高（值越大），分数越高（300 -> 1）
# 排序后，第一名的因子值最大，应得300分
scores_up = np.linspace(num_stocks, 1, num_stocks)

![](img/2606b1a40e390daa21a5af4494205fdd_26.png)

# 为“越低越好”的因子打分：排名越高（值越小），分数越高（300 -> 1）
# 排序后，第一名的因子值最小，也应得300分
scores_down = np.linspace(num_stocks, 1, num_stocks)
```
注意：虽然两个序列的代码相同，但应用的前提是排序方向不同（`ascending`参数）。对于`df_up`我们使用降序(`ascending=False`)，对于`df_down`我们使用升序(`ascending=True`)，这样都能保证表现最好的股票（在各自标准下）获得最高分。

![](img/2606b1a40e390daa21a5af4494205fdd_28.png)

![](img/2606b1a40e390daa21a5af4494205fdd_30.png)

以下是整合了排序和打分的完整循环过程：
```python
# 对“越高越好”的因子进行排序和打分
for factor in df_up.columns.tolist():
    df_up.sort_values(by=factor, ascending=False, inplace=True)
    df_up[factor] = np.linspace(len(df_up), 1, len(df_up))

![](img/2606b1a40e390daa21a5af4494205fdd_32.png)

# 对“越低越好”的因子进行排序和打分
for factor in df_down.columns.tolist():
    df_down.sort_values(by=factor, ascending=True, inplace=True)
    df_down[factor] = np.linspace(len(df_down), 1, len(df_down))
```

![](img/2606b1a40e390daa21a5af4494205fdd_34.png)

## 合并数据并计算总分 ➕

完成所有因子的打分后，我们得到了两个包含因子得分的DataFrame。为了计算每只股票的综合得分，我们需要将它们合并。

以下是合并DataFrame的步骤：
1.  将`df_up`和`df_down`按股票代码（索引）进行合并。
2.  对合并后DataFrame中的所有因子得分进行横向求和（按行求和），得到每只股票的总分。
3.  根据总分对股票进行最终排序，选出综合表现最佳的股票。

![](img/2606b1a40e390daa21a5af4494205fdd_36.png)

![](img/2606b1a40e390daa21a5af4494205fdd_38.png)

实现代码如下：
```python
# 合并两个打分后的DataFrame
df_combined = pd.concat([df_up, df_down], axis=1)

# 计算每只股票的总分（假设所有因子权重相等）
df_combined[‘total_score‘] = df_combined.sum(axis=1)

# 根据总分进行降序排序，得到最终股票排名
final_ranking = df_combined.sort_values(by=‘total_score‘, ascending=False)
```

![](img/2606b1a40e390daa21a5af4494205fdd_40.png)

![](img/2606b1a40e390daa21a5af4494205fdd_42.png)

本节课中我们一起学习了量化交易中因子打分与排序的核心流程。我们首先遍历并排序了不同特性的因子，然后利用`np.linspace`函数基于排名进行线性打分，最后通过合并数据与求和计算出了每只股票的综合得分，为下一步的投资组合构建奠定了基础。