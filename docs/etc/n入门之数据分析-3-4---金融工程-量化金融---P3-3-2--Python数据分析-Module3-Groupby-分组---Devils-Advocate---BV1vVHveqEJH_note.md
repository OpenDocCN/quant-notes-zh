# 量化交易：Python入门之数据分析【3/4】：3-2. Python数据分析：Module3-Groupby 分组 📊

在本节课中，我们将要学习Python Pandas库中一个非常强大的功能——`groupby`（分组）操作。`groupby`允许我们根据某些条件将数据分组，然后对每个组进行聚合计算，例如求和、求平均值或计数。这对于金融数据分析至关重要，例如按行业分析股票收益或按日期汇总交易量。

## 概述

`groupby`操作的核心思想是“拆分-应用-合并”。首先，数据根据一个或多个键（列）被拆分成多个组。然后，将一个函数（如求和、求平均值）独立地应用到每个组上。最后，将各组的结果合并成一个新的数据结构。接下来，我们将通过具体的例子来学习如何使用它。

![](img/ab50be52a7ee1ccf3b07e5d294ce2b23_1.png)

## 创建示例数据

![](img/ab50be52a7ee1ccf3b07e5d294ce2b23_3.png)

首先，我们需要创建一个示例的DataFrame来演示`groupby`操作。以下代码创建了一个包含`category`和`values`两列的简单DataFrame。

```python
import pandas as pd

data = {
    'category': ['A', 'A', 'A', 'B', 'B', 'B'],
    'values': [10, 30, 50, 20, 40, 60]
}
df = pd.DataFrame(data)
print(df)
```

![](img/ab50be52a7ee1ccf3b07e5d294ce2b23_5.png)

运行上述代码，我们可以看到DataFrame的结构如下：

![](img/ab50be52a7ee1ccf3b07e5d294ce2b23_7.png)

```
  category  values
0        A      10
1        A      30
2        A      50
3        B      20
4        B      40
5        B      60
```

## 基础分组与聚合

![](img/ab50be52a7ee1ccf3b07e5d294ce2b23_9.png)

上一节我们创建了数据，本节中我们来看看如何进行基础的分组。`groupby`方法最常见的用法是结合聚合函数，如`sum()`、`mean()`和`count()`。

![](img/ab50be52a7ee1ccf3b07e5d294ce2b23_11.png)

以下是使用`sum()`函数对`category`列进行分组求和的示例：

![](img/ab50be52a7ee1ccf3b07e5d294ce2b23_12.png)

```python
group_sum = df.groupby('category').sum()
print(group_sum)
```

![](img/ab50be52a7ee1ccf3b07e5d294ce2b23_14.png)

输出结果显示了A组和B组各自`values`的总和：

```
          values
category        
A             90
B            120
```

![](img/ab50be52a7ee1ccf3b07e5d294ce2b23_16.png)

除了求和，我们还可以计算每组的平均值。以下是使用`mean()`函数的示例：

![](img/ab50be52a7ee1ccf3b07e5d294ce2b23_18.png)

```python
group_mean = df.groupby('category').mean()
print(group_mean)
```

输出结果为：

![](img/ab50be52a7ee1ccf3b07e5d294ce2b23_20.png)

```
          values
category        
A           30.0
B           40.0
```

同样，我们可以使用`count()`函数来计算每组有多少条数据：

```python
group_count = df.groupby('category').count()
print(group_count)
```

![](img/ab50be52a7ee1ccf3b07e5d294ce2b23_22.png)

输出结果为：

![](img/ab50be52a7ee1ccf3b07e5d294ce2b23_24.png)

```
          values
category        
A              3
B              3
```

![](img/ab50be52a7ee1ccf3b07e5d294ce2b23_26.png)

## 多列分组

在实际数据分析中，我们经常需要根据多个条件进行分组。例如，数据中可能不仅有类别，还有子类型。

![](img/ab50be52a7ee1ccf3b07e5d294ce2b23_28.png)

假设我们在DataFrame中增加一列`type`：

![](img/ab50be52a7ee1ccf3b07e5d294ce2b23_30.png)

