# 量化投资基础：P48：因子打分与排序

![](img/3c6419e7737d0ac1d2d3d02e34795c61_0.png)

## 📋 概述
在本节课中，我们将学习如何对筛选出的因子进行排序和打分。这是构建多因子模型的关键步骤，我们将遍历两个包含不同属性（“越高越好”和“越低越好”）因子的数据框，为每个因子下的股票进行排名并赋予相应的分数。

![](img/3c6419e7737d0ac1d2d3d02e34795c61_2.png)

![](img/3c6419e7737d0ac1d2d3d02e34795c61_4.png)

---

## 🔄 遍历数据框与因子
上一节我们介绍了如何将因子分为“越高越好”和“越低越好”两类。本节中，我们将分别遍历这两个数据框，对其中的每个因子进行排序和打分。

以下是遍历“越高越好”因子数据框的步骤：
1.  获取数据框的列名列表，以便遍历每一个因子。
2.  对每个因子列的数据进行排序。
3.  根据排序结果，为每只股票分配一个分数。

我们首先处理“越高越好”的因子。数据框的结构是300行（股票）乘以3列（因子）。我们需要遍历的是每一个因子列。

```python
# 假设 `up_df` 是“越高越好”因子的数据框
for factor in up_df.columns:
    # 对当前因子列进行排序，inplace=True表示直接修改原数据框
    up_df.sort_values(by=factor, inplace=True)
    # 接下来将进行打分操作
```

排序完成后，我们需要进行打分。为了简单起见，我们直接根据排名顺序来打分：第一名得300分，第二名得299分，以此类推，因为总共有300只股票。

---

## 🧮 使用`linspace`函数进行打分
为了高效地生成分数序列，我们将使用NumPy库中的`linspace`函数。这个函数可以生成一个指定范围内均匀间隔的数字序列。

![](img/3c6419e7737d0ac1d2d3d02e34795c61_6.png)

以下是`linspace`函数的基本用法：
*   `linspace(start, stop, num)`：生成从`start`到`stop`（包含两端）的等间隔序列，序列长度为`num`。

![](img/3c6419e7737d0ac1d2d3d02e34795c61_8.png)

```python
import numpy as np
# 生成从1到300的300个数字
score_sequence_asc = np.linspace(1, 300, 300)
print(score_sequence_asc) # 输出: [  1.   2.   3. ... 298. 299. 300.]
```

![](img/3c6419e7737d0ac1d2d3d02e34795c61_10.png)

对于“越高越好”的因子，我们希望因子值最大的股票获得最高分（300分）。由于我们之前是按因子值**升序**排列（从小到大），所以需要将分数序列**降序**分配，即第一个位置（因子值最小）得1分，最后一个位置（因子值最大）得300分。这可以通过生成一个从300到1的序列来实现。

![](img/3c6419e7737d0ac1d2d3d02e34795c61_12.png)

![](img/3c6419e7737d0ac1d2d3d02e34795c61_14.png)

```python
# 生成从300到1的300个数字（降序）
score_sequence_desc = np.linspace(300, 1, 300)
```

![](img/3c6419e7737d0ac1d2d3d02e34795c61_16.png)

---

![](img/3c6419e7737d0ac1d2d3d02e34795c61_18.png)

![](img/3c6419e7737d0ac1d2d3d02e34795c61_20.png)

## ✍️ 实现因子打分
现在，我们将排序和打分步骤整合到完整的代码中。首先需要导入必要的库。

![](img/3c6419e7737d0ac1d2d3d02e34795c61_22.png)

```python
import numpy as np
import pandas as pd

![](img/3c6419e7737d0ac1d2d3d02e34795c61_24.png)

# 假设 up_df 和 down_df 已经定义，分别是“越高越好”和“越低越好”的因子数据框
stock_count = len(up_df) # 股票总数，这里是300

![](img/3c6419e7737d0ac1d2d3d02e34795c61_26.png)

# 1. 对“越高越好”的因子进行打分
for factor in up_df.columns:
    up_df.sort_values(by=factor, inplace=True) # 按当前因子升序排序
    # 分配降序分数：因子值越大，分数越高
    up_df[factor] = np.linspace(stock_count, 1, stock_count)

![](img/3c6419e7737d0ac1d2d3d02e34795c61_28.png)

![](img/3c6419e7737d0ac1d2d3d02e34795c61_30.png)

# 2. 对“越低越好”的因子进行打分
for factor in down_df.columns:
    down_df.sort_values(by=factor, inplace=True) # 按当前因子升序排序
    # 分配升序分数：因子值越小，分数越高
    down_df[factor] = np.linspace(1, stock_count, stock_count)
```

![](img/3c6419e7737d0ac1d2d3d02e34795c61_32.png)

**代码解释**：
*   对于`up_df`（越高越好）：排序后，我们使用`np.linspace(stock_count, 1, stock_count)`生成从300到1的分数，并直接赋值回原列。这样，排在最后的股票（因子值最大）得到最高分。
*   对于`down_df`（越低越好）：排序后，我们使用`np.linspace(1, stock_count, stock_count)`生成从1到300的分数。这样，排在最前的股票（因子值最小）得到最高分。

![](img/3c6419e7737d0ac1d2d3d02e34795c61_34.png)

---

## ➕ 合并数据框计算总分
完成所有因子的独立打分后，我们得到了两个新的数据框（`up_df`和`down_df`），其中每个单元格的值不再是原始因子值，而是该股票在该因子上的得分。

为了计算每只股票的最终综合得分，我们需要将这两个数据框合并，然后对每一行（即每一只股票）的所有因子得分进行求和。

```python
# 将两个打分后的数据框合并
total_score_df = pd.concat([up_df, down_df], axis=1)

![](img/3c6419e7737d0ac1d2d3d02e34795c61_36.png)

# 计算每只股票的总分（横向求和）
total_score_df['综合得分'] = total_score_df.sum(axis=1)

![](img/3c6419e7737d0ac1d2d3d02e34795c61_38.png)

# 可以按综合得分进行排序，找到得分最高的股票
final_ranking = total_score_df.sort_values(by='综合得分', ascending=False)
print(final_ranking[['综合得分']].head()) # 查看综合得分最高的前几名
```

---

## 📝 总结
本节课中我们一起学习了因子打分与排序的核心流程。
1.  **遍历与排序**：我们遍历“越高越好”和“越低越好”两类因子，并对每个因子下的股票进行排序。
2.  **使用`linspace`打分**：我们利用NumPy的`np.linspace`函数，根据因子的属性（越大越好或越小越好）生成相应的分数序列，并赋值给排序后的股票。
3.  **合并与总分计算**：最后，我们将所有因子的得分数据框合并，通过横向求和计算出每只股票的综合得分，从而得到股票的最终排名。

![](img/3c6419e7737d0ac1d2d3d02e34795c61_40.png)

![](img/3c6419e7737d0ac1d2d3d02e34795c61_42.png)

这个过程是多因子选股模型的基础，它将不同量纲和方向的因子统一转化为可加和的分数，为后续的投资决策提供了量化依据。