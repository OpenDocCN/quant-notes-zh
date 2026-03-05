# Python金融量化+业务数据分析：P23：pandas高级操作之分组聚合

![](img/799b0091907d100eef1fa07462305359_0.png)

![](img/799b0091907d100eef1fa07462305359_2.png)

## 概述
在本节课中，我们将要学习pandas库中一个核心且强大的功能：数据分组与聚合。这与SQL数据库中的`GROUP BY`操作类似，但我们将学习如何在pandas的DataFrame上实现它。我们将从基础的分组操作开始，逐步深入到分组后的聚合计算，以及如何使用自定义函数进行高级聚合。

![](img/799b0091907d100eef1fa07462305359_4.png)

![](img/799b0091907d100eef1fa07462305359_6.png)

## 基础分组操作

![](img/799b0091907d100eef1fa07462305359_8.png)

上一节我们介绍了pandas的基本数据结构，本节中我们来看看如何对数据进行分组。

![](img/799b0091907d100eef1fa07462305359_10.png)

![](img/799b0091907d100eef1fa07462305359_12.png)

首先，我们创建一组示例数据。这组数据包含四列：`item`（水果种类）、`price`（水果价格）、`color`（水果颜色）和`weight`（水果重量）。

![](img/799b0091907d100eef1fa07462305359_14.png)

```python
import pandas as pd

![](img/799b0091907d100eef1fa07462305359_16.png)

data = {
    'item': ['apple', 'banana', 'orange', 'banana', 'orange', 'apple'],
    'price': [4, 3, 3.5, 2.5, 4, 2],
    'color': ['red', 'yellow', 'orange', 'green', 'yellow', 'green'],
    'weight': [12, 35, 30, 32, 33, 10]
}
df = pd.DataFrame(data)
print(df)
```

![](img/799b0091907d100eef1fa07462305359_18.png)

![](img/799b0091907d100eef1fa07462305359_20.png)

![](img/799b0091907d100eef1fa07462305359_22.png)

接下来，我们指定分组条件对数据进行分组。例如，我们想按水果种类（`item`列）进行分组。

![](img/799b0091907d100eef1fa07462305359_24.png)

![](img/799b0091907d100eef1fa07462305359_26.png)

分组操作使用`groupby()`方法，分组条件是`item`列。原始数据中有三种水果，因此会被分成三组。

![](img/799b0091907d100eef1fa07462305359_28.png)

```python
grouped = df.groupby('item')
print(type(grouped))  # 输出：<class 'pandas.core.groupby.generic.DataFrameGroupBy'>
```

![](img/799b0091907d100eef1fa07462305359_30.png)

![](img/799b0091907d100eef1fa07462305359_32.png)

执行`groupby`后，返回的是一个`DataFrameGroupBy`对象，它存储了分组的结果。如果想查看详细的分组情况，可以调用该对象的`groups`属性。

![](img/799b0091907d100eef1fa07462305359_34.png)

```python
print(grouped.groups)
```
`groups`属性返回一个字典，显示了每种水果（分组键）对应原始数据中的哪些行索引。

![](img/799b0091907d100eef1fa07462305359_36.png)

## 分组聚合操作

仅仅分组通常没有意义，我们分组的目的是为了进行聚合计算。例如，我们想计算每种水果的平均价格。

![](img/799b0091907d100eef1fa07462305359_38.png)

![](img/799b0091907d100eef1fa07462305359_40.png)

以下是实现步骤：先按`item`分组，然后对`price`列求平均值。

![](img/799b0091907d100eef1fa07462305359_42.png)

```python
# 直接对分组后的所有数值列求均值
mean_all = df.groupby('item').mean()
print(mean_all)

![](img/799b0091907d100eef1fa07462305359_44.png)

![](img/799b0091907d100eef1fa07462305359_46.png)

# 只对‘price’列求均值
mean_price = df.groupby('item')['price'].mean()
print(mean_price)
```
`mean()`是pandas内置的聚合函数，它默认只对数值型列进行计算。如果我们只想对特定列（如`price`）进行聚合，可以在分组后先选中该列。

![](img/799b0091907d100eef1fa07462305359_48.png)

![](img/799b0091907d100eef1fa07462305359_50.png)

![](img/799b0091907d100eef1fa07462305359_52.png)

## 多条件分组与聚合

![](img/799b0091907d100eef1fa07462305359_53.png)

