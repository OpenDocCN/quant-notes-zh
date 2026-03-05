# 量化交易实战课程：P25：DataFrame数据对齐与缺失数据处理 📊

在本节课中，我们将学习Pandas中`DataFrame`对象的两个核心操作：数据对齐与缺失数据处理。理解这些概念对于确保数据分析的准确性和完整性至关重要。

## 数据对齐

![](img/45648cb1283d73afdc7a4d15619cbd8f_1.png)

上一节我们介绍了`Series`的数据对齐，本节中我们来看看`DataFrame`的数据对齐。与`Series`类似，`DataFrame`在进行算术运算时也会自动对齐数据。但由于`DataFrame`拥有行索引和列索引两个维度，因此需要在这两个维度上分别进行对齐。

例如，我们创建两个`DataFrame`对象`df1`和`df2`，它们的行索引顺序不同。当执行`df1 + df2`时，Pandas会自动根据行索引和列索引进行匹配对齐。如果某个位置在其中一个`DataFrame`中不存在（值为`NaN`），那么运算结果在该位置也会是`NaN`。

**代码示例：**
```python
import pandas as pd
import numpy as np

![](img/45648cb1283d73afdc7a4d15619cbd8f_3.png)

# 创建两个行索引顺序不同的DataFrame
df1 = pd.DataFrame({'A': [1, 2, 3], 'B': [4, 5, 6]}, index=['a', 'b', 'c'])
df2 = pd.DataFrame({'A': [7, 8, 9], 'B': [10, 11, 12]}, index=['b', 'c', 'a'])

![](img/45648cb1283d73afdc7a4d15619cbd8f_5.png)

result = df1 + df2
print(result)
```
列索引同样遵循对齐规则。如果两个`DataFrame`的列顺序不同，运算时也会根据列名进行对齐。

![](img/45648cb1283d73afdc7a4d15619cbd8f_7.png)

## 缺失数据处理

`DataFrame`处理缺失数据的方法与`Series`大部分相似，但也存在一些重要区别。以下是处理缺失数据的常用方法。

### 填充缺失值

使用`fillna()`方法可以将缺失值替换为指定的值，这与`Series`的操作完全一致。

**代码示例：**
```python
df2_filled = df2.fillna(0)  # 将所有NaN填充为0
```

### 删除缺失值

使用`dropna()`方法可以删除包含缺失值的行或列。这是与`Series`处理方式的主要区别点，因为`DataFrame`需要考虑轴向。

默认情况下，`dropna()`会删除任何包含缺失值的行。

**代码示例：**
```python
df_cleaned = df.dropna()  # 默认删除任何包含NaN的行
```

`dropna()`方法有两个关键参数可以改变其行为：
1.  **`how`参数**：决定删除条件。
    *   `how=‘any’`（默认）：如果某行（或列）中**有任何**一个值是`NaN`，则删除该行（或列）。
    *   `how=‘all’`：只有某行（或列）中**所有**值都是`NaN`时，才删除它。
2.  **`axis`参数**：决定删除的轴向。
    *   `axis=0` 或 `axis=‘index’`（默认）：按行操作，删除包含`NaN`的行。
    *   `axis=1` 或 `axis=‘columns’`：按列操作，删除包含`NaN`的列。

以下是具体应用场景：

假设我们有一个`DataFrame` `df2`，其中`C`行有一个缺失值，`D`行全部是缺失值。
*   如果我们希望只删除全部为缺失值的行（即`D`行），可以设置`how=‘all’`。
    ```python
    df2.dropna(how=‘all’)
    ```
*   如果我们希望删除包含缺失值的列，而不是行，可以设置`axis=1`。
    ```python
    df2.dropna(axis=1)  # 删除任何包含NaN的列
    ```

### 检测缺失值

`isna()`和`notna()`方法用于检测缺失值，它们会返回一个布尔型的`DataFrame`，指示每个元素是否为`NaN`。这两个方法在`DataFrame`和`Series`中的用法完全一致。

**代码示例：**
```python
# 检测缺失值
mask = df.isna()
print(mask)

![](img/45648cb1283d73afdc7a4d15619cbd8f_9.png)

# 检测非缺失值
mask_not = df.notna()
print(mask_not)
```

![](img/45648cb1283d73afdc7a4d15619cbd8f_11.png)

## 总结

本节课中我们一起学习了`DataFrame`的数据对齐与缺失数据处理。
*   **数据对齐**：`DataFrame`在运算时会自动根据行索引和列索引对齐数据，未对齐的位置会产生`NaN`。
*   **缺失数据处理**：我们重点掌握了`dropna()`方法，通过`how`和`axis`参数可以灵活控制是删除行还是列，以及是部分删除还是全部删除才删除。`fillna()`、`isna()`和`notna()`等方法则与`Series`中的用法保持一致。

![](img/45648cb1283d73afdc7a4d15619cbd8f_13.png)

熟练掌握这些操作，是进行干净、可靠的数据分析的基础。