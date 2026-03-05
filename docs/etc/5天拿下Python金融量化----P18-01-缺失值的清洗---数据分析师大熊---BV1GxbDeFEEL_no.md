# Python金融量化：P18：01 缺失值的清洗 📊

![](img/4e94a02145142b7d45da849a0cf7c0c3_1.png)

在本节课中，我们将要学习在Python数据分析中，如何使用pandas模块进行数据清洗，特别是如何处理数据中的缺失值。数据清洗是数据分析的必要前提，因为原始数据中可能存在缺失值、重复值或异常值，这些都会干扰最终的分析结果。本节我们将重点探讨缺失值的识别与处理方法。

---

![](img/4e94a02145142b7d45da849a0cf7c0c3_3.png)

## 缺失值的概念与重要性

![](img/4e94a02145142b7d45da849a0cf7c0c3_5.png)

数据清洗是pandas模块中常用的分析形式。进行数据分析的一个必要前提条件，是需要对原始数据进行某种形式的清洗。

为什么需要清洗呢？因为在我们的原始数据中，可能会存在缺失值。这里的缺失值指的就是空值。

![](img/4e94a02145142b7d45da849a0cf7c0c3_7.png)

在某些常规的指定条件或需求下，原始数据中存在的空值是没有意义的。并且这些空值如果存在，反而会干扰整个分析结果的产生。

![](img/4e94a02145142b7d45da849a0cf7c0c3_9.png)

因此，我们需要将原始数据中的这些缺失值（即空值）进行某种形式的清洗。清洗意味着我们可以将其删除，或者进行其他操作。

在pandas数据清洗的过程中，我们需要清洗如下三种形式的值：
1.  缺失值
2.  重复值
3.  异常值

![](img/4e94a02145142b7d45da849a0cf7c0c3_11.png)

![](img/4e94a02145142b7d45da849a0cf7c0c3_13.png)

接下来，我们首先看一下缺失值（即空值）的处理方式。

![](img/4e94a02145142b7d45da849a0cf7c0c3_15.png)

---

![](img/4e94a02145142b7d45da849a0cf7c0c3_17.png)

## 两种空值：`None` 与 `np.nan`

如果产生了空值，那么空值会有两种形式。我们之前在讲解Series算术运算时提到，Series进行算术运算时，只能让索引一致的元素进行运算，否则会补空。那时我们补的空是`np.nan`形式的空，而不是`None`形式的空。

在数据分析中，我们所见到的、所使用的、所产生的空一定是`np.nan`形式的空，而不会是`None`的空。接下来我们看一下这两种空的区别。

![](img/4e94a02145142b7d45da849a0cf7c0c3_19.png)

首先，我们通过`type`查看`None`的类型。

![](img/4e94a02145142b7d45da849a0cf7c0c3_21.png)

```python
type(None)
```

`None`的数据类型是一个对象类型。

而我们在数据分析中能够用到的`np.nan`是怎样的类型呢？我们通过`np`模块中的`nan`属性可以产生一个`np.nan`形式的空。

![](img/4e94a02145142b7d45da849a0cf7c0c3_23.png)

```python
import numpy as np
np.nan
type(np.nan)
```

这个`np.nan`形式的空是浮点类型。

![](img/4e94a02145142b7d45da849a0cf7c0c3_25.png)

从这我们就能看到这两种形式的空的区别。那么，为什么在数据分析中需要用到浮点类型的空呢？

![](img/4e94a02145142b7d45da849a0cf7c0c3_27.png)

![](img/4e94a02145142b7d45da849a0cf7c0c3_29.png)

我们来看一个运算示例。让对象类型的空加上一个整数。

```python
None + 1
```

![](img/4e94a02145142b7d45da849a0cf7c0c3_31.png)

这个运算没有结果，因为类型不同，对象类型不能加上整数类型。

![](img/4e94a02145142b7d45da849a0cf7c0c3_33.png)

![](img/4e94a02145142b7d45da849a0cf7c0c3_35.png)

现在我们换用浮点类型的空加上一个整数。

![](img/4e94a02145142b7d45da849a0cf7c0c3_37.png)