![](img/799b0091907d100eef1fa07462305359_55.png)

![](img/799b0091907d100eef1fa07462305359_57.png)

分组条件可以是多个。例如，我们想计算每种颜色下水果的平均重量。

以下是实现方法：按`color`列分组，然后对`weight`列求平均值。

![](img/799b0091907d100eef1fa07462305359_59.png)

![](img/799b0091907d100eef1fa07462305359_61.png)

```python
mean_weight_by_color = df.groupby('color')['weight'].mean()
print(mean_weight_by_color)
```

![](img/799b0091907d100eef1fa07462305359_63.png)

## 将聚合结果映射回原数据

![](img/799b0091907d100eef1fa07462305359_65.png)

![](img/799b0091907d100eef1fa07462305359_67.png)

有时我们需要将聚合计算的结果（如平均重量）作为新的一列添加回原始DataFrame中。

![](img/799b0091907d100eef1fa07462305359_69.png)

![](img/799b0091907d100eef1fa07462305359_71.png)

直接赋值可能会因为数据长度不匹配而失败。正确的方法是使用`map`函数进行映射。

![](img/799b0091907d100eef1fa07462305359_73.png)

![](img/799b0091907d100eef1fa07462305359_75.png)

首先，我们得到每种颜色的平均重量（一个Series）。
然后，将这个Series转换为字典，作为映射表。
最后，使用`map`函数根据`color`列将平均重量映射到每一行。

![](img/799b0091907d100eef1fa07462305359_77.png)

```python
# 计算映射表：每种颜色的平均重量
avg_weight_dict = df.groupby('color')['weight'].mean().to_dict()
print(avg_weight_dict)

# 使用map函数将平均重量映射到新列
df['mean_weight'] = df['color'].map(avg_weight_dict)
print(df)
```
现在，`df` DataFrame中新增了`mean_weight`列，每一行都显示了对应颜色的水果平均重量。

## 高级聚合：使用自定义函数

上一节我们使用了内置的聚合函数（如`mean`）。但如果pandas没有提供我们需要的聚合函数怎么办？这时就需要使用自定义函数进行高级聚合。

![](img/799b0091907d100eef1fa07462305359_79.png)

例如，我们想用自定义函数`my_mean`来计算平均价格。

我们不能直接在分组对象上调用自定义函数。需要使用`transform`或`apply`方法。

![](img/799b0091907d100eef1fa07462305359_81.png)

首先，定义自定义聚合函数`my_mean`，它接收一个序列（如一组价格），返回其平均值。

![](img/799b0091907d100eef1fa07462305359_83.png)

![](img/799b0091907d100eef1fa07462305359_85.png)

![](img/799b0091907d100eef1fa07462305359_87.png)

```python
def my_mean(s):
    """自定义求均值函数"""
    total = 0
    for i in s:
        total += i
    return total / len(s)

![](img/799b0091907d100eef1fa07462305359_89.png)

# 方法一：使用transform。结果会自动按原索引对齐，长度与原始数据一致。
df['price_my_mean_transform'] = df.groupby('item')['price'].transform(my_mean)
print(df[['item', 'price', 'price_my_mean_transform']])

![](img/799b0091907d100eef1fa07462305359_91.png)

# 方法二：使用apply。返回的是按组聚合后的结果（默认是Series）。
price_my_mean_apply = df.groupby('item')['price'].apply(my_mean)
print(price_my_mean_apply)
```
`transform`方法会将函数应用到每个分组，并返回一个与原始数据行数相同的结果，非常适合创建新列。`apply`方法更通用，但返回结果的形状取决于函数本身。

![](img/799b0091907d100eef1fa07462305359_93.png)

![](img/799b0091907d100eef1fa07462305359_95.png)

## 总结
本节课中我们一起学习了pandas中至关重要的分组聚合操作。
我们首先学习了如何使用`groupby()`进行基础分组，并查看分组结果。
接着，我们探讨了分组后如何进行聚合计算，包括使用内置函数和针对特定列计算。
然后，我们解决了如何将聚合结果作为新列添加回原数据的问题，这用到了`map`映射技巧。
最后，我们深入学习了高级聚合技术，即当内置函数不满足需求时，如何通过`transform`或`apply`方法使用自定义函数来完成复杂的聚合逻辑。
掌握分组聚合是进行高效数据分析的基础，请务必多加练习。