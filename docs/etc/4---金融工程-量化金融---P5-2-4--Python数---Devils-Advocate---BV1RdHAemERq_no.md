# 量化交易：Python入门之数据分析【2/4】：2-4. Python数据分析：Pandas数据结构 📊

在本节课中，我们将要学习Pandas库中一个核心的数据结构——DataFrame。DataFrame类似于Excel表格，是一种二维的、大小可变且可以存储多种类型数据的表格结构。我们将学习如何创建DataFrame，以及如何对它进行查询、切片、转置和修改等基本操作。

---

上一节我们介绍了Pandas Series，它是一种一维数据结构。本节中我们来看看如何将多个Series组合成更强大的二维表格——DataFrame。

![](img/9bf7f248d5c1c438cb937e0c5c83c6e6_1.png)

## 创建DataFrame

![](img/9bf7f248d5c1c438cb937e0c5c83c6e6_3.png)

首先，我们需要创建三个Pandas Series，分别代表顾客姓名、购买物品和价格。

![](img/9bf7f248d5c1c438cb937e0c5c83c6e6_5.png)

![](img/9bf7f248d5c1c438cb937e0c5c83c6e6_7.png)

```python
import pandas as pd

# 创建三个Series
purchase_1 = pd.Series({'Name': 'John', 'Item': 'Keyboard', 'Cost': 105})
purchase_2 = pd.Series({'Name': 'Mary', 'Item': 'Mouse', 'Cost': 25})
purchase_3 = pd.Series({'Name': 'Scott', 'Item': 'Monitor', 'Cost': 599.5})
```

![](img/9bf7f248d5c1c438cb937e0c5c83c6e6_9.png)

![](img/9bf7f248d5c1c438cb937e0c5c83c6e6_10.png)

为了将这些单列数据组合成面板数据（即DataFrame），我们可以使用 `pd.DataFrame()` 函数，并传入一个包含这些Series的列表。

![](img/9bf7f248d5c1c438cb937e0c5c83c6e6_12.png)

```python
# 将Series列表组合成DataFrame
df = pd.DataFrame([purchase_1, purchase_2, purchase_3], index=['Store 1', 'Store 1', 'Store 2'])
```

![](img/9bf7f248d5c1c438cb937e0c5c83c6e6_14.png)

执行上述代码后，我们得到一个名为 `df` 的DataFrame。它的行索引是商店名称，列则包含了`Name`、`Item`和`Cost`。

![](img/9bf7f248d5c1c438cb937e0c5c83c6e6_15.png)

![](img/9bf7f248d5c1c438cb937e0c5c83c6e6_16.png)

## 查询DataFrame中的数据

![](img/9bf7f248d5c1c438cb937e0c5c83c6e6_17.png)

![](img/9bf7f248d5c1c438cb937e0c5c83c6e6_19.png)

创建DataFrame后，我们常常需要查询其中的特定数据。

![](img/9bf7f248d5c1c438cb937e0c5c83c6e6_21.png)

以下是使用 `.loc` 方法进行数据查询的示例：

![](img/9bf7f248d5c1c438cb937e0c5c83c6e6_22.png)

![](img/9bf7f248d5c1c438cb937e0c5c83c6e6_23.png)

```python
# 查询‘Store 2’的所有数据
store2_data = df.loc['Store 2']
print(store2_data)
```

![](img/9bf7f248d5c1c438cb937e0c5c83c6e6_25.png)

![](img/9bf7f248d5c1c438cb937e0c5c83c6e6_26.png)

`.loc` 方法通过索引标签来定位数据。如果查询结果只有一行，返回的是一个Series；如果有多行，则返回一个DataFrame。

![](img/9bf7f248d5c1c438cb937e0c5c83c6e6_28.png)

## DataFrame的转置

![](img/9bf7f248d5c1c438cb937e0c5c83c6e6_29.png)

![](img/9bf7f248d5c1c438cb937e0c5c83c6e6_31.png)

DataFrame可以进行转置操作，即行和列互换。这通过属性 `.T` 来实现。

![](img/9bf7f248d5c1c438cb937e0c5c83c6e6_33.png)

![](img/9bf7f248d5c1c438cb937e0c5c83c6e6_35.png)

```python
# 对DataFrame进行转置
df_transposed = df.T
print(df_transposed)
```

