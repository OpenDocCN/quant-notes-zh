# Python金融分析+量化交易：P9：08 缺失值的清洗

![](img/7c57e7aade23f5b3e46729c629cff825_1.png)

![](img/7c57e7aade23f5b3e46729c629cff825_3.png)

在本节课中，我们将要学习在Python数据分析中，如何使用pandas模块进行数据清洗，特别是针对缺失值的处理。数据清洗是数据分析的必要前提，因为原始数据中可能存在缺失值、重复值或异常值，这些都会干扰最终的分析结果。

## 数据清洗概述

![](img/7c57e7aade23f5b3e46729c629cff825_5.png)

在pandas模块中，数据清洗是常用的分析形式。进行数据分析的必要前提条件，是需要对原始数据进行某种形式的清洗。原始数据中可能会存在缺失值，这里的缺失值指的是空值。在某些常规的指定条件或需求下，原始数据中存在的空值没有意义，并且这些空值如果存在，会干扰整个分析结果的产生。因此，我们需要将原始数据中的这些缺失值进行清洗。清洗意味着我们将其删除。除了删除，还有其他对应的操作，我们将逐步往后看。

![](img/7c57e7aade23f5b3e46729c629cff825_7.png)

![](img/7c57e7aade23f5b3e46729c629cff825_9.png)

除了空值之外，原始数据中还可能产生重复数据或重复值。原始数据中如果产生重复值，那么重复值没有必要进行多次分析或处理。我们可能需要将相关的重复值进行过滤和清洗。

![](img/7c57e7aade23f5b3e46729c629cff825_11.png)

![](img/7c57e7aade23f5b3e46729c629cff825_13.png)

最后一种形式是异常值。由于数据采集的手段不同，采集到的数据中可能会产生某种形式的异常值。异常值也一定会干扰最终分析结论的产生。因此，我们也需要把异常值进行过滤。

![](img/7c57e7aade23f5b3e46729c629cff825_15.png)

在pandas数据清洗的过程中，我们需要清洗如下三种形式的值：第一个是缺失值，第二个是重复值，第三个是异常值。

![](img/7c57e7aade23f5b3e46729c629cff825_17.png)

## 缺失值的处理方式

![](img/7c57e7aade23f5b3e46729c629cff825_19.png)

上一节我们介绍了数据清洗的三种目标，本节中我们来看看如何对缺失值进行清洗。

![](img/7c57e7aade23f5b3e46729c629cff825_21.png)

首先，如果产生了空值，那么空值会有两种形式。之前在讲解Series算术运算时，我们说Series进行算术运算时，只能让索引一致的元素进行算术运算，否则会补空。那时我们补的空是`NaN`形式的空，而不是`None`形式的空。在数据分析中，我们所见到的、所使用的、所产生的空一定是`NaN`形式的空，而不会是`None`的空。

### `None`与`NaN`的区别

![](img/7c57e7aade23f5b3e46729c629cff825_23.png)

接下来我们看一下这两种空的区别。

![](img/7c57e7aade23f5b3e46729c629cff825_25.png)

![](img/7c57e7aade23f5b3e46729c629cff825_27.png)

首先，通过`type`函数查看`None`的类型，会发现它是一个`NoneType`对象。这意味着`None`是一个对象类型。

![](img/7c57e7aade23f5b3e46729c629cff825_29.png)

而在数据分析中能够用到的`NaN`是什么类型呢？通过`np.nan`（需要导入`numpy`模块）可以产生一个`NaN`形式的空。通过`type`查看其类型，会发现`NaN`是浮点类型。

![](img/7c57e7aade23f5b3e46729c629cff825_31.png)

![](img/7c57e7aade23f5b3e46729c629cff825_33.png)

![](img/7c57e7aade23f5b3e46729c629cff825_35.png)

从这我们就能看到这两种形式的空的区别。那么，为什么在数据分析中需要用到浮点类型的空呢？

![](img/7c57e7aade23f5b3e46729c629cff825_37.png)

我们做一个操作：让`None`加上一个整数1。这样的算术运算执行后没有结果，因为类型不一样，对象类型不能加上一个整数类型。

![](img/7c57e7aade23f5b3e46729c629cff825_39.png)

我们换用`np.nan`，让浮点类型的空加上一个整数1。它能运算，并且返回的仍然是`NaN`（空），而不是零。零跟空是不一样的。

![](img/7c57e7aade23f5b3e46729c629cff825_41.png)

因此，在数据分析中，常常会使用某些形式的运算来处理原始数据。如果原始数据中的空值为`NaN`的形式，则不会干扰或中断整个运算流程，因为`NaN`可以参与运算。而`None`是不可以参与运算的。这是这两种空在本质意义上的区别。

![](img/7c57e7aade23f5b3e46729c629cff825_43.png)

