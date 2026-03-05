# Python金融量化分析：P27：DataFrame数据对齐与缺失数据处理 📊

在本节课中，我们将要学习`DataFrame`对象的数据对齐机制以及如何处理其中的缺失数据。`DataFrame`作为二维数据结构，其数据对齐涉及行索引和列索引两个维度。同时，处理缺失数据的方法与`Series`对象类似，但也有一些针对二维结构的特有参数。

## 数据对齐机制 🔄

![](img/8e61c08cb49c396b7ac1b8c78e3891c1_1.png)

和`Series`对象一样，`DataFrame`也涉及数据对齐。由于`DataFrame`拥有行索引和列索引两个维度，因此数据对齐需要按照这两个索引分别进行。

例如，我们有两个`DataFrame`对象`df1`和`df2`，它们的行索引顺序不同。当我们执行`df1 + df2`时，Pandas会自动根据行索引和列索引进行对齐计算。如果某个位置在其中一个`DataFrame`中是缺失值（`NaN`），那么运算结果在该位置也会是`NaN`。

```python
# 示例：两个DataFrame相加，Pandas会自动根据索引对齐
result = df1 + df2
```

列索引同样遵循对齐规则。如果两个`DataFrame`的列顺序不同，运算时也会根据列名进行对齐。

![](img/8e61c08cb49c396b7ac1b8c78e3891c1_3.png)

## 缺失数据处理 🧹

![](img/8e61c08cb49c396b7ac1b8c78e3891c1_5.png)

`DataFrame`处理缺失数据的方法与`Series`对象大部分相似，但也存在一些关键区别。

### 填充缺失值

![](img/8e61c08cb49c396b7ac1b8c78e3891c1_7.png)

我们可以使用`fillna()`方法将缺失值填充为一个指定的值，这与`Series`的操作完全一致。

```python
# 将DataFrame中的所有NaN填充为0
df_filled = df2.fillna(0)
```

### 删除缺失值

删除缺失值的主要方法是`dropna()`。这里是与`Series`处理方式不同的重点。

默认情况下，`df.dropna()`会删除**包含任何缺失值（NaN）的行**。这意味着，只要某一行中有一个`NaN`，整行都会被删除。

然而，有时我们可能希望只删除那些**全部值都是缺失值的行**，而保留仅部分列缺失的行。这时可以通过设置`how`参数来实现。

以下是`dropna()`方法的关键参数说明：

*   **`how=‘any’` (默认值)**：如果某一行中有**任何一个**值是`NaN`，则删除该行。
*   **`how=‘all’`**：只有当某一行**所有**的值都是`NaN`时，才删除该行。

```python
# 默认行为：删除包含任何NaN的行
df_dropped_any = df2.dropna()
# 或显式指定
df_dropped_any = df2.dropna(how=‘any‘)

# 只删除全部值都为NaN的行
df_dropped_all = df2.dropna(how=‘all‘)
```

### 按列删除缺失值

在数据分析中，通常将每一行视为一条记录，每一列视为一个属性。但有时我们可能需要按列（属性）来删除缺失值。例如，如果某一列存在缺失值，我们希望删除整列。

这可以通过设置`axis`参数来实现。`axis`参数在许多`DataFrame`函数中都会用到，用于指定操作的方向。

*   **`axis=0` 或 `axis=‘index’` (默认)**：按行操作。`dropna(axis=0)`会删除包含缺失值的行。
*   **`axis=1` 或 `axis=‘columns’`**：按列操作。`dropna(axis=1)`会删除包含缺失值的列。

```python
# 删除包含任何NaN的列
df_dropped_column = df.dropna(axis=1)
```

可以将`axis`参数与`how`参数结合使用，实现更精细的控制。

```python
# 删除所有值都为NaN的列
df_dropped_column_all = df.dropna(axis=1, how=‘all‘)
```

关于缺失值的检测，`DataFrame`也提供了`isna()`和`notna()`方法，其功能与`Series`中的对应方法完全一致，用于生成布尔型的`DataFrame`，标识每个位置是否为缺失值。

![](img/8e61c08cb49c396b7ac1b8c78e3891c1_9.png)

## 总结 📝

![](img/8e61c08cb49c396b7ac1b8c78e3891c1_11.png)

本节课我们一起学习了`DataFrame`的数据对齐与缺失数据处理。

*   我们了解到`DataFrame`在进行运算时会根据行索引和列索引自动对齐数据。
*   在缺失数据处理方面，`fillna()`方法用于填充缺失值，其用法与`Series`相同。
*   重点掌握了`dropna()`方法：其默认行为是删除包含任何缺失值的行；通过`how=‘all’`参数可以改为只删除全部为缺失值的行；通过`axis=1`参数可以切换为按列删除缺失值。
*   最后，`isna()`和`notna()`方法用于检测缺失值。

![](img/8e61c08cb49c396b7ac1b8c78e3891c1_13.png)

理解这些操作对于清理和准备金融量化分析中的数据至关重要。