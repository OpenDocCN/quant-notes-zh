# Python金融量化：P8：02 使用Pandas数组实现金融数据高效计算（一）📊

![](img/f3a4302f49445d3a19cbb99e3db87413_0.png)

![](img/f3a4302f49445d3a19cbb99e3db87413_2.png)

![](img/f3a4302f49445d3a19cbb99e3db87413_4.png)

![](img/f3a4302f49445d3a19cbb99e3db87413_6.png)

## 概述
在本节课中，我们将要学习Python数据分析的核心库——Pandas。我们将重点介绍Pandas的两种核心数据结构：Series和DataFrame，并学习如何创建它们、读取外部金融数据文件，以及进行数据预处理，为后续的金融分析打下坚实基础。

上一讲我们介绍了NumPy数组，它结合SciPy和Matplotlib使用效果非常强大。Pandas是Python中一个专门用于数据分析的模块，也是数据分析的利器之一。在处理格式相对规整的金融数据时，例如从Wind等终端导出的数据，使用Pandas进行读取和分析会非常快捷方便。

![](img/f3a4302f49445d3a19cbb99e3db87413_8.png)

![](img/f3a4302f49445d3a19cbb99e3db87413_10.png)

## Pandas数据结构介绍

### Series数据结构
首先，我们来介绍Series这种数据结构。Series可以理解为一个带标签的一维数组，特别类似于金融学中的时间序列结构，它通常以时间作为索引标签。

我们导入Pandas的方法是 `import pandas as pd`，之后调用模块只需输入 `pd.` 即可。

以下是创建Series的几种方法：

*   **通过列表创建**：默认索引为从0开始的整数。
    ```python
    import pandas as pd
    S1 = pd.Series(['A', 'B', 'C', 'D'])
    ```
*   **创建时指定索引**：可以自定义索引标签。
    ```python
    S2 = pd.Series(['A', 'B', 'C', 'D'], index=['一', '二', '三', '四'])
    ```
*   **通过字典创建**：字典的键（key）将成为Series的索引，值（value）成为序列的值。
    ```python
    S3 = pd.Series({'A': 1, 'B': 2, 'C': 3, 'D': 4})
    ```

![](img/f3a4302f49445d3a19cbb99e3db87413_12.png)

Series有两个常用属性：`index` 和 `values`。
*   `index` 属性返回索引。
*   `values` 属性返回值。

![](img/f3a4302f49445d3a19cbb99e3db87413_14.png)

```python
S1.index  # 返回 RangeIndex(start=0, stop=4, step=1)
S1.values # 返回 array(['A', 'B', 'C', 'D'], dtype=object)
```

### DataFrame数据结构
上一节我们介绍了Series，它类似于一列数据。本节中我们来看看DataFrame，它是一种表格型的数据结构，非常类似于Excel中的表格，有行和列。

创建DataFrame也有多种方式：

*   **通过列表或NumPy数组创建**：可以传递二维列表或数组。
    ```python
    data = [['a', 'A'], ['b', 'B'], ['c', 'C'], ['d', 'D']]
    DF1 = pd.DataFrame(data)
    ```
*   **创建时指定列名和索引**：使用 `columns` 参数指定列名，使用 `index` 参数指定行索引。
    ```python
    DF2 = pd.DataFrame(data,
                       columns=['lower_case', 'upper_case'],
                       index=['1', '2', '3', '4'])
    ```
*   **通过字典创建**：字典的键将成为列名，值（应为列表）成为该列的数据。
    ```python
    data_dict = {'lower_case': ['a', 'b', 'c', 'd'],
                 'upper_case': ['A', 'B', 'C', 'D']}
    DF3 = pd.DataFrame(data_dict, index=['3', '4', '5', '6'])
    ```

与Series类似，DataFrame也有 `index`、`columns` 和 `values` 属性。
```python
DF2.index    # 获取行索引
DF2.columns  # 获取列名
DF2.values   # 获取底层数据（NumPy数组）
```

## 读取外部金融数据
掌握了数据结构的基本创建后，我们需要学习如何将外部的金融数据读入Pandas。金融数据通常以Excel、CSV或TXT格式存储。

以下是读取不同类型文件的方法：

![](img/f3a4302f49445d3a19cbb99e3db87413_16.png)

*   **读取TXT文件**：使用 `pd.read_table()` 函数。关键参数包括 `filepath`（文件路径）和 `sep`（分隔符）。对于从Wind导出的数据，可能需要注意表头问题，可以使用 `header=None` 参数，后续再重命名列。
    ```python
    DF_txt = pd.read_table(‘data/PE_TTM.txt', header=None, sep=‘\t’)
    ```
*   **读取Excel文件**：使用 `pd.read_excel()` 函数。除了文件路径，常用参数有 `sheet_name`（指定工作表）、`header`（指定表头行）、`skiprows`（跳过开头行数）和 `skipfooter`（跳过末尾行数）。
    ```python
    DF_excel = pd.read_excel(‘data/行业指数.xlsx', skiprows=1, skipfooter=2)
    ```
*   **读取CSV文件**：使用 `pd.read_csv()` 函数。参数与 `read_table` 类似。有时需要指定 `engine=‘python’` 来解决编码问题。
    ```python
    DF_csv = pd.read_csv(‘data/行业指数_PE_TTM.csv', engine=‘python’, skiprows=1)
    ```

![](img/f3a4302f49445d3a19cbb99e3db87413_18.png)

## 数据预览与描述性统计
数据读取后，我们需要快速了解其概况。Pandas提供了便捷的方法。

