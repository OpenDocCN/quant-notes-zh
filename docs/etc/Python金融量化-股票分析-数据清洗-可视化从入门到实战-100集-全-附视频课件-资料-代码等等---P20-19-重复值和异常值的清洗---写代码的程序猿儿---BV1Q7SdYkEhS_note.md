# Python金融量化：P20：19.重复值和异常值的清洗 🧹

![](img/97c59bb98fb4e3ad69cd883996a0108d_1.png)

![](img/97c59bb98fb4e3ad69cd883996a0108d_3.png)

![](img/97c59bb98fb4e3ad69cd883996a0108d_5.png)

在本节课中，我们将要学习数据清洗中最后两个重要环节：**重复值**和**异常值**的处理。我们将通过具体的代码示例，学习如何识别并清理数据集中的重复行，以及如何根据设定的条件来识别和删除异常值。

![](img/97c59bb98fb4e3ad69cd883996a0108d_7.png)

![](img/97c59bb98fb4e3ad69cd883996a0108d_9.png)

上一节我们介绍了缺失值的处理方法，本节中我们来看看如何清理重复和异常的数据。

![](img/97c59bb98fb4e3ad69cd883996a0108d_11.png)

![](img/97c59bb98fb4e3ad69cd883996a0108d_13.png)

## 重复数据的清洗 🔄

![](img/97c59bb98fb4e3ad69cd883996a0108d_15.png)

![](img/97c59bb98fb4e3ad69cd883996a0108d_17.png)

重复数据通常指数据集中完全相同的行。这些重复项可能会影响数据分析的准确性，因此需要被识别和移除。

![](img/97c59bb98fb4e3ad69cd883996a0108d_19.png)

以下是处理重复数据的步骤：

![](img/97c59bb98fb4e3ad69cd883996a0108d_21.png)

1.  **生成包含重复行的数据**：首先，我们创建一个包含重复行的示例DataFrame。
    ```python
    import pandas as pd
    import numpy as np

    # 生成一个8行4列的随机整数DataFrame
    df = pd.DataFrame(np.random.randint(0, 100, size=(8, 4)))
    # 人为制造重复行：将第2、4、5行的数据设置为与第2行相同
    df.iloc[2] = [0, 0, 0, 0]
    df.iloc[4] = df.iloc[2]
    df.iloc[5] = df.iloc[2]
    print(df)
    ```

![](img/97c59bb98fb4e3ad69cd883996a0108d_23.png)

![](img/97c59bb98fb4e3ad69cd883996a0108d_25.png)

2.  **使用`drop_duplicates()`方法**：Pandas提供了`drop_duplicates()`方法来删除重复的行。
    *   `keep=‘first’`：保留第一次出现的重复行（默认值）。
    *   `keep=‘last’`：保留最后一次出现的重复行。
    *   `keep=False`：删除所有重复的行。
    ```python
    # 删除重复行，默认保留第一个出现的
    df_deduped = df.drop_duplicates()
    print(df_deduped)
    ```

通过以上操作，我们可以轻松地清洗掉数据集中的重复记录。

![](img/97c59bb98fb4e3ad69cd883996a0108d_27.png)

## 异常数据的清洗 🚨

![](img/97c59bb98fb4e3ad69cd883996a0108d_29.png)

异常数据是指那些明显偏离正常范围或不符合业务逻辑的数值。例如，在蔬菜大棚的温度数据中，出现120℃的读数显然是一个异常值。

![](img/97c59bb98fb4e3ad69cd883996a0108d_31.png)

处理异常值的关键在于定义一个合理的判定条件。我们将通过一个示例来演示：假设C列中，任何大于该列数据**两倍标准差**的值都被视为异常值，并需要将其所在行删除。

![](img/97c59bb98fb4e3ad69cd883996a0108d_33.png)

以下是清洗异常数据的步骤：

![](img/97c59bb98fb4e3ad69cd883996a0108d_35.png)

1.  **生成模拟数据**：创建一个1000行3列的DataFrame，数值范围在0到1之间。
    ```python
    # 生成1000行3列的随机数据
    df = pd.DataFrame(np.random.rand(1000, 3), columns=[‘A‘, ‘B‘, ‘C‘])
    print(df.head())
    ```

![](img/97c59bb98fb4e3ad69cd883996a0108d_37.png)

2.  **定义异常值判定条件**：计算C列的两倍标准差，并以此作为阈值。
    ```python
    # 计算C列的两倍标准差
    twice_std = df[‘C‘].std() * 2
    print(f“C列的两倍标准差为：{twice_std}“)
    ```

![](img/97c59bb98fb4e3ad69cd883996a0108d_39.png)

![](img/97c59bb98fb4e3ad69cd883996a0108d_41.png)

![](img/97c59bb98fb4e3ad69cd883996a0108d_43.png)

3.  **识别并过滤异常值**：创建一个布尔序列，标记哪些行的C列值大于阈值（即异常值）。然后，通过取反操作，保留正常值所在的行。
    ```python
    # 创建布尔掩码：True表示该行C列的值大于两倍标准差（异常值）
    condition = df[‘C‘] > twice_std
    # 取反，得到正常值的掩码，并用于筛选DataFrame
    df_cleaned = df.loc[~condition]
    print(df_cleaned.head())
    # 验证清洗后C列的最大值是否小于等于两倍标准差
    print(f“清洗后C列的最大值：{df_cleaned[‘C‘].max()}“)
    ```

![](img/97c59bb98fb4e3ad69cd883996a0108d_45.png)

![](img/97c59bb98fb4e3ad69cd883996a0108d_47.png)

![](img/97c59bb98fb4e3ad69cd883996a0108d_49.png)

通过以上步骤，我们成功地将所有被判定为异常的数据行从数据集中移除。

![](img/97c59bb98fb4e3ad69cd883996a0108d_51.png)

本节课中我们一起学习了数据清洗的最后两个核心操作。我们掌握了如何使用`drop_duplicates()`方法高效地删除重复行，也学会了如何通过定义统计条件（如两倍标准差）来识别和过滤异常值。结合之前学习的缺失值处理，你现在已经具备了完成基础数据清洗工作的能力，这是进行高质量金融量化分析的重要前提。