# 数据分析+金融量化+数据清洗：P38：DataFrame级联-02

![](img/201aede0fc92d1f99ffb27bb2f301da2_1.png)

在本节课中，我们将继续深入学习Pandas中DataFrame的级联操作，重点探讨`join`、`join_axes`、`ignore_index`和`keys`等关键参数的使用方法，并通过一个成绩表的实例来巩固所学知识。

![](img/201aede0fc92d1f99ffb27bb2f301da2_3.png)

上一节我们介绍了DataFrame级联的基本概念和`pd.concat()`函数，本节中我们来看看如何通过参数更精细地控制级联结果。

## 创建DataFrame的辅助函数

![](img/201aede0fc92d1f99ffb27bb2f301da2_5.png)

![](img/201aede0fc92d1f99ffb27bb2f301da2_7.png)

为了后续演示方便，我们首先创建一个用于生成示例DataFrame的函数。

![](img/201aede0fc92d1f99ffb27bb2f301da2_8.png)

![](img/201aede0fc92d1f99ffb27bb2f301da2_10.png)

![](img/201aede0fc92d1f99ffb27bb2f301da2_12.png)

以下是`create_df`函数的定义，它接受行索引和列索引列表，返回一个填充了随机数据的DataFrame。

![](img/201aede0fc92d1f99ffb27bb2f301da2_14.png)

![](img/201aede0fc92d1f99ffb27bb2f301da2_16.png)

```python
import pandas as pd
import numpy as np

![](img/201aede0fc92d1f99ffb27bb2f301da2_18.png)

![](img/201aede0fc92d1f99ffb27bb2f301da2_20.png)

![](img/201aede0fc92d1f99ffb27bb2f301da2_22.png)

def create_df(index, columns):
    data = {}
    for i in range(len(index)):
        for k in range(len(columns)):
            # 生成类似‘A0’，‘B1’的列名
            col_name = f'{index[i]}{columns[k]}'
            data[col_name] = np.random.randint(0, 10)
    df = pd.DataFrame(data, index=index)
    df.columns = columns
    return df
```

![](img/201aede0fc92d1f99ffb27bb2f301da2_24.png)

![](img/201aede0fc92d1f99ffb27bb2f301da2_26.png)

我们来验证一下这个函数。

![](img/201aede0fc92d1f99ffb27bb2f301da2_28.png)

```python
df_test = create_df(['A', 'B', 'C', 'D', 'E'], [1, 2, 3, 4, 5])
print(df_test)
```

![](img/201aede0fc92d1f99ffb27bb2f301da2_30.png)

![](img/201aede0fc92d1f99ffb27bb2f301da2_32.png)

## 级联参数详解

![](img/201aede0fc92d1f99ffb27bb2f301da2_34.png)

### `join`参数：控制标签的保留方式

![](img/201aede0fc92d1f99ffb27bb2f301da2_36.png)

![](img/201aede0fc92d1f99ffb27bb2f301da2_38.png)

`join`参数用于控制在级联方向上，如何处理两个DataFrame的标签（行标签或列标签）。它有两个选项：`inner`（内连接）和`outer`（外连接，默认值）。

![](img/201aede0fc92d1f99ffb27bb2f301da2_40.png)

![](img/201aede0fc92d1f99ffb27bb2f301da2_42.png)

首先，我们创建两个示例DataFrame。

![](img/201aede0fc92d1f99ffb27bb2f301da2_44.png)

![](img/201aede0fc92d1f99ffb27bb2f301da2_46.png)

![](img/201aede0fc92d1f99ffb27bb2f301da2_48.png)

```python
df1 = create_df(['A', 'B', 'C', 'D'], [1, 2, 3])
df2 = create_df(['B', 'C', 'D', 'E'], [2, 3, 4])
print("df1:")
print(df1)
print("\ndf2:")
print(df2)
```

![](img/201aede0fc92d1f99ffb27bb2f301da2_50.png)

![](img/201aede0fc92d1f99ffb27bb2f301da2_52.png)

**纵向级联（axis=0）**：此时级联方向是行方向，我们关注的是列标签。
*   `join=‘outer’`（默认）：保留所有列标签的并集，缺失值用NaN填充。
*   `join=‘inner’`：只保留两个DataFrame共有的列标签。