```python
np.nan + 1
```

![](img/4e94a02145142b7d45da849a0cf7c0c3_39.png)

浮点类型的空加上任意值，返回的都是空（`nan`），而不是零。零和空是不一样的。

因此，在数据分析中，常常会使用某些形式的运算来处理原始数据。如果原始数据中的空值为`np.nan`的形式，则不会干扰或中断整个运算流程，因为`np.nan`可以参与运算。而`None`是不可以参与运算的。

![](img/4e94a02145142b7d45da849a0cf7c0c3_41.png)

这是这两种空在本质意义上的区别。也就是说，在数据分析中，如果原始数据中的空是`np.nan`的形式，那么在整个运算过程中，即使不进行空值的清洗，也不会中断运算流程。而如果你的空是`None`，那么在运算过程中是会中断整个运算流程的。

所以，在数据分析中，一定要用`np.nan`形式的空，而不要用`None`形式的空。

![](img/4e94a02145142b7d45da849a0cf7c0c3_43.png)

这里有一个小特性需要知道：在pandas中，如果遇到了`None`形式的空值，则pandas会将其强制转换成`np.nan`的形式。也就是说，如果在pandas的操作过程中遇到了`None`形式也没关系，因为pandas会自动帮我们做强转。我们不需要特别关注它。但是这两种空本质意义上的区别，你需要知道是怎么回事。

---

![](img/4e94a02145142b7d45da849a0cf7c0c3_45.png)

## 缺失值的清洗方法

接下来我们看一下，如果在使用pandas进行数据分析的过程中产生了空值，那么这些空值该如何进行清洗。

首先，我们导入必要的模块并伪造一组包含空值的数据。

![](img/4e94a02145142b7d45da849a0cf7c0c3_47.png)

![](img/4e94a02145142b7d45da849a0cf7c0c3_49.png)

```python
import pandas as pd
import numpy as np
from pandas import DataFrame, Series

# 伪造一组数据，保证其中存在空值
df = DataFrame(np.random.randint(0, 100, size=(8, 6)))
# 将某些元素赋值为空值
df.iloc[2, 3] = None
df.iloc[4, 4] = np.nan
df.iloc[5, 2] = None
print(df)
```

![](img/4e94a02145142b7d45da849a0cf7c0c3_51.png)

现在我们已经有一组数据，其中包含三个空值。接下来我们需要将这三个空值进行过滤清洗。

![](img/4e94a02145142b7d45da849a0cf7c0c3_53.png)

### 方式一：删除缺失值所在的行

![](img/4e94a02145142b7d45da849a0cf7c0c3_55.png)

对空值进行过滤，这里的过滤指的是删除空值所在的行数据。不能把空值单独删掉，因为删除单个位置后，数据结构会被破坏。在DataFrame中，通常只能删除行，不能随意删除列，因为列是数据结构的一部分，除非明确指定删除无用的列。

![](img/4e94a02145142b7d45da849a0cf7c0c3_57.png)

![](img/4e94a02145142b7d45da849a0cf7c0c3_59.png)

使用的技术是`isnull()`、`notnull()`结合`any()`和`all()`函数，对空值所对应的行进行过滤。

首先，我们使用`isnull()`来检测数据中的每一个元素是否为空值。

![](img/4e94a02145142b7d45da849a0cf7c0c3_61.png)

```python
df.isnull()
```

`isnull()`返回一个布尔值DataFrame，`True`表示该位置是空值。我们可以看到有三个`True`，它们对应的位置就是空值。

![](img/4e94a02145142b7d45da849a0cf7c0c3_63.png)

![](img/4e94a02145142b7d45da849a0cf7c0c3_65.png)

`notnull()`与`isnull()`刚好相反。

```python
df.notnull()
```

`notnull()`返回的布尔值DataFrame中，`False`对应的位置是空值。

我们最终是想把空值所对应的行删掉。我们需要知道哪些行中存在`True`（对于`isnull`）或`False`（对于`notnull`）。这时可以使用`any()`或`all()`函数。

`any()`的作用是检测行或列中是否存在`True`，如果有就返回`True`，否则返回`False`。

