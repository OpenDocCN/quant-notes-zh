# 量化交易：Python入门之数据分析【2／4】：2-6. Python数据分析：Pandas 索引与缺失值处理 📊

![](img/74bd2fb9e97ebe25d1c7502ab653a16d_1.png)

在本节课中，我们将要学习Pandas库中两个重要的数据处理技巧：如何设置和更改DataFrame的索引，以及如何处理数据中的缺失值。掌握这些技能对于高效地组织和清理数据至关重要。

![](img/74bd2fb9e97ebe25d1c7502ab653a16d_3.png)

![](img/74bd2fb9e97ebe25d1c7502ab653a16d_5.png)

上一节我们介绍了DataFrame的基本选择方法，本节中我们来看看如何通过自定义索引来更好地组织数据。

![](img/74bd2fb9e97ebe25d1c7502ab653a16d_7.png)

## 设置索引

默认情况下，Pandas会为DataFrame生成从0开始的整数行索引。但我们可以根据需求，将某一列或多列设置为新的索引。

![](img/74bd2fb9e97ebe25d1c7502ab653a16d_9.png)

![](img/74bd2fb9e97ebe25d1c7502ab653a16d_11.png)

首先，我们需要导入Pandas库并加载数据。

```python
import pandas as pd
df = pd.read_csv('titanic.csv')
```

![](img/74bd2fb9e97ebe25d1c7502ab653a16d_13.png)

假设我们想将乘客的`Name`列设置为索引，可以使用`set_index`方法。

![](img/74bd2fb9e97ebe25d1c7502ab653a16d_15.png)

```python
df = df.set_index('Name')
```
执行此操作后，`Name`列将移动到最前面，成为新的行索引。这样做的好处是，在进行数据筛选时，关键信息（如姓名）能始终显示在最前面。

![](img/74bd2fb9e97ebe25d1c7502ab653a16d_17.png)

![](img/74bd2fb9e97ebe25d1c7502ab653a16d_18.png)

例如，我们可以筛选出年龄小于等于25岁的乘客：

![](img/74bd2fb9e97ebe25d1c7502ab653a16d_19.png)

```python
df[df['Age'] <= 25]
```

![](img/74bd2fb9e97ebe25d1c7502ab653a16d_21.png)

## 使用复合索引

![](img/74bd2fb9e97ebe25d1c7502ab653a16d_23.png)

![](img/74bd2fb9e97ebe25d1c7502ab653a16d_25.png)

有时，单一列作为索引可能不够，我们可以设置由多列组成的复合索引。

![](img/74bd2fb9e97ebe25d1c7502ab653a16d_27.png)

以下是设置`Sex`和`Age`为复合索引的示例：

![](img/74bd2fb9e97ebe25d1c7502ab653a16d_29.png)

![](img/74bd2fb9e97ebe25d1c7502ab653a16d_30.png)

```python
df = df.set_index(['Sex', 'Age'])
```
这样，数据会先按性别分组，再在每个性别组内按年龄排序，使得数据的层次结构更加清晰。

![](img/74bd2fb9e97ebe25d1c7502ab653a16d_32.png)

在操作完成后，如果需要恢复默认的整数索引，可以重新读取数据或使用`reset_index`方法。

![](img/74bd2fb9e97ebe25d1c7502ab653a16d_34.png)

```python
df = pd.read_csv('titanic.csv')  # 重新读取以复位索引
# 或使用 df.reset_index(inplace=True)
```

## 处理缺失值

![](img/74bd2fb9e97ebe25d1c7502ab653a16d_36.png)

![](img/74bd2fb9e97ebe25d1c7502ab653a16d_38.png)

数据中经常存在缺失值（NaN）。除了直接删除（`dropna`）外，Pandas提供了填充缺失值的方法。

![](img/74bd2fb9e97ebe25d1c7502ab653a16d_40.png)

![](img/74bd2fb9e97ebe25d1c7502ab653a16d_41.png)

以下是两种常用的填充策略：

![](img/74bd2fb9e97ebe25d1c7502ab653a16d_43.png)

### 向前填充 (Forward Fill)

![](img/74bd2fb9e97ebe25d1c7502ab653a16d_44.png)

向前填充是指用缺失值前面的一个非缺失值来填充当前的缺失值。

```python
df['Cabin'].fillna(method='ffill', inplace=True)
```
此方法会沿着列向下查找，将遇到的第一个NaN用其上方最近的非NaN值替换。如果序列开头的值是NaN，则它无法被填充，会保持为NaN。

![](img/74bd2fb9e97ebe25d1c7502ab653a16d_46.png)

![](img/74bd2fb9e97ebe25d1c7502ab653a16d_48.png)

### 向后填充 (Backward Fill)

![](img/74bd2fb9e97ebe25d1c7502ab653a16d_49.png)

向后填充与向前填充相反，是用缺失值后面的一个非缺失值来填充。

![](img/74bd2fb9e97ebe25d1c7502ab653a16d_51.png)

```python
df['Cabin'].fillna(method='bfill', inplace=True)
```
此方法会沿着列向上查找，用下方最近的非NaN值来填充上方的NaN。

**注意**：在较新的Pandas版本中，`fillna`方法的参数名已更新。推荐使用以下写法以避免警告：

![](img/74bd2fb9e97ebe25d1c7502ab653a16d_53.png)

![](img/74bd2fb9e97ebe25d1c7502ab653a16d_55.png)

```python
# 向前填充
df['Cabin'].fillna(method='ffill', inplace=True)
# 向后填充
df['Cabin'].fillna(method='bfill', inplace=True)
```
选择哪种填充方式取决于具体的业务逻辑和数据上下文。

![](img/74bd2fb9e97ebe25d1c7502ab653a16d_57.png)

![](img/74bd2fb9e97ebe25d1c7502ab653a16d_59.png)

---

![](img/74bd2fb9e97ebe25d1c7502ab653a16d_61.png)

本节课中我们一起学习了Pandas索引的设置与重置，以及如何使用向前填充和向后填充的方法来处理数据中的缺失值。合理设置索引能优化数据查询，而恰当的缺失值处理则是保证数据质量的关键步骤。