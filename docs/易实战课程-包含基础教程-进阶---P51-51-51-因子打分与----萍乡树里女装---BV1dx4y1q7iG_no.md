# 量化交易教程：P51：因子打分与排序

![](img/2cc971384a7434aa25e67e54f9cabb5c_0.png)

在本节课中，我们将学习如何对筛选出的因子进行排序和打分。这是构建多因子模型的关键步骤，我们将分别处理“越高越好”和“越低越好”两类因子，并为每只股票在每个因子上分配一个得分。

上一节我们介绍了如何根据因子特性（如市盈率、市净率）将股票数据分为“越高越好”和“越低越好”两个`DataFrame`。本节中，我们来看看如何对这两个数据框中的因子分别进行排序和打分。

![](img/2cc971384a7434aa25e67e54f9cabb5c_2.png)

![](img/2cc971384a7434aa25e67e54f9cabb5c_3.png)

## 遍历因子与排序

首先，我们需要遍历每个`DataFrame`中的每一个因子列。以下是具体步骤：

![](img/2cc971384a7434aa25e67e54f9cabb5c_5.png)

1.  **获取因子列名**：将`DataFrame`的列名转换为列表，以便遍历。
2.  **对因子值进行排序**：对于“越高越好”的因子，值越大排名应越靠前；对于“越低越好”的因子则相反。
3.  **使用`inplace`参数**：`inplace=True`确保排序操作直接修改原`DataFrame`，而无需重新赋值。

以下是实现排序的代码框架：
```python
import pandas as pd
import numpy as np

![](img/2cc971384a7434aa25e67e54f9cabb5c_7.png)

# 假设 df_high 是“越高越好”的因子DataFrame
for factor in df_high.columns.tolist():
    df_high.sort_values(by=factor, inplace=True)
    # 后续将在此处添加打分代码

# 假设 df_low 是“越低越好”的因子DataFrame
for factor in df_low.columns.tolist():
    df_low.sort_values(by=factor, inplace=True)
    # 后续将在此处添加打分代码
```

## 为排序后的因子分配得分

![](img/2cc971384a7434aa25e67e54f9cabb5c_9.png)

排序完成后，我们需要为每只股票在每个因子上的表现打分。一个简单的方法是直接根据排名顺序赋分：排名第一的得最高分，排名最后的得最低分。

![](img/2cc971384a7434aa25e67e54f9cabb5c_11.png)

![](img/2cc971384a7434aa25e67e54f9cabb5c_12.png)

我们将使用NumPy的`linspace`函数来生成一个等差分数序列。该函数的参数如下：
*   `start`：序列的起始值。
*   `stop`：序列的结束值。
*   `num`：要生成的样本数量。

例如，对于300只股票：
*   `np.linspace(1, 300, 300)` 生成从1到300的300个分数（1, 2, 3, ..., 300）。
*   `np.linspace(300, 1, 300)` 生成从300到1的300个分数（300, 299, 298, ..., 1）。

![](img/2cc971384a7434aa25e67e54f9cabb5c_14.png)

以下是打分的逻辑：
*   **对于“越高越好”的因子**：排序后，因子值最大的股票排在最前面，应得最高分。因此使用`np.linspace(300, 1, 股票数量)`，将高分分配给排名靠前的股票。
*   **对于“越低越好”的因子**：排序后，因子值最小的股票排在最前面，应得最高分。因此也使用`np.linspace(300, 1, 股票数量)`。

![](img/2cc971384a7434aa25e67e54f9cabb5c_15.png)

![](img/2cc971384a7434aa25e67e54f9cabb5c_16.png)

![](img/2cc971384a7434aa25e67e54f9cabb5c_17.png)

以下是整合了排序和打分的完整代码示例：
```python
import pandas as pd
import numpy as np

![](img/2cc971384a7434aa25e67e54f9cabb5c_18.png)

![](img/2cc971384a7434aa25e67e54f9cabb5c_19.png)

# 假设 df_high 和 df_low 是已经准备好的DataFrame
num_stocks = len(df_high)  # 股票数量，假设为300

![](img/2cc971384a7434aa25e67e54f9cabb5c_21.png)

# 对“越高越好”的因子进行打分
for factor in df_high.columns.tolist():
    df_high.sort_values(by=factor, inplace=True)
    # 生成从高到低的分数 (300, 299, ..., 1)
    scores = np.linspace(num_stocks, 1, num_stocks)
    df_high[factor] = scores  # 用得分替换原来的因子值

![](img/2cc971384a7434aa25e67e54f9cabb5c_22.png)

![](img/2cc971384a7434aa25e67e54f9cabb5c_24.png)

# 对“越低越好”的因子进行打分
for factor in df_low.columns.tolist():
    df_low.sort_values(by=factor, inplace=True)
    # 生成从高到低的分数 (300, 299, ..., 1)
    scores = np.linspace(num_stocks, 1, num_stocks)
    df_low[factor] = scores  # 用得分替换原来的因子值

![](img/2cc971384a7434aa25e67e54f9cabb5c_26.png)

print(“越高越好因子得分表：”)
print(df_high.head())
print(“\n越低越好因子得分表：”)
print(df_low.head())
```

![](img/2cc971384a7434aa25e67e54f9cabb5c_28.png)

![](img/2cc971384a7434aa25e67e54f9cabb5c_29.png)

## 合并数据表与计算总分

完成所有因子的打分后，我们得到了两个得分表（`df_high`和`df_low`）。为了计算每只股票的综合得分，需要将这两个表合并。

我们可以使用Pandas的`concat`函数，按列方向（`axis=1`）将两个`DataFrame`连接起来。然后，对每一行（即每一只股票）的所有因子得分进行求和，得到该股票的总分。

以下是合并与计算总分的代码：
```python
# 合并两个得分表
combined_df = pd.concat([df_high, df_low], axis=1)

![](img/2cc971384a7434aa25e67e54f9cabb5c_31.png)

# 计算每只股票的总分（横向求和）
combined_df[‘总分’] = combined_df.sum(axis=1)

# 按总分从高到低进行排序，选出综合表现最好的股票
final_ranking = combined_df.sort_values(by=‘总分’, ascending=False)

![](img/2cc971384a7434aa25e67e54f9cabb5c_33.png)

print(“合并后的得分表与总分（前10名）：”)
print(final_ranking.head(10))
```

![](img/2cc971384a7434aa25e67e54f9cabb5c_34.png)

![](img/2cc971384a7434aa25e67e54f9cabb5c_35.png)

本节课中我们一起学习了因子评价模型中的打分与排序步骤。我们掌握了如何遍历因子、根据因子特性进行排序、利用`np.linspace`为排名分配得分，以及最后如何合并得分表并计算股票的综合总分。这是将多个因子信息汇总成单一投资信号的关键过程，为后续的选股策略奠定了基础。