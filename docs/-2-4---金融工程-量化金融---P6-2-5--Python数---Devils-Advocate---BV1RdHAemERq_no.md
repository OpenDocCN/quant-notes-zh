# 量化交易：Python入门之数据分析【2/4】：2-5. Python数据分析：Pandas DataFrame基础 📊

## 概述
在本节课中，我们将继续学习Pandas的核心数据结构——DataFrame。我们将重点掌握如何使用DataFrame进行数据选择、条件筛选以及处理缺失值。课程将以泰坦尼克号数据集为例，演示这些核心操作。

![](img/9297cc73d7277b29dabde1c81c50b853_1.png)

---

![](img/9297cc73d7277b29dabde1c81c50b853_3.png)

## 导入数据与初步查看

首先，我们需要导入Pandas库并加载数据集。

![](img/9297cc73d7277b29dabde1c81c50b853_5.png)

```python
import pandas as pd
```

![](img/9297cc73d7277b29dabde1c81c50b853_7.png)

加载数据文件时，如果CSV文件与Jupyter Notebook文件在同一文件夹，可以直接使用文件名。如果文件在其他位置，建议使用完整的文件路径。

```python
df = pd.read_csv('titanic.csv')
```

![](img/9297cc73d7277b29dabde1c81c50b853_9.png)

加载完成后，我们可以查看数据的基本信息。这是一个关于泰坦尼克号乘客的统计表，包含以下列：
*   `PassengerId`: 乘客ID
*   `Survived`: 是否存活（1代表存活，0代表遇难）
*   `Pclass`: 船舱等级（1/2/3等舱）
*   `Name`: 乘客姓名
*   `Sex`: 性别
*   `Age`: 年龄
*   `SibSp`: 同行的兄弟姐妹或配偶数量
*   `Parch`: 同行的父母或子女数量
*   `Ticket`: 船票信息
*   `Fare`: 票价
*   `Cabin`: 船舱号
*   `Embarked`: 登船港口

![](img/9297cc73d7277b29dabde1c81c50b853_11.png)

![](img/9297cc73d7277b29dabde1c81c50b853_13.png)

---

![](img/9297cc73d7277b29dabde1c81c50b853_14.png)

## DataFrame的数据选择

![](img/9297cc73d7277b29dabde1c81c50b853_16.png)

上一节我们介绍了DataFrame的基本结构，本节中我们来看看如何精确地选择其中的数据。

![](img/9297cc73d7277b29dabde1c81c50b853_17.png)

![](img/9297cc73d7277b29dabde1c81c50b853_18.png)

虽然直接使用列名进行选择（如`df[‘列名’]`）很常用，但未来可能被弃用。因此，更推荐使用`.loc`方法。

![](img/9297cc73d7277b29dabde1c81c50b853_20.png)

`.loc`方法基于标签进行选择，其基本语法是**先选择行，再选择列**。

![](img/9297cc73d7277b29dabde1c81c50b853_21.png)

![](img/9297cc73d7277b29dabde1c81c50b853_22.png)

```python
# 选择‘Fare’列的所有数据
df.loc[:, ‘Fare’]
```

![](img/9297cc73d7277b29dabde1c81c50b853_24.png)

如果想对整列数据进行运算，例如将票价（Fare）全部增加2，可以这样做：

![](img/9297cc73d7277b29dabde1c81c50b853_26.png)

![](img/9297cc73d7277b29dabde1c81c50b853_27.png)

```python
df.loc[:, ‘Fare’] + 2
```

![](img/9297cc73d7277b29dabde1c81c50b853_29.png)

需要注意的是，上面的操作并不会改变原始`df`中的数据，它只是生成了一个临时的计算结果。如果希望永久修改数据，需要将结果赋值回去。

![](img/9297cc73d7277b29dabde1c81c50b853_30.png)

![](img/9297cc73d7277b29dabde1c81c50b853_32.png)

```python
df[‘Fare’] = df.loc[:, ‘Fare’] + 2
# 或者使用 .loc 赋值
df.loc[:, ‘Fare’] = df.loc[:, ‘Fare’] + 2
```

---

![](img/9297cc73d7277b29dabde1c81c50b853_34.png)

![](img/9297cc73d7277b29dabde1c81c50b853_36.png)

## 使用条件进行筛选

![](img/9297cc73d7277b29dabde1c81c50b853_38.png)

掌握了基础选择后，我们来看看如何根据条件筛选数据。例如，我们想筛选出所有年龄大于25岁的乘客。

![](img/9297cc73d7277b29dabde1c81c50b853_40.png)

![](img/9297cc73d7277b29dabde1c81c50b853_41.png)

如果直接运行`df[‘Age’] > 25`，会得到一个布尔值（True/False）序列，也称为“掩码”（Mask）。它表示每一行数据是否满足“年龄大于25”的条件。

```python
mask = df[‘Age’] > 25
print(mask.head())
```

