# Python金融量化与业务数据分析：P28：2.数据的预处理~1

![](img/c7fa670606a3dafb2cf8d077d2681c3a_1.png)

![](img/c7fa670606a3dafb2cf8d077d2681c3a_3.png)

![](img/c7fa670606a3dafb2cf8d077d2681c3a_5.png)

![](img/c7fa670606a3dafb2cf8d077d2681c3a_7.png)

在本节课中，我们将学习如何对数据进行预处理。数据预处理是数据分析中至关重要的一步，它直接影响到后续分析的准确性和效率。我们将从加载数据开始，逐步完成数据类型的检查、缺失值处理、时间格式转换、数据统计描述以及添加新列等操作。

![](img/c7fa670606a3dafb2cf8d077d2681c3a_9.png)

![](img/c7fa670606a3dafb2cf8d077d2681c3a_11.png)

## 数据的加载

首先，我们需要将数据加载到Python环境中。我们使用`pandas`库的`read_csv`函数来读取一个文本文件。

![](img/c7fa670606a3dafb2cf8d077d2681c3a_13.png)

![](img/c7fa670606a3dafb2cf8d077d2681c3a_15.png)

```python
import pandas as pd

![](img/c7fa670606a3dafb2cf8d077d2681c3a_17.png)

![](img/c7fa670606a3dafb2cf8d077d2681c3a_19.png)

![](img/c7fa670606a3dafb2cf8d077d2681c3a_21.png)

df = pd.read_csv('data/data.txt', header=None, sep='\s+', names=['user_id', 'order_dt', 'order_product', 'order_amount'])
```

![](img/c7fa670606a3dafb2cf8d077d2681c3a_23.png)

**代码解释**：
*   `header=None`：指定原始文件没有表头。
*   `sep='\s+'`：使用正则表达式`\s+`表示以一个或多个空格作为列的分隔符。
*   `names=['user_id', 'order_dt', 'order_product', 'order_amount']`：为读取的四列数据分别指定列名，含义依次为用户ID、购买日期、购买产品数量、消费金额。

执行上述代码后，我们成功将数据加载到名为`df`的`DataFrame`中，并为其赋予了清晰的列名。

![](img/c7fa670606a3dafb2cf8d077d2681c3a_25.png)

![](img/c7fa670606a3dafb2cf8d077d2681c3a_27.png)

## 数据观察与类型检查

![](img/c7fa670606a3dafb2cf8d077d2681c3a_29.png)

![](img/c7fa670606a3dafb2cf8d077d2681c3a_31.png)

上一节我们成功加载了数据，本节中我们来看看如何观察数据的基本情况，特别是检查数据类型和是否存在缺失值。

![](img/c7fa670606a3dafb2cf8d077d2681c3a_33.png)

![](img/c7fa670606a3dafb2cf8d077d2681c3a_35.png)

我们可以使用`info()`方法来快速查看数据集的整体信息。

![](img/c7fa670606a3dafb2cf8d077d2681c3a_37.png)

![](img/c7fa670606a3dafb2cf8d077d2681c3a_39.png)

```python
df.info()
```

![](img/c7fa670606a3dafb2cf8d077d2681c3a_41.png)

![](img/c7fa670606a3dafb2cf8d077d2681c3a_43.png)

**代码解释**：
*   调用`df.info()`会输出`DataFrame`的摘要信息，包括：
    *   每列的非空值数量（`Non-Null Count`），这可以帮助我们判断是否存在缺失值。
    *   每列的数据类型（`Dtype`），例如`int64`（整数）、`float64`（浮点数）、`datetime64`（日期时间）等。

![](img/c7fa670606a3dafb2cf8d077d2681c3a_45.png)

![](img/c7fa670606a3dafb2cf8d077d2681c3a_47.png)

通过观察输出，我们可以确认数据中不存在缺失值，并且了解了每一列当前的数据类型。

![](img/c7fa670606a3dafb2cf8d077d2681c3a_49.png)

## 转换时间数据类型

![](img/c7fa670606a3dafb2cf8d077d2681c3a_51.png)

在数据观察中，我们发现`order_dt`列是整数类型，但它的实际含义是日期。为了后续进行基于时间的分析，我们需要将其转换为`datetime`类型。

![](img/c7fa670606a3dafb2cf8d077d2681c3a_53.png)

以下是转换时间数据类型的步骤：

![](img/c7fa670606a3dafb2cf8d077d2681c3a_55.png)

![](img/c7fa670606a3dafb2cf8d077d2681c3a_57.png)

