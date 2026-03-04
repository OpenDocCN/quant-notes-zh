# 数据分析之量化案例：P19：Day05-3. 重复值和异常值的清洗 📊

![](img/4c8fb51c07162ad7c2ef2e2b1e1a47ee_1.png)

![](img/4c8fb51c07162ad7c2ef2e2b1e1a47ee_3.png)

![](img/4c8fb51c07162ad7c2ef2e2b1e1a47ee_5.png)

在本节课中，我们将学习数据清洗的最后两个重要部分：如何处理数据集中的重复行数据以及如何识别并清洗异常值。掌握这些技能对于保证数据分析结果的准确性和可靠性至关重要。

![](img/4c8fb51c07162ad7c2ef2e2b1e1a47ee_7.png)

![](img/4c8fb51c07162ad7c2ef2e2b1e1a47ee_9.png)

上一节我们介绍了缺失值的处理方法，本节中我们来看看如何清理重复数据和异常数据。

![](img/4c8fb51c07162ad7c2ef2e2b1e1a47ee_11.png)

![](img/4c8fb51c07162ad7c2ef2e2b1e1a47ee_13.png)

## 重复数据的清洗 🔄

![](img/4c8fb51c07162ad7c2ef2e2b1e1a47ee_15.png)

![](img/4c8fb51c07162ad7c2ef2e2b1e1a47ee_17.png)

重复数据指的是在数据集中完全相同的行。这些重复项可能是由于数据采集或合并过程中的错误产生的，如果不处理，会影响分析的准确性。

![](img/4c8fb51c07162ad7c2ef2e2b1e1a47ee_19.png)

以下是创建包含重复数据的示例数据集的步骤：

![](img/4c8fb51c07162ad7c2ef2e2b1e1a47ee_21.png)

```python
import pandas as pd
import numpy as np

![](img/4c8fb51c07162ad7c2ef2e2b1e1a47ee_23.png)

![](img/4c8fb51c07162ad7c2ef2e2b1e1a47ee_25.png)

# 创建一个8行4列的随机DataFrame
DF = pd.DataFrame(np.random.randint(0, 100, size=(8, 4)))
# 手动创建几行重复数据
DF.iloc[2] = [0, 0, 0, 0]
DF.iloc[4] = [0, 0, 0, 0]
DF.iloc[5] = [0, 0, 0, 0]
```

现在，`DF` 中索引为2、4、5的行是重复的。Pandas提供了 `drop_duplicates()` 方法来清洗重复数据。

以下是 `drop_duplicates()` 方法的关键参数说明：
*   **`keep=‘first’`**：保留第一次出现的重复行，删除后续的重复行。这是默认设置。
*   **`keep=‘last’`**：保留最后一次出现的重复行，删除之前的重复行。
*   **`keep=False`**：删除所有重复的行。

![](img/4c8fb51c07162ad7c2ef2e2b1e1a47ee_27.png)

应用该方法清洗数据：
```python
DF_cleaned = DF.drop_duplicates(keep=‘first’)
```
执行后，`DF_cleaned` 将只保留索引为2的原始行，而索引为4和5的重复行已被删除。

![](img/4c8fb51c07162ad7c2ef2e2b1e1a47ee_29.png)

## 异常数据的清洗 🚨

![](img/4c8fb51c07162ad7c2ef2e2b1e1a47ee_31.png)

![](img/4c8fb51c07162ad7c2ef2e2b1e1a47ee_33.png)

异常值是指那些明显偏离数据集整体模式或常规范围的数值。例如，在蔬菜大棚的温度数据中，出现120℃的读数显然是不合理的。我们需要根据特定条件识别并处理这些值。

![](img/4c8fb51c07162ad7c2ef2e2b1e1a47ee_35.png)

我们首先生成一个模拟数据集：
```python
# 创建一个1000行3列的DataFrame，数值范围在0到1之间
DF = pd.DataFrame(np.random.random(size=(1000, 3)), columns=[‘A‘, ‘B‘, ‘C‘])
```

![](img/4c8fb51c07162ad7c2ef2e2b1e1a47ee_37.png)

假设我们的业务规则是：**C列中任何大于该列两倍标准差的值都被视为异常值**。我们的目标是将这些异常值所在的行从数据集中删除。

![](img/4c8fb51c07162ad7c2ef2e2b1e1a47ee_39.png)

![](img/4c8fb51c07162ad7c2ef2e2b1e1a47ee_41.png)

以下是识别和清洗异常值的完整流程：

![](img/4c8fb51c07162ad7c2ef2e2b1e1a47ee_43.png)

![](img/4c8fb51c07162ad7c2ef2e2b1e1a47ee_45.png)

1.  **计算判定阈值**：计算C列的两倍标准差。
    ```python
    twice_std = DF[‘C‘].std() * 2
    ```
2.  **创建布尔掩码**：找出C列中所有大于该阈值的值，生成一个布尔序列（True表示异常值）。
    ```python
    condition = DF[‘C‘] > twice_std
    ```
3.  **取反并过滤**：对布尔序列取反（`~`），使True变为False，False变为True。然后使用这个取反后的序列作为行索引来筛选DataFrame，从而**保留所有正常值（原False），剔除所有异常值（原True）**。
    ```python
    DF_cleaned = DF[~condition]
    ```

![](img/4c8fb51c07162ad7c2ef2e2b1e1a47ee_47.png)

![](img/4c8fb51c07162ad7c2ef2e2b1e1a47ee_49.png)

![](img/4c8fb51c07162ad7c2ef2e2b1e1a47ee_51.png)

执行以上操作后，`DF_cleaned` 中C列的所有值都将小于或等于 `twice_std`，异常数据已被成功清洗。

![](img/4c8fb51c07162ad7c2ef2e2b1e1a47ee_53.png)

本节课中我们一起学习了数据清洗的核心操作。我们掌握了使用 `drop_duplicates()` 方法高效去除重复行，也学会了通过定义业务规则（如基于标准差）来识别异常值，并利用布尔索引将其过滤。至此，我们已经涵盖了缺失值、重复值和异常值这三类主要数据问题的清洗方法，为后续的量化分析打下了坚实的数据基础。