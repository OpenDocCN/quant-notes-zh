# 数据分析+金融量化+数据清洗：P20：3.重复值和异常值的清洗 🧹

![](img/c0ea72a69c0dea62b9cee6c3969c28b2_1.png)

![](img/c0ea72a69c0dea62b9cee6c3969c28b2_3.png)

![](img/c0ea72a69c0dea62b9cee6c3969c28b2_5.png)

在本节课中，我们将学习数据清洗的最后两个重要部分：**重复值**和**异常值**的处理。掌握这些技能，能让你的数据集更加干净、可靠，为后续的分析工作打下坚实基础。

![](img/c0ea72a69c0dea62b9cee6c3969c28b2_7.png)

![](img/c0ea72a69c0dea62b9cee6c3969c28b2_9.png)

![](img/c0ea72a69c0dea62b9cee6c3969c28b2_11.png)

上一节我们介绍了缺失值的处理方法，本节中我们来看看如何处理数据中的重复行和不符合常规的异常值。

![](img/c0ea72a69c0dea62b9cee6c3969c28b2_13.png)

![](img/c0ea72a69c0dea62b9cee6c3969c28b2_15.png)

![](img/c0ea72a69c0dea62b9cee6c3969c28b2_17.png)

## 重复值的清洗

重复值指的是数据集中完全相同的行数据。这些重复项可能是由于数据采集或合并过程中的错误产生的，需要被识别并清理。

![](img/c0ea72a69c0dea62b9cee6c3969c28b2_19.png)

![](img/c0ea72a69c0dea62b9cee6c3969c28b2_21.png)

以下是处理重复值的步骤：

![](img/c0ea72a69c0dea62b9cee6c3969c28b2_23.png)

![](img/c0ea72a69c0dea62b9cee6c3969c28b2_25.png)

1.  **创建包含重复行的示例数据**：首先，我们生成一个包含重复行的`DataFrame`，以便演示清洗过程。
    ```python
    import pandas as pd
    import numpy as np

    # 生成一个8行4列的随机整数DataFrame
    df = pd.DataFrame(np.random.randint(0, 100, size=(8, 4)))
    # 人为制造重复行：将索引为2、4、5的行数据都设置为[0,0,0,0]
    df.iloc[2] = [0,0,0,0]
    df.iloc[4] = [0,0,0,0]
    df.iloc[5] = [0,0,0,0]
    print(df)
    ```

2.  **使用`drop_duplicates()`函数清洗**：Pandas提供了`drop_duplicates()`函数来删除重复的行。
    *   `keep=‘first’`：保留第一次出现的重复行，删除后续的重复行。这是默认参数。
    *   `keep=‘last’`：保留最后一次出现的重复行。
    *   `keep=False`：删除所有重复的行。
    ```python
    # 默认保留第一个出现的重复行
    df_cleaned = df.drop_duplicates()
    print(df_cleaned)
    ```

![](img/c0ea72a69c0dea62b9cee6c3969c28b2_27.png)

通过以上操作，数据集中的重复行就被成功移除了。

![](img/c0ea72a69c0dea62b9cee6c3969c28b2_29.png)

## 异常值的清洗

![](img/c0ea72a69c0dea62b9cee6c3969c28b2_31.png)

![](img/c0ea72a69c0dea62b9cee6c3969c28b2_33.png)

异常值是指那些明显偏离数据集整体范围或分布规律的数值。例如，在记录蔬菜大棚温度的数据中，出现120℃的读数显然是不合理的。处理异常值通常需要先定义判定规则，然后根据规则进行过滤或修正。

![](img/c0ea72a69c0dea62b9cee6c3969c28b2_35.png)

接下来，我们通过一个具体案例来学习如何清洗异常值。

1.  **创建示例数据与定义异常条件**：我们生成一个1000行3列（A, B, C）的数据集，数值范围在0到1之间。我们定义：如果C列中的某个值**大于C列所有数值的两倍标准差**，则判定该值为异常值。
    ```python
    # 生成1000行3列的随机数据
    df = pd.DataFrame(np.random.random(size=(1000, 3)), columns=[‘A‘, ‘B‘, ‘C‘])
    # 计算C列的两倍标准差，作为异常值阈值
    twice_std = df[‘C‘].std() * 2
    print(“两倍标准差阈值是：“, twice_std)
    ```

![](img/c0ea72a69c0dea62b9cee6c3969c28b2_37.png)

![](img/c0ea72a69c0dea62b9cee6c3969c28b2_39.png)

![](img/c0ea72a69c0dea62b9cee6c3969c28b2_41.png)

2.  **识别并过滤异常值**：根据上述条件生成一个布尔序列（True/False），然后利用这个序列对原数据进行筛选，只保留正常值。
    ```python
    # 生成布尔序列：True表示正常值（值 <= 阈值），False表示异常值
    normal_mask = df[‘C‘] <= twice_std
    # 使用布尔索引过滤，只保留标记为True（正常）的行
    df_cleaned = df[normal_mask]
    print(df_cleaned)
    # 验证清洗后C列的最大值是否已小于等于阈值
    print(“清洗后C列最大值：“, df_cleaned[‘C‘].max())
    ```

![](img/c0ea72a69c0dea62b9cee6c3969c28b2_43.png)

![](img/c0ea72a69c0dea62b9cee6c3969c28b2_45.png)

![](img/c0ea72a69c0dea62b9cee6c3969c28b2_47.png)

![](img/c0ea72a69c0dea62b9cee6c3969c28b2_49.png)

这种方法的核心是**先定义明确的异常判定规则（条件），然后利用布尔索引将满足异常条件的行数据从数据集中剔除**。

![](img/c0ea72a69c0dea62b9cee6c3969c28b2_51.png)

本节课中我们一起学习了数据清洗的最后两个关键环节。我们掌握了如何使用`drop_duplicates()`函数高效地**删除重复行数据**，也学会了通过定义统计规则（如两倍标准差）来**识别并过滤异常值**。至此，你已经具备了处理缺失值、重复值和异常值这三大常见数据质量问题的基本能力，可以让你手中的数据集变得更加整洁、可用。