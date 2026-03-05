# Python金融量化分析：P50：因子打分与排序

![](img/ff04efe8d31128cc9c55827349bd10bb_0.png)

![](img/ff04efe8d31128cc9c55827349bd10bb_2.png)

![](img/ff04efe8d31128cc9c55827349bd10bb_4.png)

在本节课中，我们将学习如何对筛选出的因子进行排序和打分。这是构建多因子模型的关键步骤，通过为每个股票的每个因子赋予一个分数，我们可以综合评估股票的整体表现。

上一节我们介绍了如何根据因子特性（越高越好或越低越好）将因子数据分成两个独立的DataFrame。本节中，我们来看看如何遍历这些因子，为它们排序并计算得分。

![](img/ff04efe8d31128cc9c55827349bd10bb_6.png)

## 遍历因子并排序

以下是遍历“越高越好”因子DataFrame的步骤：

1.  **导入必要的库**：首先，我们需要导入`pandas`和`numpy`库来处理数据和生成分数序列。
    ```python
    import pandas as pd
    import numpy as np
    ```

![](img/ff04efe8d31128cc9c55827349bd10bb_8.png)

2.  **遍历因子列**：我们需要获取DataFrame的列名，这些列名就是因子名称，然后遍历每一列。
    ```python
    for factor in up_df.columns:
        # 获取当前因子的数据列
        current_factor_data = up_df[factor]
    ```

![](img/ff04efe8d31128cc9c55827349bd10bb_10.png)

3.  **对因子值进行排序**：对当前因子的数据进行排序。对于“越高越好”的因子，我们按降序排列，使因子值最大的股票排在最前面。使用`sort_values`方法，并设置`inplace=True`以直接在原DataFrame上修改。
    ```python
        up_df.sort_values(by=factor, ascending=False, inplace=True)
    ```

![](img/ff04efe8d31128cc9c55827349bd10bb_12.png)

## 为排序后的因子打分

![](img/ff04efe8d31128cc9c55827349bd10bb_14.png)

![](img/ff04efe8d31128cc9c55827349bd10bb_16.png)

排序完成后，我们需要为每个位置分配一个分数。一个简单的方法是：排名第一的股票获得最高分，排名最后的股票获得最低分。

![](img/ff04efe8d31128cc9c55827349bd10bb_18.png)

![](img/ff04efe8d31128cc9c55827349bd10bb_20.png)

我们将使用`numpy.linspace`函数来生成一个等差分数序列。这个函数接受三个主要参数：起始值、结束值和生成的数字个数。

![](img/ff04efe8d31128cc9c55827349bd10bb_22.png)

![](img/ff04efe8d31128cc9c55827349bd10bb_24.png)

*   对于“越高越好”的因子，排名第一（因子值最大）的股票应得最高分。因此，我们生成一个从最高分到最低分的序列。
    ```python
        # 假设有300只股票
        num_stocks = len(up_df)
        # 生成分数：第1名300分，第300名1分
        scores = np.linspace(num_stocks, 1, num_stocks)
    ```
*   对于“越低越好”的因子，排名第一（因子值最小）的股票应得最高分。因此，我们生成一个从最高分到最低分的序列（与“越高越好”相同），但排序时是按升序排列。
    ```python
        down_df.sort_values(by=factor, ascending=True, inplace=True)
        # 生成分数：第1名（值最小）300分，第300名1分
        scores = np.linspace(num_stocks, 1, num_stocks)
    ```

![](img/ff04efe8d31128cc9c55827349bd10bb_26.png)

![](img/ff04efe8d31128cc9c55827349bd10bb_28.png)

4.  **分配分数**：将生成的分数序列赋值给排序后的DataFrame中的对应因子列。
    ```python
        up_df[factor] = scores
    ```

![](img/ff04efe8d31128cc9c55827349bd10bb_30.png)

对“越低越好”的因子DataFrame执行类似的操作，但注意排序方向为升序(`ascending=True`)。

![](img/ff04efe8d31128cc9c55827349bd10bb_32.png)

![](img/ff04efe8d31128cc9c55827349bd10bb_34.png)

## 合并数据并计算总分

![](img/ff04efe8d31128cc9c55827349bd10bb_36.png)

![](img/ff04efe8d31128cc9c55827349bd10bb_38.png)

完成两个DataFrame中所有因子的打分后，我们得到了两个新的DataFrame，其中原始因子值已被替换为分数。

为了计算每只股票的综合得分，我们需要将这两个DataFrame合并。由于它们的索引（股票代码）和列结构（因子名称）是互补的，我们可以使用`pandas.concat`函数进行拼接。

```python
# 沿列方向合并两个DataFrame
combined_score_df = pd.concat([up_df, down_df], axis=1)
```

![](img/ff04efe8d31128cc9c55827349bd10bb_40.png)

合并后，`combined_score_df`的每一行代表一只股票，每一列代表该股票在某个因子上的得分。股票的综合得分就是其所有因子得分的总和。

![](img/ff04efe8d31128cc9c55827349bd10bb_42.png)

```python
# 计算每只股票的总分
combined_score_df['total_score'] = combined_score_df.sum(axis=1)
```

最后，我们可以根据`total_score`对股票进行最终排序，总分最高的股票就是我们的优选组合。

![](img/ff04efe8d31128cc9c55827349bd10bb_44.png)

![](img/ff04efe8d31128cc9c55827349bd10bb_46.png)

本节课中我们一起学习了因子打分与排序的完整流程：首先遍历并排序因子，接着利用`np.linspace`生成分数序列，然后为因子赋值，最后合并所有因子分数并计算总分。这是将多个单因子综合成可操作投资建议的核心步骤。