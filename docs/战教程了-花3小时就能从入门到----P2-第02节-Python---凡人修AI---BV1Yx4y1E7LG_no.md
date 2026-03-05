# Python量化金融实战教程：02：Python for Finance 常用packages 学习I 🐍📊

在本节课中，我们将要学习Python在量化金融中三个核心库的基础应用：NumPy、Pandas和Matplotlib。我们将从创建和操作数组开始，然后学习如何处理更复杂的数据框，最后掌握数据可视化的基本方法。

![](img/d882eaaa6aa6ca7830f02979ebb96236_1.png)

## NumPy数组操作与应用 🔢

上一节我们介绍了Python的基础语法，本节中我们来看看如何利用NumPy库进行高效的数值计算。NumPy是科学计算的基础包，提供了强大的多维数组对象和运算功能。

### 数组的创建

以下是创建数组的几种基本方法。

![](img/d882eaaa6aa6ca7830f02979ebb96236_3.png)

*   `np.arange(10)`：创建一个包含0到9整数的数组。
*   `np.array([...])`：将列表转换为数组，可以包含任意类型的数据。
*   `np.random.randn(2, 3)`：生成一个2行3列、服从标准正态分布的随机数数组。

### 特殊矩阵的生成

以下是生成几种特殊矩阵的函数。

![](img/d882eaaa6aa6ca7830f02979ebb96236_5.png)

*   `np.zeros([3, 4])`：创建一个3行4列、所有元素为0的矩阵。
*   `np.empty([2, 2])`：创建一个2行2列的空矩阵，仅分配内存空间，不进行初始化。
*   `np.eye(2)`：创建一个2行2列的单位矩阵。

### 数组的索引与运算

![](img/d882eaaa6aa6ca7830f02979ebb96236_7.png)

![](img/d882eaaa6aa6ca7830f02979ebb96236_9.png)

数组索引从0开始，支持灵活的切片操作。

![](img/d882eaaa6aa6ca7830f02979ebb96236_11.png)

*   `arr2d[0]`：获取二维数组的第一行。
*   `arr2d[0, 0]`：获取第一行第一列的元素。
*   `arr2d[0, 0:2]`：获取第一行，第0列到第1列（左闭右开）的元素。

数组支持逐元素的标量运算，这与矩阵乘法不同。
```python
arr = np.array([[1,2,3],[4,5,6]])
result = arr * 2  # 每个元素乘以2
result2 = arr * arr  # 逐元素相乘，非矩阵乘法
```

### 矩阵运算与常用函数

NumPy提供了完整的线性代数运算功能。

*   `arr.T`：对数组进行转置。
*   `np.dot(A, B)` 或 `A @ B`：进行矩阵乘法。
*   `np.linalg.inv(A)`：对方阵A求逆。
*   `np.sqrt(arr)`, `np.exp(arr)`, `np.log(arr)`：对数组每个元素进行数学运算。

以下是NumPy中一些常用的数学函数列表。

![](img/d882eaaa6aa6ca7830f02979ebb96236_13.png)

![](img/d882eaaa6aa6ca7830f02979ebb96236_14.png)

*   `np.abs`：取绝对值
*   `np.square`：计算平方
*   `np.sign`：计算符号
*   `np.ceil` / `np.floor`：向上/向下取整
*   `np.isnan` / `np.isinf`：判断是否为NaN或无穷大
*   `np.sin`, `np.cos`, `np.tan`：三角函数

### 条件逻辑与统计函数

`np.where`函数能实现基于条件的数组操作。
```python
# 若data>0则为2，否则为-2
new_data = np.where(data > 0, 2, -2)
# 若data>0则保留原值，否则设为0
new_data2 = np.where(data > 0, data, 0)
```

以下是常用的数组统计方法。

*   `arr.mean(axis=0)`：沿轴0（列）计算平均值。
*   `arr.sum(axis=1)`：沿轴1（行）计算和。
*   `arr.std()`：计算数组的标准差。
*   `np.unique(arr)`：返回数组中的唯一值。

### 矩阵分解

矩阵分解在数据分析中至关重要。

*   **QR分解**：`Q, R = np.linalg.qr(A)`，用于求解线性方程组。
*   **特征值分解**：`eigenvalues, eigenvectors = np.linalg.eig(A)`，用于主成分分析等。
*   **奇异值分解(SVD)**：`U, S, Vh = np.linalg.svd(A)`，应用更广泛的矩阵分解方法。

## Pandas数据框处理 📈

掌握了数组操作后，我们进入数据处理的核心——Pandas库。Pandas的DataFrame结构是存储和分析表格化数据的理想工具。

### 数据框的创建与查看

创建DataFrame最常用的方法是从字典构建。
```python
data = {'state': ['Ohio', 'Ohio', 'Nevada'], 'year': [2000, 2001, 2002]}
frame = pd.DataFrame(data)
```
使用`frame.head()`可以快速查看数据框的前几行，了解数据结构。

![](img/d882eaaa6aa6ca7830f02979ebb96236_16.png)

### 索引、选取与修改

可以通过多种方式访问和修改DataFrame中的数据。

![](img/d882eaaa6aa6ca7830f02979ebb96236_18.png)

![](img/d882eaaa6aa6ca7830f02979ebb96236_20.png)

*   **设置索引**：`frame.index = ['a', 'b', 'c']`
*   **选取列**：`frame['year']` 或 `frame.year`
*   **按位置选取(iloc)**：`frame.iloc[0, 1]` 选取第一行第二列。
*   **按标签选取(loc)**：`frame.loc['a', 'year']` 选取索引为‘a’，列名为‘year’的值。
*   **删除行/列**：`frame.drop('a')` 删除索引为‘a’的行；`del frame['year']` 删除‘year’列。
*   **增加列**：`frame['new_col'] = [1, 2, 3]`
*   **条件选取**：`frame[frame.year == 2001]`

