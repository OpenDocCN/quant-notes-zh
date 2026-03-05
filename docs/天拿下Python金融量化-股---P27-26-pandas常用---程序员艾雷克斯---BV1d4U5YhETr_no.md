# Python金融量化投资分析与股票交易：P27：pandas常用函数 📊

在本节课中，我们将学习pandas库中一些常用的数据处理函数，包括计算平均值、求和以及数据排序。这些功能是金融数据分析中清洗和准备数据的基础。

![](img/2966026b74d7840335e7b09e048a5020_1.png)

上一节我们介绍了如何处理缺失值，本节中我们来看看如何对数据进行基本的统计计算和排序操作。

## 求平均值

![](img/2966026b74d7840335e7b09e048a5020_3.png)

pandas的`mean`方法用于计算平均值。对于一个DataFrame对象，`mean`方法默认会对每一列（即沿axis=0的方向）计算平均值，并返回一个Series对象。

**公式**：`df.mean(axis=0)`

以下是一个示例：
```python
import pandas as pd
import numpy as np

# 创建一个包含缺失值的示例DataFrame
df = pd.DataFrame({
    'one': [5, 6, 7, np.nan],
    'two': [1, 2, 3, 4]
})
print(df.mean())
```
执行上述代码，会分别计算‘one’列和‘two’列的平均值。‘one’列会忽略缺失值NaN，计算(5+6+7)/3=6；‘two’列计算(1+2+3+4)/4=2.5。

如果想按行计算平均值，需要指定参数`axis=1`。

**公式**：`df.mean(axis=1)`

## 求和

`sum`方法用于计算总和，其用法与`mean`方法类似。默认按列求和（axis=0），指定`axis=1`则按行求和。

**公式**：`df.sum(axis=0)` 或 `df.sum(axis=1)`

## 数据排序

pandas提供了两种主要的排序方式：按值排序和按索引排序。

### 按值排序

使用`sort_values`方法可以按指定列的值进行排序。参数`by`用于指定依据哪一列排序，`ascending`参数控制升序（True）或降序（False）。

**代码**：`df.sort_values(by=‘column_name’, ascending=False)`

当排序的列中存在缺失值NaN时，所有包含NaN的行不会参与排序比较，而是统一被放置在结果的最后。

### 按索引排序

使用`sort_index`方法可以按行索引或列索引的标签进行排序。默认按行索引升序排列。

**代码**：`df.sort_index(ascending=False)`

如果想对列索引进行排序，需要指定参数`axis=1`。

**代码**：`df.sort_index(axis=1, ascending=False)`

以下是两种排序方式的示例列表：

*   **按‘two’列降序排列**：`df.sort_values(by=‘two’, ascending=False)`
*   **按行索引标签降序排列**：`df.sort_index(ascending=False)`
*   **按列索引标签降序排列**：`df.sort_index(axis=1, ascending=False)`

## 其他通用函数

除了上述方法，之前在NumPy库中介绍过的许多通用函数（ufunc）同样适用于pandas对象。这些函数能够高效地对整个Series或DataFrame执行逐元素操作。

以下是一些同样可用的统计函数：

*   `std()`: 计算标准差
*   `var()`: 计算方差
*   `max()`: 找出最大值
*   `min()`: 找出最小值

![](img/2966026b74d7840335e7b09e048a5020_5.png)

本节课中我们一起学习了pandas中用于数据统计和排序的核心函数。我们掌握了如何使用`mean`和`sum`进行基本计算，以及如何使用`sort_values`和`sort_index`对数据进行排序。理解这些方法是进行有效金融数据清洗和分析的重要一步。