*   **预览数据**：使用 `.head(n)` 查看前n行，使用 `.tail(n)` 查看后n行。
    ```python
    DF_csv.head(5)  # 查看前5行
    ```
*   **查看维度**：使用 `.shape` 属性获取数据形状（行数，列数）。
    ```python
    DF_csv.shape  # 返回 (行数， 列数)
    ```
*   **查看信息**：使用 `.info()` 方法查看数据类型、非空值数量等摘要信息。
    ```python
    DF_csv.info()
    ```
*   **描述性统计**：使用 `.describe()` 方法快速计算数值型列的基本统计量，如计数、均值、标准差、分位数等。这能极大提升分析效率。
    ```python
    DF_csv.describe()
    ```
    可以设置Pandas的显示选项，例如 `pd.set_option(‘display.max_rows’， 30)` 来限制最大显示行数。

## 数据预处理
在实际分析中，大量时间花费在数据清洗和预处理上。主要包括缺失值、重复值和异常值的处理。

### 缺失值处理
首先，我们创建一个包含缺失值的DataFrame。在Pandas中，缺失值通常用 `NaN`（Not a Number）表示，可以使用 `np.nan` 来创建。

```python
import numpy as np
data = {‘编号’: [‘A1’， ‘A2’， np.nan， ‘A4’],
        ‘性别’: [‘男’， np.nan， np.nan， ‘男’],
        ‘注册时间’: [‘2018-08-08’， ‘2018-08-09’， np.nan， ‘2018-08-11’]}
DF = pd.DataFrame(data)
```

*   **查看缺失值**：使用 `.isna()` 方法，返回布尔型DataFrame，缺失位置为True。
    ```python
    DF.isna()
    ```
*   **删除缺失值**：使用 `.dropna()` 方法。参数 `how=‘any’`（默认）表示某行任意列有缺失即删除；`how=‘all’` 表示某行所有列都为缺失才删除。
    ```python
    DF_dropped = DF.dropna(how=‘any’)
    ```
*   **填充缺失值**：使用 `.fillna()` 方法。可以用单一值填充，也可以用字典指定不同列用不同值填充。
    ```python
    # 用0填充所有缺失值
    DF_filled = DF.fillna(0)
    # 分列填充
    fill_dict = {‘编号’: ‘A’， ‘性别’: ‘男’， ‘注册时间’: ‘2018-08-10’}
    DF_filled_custom = DF.fillna(fill_dict)
    ```

### 重复值处理
重复值在数据记录中也很常见。

```python
data_dup = {‘order’: [‘A1’， ‘A1’， ‘A2’， ‘A3’， ‘A3’， ‘A4’， ‘A4’],
            ‘name’: [‘Alice’， ‘Alice’， ‘Bob’， ‘Jack’， ‘Jack’， ‘Luna’， ‘Luna’],
            ‘date’: [‘2018-08-08’， ‘2018-08-09’， ‘2018-08-10’， ‘2018-08-10’，
                     ‘2018-08-11’， ‘2018-08-11’， ‘2018-08-12’]}
DF_dup = pd.DataFrame(data_dup)
```

*   **删除重复值**：使用 `.drop_duplicates()` 方法。参数 `subset` 指定依据哪些列判断重复；`keep=‘first’`（默认）保留第一次出现的，`keep=‘last’` 保留最后一次出现的。
    ```python
    # 删除所有列完全重复的行
    DF_nodup = DF_dup.drop_duplicates()
    # 仅根据‘order’列删除重复，保留最后一个
    DF_nodup_order = DF_dup.drop_duplicates(subset=[‘order’]， keep=‘last’)
    ```

### 数据类型转换与索引设置
有时需要调整列的数据类型以方便计算。

*   **查看与转换数据类型**：使用 `.dtype` 查看列类型，使用 `.astype()` 进行转换。
    ```python
    # 假设‘code’列是整数，转换为浮点数
    DF[‘code’] = DF[‘code’].astype(‘float64’)
    ```
*   **设置索引**：使用 `.set_index()` 方法将某一列设置为索引，`inplace=True` 表示在原数据上修改。
    ```python
    # 将‘日期’列设为索引
    DF.set_index(‘日期’， inplace=True)
    ```
*   **层次化索引**：可以将多列设置为索引，形成层次化（MultiIndex）结构。
    ```python
    DF_multi = DF.set_index([‘code’， ‘name’])
    ```
*   **重命名索引/列名**：使用 `.rename()` 方法，传入字典 `{旧名： 新名}`。
    ```python
    DF_renamed = DF.rename(columns={‘order’: ‘新订单号’， ‘name’: ‘姓名’})
    ```
*   **重置索引**：使用 `.reset_index()` 将层次化索引恢复为平坦的默认整数索引。
    ```python
    DF_flat = DF_multi.reset_index()
    ```

![](img/f3a4302f49445d3a19cbb99e3db87413_20.png)

![](img/f3a4302f49445d3a19cbb99e3db87413_21.png)

## 总结
本节课我们一起学习了Pandas库的核心基础。我们首先认识了Series和DataFrame这两种关键的数据结构，并学会了如何创建它们。接着，我们掌握了从TXT、Excel和CSV文件读取金融数据的实用技能。然后，我们深入探讨了数据预处理的关键步骤，包括缺失值的查找、删除与填充，重复值的识别与处理，以及数据类型的转换和索引的灵活设置。这些操作是进行任何实质性金融量化分析前必不可少的准备工作，熟练掌握它们能显著提升数据处理的效率与准确性。在下一讲中，我们将基于清洗好的数据，学习如何进行数据的选择、切片以及更复杂的计算。