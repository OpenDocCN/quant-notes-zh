# Python金融量化+股票交易：P19：重复值和异常值的清洗 🧹

![](img/765db0025de39e1d26a03e5386dfedfb_1.png)

![](img/765db0025de39e1d26a03e5386dfedfb_3.png)

![](img/765db0025de39e1d26a03e5386dfedfb_5.png)

在本节课中，我们将学习数据清洗的最后两个重要部分：**重复值**和**异常值**的处理。数据清洗是数据分析前的关键步骤，能确保我们后续分析的准确性和可靠性。

![](img/765db0025de39e1d26a03e5386dfedfb_7.png)

![](img/765db0025de39e1d26a03e5386dfedfb_9.png)

上一节我们介绍了缺失值的处理方法，本节中我们来看看如何处理重复和异常的数据。

![](img/765db0025de39e1d26a03e5386dfedfb_11.png)

![](img/765db0025de39e1d26a03e5386dfedfb_13.png)

![](img/765db0025de39e1d26a03e5386dfedfb_15.png)

## 重复值的清洗 🔄

![](img/765db0025de39e1d26a03e5386dfedfb_17.png)

重复值指的是数据集中完全相同的行。在金融数据分析中，重复的交易记录或报价数据会干扰分析结果，因此需要被识别和移除。

![](img/765db0025de39e1d26a03e5386dfedfb_19.png)

![](img/765db0025de39e1d26a03e5386dfedfb_21.png)

以下是处理重复值的步骤：

![](img/765db0025de39e1d26a03e5386dfedfb_23.png)

1.  **生成包含重复行的示例数据**：首先，我们创建一个包含重复行的DataFrame。
    ```python
    import pandas as pd
    import numpy as np

    # 创建一个8行4列的DataFrame，数值在0到100之间
    df = pd.DataFrame(np.random.randint(0, 100, size=(8, 4)))
    print(“原始数据：”)
    print(df)

    # 手动创建几行重复数据
    df.iloc[2] = [0, 0, 0, 0]  # 将索引为2的行设置为全0
    df.iloc[4] = [0, 0, 0, 0]  # 将索引为4的行设置为全0
    df.iloc[5] = [0, 0, 0, 0]  # 将索引为5的行设置为全0
    print(“\n插入重复行后的数据：”)
    print(df)
    ```

![](img/765db0025de39e1d26a03e5386dfedfb_25.png)

2.  **使用`drop_duplicates()`方法清洗重复值**：Pandas提供了`drop_duplicates()`方法来删除重复的行。其核心参数`keep`决定了保留哪一行。
    *   `keep=‘first’`：保留第一次出现的重复行（默认值）。
    *   `keep=‘last’`：保留最后一次出现的重复行。
    *   `keep=False`：删除所有重复的行。

    ```python
    # 删除重复行，默认保留第一个出现的
    df_cleaned = df.drop_duplicates()
    print(“\n清洗重复值后的数据（keep=‘first’）：”)
    print(df_cleaned)
    ```

通过以上操作，我们成功移除了数据集中的重复记录。

![](img/765db0025de39e1d26a03e5386dfedfb_27.png)

## 异常值的清洗 🚨

![](img/765db0025de39e1d26a03e5386dfedfb_29.png)

异常值是指与数据集中的其他观测值显著不同的数据点。在金融场景中，这可能是由于数据录入错误、系统故障或市场极端事件（如“闪崩”）造成的。异常值会扭曲统计分析结果，因此需要被识别和处理。

![](img/765db0025de39e1d26a03e5386dfedfb_31.png)

![](img/765db0025de39e1d26a03e5386dfedfb_33.png)

以下是清洗异常值的一个典型流程：

![](img/765db0025de39e1d26a03e5386dfedfb_35.png)

1.  **生成模拟数据并定义异常判定条件**：我们创建一个模拟数据集，并设定一个规则来识别异常值。例如，我们规定：如果C列中的某个值大于该列所有值的**两倍标准差**，则视其为异常值。
    ```python
    # 生成1000行3列的模拟数据，数值在0到1之间
    df = pd.DataFrame(np.random.random(size=(1000, 3)), columns=[‘A‘, ’B‘, ’C‘])
    print(“原始数据（前5行）：”)
    print(df.head())

    # 计算C列的两倍标准差，作为异常值阈值
    twice_std = df[‘C‘].std() * 2
    print(f“\nC列的两倍标准差（异常阈值）为：{twice_std}”)
    ```

2.  **识别并过滤异常值**：根据设定的条件，生成一个布尔序列（True/False），标记哪些行是异常的。然后，我们**取反**这个布尔序列（`~`操作符），并利用布尔索引来**保留所有正常值**。
    ```python
    # 判断哪些行的C列值大于阈值（异常条件）
    is_abnormal = df[‘C‘] > twice_std
    print(“\n异常值标记（前10个）：”)
    print(is_abnormal.head(10))

    # 取反，得到正常值的标记
    is_normal = ~is_abnormal

    # 使用布尔索引过滤，只保留正常值所在的行
    df_cleaned = df[is_normal]
    print(f“\n清洗后数据形状：{df_cleaned.shape}， 原始数据形状：{df.shape}”)
    print(f“共删除了 {df.shape[0] - df_cleaned.shape[0]} 行异常数据。”)

    # 验证清洗后C列的最大值是否小于阈值
    print(f“\n清洗后C列的最大值为：{df_cleaned[‘C‘].max()}， 小于阈值 {twice_std}。”)
    ```

![](img/765db0025de39e1d26a03e5386dfedfb_37.png)

![](img/765db0025de39e1d26a03e5386dfedfb_39.png)

![](img/765db0025de39e1d26a03e5386dfedfb_41.png)

![](img/765db0025de39e1d26a03e5386dfedfb_43.png)

**核心概念**：异常值的判定标准（阈值）可以根据业务知识灵活设定，常见的有：
*   **公式：`平均值 ± N * 标准差`**（如本例）。
*   **分位数法**：例如，认为小于1%分位数或大于99%分位数的值为异常。
*   **绝对边界法**：例如，在温度数据中，认为高于50℃或低于-10℃的值为异常。

![](img/765db0025de39e1d26a03e5386dfedfb_45.png)

![](img/765db0025de39e1d26a03e5386dfedfb_47.png)

![](img/765db0025de39e1d26a03e5386dfedfb_49.png)

---

![](img/765db0025de39e1d26a03e5386dfedfb_51.png)

本节课中我们一起学习了数据清洗的最后两个关键环节。我们掌握了如何使用`drop_duplicates()`方法高效地**删除重复行**，也学会了通过定义统计规则（如两倍标准差）来**识别并过滤异常值**。结合之前学习的缺失值处理，你现在已经具备了完成一套完整数据清洗流程的能力，这是进行高质量金融量化分析的重要基础。记住，干净、一致的数据是任何成功的数据分析项目的起点。