![](img/4e94a02145142b7d45da849a0cf7c0c3_67.png)

![](img/4e94a02145142b7d45da849a0cf7c0c3_69.png)

```python
# 结合 isnull 和 any，找出存在空值的行
df.isnull().any(axis=1)
```

![](img/4e94a02145142b7d45da849a0cf7c0c3_71.png)

返回的布尔值Series中，`True`表示该行存在空值（第2、4、5行）。

![](img/4e94a02145142b7d45da849a0cf7c0c3_73.png)

我们可以将这组布尔值作为原数据的行索引，来筛选出存在空值的行。

![](img/4e94a02145142b7d45da849a0cf7c0c3_75.png)

```python
# 获取存在空值的行数据
rows_with_nan = df.loc[df.isnull().any(axis=1)]
print(rows_with_nan)
```

然后，我们可以获取这些行的索引，并使用`drop`方法删除它们。

![](img/4e94a02145142b7d45da849a0cf7c0c3_77.png)

![](img/4e94a02145142b7d45da849a0cf7c0c3_79.png)

```python
# 获取要删除的行索引
index_to_drop = rows_with_nan.index
# 删除这些行
df_cleaned = df.drop(labels=index_to_drop, axis=0)
print(df_cleaned)
```

![](img/4e94a02145142b7d45da849a0cf7c0c3_81.png)

![](img/4e94a02145142b7d45da849a0cf7c0c3_83.png)

同样，我们也可以使用`notnull()`结合`all()`来实现。`all()`的作用是检测行或列中是否全部为`True`，是则返回`True`，否则返回`False`。

![](img/4e94a02145142b7d45da849a0cf7c0c3_85.png)

```python
# 结合 notnull 和 all，找出没有空值的行（即所有值都不为空）
rows_without_nan = df.loc[df.notnull().all(axis=1)]
print(rows_without_nan)
```

这样就直接过滤掉了包含空值的行。

![](img/4e94a02145142b7d45da849a0cf7c0c3_87.png)

总结规律：
*   `isnull()` 通常结合 `any()` 使用，来找出存在空值的行。
*   `notnull()` 通常结合 `all()` 使用，来找出没有空值的行。

![](img/4e94a02145142b7d45da849a0cf7c0c3_89.png)

通过 `isnull()`/`notnull()` 和 `any()`/`all()` 就可以删除空值所对应的行数据。

![](img/4e94a02145142b7d45da849a0cf7c0c3_91.png)

![](img/4e94a02145142b7d45da849a0cf7c0c3_93.png)

### 方式二：使用 `dropna()` 函数直接删除

在pandas中，有一个更简单的方式，已经集成了一个函数叫做`dropna()`，可以直接将缺失的行或列进行删除。

![](img/4e94a02145142b7d45da849a0cf7c0c3_95.png)

![](img/4e94a02145142b7d45da849a0cf7c0c3_97.png)

```python
# 删除包含空值的行 (axis=0 表示行)
df_cleaned_direct = df.dropna(axis=0)
print(df_cleaned_direct)

![](img/4e94a02145142b7d45da849a0cf7c0c3_99.png)

# 删除包含空值的列 (axis=1 表示列)
# df_cleaned_columns = df.dropna(axis=1)
```

![](img/4e94a02145142b7d45da849a0cf7c0c3_101.png)

![](img/4e94a02145142b7d45da849a0cf7c0c3_103.png)

使用`dropna()`一步到位，无需再使用`isnull()`、`any()`等组合。但是，前面的`isnull()`、`any()`等方法最好也需要掌握，以理解其原理。

---

## 缺失值的覆盖（填充）

![](img/4e94a02145142b7d45da849a0cf7c0c3_105.png)

![](img/4e94a02145142b7d45da849a0cf7c0c3_107.png)

除了删除，缺失值还可以被覆盖（填充）。这是另一种处理缺失值的方式。

![](img/4e94a02145142b7d45da849a0cf7c0c3_109.png)

![](img/4e94a02145142b7d45da849a0cf7c0c3_111.png)