![](img/201aede0fc92d1f99ffb27bb2f301da2_54.png)

以下是纵向级联时`join`参数的效果。

![](img/201aede0fc92d1f99ffb27bb2f301da2_56.png)

![](img/201aede0fc92d1f99ffb27bb2f301da2_58.png)

```python
# 默认outer连接，保留所有列（1,2,3,4）
result_outer = pd.concat([df1, df2], axis=0)
print("纵向outer连接:")
print(result_outer)

![](img/201aede0fc92d1f99ffb27bb2f301da2_60.png)

# inner连接，只保留共有列（2,3）
result_inner = pd.concat([df1, df2], axis=0, join=‘inner’)
print("\n纵向inner连接:")
print(result_inner)
```

![](img/201aede0fc92d1f99ffb27bb2f301da2_62.png)

![](img/201aede0fc92d1f99ffb27bb2f301da2_64.png)

**横向级联（axis=1）**：此时级联方向是列方向，我们关注的是行标签。
*   `join=‘outer’`：保留所有行标签的并集。
*   `join=‘inner’`：只保留两个DataFrame共有的行标签。

![](img/201aede0fc92d1f99ffb27bb2f301da2_66.png)

以下是横向级联时`join`参数的效果。

![](img/201aede0fc92d1f99ffb27bb2f301da2_68.png)

![](img/201aede0fc92d1f99ffb27bb2f301da2_70.png)

```python
# 默认outer连接，保留所有行（A,B,C,D,E）
result_outer_h = pd.concat([df1, df2], axis=1)
print("横向outer连接:")
print(result_outer_h)

![](img/201aede0fc92d1f99ffb27bb2f301da2_72.png)

![](img/201aede0fc92d1f99ffb27bb2f301da2_74.png)

# inner连接，只保留共有行（B,C,D）
result_inner_h = pd.concat([df1, df2], axis=1, join=‘inner’)
print("\n横向inner连接:")
print(result_inner_h)
```

![](img/201aede0fc92d1f99ffb27bb2f301da2_76.png)

![](img/201aede0fc92d1f99ffb27bb2f301da2_78.png)

### `join_axes`参数：自定义保留的标签

![](img/201aede0fc92d1f99ffb27bb2f301da2_80.png)

![](img/201aede0fc92d1f99ffb27bb2f301da2_82.png)

`join`参数只能选择交集或并集。如果想自定义要保留的特定标签，可以使用`join_axes`参数。它接受一个列表，列表中的元素是`pd.Index`对象，用于指定在级联方向上要保留的确切标签。

![](img/201aede0fc92d1f99ffb27bb2f301da2_84.png)

**重要**：`join_axes`的格式比较特殊，它要求传入一个列表，列表中的元素是`pd.Index`类型。

![](img/201aede0fc92d1f99ffb27bb2f301da2_86.png)

例如，在横向级联时，如果我们只想保留行标签`[‘B‘, ‘C‘, ‘D‘]`，可以这样做：

![](img/201aede0fc92d1f99ffb27bb2f301da2_88.png)

```python
# 首先创建要保留的索引对象
custom_index = pd.Index([‘B‘, ‘C‘, ‘D‘])

![](img/201aede0fc92d1f99ffb27bb2f301da2_90.png)

![](img/201aede0fc92d1f99ffb27bb2f301da2_92.png)

![](img/201aede0fc92d1f99ffb27bb2f301da2_94.png)

# 将pd.Index对象放入列表中，传给join_axes
result_custom = pd.concat([df1, df2], axis=1, join_axes=[custom_index])
print("使用join_axes自定义保留行标签[B, C, D]:")
print(result_custom)
```

![](img/201aede0fc92d1f99ffb27bb2f301da2_96.png)

`join_axes`参数允许我们进行非常灵活的级联操作，但需要注意，指定的标签必须与级联方向上的标签类型匹配。

![](img/201aede0fc92d1f99ffb27bb2f301da2_98.png)

![](img/201aede0fc92d1f99ffb27bb2f301da2_100.png)

![](img/201aede0fc92d1f99ffb27bb2f301da2_102.png)

### `ignore_index`参数：忽略并重建索引

