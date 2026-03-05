# Python金融分析与量化交易：P11：10 重复值和异常值的清洗 🧹

![](img/42fe1e7ca808fe839663c6b6a2184eae_1.png)

![](img/42fe1e7ca808fe839663c6b6a2184eae_3.png)

![](img/42fe1e7ca808fe839663c6b6a2184eae_5.png)

在本节课中，我们将学习数据清洗的最后两个重要部分：如何处理数据集中的重复行和异常值。掌握这些技能对于保证数据分析结果的准确性至关重要。

![](img/42fe1e7ca808fe839663c6b6a2184eae_7.png)

上一节我们介绍了缺失值的处理方法，本节中我们来看看如何识别并处理重复数据和异常数据。

![](img/42fe1e7ca808fe839663c6b6a2184eae_9.png)

![](img/42fe1e7ca808fe839663c6b6a2184eae_11.png)

## 重复数据的清洗 🔄

![](img/42fe1e7ca808fe839663c6b6a2184eae_13.png)

![](img/42fe1e7ca808fe839663c6b6a2184eae_15.png)

重复数据通常指数据集中完全相同的行。这些重复项可能是由于数据采集或合并过程中的错误产生的，需要被识别并移除。

![](img/42fe1e7ca808fe839663c6b6a2184eae_17.png)

以下是创建包含重复行的示例数据集的步骤：

![](img/42fe1e7ca808fe839663c6b6a2184eae_19.png)

```python
import pandas as pd
import numpy as np

![](img/42fe1e7ca808fe839663c6b6a2184eae_21.png)

# 生成一个8行4列的随机整数DataFrame
DF = pd.DataFrame(np.random.randint(0, 100, size=(8, 4)))
# 人为制造重复行：将索引为4和5的行设置为与索引为2的行相同
DF.iloc[4] = DF.iloc[2]
DF.iloc[5] = DF.iloc[2]
```

![](img/42fe1e7ca808fe839663c6b6a2184eae_23.png)

![](img/42fe1e7ca808fe839663c6b6a2184eae_25.png)

现在，数据集中包含了三行完全相同的数据。Pandas提供了 `drop_duplicates()` 方法来清洗重复数据。

以下是 `drop_duplicates()` 方法的关键参数 `keep` 的说明：
*   **`keep=‘first’`**：保留第一次出现的重复行，删除后续的重复行。这是默认设置。
*   **`keep=‘last’`**：保留最后一次出现的重复行，删除之前的重复行。
*   **`keep=False`**：删除所有重复的行，即只要重复就删除。

应用该方法清洗数据：
```python
# 使用默认参数，保留每组重复行中的第一个
DF_cleaned = DF.drop_duplicates()
```
执行后，只有第一次出现的重复行被保留，其余重复行均被删除。重复数据的处理相对直接，我们快速带过。

![](img/42fe1e7ca808fe839663c6b6a2184eae_27.png)

## 异常数据的清洗 🚨

![](img/42fe1e7ca808fe839663c6b6a2184eae_29.png)

异常数据是指那些明显偏离正常范围或模式的数据点，例如传感器故障产生的离谱读数。处理异常值通常需要先定义判定规则。

![](img/42fe1e7ca808fe839663c6b6a2184eae_31.png)

我们首先生成一个模拟数据集：
```python
# 生成一个1000行3列，数值在0-1之间的DataFrame
DF = pd.DataFrame(np.random.random(size=(1000, 3)), columns=[‘A‘, ‘B‘, ‘C‘])
```

![](img/42fe1e7ca808fe839663c6b6a2184eae_33.png)

假设我们的业务规则是：判定 **C列中大于该列两倍标准差的值** 为异常值。我们的目标是删除这些异常值所在的行。

![](img/42fe1e7ca808fe839663c6b6a2184eae_35.png)

以下是清洗异常值的完整流程：

1.  **计算判定阈值**：求出C列的两倍标准差。
    ```python
    twice_std = DF[‘C‘].std() * 2
    ```

![](img/42fe1e7ca808fe839663c6b6a2184eae_37.png)

![](img/42fe1e7ca808fe839663c6b6a2184eae_39.png)

2.  **定位异常值**：创建一个布尔序列，标记哪些行满足异常条件。
    ```python
    # 结果为True表示该行的C列值是异常值
    condition = DF[‘C‘] > twice_std
    ```

![](img/42fe1e7ca808fe839663c6b6a2184eae_41.png)

![](img/42fe1e7ca808fe839663c6b6a2184eae_43.png)

![](img/42fe1e7ca808fe839663c6b6a2184eae_45.png)

3.  **过滤数据**：我们需要保留正常值（即条件为False的行）。通过对布尔序列取反，并将其作为行索引来实现。
    ```python
    # 取反后，True对应正常值，False对应异常值。索引操作会保留所有为True的行。
    DF_cleaned = DF[~(DF[‘C‘] > twice_std)]
    # 等价于
    # DF_cleaned = DF[~condition]
    ```

![](img/42fe1e7ca808fe839663c6b6a2184eae_47.png)

![](img/42fe1e7ca808fe839663c6b6a2184eae_49.png)

清洗完成后，新的数据集中，C列的所有值都将小于或等于之前计算出的两倍标准差阈值，异常数据已被成功过滤。

![](img/42fe1e7ca808fe839663c6b6a2184eae_51.png)

本节课中我们一起学习了数据清洗的两个关键操作。我们掌握了使用 `drop_duplicates()` 方法快速移除重复行，也学会了通过定义业务规则（如基于标准差）来识别并删除异常值。结合之前学习的缺失值处理，你现在已经具备了完成基础数据清洗工作的能力，这是进行高质量金融数据分析的重要前提。