为了根据这个掩码筛选出符合条件的行，我们需要将这个布尔序列放入方括号内，作用于原始的DataFrame。

![](img/9297cc73d7277b29dabde1c81c50b853_43.png)

![](img/9297cc73d7277b29dabde1c81c50b853_44.png)

```python
df_older_than_25 = df[mask]
```

![](img/9297cc73d7277b29dabde1c81c50b853_46.png)

这样，`df_older_than_25`中就只包含了年龄大于25岁的乘客信息。

![](img/9297cc73d7277b29dabde1c81c50b853_48.png)

---

![](img/9297cc73d7277b29dabde1c81c50b853_50.png)

## 处理缺失值

![](img/9297cc73d7277b29dabde1c81c50b853_52.png)

在实际数据中，经常存在缺失值（NaN）。例如，`Cabin`（船舱号）列就有很多缺失。我们可能希望筛选出`Cabin`信息完整的乘客。

![](img/9297cc73d7277b29dabde1c81c50b853_54.png)

Pandas提供了`.dropna()`方法来删除包含缺失值的行。

![](img/9297cc73d7277b29dabde1c81c50b853_56.png)

```python
# 删除‘Cabin’列为空的行，但不改变原始df
df_with_cabin = df.dropna(subset=[‘Cabin’])

![](img/9297cc73d7277b29dabde1c81c50b853_58.png)

# 如果想直接在原始df上删除，可以使用 inplace 参数
df.dropna(subset=[‘Cabin’], inplace=True)
# 或者重新赋值
df = df.dropna(subset=[‘Cabin’])
```

![](img/9297cc73d7277b29dabde1c81c50b853_60.png)

除了使用掩码筛选，还可以使用`.where()`方法进行条件选择。`.where()`会保留满足条件的值，不满足条件的则替换为NaN。

![](img/9297cc73d7277b29dabde1c81c50b853_62.png)

```python
# 保留年龄大于25的值，其他变为NaN
df_where = df.where(df[‘Age’] > 25)
# 通常需要再配合 .dropna() 使用
df_filtered = df_where.dropna()
```

![](img/9297cc73d7277b29dabde1c81c50b853_64.png)

---

![](img/9297cc73d7277b29dabde1c81c50b853_66.png)

## 组合多个条件进行筛选

![](img/9297cc73d7277b29dabde1c81c50b853_67.png)

![](img/9297cc73d7277b29dabde1c81c50b853_68.png)

更复杂的情况是，我们需要组合多个条件。例如，我们想找出**年龄大于25岁** **并且** **最终存活下来**的乘客。

![](img/9297cc73d7277b29dabde1c81c50b853_69.png)

![](img/9297cc73d7277b29dabde1c81c50b853_70.png)

这时，我们需要使用逻辑运算符来组合条件。在Pandas中：
*   **与** 操作使用 `&`
*   **或** 操作使用 `|`
*   每个条件都需要用括号括起来

![](img/9297cc73d7277b29dabde1c81c50b853_72.png)

以下是实现“年龄大于25且存活”的代码：

```python
# 定义条件1：年龄大于25
condition_age = (df[‘Age’] > 25)
# 定义条件2：存活（Survived等于1）
condition_survived = (df[‘Survived’] == 1)

![](img/9297cc73d7277b29dabde1c81c50b853_74.png)

# 组合条件（与操作）
combined_condition = condition_age & condition_survived

![](img/9297cc73d7277b29dabde1c81c50b853_76.png)

![](img/9297cc73d7277b29dabde1c81c50b853_78.png)

# 应用组合条件进行筛选
df_age_survived = df[combined_condition]
```

筛选完成后，我们可以使用`len()`函数或直接查看DataFrame的形状来确认筛选出的数据量。

```python
print(f”满足条件的乘客数量：{len(df_age_survived)}“)
# 或者
print(df_age_survived.shape)
```

![](img/9297cc73d7277b29dabde1c81c50b853_80.png)

---

![](img/9297cc73d7277b29dabde1c81c50b853_82.png)

![](img/9297cc73d7277b29dabde1c81c50b853_84.png)

## 总结

![](img/9297cc73d7277b29dabde1c81c50b853_86.png)

![](img/9297cc73d7277b29dabde1c81c50b853_87.png)

本节课我们一起学习了Pandas DataFrame的核心数据操作技能：
1.  **数据选择**：掌握了使用`.loc`方法进行行列选择的标准方式。
2.  **条件筛选**：学会了通过创建布尔掩码（`df[‘列’] > 值`）来筛选满足特定条件的行。
3.  **处理缺失值**：了解了如何使用`.dropna()`方法清理数据中的空值。
4.  **多条件组合**：能够运用逻辑运算符`&`（与）和`|`（或）来构建复杂的筛选逻辑。

![](img/9297cc73d7277b29dabde1c81c50b853_89.png)

这些技能是进行数据清洗、预处理和分析的基础，对于后续的量化金融数据分析至关重要。