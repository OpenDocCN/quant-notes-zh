# Python金融量化投资分析与股票交易：P24：DataFrame数据对齐与缺失数据处理 📊

在本节课中，我们将学习Pandas中`DataFrame`对象的两个重要功能：数据对齐与缺失数据处理。理解这些概念对于确保金融数据分析的准确性和完整性至关重要。

![](img/1a94e3acee62bd50a11d4eaf19079e62_1.png)

## 数据对齐 🔄

上一节我们介绍了`Series`的数据对齐，本节中我们来看看`DataFrame`的数据对齐。与`Series`类似，`DataFrame`在进行运算时也会自动对齐数据。但由于`DataFrame`拥有行索引和列索引两个维度，因此需要按照这两个索引分别进行对齐。

例如，我们有两个`DataFrame`对象`df1`和`df2`，它们的行索引顺序不同。当执行`df1 + df2`运算时，Pandas会自动根据行索引和列索引对齐数据，然后进行对应位置的元素相加。如果某个位置在其中一个`DataFrame`中为缺失值（NaN），那么运算结果在该位置也会是NaN。

![](img/1a94e3acee62bd50a11d4eaf19079e62_3.png)

```python
# 示例：DataFrame数据对齐
import pandas as pd
import numpy as np

![](img/1a94e3acee62bd50a11d4eaf19079e62_5.png)

df1 = pd.DataFrame({'A': [1, 2], 'B': [3, 4]}, index=['x', 'y'])
df2 = pd.DataFrame({'A': [5, 6], 'B': [7, 8]}, index=['y', 'x'])

![](img/1a94e3acee62bd50a11d4eaf19079e62_7.png)

result = df1 + df2
print(result)
```

## 缺失数据处理 🧹

`DataFrame`处理缺失数据的方法与`Series`对象大部分相似，但也存在一些关键区别。以下是处理缺失数据的核心方法。

### 填充缺失值

使用`fillna()`方法可以将缺失值替换为指定的值。这与`Series`的用法一致。

```python
# 示例：填充缺失值为0
df2_filled = df2.fillna(0)
```

### 删除缺失值

使用`dropna()`方法可以删除包含缺失值的行或列。这是与`Series`处理方式的主要区别点，因为`DataFrame`需要考虑删除的维度（行或列）。

以下是`dropna()`方法的关键参数：

*   **`how`参数**：决定删除行的条件。
    *   `how=‘any’`（默认值）：如果某一行中有**任何一个**值是缺失值，则删除该行。
    *   `how=‘all’`：只有当某一行中**所有**值都是缺失值时，才删除该行。
*   **`axis`参数**：决定删除的轴（维度）。
    *   `axis=0` 或 `axis=‘index’`（默认值）：删除包含缺失值的**行**。
    *   `axis=1` 或 `axis=‘columns’`：删除包含缺失值的**列**。

```python
# 示例1：删除任何包含NaN的行（默认行为）
df_cleaned_default = df.dropna()

# 示例2：删除全部为NaN的行
df_cleaned_all = df.dropna(how=‘all’)

![](img/1a94e3acee62bd50a11d4eaf19079e62_9.png)

# 示例3：删除任何包含NaN的列
df_cleaned_columns = df.dropna(axis=1)
```

![](img/1a94e3acee62bd50a11d4eaf19079e62_11.png)

此外，`DataFrame`也支持`isna()`和`notna()`方法来判断缺失值，其功能与`Series`中的对应方法完全一致。

![](img/1a94e3acee62bd50a11d4eaf19079e62_13.png)

本节课中我们一起学习了`DataFrame`的数据对齐机制以及处理缺失数据的多种方法。掌握如何填充或删除缺失值，是进行高质量金融数据清洗和分析的基础步骤。