也就是说，在数据分析中，如果原始数据中的空是`NaN`的形式，那么在整个运算过程中，即使不进行空值的清洗，也不会中断运算流程。而如果空是`None`，那么在运算过程中是会中断整个运算流程的。因此，在数据分析中，一定要用`NaN`形式的空，而不要用`None`形式的空。

![](img/7c57e7aade23f5b3e46729c629cff825_45.png)

有一个小的特性需要知道：在pandas中，如果遇到了`None`形式的空值，则pandas会将其强制转换成`NaN`的形式。也就是说，如果在pandas的操作过程中遇到了`None`形式也没关系，因为pandas会自动帮我们做强转。我们不需要关注它。但是这两种空本质意义上的区别，你需要知道是怎么回事。

### 清洗缺失值的操作

![](img/7c57e7aade23f5b3e46729c629cff825_47.png)

![](img/7c57e7aade23f5b3e46729c629cff825_49.png)

接下来我们看一下，如果在使用pandas进行数据分析的过程中产生了空值，这些空值该如何进行清洗。

![](img/7c57e7aade23f5b3e46729c629cff825_51.png)

首先，我们导入必要的模块并伪造一组包含空值的数据。

![](img/7c57e7aade23f5b3e46729c629cff825_53.png)

![](img/7c57e7aade23f5b3e46729c629cff825_55.png)

![](img/7c57e7aade23f5b3e46729c629cff825_57.png)

```python
import pandas as pd
import numpy as np
from pandas import DataFrame, Series

![](img/7c57e7aade23f5b3e46729c629cff825_59.png)

# 伪造一组数据，保证其中存在空值
df = DataFrame(np.random.randint(0, 100, size=(8, 6)))
# 将其中某些元素赋值为空值
df.iloc[2, 3] = None
df.iloc[4, 4] = np.nan
df.iloc[5, 2] = None
print(df)
```

![](img/7c57e7aade23f5b3e46729c629cff825_61.png)

现在，我们已经有了一组数据，这组数据中有三个空值。接下来我们需要将这三个空值进行过滤。这里的过滤指的是删除空所在的行数据。不能把空单独删掉，也不能轻易删列，因为列是数据结构。在DataFrame中，通常只能删行，不能删列，除非明确指定删除一个无用的列。

![](img/7c57e7aade23f5b3e46729c629cff825_63.png)

![](img/7c57e7aade23f5b3e46729c629cff825_65.png)

以下是处理缺失值的几种方法。

#### 方式一：使用 `isnull()`/`notnull()` 结合 `any()`/`all()` 删除空行

使用的技术是 `isnull` 或 `notnull`，以及 `any` 和 `all`。使用这几种形式的技术，对空值所对应的行进行过滤。

![](img/7c57e7aade23f5b3e46729c629cff825_67.png)

![](img/7c57e7aade23f5b3e46729c629cff825_69.png)

首先，`df.isnull()` 返回一个布尔值DataFrame，其中`True`表示该位置是空值。`df.notnull()` 则相反。

![](img/7c57e7aade23f5b3e46729c629cff825_71.png)

![](img/7c57e7aade23f5b3e46729c629cff825_73.png)

```python
# 使用 isnull().any(axis=1) 找出存在空值的行
has_null_rows = df.isnull().any(axis=1)
print(has_null_rows)
# 将布尔序列作为行索引，筛选出存在空值的行
null_rows = df.loc[has_null_rows]
print(null_rows)
# 获取这些行的索引
to_drop_index = null_rows.index
print(to_drop_index)
# 使用 drop 方法删除这些行
df_dropped = df.drop(labels=to_drop_index, axis=0)
print(df_dropped)
```

![](img/7c57e7aade23f5b3e46729c629cff825_75.png)

同样，可以使用 `notnull().all(axis=1)` 来找出没有空值的行（即所有值都不为空的行），然后取其反或直接用于筛选。

![](img/7c57e7aade23f5b3e46729c629cff825_77.png)

![](img/7c57e7aade23f5b3e46729c629cff825_79.png)

```python
# 使用 notnull().all(axis=1) 找出没有空值的行
no_null_rows = df.notnull().all(axis=1)
print(no_null_rows)
# 直接筛选出没有空值的行
df_cleaned = df.loc[no_null_rows]
print(df_cleaned)
```

![](img/7c57e7aade23f5b3e46729c629cff825_81.png)

![](img/7c57e7aade23f5b3e46729c629cff825_83.png)

![](img/7c57e7aade23f5b3e46729c629cff825_85.png)

规律是：`isnull()` 通常结合 `any()` 使用，`notnull()` 通常结合 `all()` 使用。通过 `isnull()`、`notnull()`、`any()` 和 `all()` 就可以删除空值所对应的行数据。

![](img/7c57e7aade23f5b3e46729c629cff825_87.png)

#### 方式二：使用 `dropna()` 直接删除缺失值

![](img/7c57e7aade23f5b3e46729c629cff825_89.png)

在pandas中，已经集成了一个函数 `dropna()`，可以直接将缺失的行或列进行删除，一步到位。

