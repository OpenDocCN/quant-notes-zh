# Python金融量化与业务数据分析：P22：pandas高级操作之映射

![](img/342b72a0859967b78d5c411462edf4b3_1.png)

![](img/342b72a0859967b78d5c411462edf4b3_3.png)

![](img/342b72a0859967b78d5c411462edf4b3_5.png)

![](img/342b72a0859967b78d5c411462edf4b3_7.png)

![](img/342b72a0859967b78d5c411462edf4b3_9.png)

在本节课中，我们将要学习pandas库中的几个高级操作，包括数据替换、映射、随机抽样和分类处理。这些操作在数据清洗和预处理阶段非常有用，能够帮助我们更高效地处理和分析数据。

![](img/342b72a0859967b78d5c411462edf4b3_11.png)

![](img/342b72a0859967b78d5c411462edf4b3_13.png)

## 替换操作

![](img/342b72a0859967b78d5c411462edf4b3_15.png)

![](img/342b72a0859967b78d5c411462edf4b3_17.png)

上一节我们介绍了pandas的基础操作，本节中我们来看看如何使用`replace`函数对DataFrame或Series中的指定元素进行替换。

![](img/342b72a0859967b78d5c411462edf4b3_19.png)

![](img/342b72a0859967b78d5c411462edf4b3_21.png)

![](img/342b72a0859967b78d5c411462edf4b3_23.png)

![](img/342b72a0859967b78d5c411462edf4b3_25.png)

`replace`函数可以将数据中的特定值替换为另一个值。其基本语法是 `df.replace(to_replace, value)`，其中`to_replace`是要被替换的值，`value`是替换后的新值。

![](img/342b72a0859967b78d5c411462edf4b3_27.png)

![](img/342b72a0859967b78d5c411462edf4b3_29.png)

首先，我们导入必要的库并创建一个示例DataFrame。

![](img/342b72a0859967b78d5c411462edf4b3_31.png)

![](img/342b72a0859967b78d5c411462edf4b3_33.png)

![](img/342b72a0859967b78d5c411462edf4b3_35.png)

```python
import pandas as pd
import numpy as np

![](img/342b72a0859967b78d5c411462edf4b3_37.png)

# 创建一个5行6列的随机整数DataFrame
df = pd.DataFrame(np.random.randint(0, 100, size=(5, 6)))
print(df)
```

![](img/342b72a0859967b78d5c411462edf4b3_39.png)

![](img/342b72a0859967b78d5c411462edf4b3_41.png)

![](img/342b72a0859967b78d5c411462edf4b3_43.png)

![](img/342b72a0859967b78d5c411462edf4b3_45.png)

以下是`replace`函数的几种用法：

![](img/342b72a0859967b78d5c411462edf4b3_47.png)

*   **基本替换**：将DataFrame中所有的特定值替换为新值。
    ```python
    # 将所有的整数2替换为字符串‘two’
    df_replaced = df.replace(2, ‘two’)
    print(df_replaced)
    ```
*   **使用字典进行多值替换**：可以一次性替换多个值。
    ```python
    # 将1替换为‘one’，将3替换为‘three’
    df_replaced = df.replace({1: ‘one’, 3: ‘three’})
    print(df_replaced)
    ```
*   **指定列替换**：只替换某一列中的特定值。
    ```python
    # 只将第4列（索引为3）中的5替换为‘five’
    df_replaced = df.replace({3: {5: ‘five’}})
    print(df_replaced)
    ```

![](img/342b72a0859967b78d5c411462edf4b3_49.png)

`replace`函数同样可以作用于Series对象，用法类似。

![](img/342b72a0859967b78d5c411462edf4b3_51.png)

## 映射操作

![](img/342b72a0859967b78d5c411462edf4b3_53.png)

替换操作是将一个值直接改为另一个值。接下来，我们学习映射操作，它能为数据中的值提供另一种“标签”或表现形式，类似于给中文名起一个英文名。

![](img/342b72a0859967b78d5c411462edf4b3_55.png)

![](img/342b72a0859967b78d5c411462edf4b3_57.png)

![](img/342b72a0859967b78d5c411462edf4b3_59.png)

映射主要通过Series的`map`方法实现。`map`方法接受一个字典或函数作为参数，对Series中的每个元素进行转换。

![](img/342b72a0859967b78d5c411462edf4b3_61.png)

首先，我们创建一个包含姓名和薪资的DataFrame。

![](img/342b72a0859967b78d5c411462edf4b3_63.png)

![](img/342b72a0859967b78d5c411462edf4b3_65.png)

![](img/342b72a0859967b78d5c411462edf4b3_67.png)

```python
data = {‘name’: [‘张三‘， ’李四‘， ’张三‘],
        ‘salary’: [15000, 20000, 15000]}
df = pd.DataFrame(data)
print(df)
```

![](img/342b72a0859967b78d5c411462edf4b3_69.png)

以下是`map`方法的两种主要用途：

![](img/342b72a0859967b78d5c411462edf4b3_71.png)

*   **值映射**：根据一个映射关系字典，将Series中的值转换为对应的新值。
    ```python
    # 定义映射关系：中文名 -> 英文名
    name_map = {‘张三‘: ’Tom‘， ’李四‘: ’Jack‘}
    # 对‘name’列应用映射
    df[‘english_name’] = df[‘name’].map(name_map)
    print(df)
    ```
