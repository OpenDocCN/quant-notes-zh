# Python金融量化投资分析与股票交易：P26：DataFrame数据对齐与缺失数据处理 📊

在本节课中，我们将要学习Pandas DataFrame对象的两个核心功能：数据对齐与缺失数据处理。理解这些概念对于后续进行金融数据的清洗、分析和计算至关重要。

![](img/67a08956887492cc63d5d41baf934fbc_1.png)

## 数据对齐

上一节我们介绍了Series的数据对齐，本节中我们来看看DataFrame的数据对齐。与Series不同，DataFrame拥有行索引和列索引两个维度，因此在进行运算时，需要同时按照行和列进行对齐。

例如，我们有两个DataFrame对象 `df1` 和 `df2`，它们的行索引顺序不同。当执行 `df1 + df2` 时，Pandas会自动根据行索引和列索引进行匹配对齐，然后执行计算。如果某个位置在其中一个DataFrame中缺失，则结果会显示为NaN。

![](img/67a08956887492cc63d5d41baf934fbc_3.png)

```python
# 示例：DataFrame数据对齐
import pandas as pd
import numpy as np

![](img/67a08956887492cc63d5d41baf934fbc_5.png)

df1 = pd.DataFrame(np.arange(12.).reshape((3, 4)), columns=list(‘abcd‘))
df2 = pd.DataFrame(np.arange(20.).reshape((4, 5)), columns=list(‘abcde‘))
result = df1 + df2  # 自动根据行和列索引对齐
```

![](img/67a08956887492cc63d5d41baf934fbc_7.png)

如果列的顺序不同，计算时同样会按照列名进行对齐。

## 缺失数据处理

DataFrame处理缺失数据的方法与Series大部分相似，但在某些操作上存在区别。

### 填充缺失值

使用 `fillna()` 方法可以将缺失值填充为指定的值，这与Series的操作完全一致。

```python
# 将缺失值填充为0
df2_filled = df2.fillna(0)
```

### 删除缺失值

使用 `dropna()` 方法可以删除包含缺失值的行或列，这是与Series处理方式的主要区别点。

以下是 `dropna()` 方法的关键参数：

*   **默认行为 (`how=‘any‘)`**：默认情况下，`dropna()` 会删除任何包含至少一个缺失值的**行**。
    ```python
    df_dropped_default = df2.dropna()  # 删除任何包含NaN的行
    ```
*   **`how=‘all‘`**：只有当一整行（或列）的值**全部**为缺失值时，才会将其删除。
    ```python
    df_dropped_all = df2.dropna(how=‘all‘)  # 只删除全部为NaN的行
    ```
*   **`axis` 参数**：这个参数决定了操作是按行还是按列进行。在DataFrame的许多函数中都会用到。
    *   `axis=0` 或 `axis=‘index‘`（默认）：按行操作，删除包含缺失值的行。
    *   `axis=1` 或 `axis=‘columns‘`：按列操作，删除包含缺失值的列。
    ```python
    # 删除包含缺失值的列
    df_dropped_column = df2.dropna(axis=1)
    ```

可以将 `how` 和 `axis` 参数结合使用，实现更精细的控制。

```python
# 删除那些所有值都为缺失值的列
df_dropped_all_columns = df2.dropna(axis=‘columns‘, how=‘all‘)
```

![](img/67a08956887492cc63d5d41baf934fbc_9.png)

此外，`isnull()` 和 `notnull()` 方法同样适用于DataFrame，用于判断每个位置是否为缺失值，与Series中的用法完全一致。

![](img/67a08956887492cc63d5d41baf934fbc_11.png)

## 总结

![](img/67a08956887492cc63d5d41baf934fbc_13.png)

本节课中我们一起学习了DataFrame的两个重要数据处理功能。我们了解了DataFrame在进行运算时会根据行索引和列索引自动对齐数据。同时，我们详细探讨了处理缺失数据的几种方法：使用 `fillna()` 进行填充，以及使用 `dropna()` 并配合 `how` 和 `axis` 参数来按条件删除包含缺失值的行或列。掌握这些数据清洗的基础操作，是为后续进行金融量化分析打下坚实的第一步。