将缺失值覆盖之后，空值就被替换了，数据集中就没有空值了。那么，如何覆盖才能更有合理性呢？我们可以使用`fillna()`函数进行缺失值的覆盖。

![](img/4e94a02145142b7d45da849a0cf7c0c3_113.png)

例如，我们手动用一个固定值（如0）来填充所有空值。

![](img/4e94a02145142b7d45da849a0cf7c0c3_115.png)

![](img/4e94a02145142b7d45da849a0cf7c0c3_117.png)

```python
df_filled = df.fillna(value=0)
print(df_filled)
```

![](img/4e94a02145142b7d45da849a0cf7c0c3_119.png)

![](img/4e94a02145142b7d45da849a0cf7c0c3_121.png)

但这样填充意义可能不大。更好的方式是，让空值用其周围邻近的值去填充。例如，在时间序列数据中，某一分钟的温度数据缺失，我们可以用前一分钟或后一分钟的温度来填充它。用近邻的值替换它，可以在最大合理范围内覆盖我们的空值。

![](img/4e94a02145142b7d45da849a0cf7c0c3_123.png)

`fillna()`函数提供了这种功能。

![](img/4e94a02145142b7d45da849a0cf7c0c3_125.png)

![](img/4e94a02145142b7d45da849a0cf7c0c3_127.png)

```python
# 向前填充 (ffill)，用前面的值填充空值
df_ffill = df.fillna(method='ffill', axis=0) # 沿行方向（垂直）向前填充
print(df_ffill)

![](img/4e94a02145142b7d45da849a0cf7c0c3_129.png)

![](img/4e94a02145142b7d45da849a0cf7c0c3_131.png)

# 向后填充 (bfill)，用后面的值填充空值
df_bfill = df.fillna(method='bfill', axis=0) # 沿行方向（垂直）向后填充
print(df_bfill)

# 也可以沿列方向（水平）填充
df_ffill_h = df.fillna(method='ffill', axis=1) # 沿列方向（水平）向前填充
print(df_ffill_h)
```

`method='ffill'`表示向前填充，`method='bfill'`表示向后填充。`axis`参数指定填充的方向（0为行/垂直方向，1为列/水平方向）。

需要注意的是，再怎么合理的填充，覆盖的值也不是原始的真实值。因此，一般情况下我们优先选择删除。那么，什么时候选择填充呢？

如果删除的成本太高，就选择填充。什么叫删除成本太高？例如，一共有10行数据，空值存在的行有6行，如果删除就只剩下4行数据，这会导致信息大量丢失，删除成本太高。此时，就应该选择填充。如果删除成本不高（例如空值行很少），我们尽可能选择删除。

---

## 总结

本节课中，我们一起学习了在Python金融量化分析中，如何使用pandas进行缺失值的数据清洗。

我们首先了解了数据清洗的重要性，以及缺失值、重复值、异常值是三种主要需要清洗的数据问题。

接着，我们深入探讨了两种空值`None`与`np.nan`的本质区别，明白了在数据分析中应使用`np.nan`，因为它可以参与运算而不中断流程。

![](img/4e94a02145142b7d45da849a0cf7c0c3_133.png)

![](img/4e94a02145142b7d45da849a0cf7c0c3_135.png)

然后，我们学习了两种处理缺失值的主要方法：
1.  **删除**：通过`isnull().any()`、`notnull().all()`组合逻辑筛选，或直接使用`dropna()`函数，删除包含缺失值的行或列。
2.  **填充**：使用`fillna()`函数，可以用固定值填充，也可以用向前(`ffill`)或向后(`bfill`)填充的方式，用邻近的值进行合理替换。

最后，我们了解到，在数据清洗实践中，如果缺失值较少，删除成本低，通常选择删除；如果缺失值较多，删除会导致数据量锐减，则应考虑使用填充方法。

![](img/4e94a02145142b7d45da849a0cf7c0c3_137.png)

![](img/4e94a02145142b7d45da849a0cf7c0c3_139.png)

掌握了缺失值的清洗，就为后续进行准确的数据分析和建模打下了坚实的基础。下一节，我们将继续学习如何处理数据中的重复值。