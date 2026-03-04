# 人工智能金融量化交易：04-4：因子打分与排序 📊

![](img/6af6ad94dc989758b66252ff5fa9caef_0.png)

![](img/6af6ad94dc989758b66252ff5fa9caef_2.png)

在本节课程中，我们将学习如何对筛选出的因子进行排序和打分。这是构建多因子模型的关键步骤，目的是将不同因子的信息转化为统一的、可比较的分数，以便后续进行综合评估。

![](img/6af6ad94dc989758b66252ff5fa9caef_4.png)

上一节我们介绍了如何根据因子特性（越高越好或越低越好）将因子数据分成两个独立的DataFrame。本节中，我们将分别遍历这两个DataFrame，对每个因子进行排序，并根据排序结果赋予相应的分数。

以下是完成因子打分与排序的具体步骤。

## 遍历因子并排序

首先，我们需要遍历每个DataFrame中的因子列。对于“越高越好”的因子，排名越高（即因子值越大）的股票应获得更高的分数。反之，对于“越低越好”的因子，排名越高（即因子值越小）的股票应获得更高的分数。

```python
import pandas as pd
import numpy as np

# 假设我们已经有了两个DataFrame: df_up (越高越好) 和 df_down (越低越好)
# 遍历“越高越好”的因子DataFrame
for factor_name in df_up.columns:
    # 对当前因子列进行排序，从低到高（值小的在前）
    df_up.sort_values(by=factor_name, inplace=True)
```

![](img/6af6ad94dc989758b66252ff5fa9caef_6.png)

在上述代码中，`sort_values`方法用于排序。参数`by`指定了排序依据的列名。设置`inplace=True`意味着排序操作会直接修改原DataFrame，而无需将结果赋值给新变量。

![](img/6af6ad94dc989758b66252ff5fa9caef_8.png)

## 为排序后的因子分配分数

![](img/6af6ad94dc989758b66252ff5fa9caef_10.png)

![](img/6af6ad94dc989758b66252ff5fa9caef_12.png)

排序完成后，我们需要为每个位置分配一个分数。一个简单的方法是：对于有N只股票的数据集，排名第一的得N分，排名第二的得N-1分，以此类推。

![](img/6af6ad94dc989758b66252ff5fa9caef_14.png)

NumPy的`linspace`函数可以方便地生成这样一个等差数列。以下是该函数的用法示例。

![](img/6af6ad94dc989758b66252ff5fa9caef_16.png)

![](img/6af6ad94dc989758b66252ff5fa9caef_18.png)

```python
# 生成从1到300的300个等间距数字（用于“越高越好”的因子）
scores_up = np.linspace(1, 300, 300)
print(scores_up[:10])  # 输出前10个分数: [1. 2. 3. ... 10.]

![](img/6af6ad94dc989758b66252ff5fa9caef_20.png)

# 生成从300到1的300个等间距数字（用于“越低越好”的因子）
scores_down = np.linspace(300, 1, 300)
print(scores_down[:10]) # 输出前10个分数: [300. 299. 298. ... 291.]
```

![](img/6af6ad94dc989758b66252ff5fa9caef_22.png)

![](img/6af6ad94dc989758b66252ff5fa9caef_24.png)

`np.linspace(start, stop, num)`函数会生成从`start`到`stop`（包含两端）的`num`个等间隔数字。

![](img/6af6ad94dc989758b66252ff5fa9caef_26.png)

![](img/6af6ad94dc989758b66252ff5fa9caef_28.png)

现在，我们将这个打分逻辑整合到循环中。

![](img/6af6ad94dc989758b66252ff5fa9caef_30.png)

![](img/6af6ad94dc989758b66252ff5fa9caef_32.png)

```python
# 获取股票数量
num_stocks = len(df_up)

![](img/6af6ad94dc989758b66252ff5fa9caef_34.png)

# 为“越高越好”的因子打分
for factor_name in df_up.columns:
    df_up.sort_values(by=factor_name, inplace=True)
    # 生成从1到num_stocks的分数序列
    scores = np.linspace(1, num_stocks, num_stocks)
    # 将分数赋值给当前因子列，替换原来的因子值
    df_up[factor_name] = scores

# 为“越低越好”的因子打分
for factor_name in df_down.columns:
    df_down.sort_values(by=factor_name, inplace=True)
    # 生成从num_stocks到1的分数序列
    scores = np.linspace(num_stocks, 1, num_stocks)
    # 将分数赋值给当前因子列
    df_down[factor_name] = scores
```

执行完上述代码后，`df_up`和`df_down`中的原始因子值已被替换为对应的排名分数。两个DataFrame的维度保持不变（例如300行×3列）。

![](img/6af6ad94dc989758b66252ff5fa9caef_36.png)

## 合并分数表

![](img/6af6ad94dc989758b66252ff5fa9caef_38.png)

为了计算每只股票在所有因子上的综合得分，我们需要将两个打过分的DataFrame合并。

```python
# 将两个DataFrame按行索引对齐合并
total_score_df = df_up + df_down
```

这里使用简单的加法运算进行合并，前提是两个DataFrame具有完全相同的行索引（股票代码）和列结构。合并后得到的`total_score_df`中，每个单元格的值就是该股票在对应因子上的得分，所有因子得分之和即为该股票的综合得分。

![](img/6af6ad94dc989758b66252ff5fa9caef_40.png)

![](img/6af6ad94dc989758b66252ff5fa9caef_42.png)

本节课中我们一起学习了因子打分与排序的核心流程。我们首先遍历不同特性的因子并进行排序，然后利用`np.linspace`函数根据排名生成分数，最后将各因子的分数表合并，为下一步计算综合得分做好准备。这个过程将不同量纲和方向的因子统一到了可加总的分数体系内。