![](img/201aede0fc92d1f99ffb27bb2f301da2_104.png)

在级联时，如果非级联方向上的索引出现重复（例如纵向级联时行索引重复），可能会带来问题。`ignore_index`参数可以解决这个问题。

![](img/201aede0fc92d1f99ffb27bb2f301da2_106.png)

![](img/201aede0fc92d1f99ffb27bb2f301da2_108.png)

当我们设置`ignore_index=True`时，Pandas会忽略原始DataFrame在非级联方向上的索引，并自动生成一个新的整数序列索引（从0开始）。

![](img/201aede0fc92d1f99ffb27bb2f301da2_110.png)

以下是`ignore_index`的示例。

![](img/201aede0fc92d1f99ffb27bb2f301da2_112.png)

![](img/201aede0fc92d1f99ffb27bb2f301da2_114.png)

![](img/201aede0fc92d1f99ffb27bb2f301da2_116.png)

```python
# 纵向级联，行索引重复（A,B,C,D 和 B,C,D,E）
concat_normal = pd.concat([df1, df2], axis=0)
print("普通纵向级联，行索引重复:")
print(concat_normal)
print(f"\n读取行‘B‘: \n{concat_normal.loc[‘B‘]}") # 会读出两行

![](img/201aede0fc92d1f99ffb27bb2f301da2_118.png)

# 使用ignore_index忽略原始行索引
concat_ignore = pd.concat([df1, df2], axis=0, ignore_index=True)
print("\n使用ignore_index=True纵向级联:")
print(concat_ignore)
# 此时行索引变为0,1,2,3,4,5,6,7，需要用整数索引访问
print(f"\n读取第1行: \n{concat_ignore.iloc[1]}")
```

![](img/201aede0fc92d1f99ffb27bb2f301da2_120.png)

![](img/201aede0fc92d1f99ffb27bb2f301da2_122.png)

![](img/201aede0fc92d1f99ffb27bb2f301da2_124.png)

**应用场景**：`ignore_index`通常用于**纵向级联**，处理行标签重复的问题。因为行标签（如ID、序号）在合并后通常可以重新编排。对于横向级联，列标签（字段名）通常具有业务含义，一般会保留。

![](img/201aede0fc92d1f99ffb27bb2f301da2_125.png)

![](img/201aede0fc92d1f99ffb27bb2f301da2_127.png)

### `keys`参数：创建多层索引

另一种处理索引重复问题的方法是使用`keys`参数。它为每个被级联的原始DataFrame添加一个外层索引，从而形成一个多层索引（MultiIndex），以区分数据来源。

![](img/201aede0fc92d1f99ffb27bb2f301da2_129.png)

![](img/201aede0fc92d1f99ffb27bb2f301da2_131.png)

这在横向级联时尤其有用，可以保留有意义的列标签。

![](img/201aede0fc92d1f99ffb27bb2f301da2_133.png)

```python
# 横向级联，使用keys参数
concat_keys = pd.concat([df1, df2], axis=1, keys=[‘first‘, ‘second‘])
print("使用keys参数进行横向级联:")
print(concat_keys)
# 可以通过多层索引访问数据
print(f"\n访问‘first‘部分的‘2‘列: \n{concat_keys[‘first‘][2]}")
```

![](img/201aede0fc92d1f99ffb27bb2f301da2_135.png)

![](img/201aede0fc92d1f99ffb27bb2f301da2_137.png)

**应用场景**：`keys`参数通常用于**横向级联**，处理列标签重复或需要标识数据来源的情况。

![](img/201aede0fc92d1f99ffb27bb2f301da2_139.png)

## 实战练习：学生成绩表管理

![](img/201aede0fc92d1f99ffb27bb2f301da2_141.png)

![](img/201aede0fc92d1f99ffb27bb2f301da2_142.png)

![](img/201aede0fc92d1f99ffb27bb2f301da2_144.png)

现在，我们通过一个实际案例来应用所学的级联知识。

![](img/201aede0fc92d1f99ffb27bb2f301da2_146.png)

![](img/201aede0fc92d1f99ffb27bb2f301da2_148.png)

**场景**：有一张学生期中考试成绩表，需要实现两个操作：
1.  新增一个考试学科“计算机”。
2.  新增一位同学“王老五”的成绩。