```python
df['order_dt'] = pd.to_datetime(df['order_dt'], format='%Y%m%d')
```

![](img/c7fa670606a3dafb2cf8d077d2681c3a_59.png)

![](img/c7fa670606a3dafb2cf8d077d2681c3a_61.png)

**代码解释**：
*   `pd.to_datetime()`：`pandas`提供的函数，用于将序列转换为`datetime`类型。
*   `format='%Y%m%d'`：指定原始数据的格式为“年月日”连接的数字格式（例如20230101）。这能确保转换的准确性。

![](img/c7fa670606a3dafb2cf8d077d2681c3a_63.png)

转换完成后，再次使用`df.info()`查看，可以看到`order_dt`列的数据类型已变为`datetime64[ns]`。

![](img/c7fa670606a3dafb2cf8d077d2681c3a_65.png)

![](img/c7fa670606a3dafb2cf8d077d2681c3a_67.png)

## 查看数据统计描述

![](img/c7fa670606a3dafb2cf8d077d2681c3a_69.png)

![](img/c7fa670606a3dafb2cf8d077d2681c3a_71.png)

为了对数据的分布有一个直观的了解，我们可以使用`describe()`方法查看数值型数据的统计摘要。

![](img/c7fa670606a3dafb2cf8d077d2681c3a_73.png)

![](img/c7fa670606a3dafb2cf8d077d2681c3a_75.png)

![](img/c7fa670606a3dafb2cf8d077d2681c3a_77.png)

```python
df.describe()
```

![](img/c7fa670606a3dafb2cf8d077d2681c3a_79.png)

![](img/c7fa670606a3dafb2cf8d077d2681c3a_81.png)

**代码解释**：
*   `df.describe()`：默认只对数值型列（`int64`， `float64`）进行计算。
*   它会返回一个包含以下统计量的表格：
    *   `count`：非空值数量。
    *   `mean`：平均值。
    *   `std`：标准差。
    *   `min`：最小值。
    *   `25%`：第一四分位数。
    *   `50%`：中位数。
    *   `75%`：第三四分位数。
    *   `max`：最大值。

![](img/c7fa670606a3dafb2cf8d077d2681c3a_83.png)

通过这个表格，我们可以快速了解数据的集中趋势和离散程度。例如，可以得知用户平均购买商品数量约为2.4个，平均消费金额约为35元。

![](img/c7fa670606a3dafb2cf8d077d2681c3a_85.png)

## 在数据中添加月份列

![](img/c7fa670606a3dafb2cf8d077d2681c3a_87.png)

在时间序列分析中，经常需要按月份进行聚合分析。原始数据只有具体的日期，没有单独的月份列。因此，我们需要从`order_dt`列中提取月份信息，并创建新列。

![](img/c7fa670606a3dafb2cf8d077d2681c3a_89.png)

以下是添加月份列的步骤：

![](img/c7fa670606a3dafb2cf8d077d2681c3a_91.png)

![](img/c7fa670606a3dafb2cf8d077d2681c3a_93.png)

```python
df['month'] = df['order_dt'].astype('datetime64[M]')
```

**代码解释**：
*   `df['order_dt'].astype('datetime64[M]')`：将`order_dt`列的数据类型转换为“月份”精度的`datetime`类型。`[M]`表示月份。
*   转换后，日期信息会被规整到当月的第一天（例如，2023-01-15会变为2023-01-01），但本质上我们只关心月份部分。
*   将结果赋值给名为`month`的新列，该列就代表了每条消费记录发生的月份。

![](img/c7fa670606a3dafb2cf8d077d2681c3a_95.png)

![](img/c7fa670606a3dafb2cf8d077d2681c3a_97.png)

![](img/c7fa670606a3dafb2cf8d077d2681c3a_99.png)

除了`[M]`，还可以使用`[Y]`获取年份，`[D]`获取日期，这为灵活处理时间数据提供了便利。

![](img/c7fa670606a3dafb2cf8d077d2681c3a_101.png)

![](img/c7fa670606a3dafb2cf8d077d2681c3a_103.png)

本节课中我们一起学习了数据预处理的核心步骤。我们从加载原始数据开始，通过指定分隔符和列名使其结构清晰。接着，我们检查了数据类型和缺失值情况。然后，我们将表示日期的整数列转换为便于分析的`datetime`类型。之后，我们使用`describe()`方法查看了数据的统计概况。最后，我们从日期列中提取出月份信息，创建了一个新的分析维度。这些步骤为后续的深入分析奠定了坚实的基础。