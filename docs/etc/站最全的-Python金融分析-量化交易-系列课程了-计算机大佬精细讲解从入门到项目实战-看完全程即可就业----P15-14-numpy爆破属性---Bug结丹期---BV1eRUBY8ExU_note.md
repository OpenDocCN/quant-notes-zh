Python金融分析与量化交易：P15：14 NumPy数组属性

![](img/e75a814a488b536854c863d07146cfdf_1.png)

![](img/e75a814a488b536854c863d07146cfdf_3.png)

![](img/e75a814a488b536854c863d07146cfdf_5.png)

在本节课中，我们将学习NumPy数组的几个核心属性。这些属性可以帮助我们快速了解数组的形状、维度、大小和数据类型，是进行数据操作和分析的基础。

![](img/e75a814a488b536854c863d07146cfdf_7.png)

![](img/e75a814a488b536854c863d07146cfdf_9.png)

上一节我们介绍了NumPy数组的一些常用方法，本节中我们来看看数组的几个关键属性。

![](img/e75a814a488b536854c863d07146cfdf_11.png)

![](img/e75a814a488b536854c863d07146cfdf_13.png)

首先，我们创建一个示例数组来演示这些属性。

![](img/e75a814a488b536854c863d07146cfdf_15.png)

![](img/e75a814a488b536854c863d07146cfdf_17.png)

```python
import numpy as np
arr = np.random.randint(0, 100, size=(5, 6))
```

![](img/e75a814a488b536854c863d07146cfdf_19.png)

![](img/e75a814a488b536854c863d07146cfdf_21.png)

现在，`arr` 是一个5行6列的二维数组，其元素是0到100之间随机生成的整数。

![](img/e75a814a488b536854c863d07146cfdf_23.png)

![](img/e75a814a488b536854c863d07146cfdf_25.png)

以下是NumPy数组的几个常用属性：

![](img/e75a814a488b536854c863d07146cfdf_27.png)

![](img/e75a814a488b536854c863d07146cfdf_29.png)

**1. shape**
`shape` 属性返回数组的形状，即每个维度的大小。
```python
arr.shape  # 输出: (5, 6)
```

![](img/e75a814a488b536854c863d07146cfdf_31.png)

![](img/e75a814a488b536854c863d07146cfdf_33.png)

**2. ndim**
`ndim` 属性返回数组的维度（轴）的数量。
```python
arr.ndim  # 输出: 2
```

![](img/e75a814a488b536854c863d07146cfdf_35.png)

![](img/e75a814a488b536854c863d07146cfdf_37.png)

**3. size**
`size` 属性返回数组中元素的总数。
```python
arr.size  # 输出: 30
```

![](img/e75a814a488b536854c863d07146cfdf_39.png)

**4. dtype**
`dtype` 属性返回数组中元素的数据类型。
```python
arr.dtype  # 输出: dtype('int64')
```

需要注意的是，`dtype` 查看的是数组元素的数据类型。若要查看数组对象本身的类型，应使用 `type(arr)`，它将返回 `numpy.ndarray`。

![](img/e75a814a488b536854c863d07146cfdf_41.png)

接下来，我们重点探讨一下数组的数据类型 `dtype`。

我们可以通过指定 `dtype` 参数在创建数组时定义元素类型。
```python
arr_int32 = np.array([1, 2, 3], dtype='int32')
arr_int32.dtype  # 输出: dtype('int32')
```

![](img/e75a814a488b536854c863d07146cfdf_43.png)

![](img/e75a814a488b536854c863d07146cfdf_45.png)

此外，我们还可以直接修改已有数组的 `dtype` 属性来改变其元素类型。
```python
arr_int32.dtype = 'uint8'
arr_int32.dtype  # 输出: dtype('uint8')
```

这里的 `int32` 表示每个数组元素占用32位内存，而 `uint8` 表示无符号的8位整数（即1个字节）。修改数据类型的一个主要目的是**优化内存使用**。例如，一个包含500万个元素的数组，若使用默认的 `int64` 类型，将占用大量内存。将其改为 `uint8` 可以显著减少内存开销。

![](img/e75a814a488b536854c863d07146cfdf_47.png)

![](img/e75a814a488b536854c863d07146cfdf_49.png)

本节课中我们一起学习了NumPy数组的四个核心属性：`shape`（形状）、`ndim`（维度）、`size`（元素总数）和 `dtype`（数据类型）。理解并熟练使用这些属性，是高效进行数组操作和内存管理的关键步骤。