![](img/201aede0fc92d1f99ffb27bb2f301da2_150.png)

![](img/201aede0fc92d1f99ffb27bb2f301da2_152.png)

**要求**：使用DataFrame级联的方式实现。

以下是实现步骤：

![](img/201aede0fc92d1f99ffb27bb2f301da2_154.png)

```python
# 1. 创建原始成绩表（张三、李四，三个学科）
score_data = np.random.randint(0, 100, size=(2, 3))
score_df = pd.DataFrame(score_data,
                        index=[‘张三‘, ‘李四‘],
                        columns=[‘语文‘, ‘数学‘, ‘英语‘])
print("原始成绩表:")
print(score_df)

![](img/201aede0fc92d1f99ffb27bb2f301da2_156.png)

# 2. 新增学科“计算机”
# 创建包含计算机成绩的新DataFrame（列级联，需要行索引一致）
computer_scores = np.random.randint(0, 100, size=(2, 1))
computer_df = pd.DataFrame(computer_scores,
                           index=score_df.index, # 行索引与原始表一致
                           columns=[‘计算机‘])
print("\n计算机成绩表:")
print(computer_df)

![](img/201aede0fc92d1f99ffb27bb2f301da2_158.png)

# 横向级联，添加新学科
score_df = pd.concat([score_df, computer_df], axis=1)
print("\n添加计算机学科后的成绩表:")
print(score_df)

![](img/201aede0fc92d1f99ffb27bb2f301da2_160.png)

![](img/201aede0fc92d1f99ffb27bb2f301da2_162.png)

# 3. 新增学生“王老五”
# 创建王老五的成绩DataFrame（行级联，需要列索引一致）
wang_scores = np.random.randint(0, 100, size=(1, 4))
wang_df = pd.DataFrame(wang_scores,
                       index=[‘王老五‘],
                       columns=score_df.columns) # 列索引与当前表一致
print("\n王老五成绩表:")
print(wang_df)

![](img/201aede0fc92d1f99ffb27bb2f301da2_164.png)

![](img/201aede0fc92d1f99ffb27bb2f301da2_166.png)

# 纵向级联，添加新学生。使用ignore_index=False会保留‘王老五‘索引，但张三李四索引会重复一次。
# 更常见的做法是使用ignore_index=True重置行索引，或者确保原始索引唯一。
score_df = pd.concat([score_df, wang_df], axis=0)
print("\n最终成绩表:")
print(score_df)

![](img/201aede0fc92d1f99ffb27bb2f301da2_168.png)

# 4. 使用append方法（纵向追加的便捷方式）
# append是concat在axis=0情况下的简化版，但未来版本可能被移除，了解即可。
# new_student = pd.DataFrame([[70,80,90,85]], columns=score_df.columns, index=[‘赵六‘])
# score_df_appended = score_df.append(new_student)
# print(score_df_appended)
```

![](img/201aede0fc92d1f99ffb27bb2f301da2_170.png)

![](img/201aede0fc92d1f99ffb27bb2f301da2_172.png)

## 总结

![](img/201aede0fc92d1f99ffb27bb2f301da2_174.png)

![](img/201aede0fc92d1f99ffb27bb2f301da2_176.png)

本节课中我们一起深入学习了DataFrame级联的几个高级参数：
*   **`join`**： 通过`inner`或`outer`控制级联方向上标签的保留策略（交集或并集）。
*   **`join_axes`**： 提供了自定义保留标签的能力，需要传入`pd.Index`对象的列表。
*   **`ignore_index`**： 忽略原始非级联方向的索引并重建，常用于解决纵向级联时的行索引重复问题。
*   **`keys`**： 为级联后的数据添加外层索引，创建多层索引，常用于标识横向级联的数据来源。

![](img/201aede0fc92d1f99ffb27bb2f301da2_178.png)

![](img/201aede0fc92d1f99ffb27bb2f301da2_180.png)

![](img/201aede0fc92d1f99ffb27bb2f301da2_182.png)

通过学生成绩表管理的实战练习，我们综合运用了横向级联（新增列）和纵向级联（新增行）来更新数据结构。掌握这些参数能让你的数据合并操作更加灵活和精准。