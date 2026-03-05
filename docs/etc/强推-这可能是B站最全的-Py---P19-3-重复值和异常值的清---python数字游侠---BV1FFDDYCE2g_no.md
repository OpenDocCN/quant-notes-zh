# Python金融量化+业务数据分析：P19：3.重复值和异常值的清洗 🧹

![](img/c2d8e6a515e9f9bfb89f4d9283240284_1.png)

![](img/c2d8e6a515e9f9bfb89f4d9283240284_3.png)

![](img/c2d8e6a515e9f9bfb89f4d9283240284_5.png)

在本节课中，我们将学习数据清洗的最后两个重要部分：**重复值**和**异常值**的处理。掌握这些技能，能让你的数据分析结果更加准确可靠。

![](img/c2d8e6a515e9f9bfb89f4d9283240284_7.png)

![](img/c2d8e6a515e9f9bfb89f4d9283240284_9.png)

![](img/c2d8e6a515e9f9bfb89f4d9283240284_11.png)

上一节我们介绍了缺失值的处理方法，本节中我们来看看如何识别并清理数据集中的重复记录和不符合常规的异常数据。

![](img/c2d8e6a515e9f9bfb89f4d9283240284_13.png)

![](img/c2d8e6a515e9f9bfb89f4d9283240284_15.png)

![](img/c2d8e6a515e9f9bfb89f4d9283240284_17.png)

## 处理重复数据 🔄

重复数据指的是数据集中完全相同的行记录。这些重复项可能会影响分析的准确性，因此需要被识别和移除。

![](img/c2d8e6a515e9f9bfb89f4d9283240284_19.png)

![](img/c2d8e6a515e9f9bfb89f4d9283240284_21.png)

以下是创建并处理重复数据的步骤：

![](img/c2d8e6a515e9f9bfb89f4d9283240284_23.png)

![](img/c2d8e6a515e9f9bfb89f4d9283240284_25.png)

1.  首先，我们创建一个包含重复行的示例数据集。
    ```python
    import pandas as pd
    import numpy as np

    # 创建一个8行4列的随机DataFrame
    df = pd.DataFrame(np.random.randint(0, 100, size=(8, 4)))
    print("原始数据：")
    print(df)

    # 手动创建几行重复数据
    df.iloc[4] = [0, 0, 0, 0]  # 将第5行设置为全0
    df.iloc[5] = [0, 0, 0, 0]  # 将第6行也设置为全0，与第5行重复
    print("\n添加重复行后的数据：")
    print(df)
    ```

2.  使用 `drop_duplicates()` 方法可以轻松删除重复行。该方法的关键参数是 `keep`，用于指定保留哪一行。
    *   `keep='first'`：保留第一次出现的重复行（默认值）。
    *   `keep='last'`：保留最后一次出现的重复行。
    *   `keep=False`：删除所有重复行。

    ```python
    # 删除重复行，默认保留第一个出现的
    df_cleaned = df.drop_duplicates()
    print("\n清洗重复值后的数据：")
    print(df_cleaned)
    ```

![](img/c2d8e6a515e9f9bfb89f4d9283240284_27.png)

## 处理异常数据 🚨

![](img/c2d8e6a515e9f9bfb89f4d9283240284_29.png)

异常值是指那些明显偏离数据集整体范围或分布规律的数值。例如，在记录室温的数据集中出现100℃的读数。处理异常值通常需要先定义判定规则。

![](img/c2d8e6a515e9f9bfb89f4d9283240284_31.png)

![](img/c2d8e6a515e9f9bfb89f4d9283240284_33.png)

我们通过一个示例来学习如何清洗异常值：假设我们有一个数据集，需要删除C列中数值大于该列两倍标准差的所有行（将这些行视为异常值）。

![](img/c2d8e6a515e9f9bfb89f4d9283240284_35.png)

以下是处理异常值的完整流程：

1.  首先，生成一个符合要求的数据集。
    ```python
    # 生成一个1000行3列，数值在0-1之间的DataFrame
    df = pd.DataFrame(np.random.random(size=(1000, 3)), columns=['A', 'B', 'C'])
    print("原始数据形状：", df.shape)
    ```

![](img/c2d8e6a515e9f9bfb89f4d9283240284_37.png)

![](img/c2d8e6a515e9f9bfb89f4d9283240284_39.png)

![](img/c2d8e6a515e9f9bfb89f4d9283240284_41.png)

2.  定义异常值的判定条件并执行清洗。
    ```python
    # 计算C列的两倍标准差
    twice_std = df['C'].std() * 2
    print("C列的两倍标准差为：", twice_std)

    # 构建布尔条件：筛选出C列值小于等于两倍标准差的正常数据
    # 对“大于两倍标准差”的条件取反，得到“是正常值”的布尔序列
    normal_data_mask = ~(df['C'] > twice_std)

    # 使用布尔索引过滤，只保留正常数据
    df_cleaned = df[normal_data_mask]
    print("\n清洗异常值后的数据形状：", df_cleaned.shape)
    print("清洗后C列最大值：", df_cleaned['C'].max())
    ```

![](img/c2d8e6a515e9f9bfb89f4d9283240284_43.png)

![](img/c2d8e6a515e9f9bfb89f4d9283240284_45.png)

![](img/c2d8e6a515e9f9bfb89f4d9283240284_47.png)

![](img/c2d8e6a515e9f9bfb89f4d9283240284_49.png)

**核心逻辑解释**：代码 `df[normal_data_mask]` 利用了Pandas的布尔索引功能。`normal_data_mask` 是一个由True和False组成的序列，True表示对应位置的行是正常值。DataFrame直接根据这个布尔序列进行筛选，只保留True对应的行，从而删除了被标记为异常值的行。

![](img/c2d8e6a515e9f9bfb89f4d9283240284_51.png)

本节课中我们一起学习了数据清洗的关键环节。我们掌握了如何使用 `drop_duplicates()` 方法高效去除重复行，也学会了通过定义统计规则（如两倍标准差）并结合布尔索引来识别和过滤异常值。至此，你已经了解了数据清洗中处理缺失值、重复值和异常值的主要方法，为后续的数据分析奠定了坚实的基础。