![](img/d882eaaa6aa6ca7830f02979ebb96236_22.png)

![](img/d882eaaa6aa6ca7830f02979ebb96236_24.png)

### 函数应用、排序与统计

![](img/d882eaaa6aa6ca7830f02979ebb96236_26.png)

![](img/d882eaaa6aa6ca7830f02979ebb96236_28.png)

可以将函数应用到数据框的行或列上。
```python
# 对每一列应用自定义函数f
frame.apply(f, axis=0)
# 对‘e’列每个元素应用平方运算
frame['e'].map(lambda x: x**2)
```

![](img/d882eaaa6aa6ca7830f02979ebb96236_30.png)

![](img/d882eaaa6aa6ca7830f02979ebb96236_32.png)

![](img/d882eaaa6aa6ca7830f02979ebb96236_34.png)

![](img/d882eaaa6aa6ca7830f02979ebb96236_36.png)

数据排序是常见操作。
```python
# 按‘d’列的值升序排列
frame.sort_values(by='d')
# 按索引排序
frame.sort_index(axis=1, ascending=False)
```

![](img/d882eaaa6aa6ca7830f02979ebb96236_38.png)

`describe()`方法能快速生成描述性统计摘要。
```python
frame.describe()
```
其他常用统计函数包括：`sum()`, `mean()`, `std()`, `max()`, `min()`, `idxmax()`, `corr()`（相关系数）。

![](img/d882eaaa6aa6ca7830f02979ebb96236_40.png)

### 数据读取与预处理

Pandas可以轻松读取各种格式的数据。
```python
# 从CSV文件读取
df = pd.read_csv('file.csv', index_col='Date')
# 从Excel文件读取
df = pd.read_excel('file.xlsx', sheet_name='Sheet1')
```

处理缺失数据是数据清洗的关键步骤。
```python
# 删除包含NaN的任何行
df.dropna()
# 用0填充所有NaN
df.fillna(0)
# 用各列的均值填充NaN
df.fillna(df.mean())
```

### 数据合并与分组

合并多个数据集是常见需求。
```python
# 连接多个DataFrame（沿行方向）
pd.concat([df1, df2, df3], axis=0)
# 基于键合并两个DataFrame
pd.merge(df1, df2, on='key')
```

`groupby`操作可以实现数据的分组聚合。
```python
# 按‘state’分组，并计算‘pop’的平均值
frame.groupby('state')['pop'].mean()
```

透视表`pivot_table`能进行多维度的数据汇总。
```python
pd.pivot_table(df, values='D', index=['A', 'B'], columns=['C'], aggfunc=np.sum)
```

## Matplotlib数据可视化 📉

最后，我们学习如何使用Matplotlib库将数据直观地展现出来。良好的可视化是分析结果沟通的桥梁。

### 基础绘图

绘制一个简单的线形图。
```python
import matplotlib.pyplot as plt
x = np.arange(10)
y = np.sqrt(x)
plt.plot(x, y)
plt.show()
```

### 多子图与图形属性

![](img/d882eaaa6aa6ca7830f02979ebb96236_42.png)

![](img/d882eaaa6aa6ca7830f02979ebb96236_44.png)

创建包含多个子图的图形布局。
```python
fig, axes = plt.subplots(2, 2, figsize=(10, 6)) # 2x2的子图网格
axes[0, 0].hist(np.random.randn(100), bins=20) # 左上角：直方图
axes[0, 1].scatter(x, y) # 右上角：散点图
axes[1, 0].plot(np.cumsum(np.random.randn(50))) # 左下角：折线图
```

可以自定义图形的颜色、线型和标记。
```python
plt.plot(x, y, color='green', linestyle='--', marker='o', label='line1')
plt.legend() # 显示图例
```

### 添加标签与注释

为图形添加标题、坐标轴标签和注释，使其更易读。
```python
plt.title('My Plot')
plt.xlabel('Quarter')
plt.ylabel('Price')
plt.xticks([0, 250, 500, 750, 1000], ['Q1', 'Q2', 'Q3', 'Q4', 'Q5'], rotation=30)
# 添加箭头注释
plt.annotate('Trough', xy=(350, -2), xytext=(400, 0),
             arrowprops=dict(facecolor='black', shrink=0.05))
```

### DataFrame绘图与3D绘图

Pandas DataFrame可以直接调用绘图方法。
```python
df.plot() # 对DataFrame的每一列绘制线图
```

Matplotlib也支持绘制3D图形。
```python
from mpl_toolkits.mplot3d import Axes3D
fig = plt.figure()
ax = fig.add_subplot(111, projection='3d')
X, Y = np.meshgrid(x, y)
Z = np.sqrt(X**2 + Y**2)
ax.plot_surface(X, Y, Z)
ax.set_xlabel('X Label')
ax.set_ylabel('Y Label')
ax.set_zlabel('Z Label')
```

## 总结与作业 🎯

本节课中我们一起学习了Python量化金融的三个基石库：用于数值计算的NumPy，用于数据处理的Pandas，以及用于数据可视化的Matplotlib。我们掌握了从创建数组、操作数据框到绘制基础图表的核心技能。

![](img/d882eaaa6aa6ca7830f02979ebb96236_46.png)

![](img/d882eaaa6aa6ca7830f02979ebb96236_48.png)

**本节课作业**：请使用Matplotlib绘制一个三维图像，展示一个二维正态分布的密度函数（相关系数ρ可设为0或0.3）。要求为每个坐标轴添加清晰的标签，并为图形添加合适的标题。