```python
df['type'] = ['X', 'Y', 'X', 'Y', 'X', 'Y']
print(df)
```

新的DataFrame如下：

![](img/ab50be52a7ee1ccf3b07e5d294ce2b23_32.png)

```
  category  values type
0        A      10    X
1        A      30    Y
2        A      50    X
3        B      20    Y
4        B      40    X
5        B      60    Y
```

![](img/ab50be52a7ee1ccf3b07e5d294ce2b23_34.png)

现在，我们可以根据`category`和`type`两列进行分组。以下是按这两列分组后求和的示例：

![](img/ab50be52a7ee1ccf3b07e5d294ce2b23_36.png)

```python
group_multi = df.groupby(['category', 'type']).sum()
print(group_multi)
```

输出结果展示了更细粒度的分组聚合：

![](img/ab50be52a7ee1ccf3b07e5d294ce2b23_38.png)

![](img/ab50be52a7ee1ccf3b07e5d294ce2b23_39.png)

```
                 values
category type          
A        X           60
         Y           30
B        X           40
         Y           80
```

## 其他常用聚合函数

![](img/ab50be52a7ee1ccf3b07e5d294ce2b23_41.png)

`groupby`可以与多种聚合函数结合使用。以下是一些其他常用的函数：

![](img/ab50be52a7ee1ccf3b07e5d294ce2b23_43.png)

*   **`min()`**: 计算每组的最小值。
*   **`max()`**: 计算每组的最大值。
*   **`std()`**: 计算每组的**标准差**。公式为：`std = sqrt(mean(abs(x - x.mean())**2))`。
*   **`var()`**: 计算每组的**方差**。公式为：`var = mean(abs(x - x.mean())**2)`。

![](img/ab50be52a7ee1ccf3b07e5d294ce2b23_45.png)

以下是使用这些函数的示例：

```python
# 最小值
print(df.groupby('category').min())

# 最大值
print(df.groupby('category').max())

# 标准差
print(df.groupby('category').std())

# 方差
print(df.groupby('category').var())
```

![](img/ab50be52a7ee1ccf3b07e5d294ce2b23_47.png)

## 自定义聚合函数

除了内置函数，`groupby`还允许我们使用自定义函数进行聚合，这提供了极大的灵活性。

![](img/ab50be52a7ee1ccf3b07e5d294ce2b23_49.png)

例如，我们想定义一个函数，先计算每组的总和，然后将结果乘以2。可以按以下步骤操作：

![](img/ab50be52a7ee1ccf3b07e5d294ce2b23_51.png)

1.  定义一个自定义函数。
2.  在`groupby`后使用`agg()`（aggregate的缩写）方法应用这个函数。

以下是具体代码：

```python
# 定义自定义聚合函数
def custom_aggregator(series):
    # series 是每个分组下的‘values’列数据
    return series.sum() * 2

![](img/ab50be52a7ee1ccf3b07e5d294ce2b23_53.png)

# 应用自定义函数
custom_result = df.groupby('category').agg(custom_aggregator)
print(custom_result)
```

![](img/ab50be52a7ee1ccf3b07e5d294ce2b23_55.png)

输出结果为：

![](img/ab50be52a7ee1ccf3b07e5d294ce2b23_57.png)

```
          values
category        
A            180
B            240
```

可以看到，A组原始和为90，乘以2后得到180；B组原始和为120，乘以2后得到240。自定义函数成功应用。

## 总结

![](img/ab50be52a7ee1ccf3b07e5d294ce2b23_59.png)

本节课中我们一起学习了Pandas中`groupby`分组的核心操作。我们首先了解了如何根据单列进行基础分组和聚合（求和、平均、计数）。接着，我们探索了如何根据多列进行更复杂的分组。然后，我们介绍了`min`、`max`、`std`、`var`等其他有用的内置聚合函数。最后，我们掌握了如何定义并使用自定义聚合函数来满足特定的分析需求。`groupby`是数据汇总和分析的利器，熟练掌握它将为你的量化金融数据分析打下坚实基础。