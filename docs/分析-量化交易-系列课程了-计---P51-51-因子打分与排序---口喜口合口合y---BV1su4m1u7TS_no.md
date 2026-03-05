# Python金融分析+量化交易：P51：因子打分与排序 📊

![](img/aa4b9e4a7b49e2470abef08925c6a008_0.png)

![](img/aa4b9e4a7b49e2470abef08925c6a008_2.png)

在本节课中，我们将学习如何对筛选出的因子进行排序和打分。这是构建多因子模型的关键步骤，我们将分别处理“越高越好”和“越低越好”两类因子，并为它们分配相应的分数。

![](img/aa4b9e4a7b49e2470abef08925c6a008_4.png)

上一节我们完成了因子的筛选与分组，得到了两个DataFrame。本节中，我们来看看如何遍历这些因子数据，并根据其数值大小进行排序和打分。

![](img/aa4b9e4a7b49e2470abef08925c6a008_6.png)

## 遍历因子与排序

以下是遍历“越高越好”因子组并进行排序的步骤：

1.  **获取因子列名**：首先，我们需要获取DataFrame的列名，这些列名就是各个因子的名称。通过将其转换为列表，我们可以方便地遍历每一个因子。
    ```python
    factor_names = df_up.columns.tolist()
    ```

![](img/aa4b9e4a7b49e2470abef08925c6a008_8.png)

2.  **遍历每个因子**：使用`for`循环遍历因子名称列表，在每次循环中，我们针对当前因子列进行排序操作。
    ```python
    for factor in factor_names:
        # 对当前因子列进行排序
    ```

3.  **执行排序操作**：使用Pandas的`sort_values`方法对DataFrame进行排序。我们需要指定`by`参数为当前因子列名，并设置`inplace=True`以使排序结果直接作用于原DataFrame，避免额外的赋值操作。
    ```python
    df_up.sort_values(by=factor, inplace=True)
    ```

![](img/aa4b9e4a7b49e2470abef08925c6a008_10.png)

## 为因子分配分数

![](img/aa4b9e4a7b49e2470abef08925c6a008_12.png)

![](img/aa4b9e4a7b49e2470abef08925c6a008_14.png)

排序完成后，我们需要为每只股票在该因子上的表现打分。简单起见，我们采用等权排名打分法：排名第一的股票获得最高分，排名最后的股票获得最低分。

![](img/aa4b9e4a7b49e2470abef08925c6a008_16.png)

![](img/aa4b9e4a7b49e2470abef08925c6a008_18.png)

我们将使用NumPy的`linspace`函数来生成一个等差分数序列。该函数的三个核心参数是：起始值、终止值和生成的样本数量。

![](img/aa4b9e4a7b49e2470abef08925c6a008_20.png)

![](img/aa4b9e4a7b49e2470abef08925c6a008_22.png)

以下是使用`np.linspace`生成分数序列的方法：

![](img/aa4b9e4a7b49e2470abef08925c6a008_24.png)

*   **对于“越高越好”的因子**：因子值越大，排名应越靠前，得分应越高。因此，我们从高分到低分生成序列（例如，300分到1分）。
    ```python
    # 假设有300只股票
    num_stocks = 300
    scores = np.linspace(num_stocks, 1, num_stocks)
    ```

![](img/aa4b9e4a7b49e2470abef08925c6a008_26.png)

![](img/aa4b9e4a7b49e2470abef08925c6a008_28.png)

*   **对于“越低越好”的因子**：因子值越小，排名应越靠前，得分应越高。因此，我们同样从高分到低分生成序列，但排序方向与“越高越好”的因子相反。
    ```python
    # 排序逻辑已在前面步骤完成，此处同样使用从高到低的分数序列
    scores = np.linspace(num_stocks, 1, num_stocks)
    ```

![](img/aa4b9e4a7b49e2470abef08925c6a008_30.png)

![](img/aa4b9e4a7b49e2470abef08925c6a008_32.png)

接下来，我们将生成的分数序列赋值给排序后的DataFrame中的对应因子列，完成打分。

![](img/aa4b9e4a7b49e2470abef08925c6a008_34.png)

![](img/aa4b9e4a7b49e2470abef08925c6a008_36.png)

```python
df_up[factor] = scores
```

![](img/aa4b9e4a7b49e2470abef08925c6a008_38.png)

对“越低越好”的因子组，我们执行类似的操作：遍历、排序、然后分配由`np.linspace`生成的分数。

## 合并数据与计算总分

完成两个因子组的打分后，我们得到了两个新的DataFrame。为了计算每只股票的最终综合得分，我们需要将这两个DataFrame合并。

![](img/aa4b9e4a7b49e2470abef08925c6a008_40.png)

以下是合并数据并计算总分的步骤：

![](img/aa4b9e4a7b49e2470abef08925c6a008_42.png)

1.  **数据合并**：使用Pandas的`concat`函数，沿列方向（`axis=1`）将两个打分后的DataFrame合并在一起。确保索引（股票代码）对齐。
    ```python
    df_combined = pd.concat([df_up, df_down], axis=1)
    ```

2.  **计算总分**：对合并后的DataFrame按行求和（`axis=1`），即可得到每只股票在所有因子上的综合得分。
    ```python
    total_score = df_combined.sum(axis=1)
    ```

![](img/aa4b9e4a7b49e2470abef08925c6a008_44.png)

![](img/aa4b9e4a7b49e2470abef08925c6a008_46.png)

本节课中我们一起学习了因子打分与排序的完整流程。我们掌握了如何遍历因子、使用`sort_values`进行排序、利用`np.linspace`生成排名分数，以及最后合并数据并计算综合得分。这是将因子数据转化为可比较的分数，进而进行股票筛选或权重分配的基础。