![](img/7c57e7aade23f5b3e46729c629cff825_91.png)

![](img/7c57e7aade23f5b3e46729c629cff825_93.png)

```python
# 删除包含空值的行
df_dropped_na = df.dropna(axis=0)
print(df_dropped_na)
# 删除包含空值的列
# df_dropped_na_col = df.dropna(axis=1)
# print(df_dropped_na_col)
```

![](img/7c57e7aade23f5b3e46729c629cff825_95.png)

![](img/7c57e7aade23f5b3e46729c629cff825_97.png)

![](img/7c57e7aade23f5b3e46729c629cff825_99.png)

`axis=0` 表示删除行，`axis=1` 表示删除列。直接使用 `dropna()` 非常方便，但前面的 `isnull()`、`notnull()`、`any()`、`all()` 组合最好也需要掌握。

![](img/7c57e7aade23f5b3e46729c629cff825_101.png)

![](img/7c57e7aade23f5b3e46729c629cff825_103.png)

#### 方式三：使用 `fillna()` 覆盖缺失值

除了删除，还可以对缺失值进行覆盖。将缺失值覆盖之后，就没有空值了。可以使用 `fillna()` 函数进行缺失值的覆盖。

![](img/7c57e7aade23f5b3e46729c629cff825_105.png)

![](img/7c57e7aade23f5b3e46729c629cff825_107.png)

![](img/7c57e7aade23f5b3e46729c629cff825_109.png)

如何覆盖才能更有合理性呢？我们可以使用近邻的值去填充它。例如，用前一分钟或后一分钟采集的数据来填充当前缺失的温度值。这样可以最大合理范围地覆盖空值。

![](img/7c57e7aade23f5b3e46729c629cff825_111.png)

![](img/7c57e7aade23f5b3e46729c629cff825_113.png)

![](img/7c57e7aade23f5b3e46729c629cff825_115.png)

```python
# 使用固定值填充，例如用0填充所有空值
df_filled_value = df.fillna(value=0)
print(df_filled_value)

![](img/7c57e7aade23f5b3e46729c629cff825_117.png)

![](img/7c57e7aade23f5b3e46729c629cff825_119.png)

# 使用向前填充 (ffill) 或向后填充 (bfill)
# 沿行方向（axis=0，默认）向前填充，用上一个非空值填充
df_filled_ffill = df.fillna(method='ffill', axis=0)
print(df_filled_ffill)
# 沿行方向向后填充，用下一个非空值填充
df_filled_bfill = df.fillna(method='bfill', axis=0)
print(df_filled_bfill)

![](img/7c57e7aade23f5b3e46729c629cff825_121.png)

![](img/7c57e7aade23f5b3e46729c629cff825_123.png)

![](img/7c57e7aade23f5b3e46729c629cff825_125.png)

# 沿列方向（axis=1）填充
# 水平方向向前填充
df_filled_ffill_h = df.fillna(method='ffill', axis=1)
print(df_filled_ffill_h)
# 水平方向向后填充
df_filled_bfill_h = df.fillna(method='bfill', axis=1)
print(df_filled_bfill_h)
```

![](img/7c57e7aade23f5b3e46729c629cff825_127.png)

![](img/7c57e7aade23f5b3e46729c629cff825_129.png)

![](img/7c57e7aade23f5b3e46729c629cff825_131.png)

`method='ffill'` 表示向前填充，`method='bfill'` 表示向后填充。`axis` 参数指定填充的方向。

再怎么合理的覆盖，覆盖的值也不是最原始合理的。因此，一般情况下我们选择删除，而不是覆盖。什么时候选择填充呢？如果删除的成本太高（例如，数据行很少，空值行占比很大），就选择覆盖。如果删除成本不高，我们尽可能选择删除。

## 总结

本节课中我们一起学习了在pandas中如何处理缺失值。

我们首先了解了数据清洗的重要性，以及需要清洗的三种数据问题：缺失值、重复值和异常值。

接着，我们重点学习了缺失值的处理。我们区分了 `None` 和 `NaN` 两种空值的本质区别，并理解了在数据分析中使用 `NaN` 的原因。

![](img/7c57e7aade23f5b3e46729c629cff825_133.png)

![](img/7c57e7aade23f5b3e46729c629cff825_135.png)

然后，我们掌握了三种处理缺失值的方法：
1.  使用 `isnull()`/`notnull()` 结合 `any()`/`all()` 来识别并删除包含空值的行。
2.  使用 `dropna()` 函数直接、高效地删除包含空值的行或列。
3.  使用 `fillna()` 函数，通过固定值或前向/后向填充的方式来覆盖空值，并了解了其适用场景（当删除数据成本过高时）。

![](img/7c57e7aade23f5b3e46729c629cff825_137.png)

![](img/7c57e7aade23f5b3e46729c629cff825_139.png)

在实际应用中，需要根据数据的具体情况和分析需求，选择最合适的缺失值处理方式。