*   **充当运算工具**：`map`可以接受一个函数，将该函数应用于Series的每个元素，实现复杂的计算或转换。
    ```python
    # 定义一个计算税后薪资的函数（假设超过3000部分缴纳50%税）
    def after_salary(s):
        tax = (s - 3000) * 0.5 if s > 3000 else 0
        return s - tax

    # 对‘salary’列应用计算函数
    df[‘after_salary’] = df[‘salary’].map(after_salary)
    print(df)
    ```
    也可以使用lambda匿名函数实现同样的功能：
    ```python
    df[‘after_salary_lambda’] = df[‘salary’].map(lambda s: s - ((s-3000)*0.5 if s>3000 else 0))
    print(df)
    ```

![](img/342b72a0859967b78d5c411462edf4b3_73.png)

![](img/342b72a0859967b78d5c411462edf4b3_75.png)

**重要提示**：`map`是Series对象的方法，不能直接在DataFrame上调用。

![](img/342b72a0859967b78d5c411462edf4b3_77.png)

![](img/342b72a0859967b78d5c411462edf4b3_79.png)

## 随机抽样

![](img/342b72a0859967b78d5c411462edf4b3_81.png)

在数据分析中，有时需要从数据集中随机抽取一部分样本。这可以通过结合`take`函数和`numpy.random.permutation`函数来实现。

![](img/342b72a0859967b78d5c411462edf4b3_83.png)

![](img/342b72a0859967b78d5c411462edf4b3_85.png)

![](img/342b72a0859967b78d5c411462edf4b3_87.png)

`permutation`函数可以生成一个随机排列的序列。`take`函数则根据提供的索引顺序从数据中抽取行或列。

![](img/342b72a0859967b78d5c411462edf4b3_89.png)

![](img/342b72a0859967b78d5c411462edf4b3_91.png)

![](img/342b72a0859967b78d5c411462edf4b3_93.png)

![](img/342b72a0859967b78d5c411462edf4b3_95.png)

我们创建一个新的DataFrame用于演示。

![](img/342b72a0859967b78d5c411462edf4b3_97.png)

![](img/342b72a0859967b78d5c411462edf4b3_99.png)

![](img/342b72a0859967b78d5c411462edf4b3_101.png)

```python
# 创建一个100行3列的DataFrame
df_large = pd.DataFrame(np.random.randn(100, 3), columns=[‘A‘， ’B‘， ’C‘])
print(df_large.head())
```

![](img/342b72a0859967b78d5c411462edf4b3_103.png)

以下是实现随机抽样的步骤：

![](img/342b72a0859967b78d5c411462edf4b3_105.png)

*   **打乱数据顺序**：首先，我们可以打乱数据的行和列顺序。
    ```python
    # 打乱列的顺序（使用隐式索引）
    col_indices = np.random.permutation(df_large.shape[1]) # 生成列索引的乱序，如 [2,0,1]
    df_shuffled_cols = df_large.take(col_indices, axis=1)

    # 在此基础上打乱行的顺序
    row_indices = np.random.permutation(df_large.shape[0]) # 生成行索引的乱序
    df_shuffled = df_shuffled_cols.take(row_indices, axis=0)
    print(df_shuffled.head())
    ```
*   **进行随机抽样**：从打乱后的数据中选取前N行，即可得到随机样本。
    ```python
    # 随机抽取50行作为样本
    sample_size = 50
    df_sample = df_shuffled.iloc[:sample_size]
    print(df_sample.shape) # 应输出 (50, 3)
    ```

![](img/342b72a0859967b78d5c411462edf4b3_107.png)

![](img/342b72a0859967b78d5c411462edf4b3_109.png)

## 分类处理

![](img/342b72a0859967b78d5c411462edf4b3_111.png)

![](img/342b72a0859967b78d5c411462edf4b3_113.png)

分类处理（通常指`cut`或`qcut`函数）用于将连续数据分段并离散化，例如将年龄分为“青年”、“中年”、“老年”，或将分数分为“A”、“B”、“C”等级。这是一个非常重要的数据分析技巧，但由于视频片段内容未详细展开，我们在此仅做概念性介绍。

![](img/342b72a0859967b78d5c411462edf4b3_115.png)

![](img/342b72a0859967b78d5c411462edf4b3_117.png)

![](img/342b72a0859967b78d5c411462edf4b3_119.png)

`pd.cut()`函数可以根据指定的分箱边界将数据分类。
`pd.qcut()`函数则根据数据的样本分位数进行分箱，确保每个箱内的数据量大致相等。

![](img/342b72a0859967b78d5c411462edf4b3_121.png)

![](img/342b72a0859967b78d5c411462edf4b3_123.png)

![](img/342b72a0859967b78d5c411462edf4b3_125.png)

![](img/342b72a0859967b78d5c411462edf4b3_127.png)

本节课中我们一起学习了pandas的四个高级操作：**替换(`replace`)**、**映射(`map`)**、**随机抽样**以及**分类处理**的概念。`replace`用于直接修改数据值；`map`功能强大，既能进行值映射，又能作为运算工具对序列进行批量计算；随机抽样技巧有助于我们从数据集中获取无偏样本；而分类处理则是数据离散化的关键。掌握这些技能将极大提升你的数据预处理效率。