![](img/9bf7f248d5c1c438cb937e0c5c83c6e6_37.png)

转置后，原来的列名（如Name, Item, Cost）变成了行索引，而原来的商店索引则变成了列名。

![](img/9bf7f248d5c1c438cb937e0c5c83c6e6_39.png)

![](img/9bf7f248d5c1c438cb937e0c5c83c6e6_41.png)

![](img/9bf7f248d5c1c438cb937e0c5c83c6e6_43.png)

## 定位与切片数据

![](img/9bf7f248d5c1c438cb937e0c5c83c6e6_45.png)

除了按行索引查询，我们还可以按列名查询，或者进行更精细的切片。

以下是几种常见的数据定位与切片方法：

![](img/9bf7f248d5c1c438cb937e0c5c83c6e6_47.png)

![](img/9bf7f248d5c1c438cb937e0c5c83c6e6_49.png)

1.  **按列查询**：直接使用列名。
    ```python
    # 获取‘Cost’列的所有数据
    cost_column = df['Cost']
    ```

![](img/9bf7f248d5c1c438cb937e0c5c83c6e6_51.png)

![](img/9bf7f248d5c1c438cb937e0c5c83c6e6_53.png)

2.  **定位单个值**：结合行索引和列名。
    ```python
    # 定位‘Store 1’的‘Cost’值（第一行）
    specific_value = df.loc['Store 1', 'Cost']
    ```

![](img/9bf7f248d5c1c438cb937e0c5c83c6e6_55.png)

3.  **使用.iloc按位置索引**：`.iloc` 通过行和列的位置（整数）来索引。
    ```python
    # 获取第0行，第2列的数据（即105）
    value_by_position = df.iloc[0, 2]
    ```

![](img/9bf7f248d5c1c438cb937e0c5c83c6e6_57.png)

![](img/9bf7f248d5c1c438cb937e0c5c83c6e6_59.png)

4.  **选择多列**：传入一个列名的列表。
    ```python
    # 选择‘Name’和‘Cost’两列的所有行数据
    name_and_cost = df.loc[:, ['Name', 'Cost']]
    ```

![](img/9bf7f248d5c1c438cb937e0c5c83c6e6_61.png)

## 修改DataFrame的结构

![](img/9bf7f248d5c1c438cb937e0c5c83c6e6_63.png)

![](img/9bf7f248d5c1c438cb937e0c5c83c6e6_64.png)

我们还可以对DataFrame进行增删改操作。

![](img/9bf7f248d5c1c438cb937e0c5c83c6e6_66.png)

以下是修改DataFrame结构的方法：

![](img/9bf7f248d5c1c438cb937e0c5c83c6e6_68.png)

![](img/9bf7f248d5c1c438cb937e0c5c83c6e6_69.png)

1.  **删除行或列**：使用 `.drop()` 方法。默认返回一个新对象，原DataFrame不变。
    ```python
    # 删除索引为‘Store 1’的行
    df_dropped = df.drop('Store 1')
    ```

![](img/9bf7f248d5c1c438cb937e0c5c83c6e6_71.png)

![](img/9bf7f248d5c1c438cb937e0c5c83c6e6_73.png)

2.  **创建副本**：为了避免修改原始数据，通常先创建副本。
    ```python
    df_copy = df.copy()
    df_copy = df_copy.drop('Store 1')
    ```

![](img/9bf7f248d5c1c438cb937e0c5c83c6e6_75.png)

3.  **添加新列**：直接为新的列名赋值即可。
    ```python
    # 添加一个名为‘Location’的新列，并暂时赋值为None
    df['Location'] = None
    ```

![](img/9bf7f248d5c1c438cb937e0c5c83c6e6_77.png)

---

![](img/9bf7f248d5c1c438cb937e0c5c83c6e6_78.png)

![](img/9bf7f248d5c1c438cb937e0c5c83c6e6_80.png)

本节课中我们一起学习了Pandas DataFrame的核心操作。我们掌握了如何从Series创建DataFrame，如何使用 `.loc` 和 `.iloc` 查询数据，如何进行转置和切片，以及如何通过 `.drop()` 和直接赋值来修改DataFrame的结构。DataFrame是进行金融数据分析的基石，熟练掌握这些操作至关重要。