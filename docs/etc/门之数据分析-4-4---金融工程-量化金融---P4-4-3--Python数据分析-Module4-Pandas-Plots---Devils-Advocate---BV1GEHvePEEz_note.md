# 量化交易：Python入门之数据分析：4-3. Python数据分析：Pandas绘图 📊

在本节课中，我们将学习如何使用Pandas库进行数据可视化。我们将探索Pandas内置的绘图功能，创建多种类型的图表，并学习如何调整图表的样式和外观。

---

## 导入必要的库

![](img/8ea732c6f3047bbc90703421a5ce1adf_1.png)

首先，我们需要导入进行数据分析和绘图所需的库。

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
```

为了在Jupyter Notebook中正确显示图表，我们需要添加以下魔法命令。

```python
%matplotlib notebook
```

![](img/8ea732c6f3047bbc90703421a5ce1adf_3.png)

---

![](img/8ea732c6f3047bbc90703421a5ce1adf_5.png)

## 设置绘图样式

![](img/8ea732c6f3047bbc90703421a5ce1adf_6.png)

Matplotlib提供了多种预设的绘图样式，可以改变图表的外观。我们可以查看所有可用的样式。

```python
print(plt.style.available)
```

![](img/8ea732c6f3047bbc90703421a5ce1adf_8.png)

以下是可用的样式列表：

![](img/8ea732c6f3047bbc90703421a5ce1adf_10.png)

*   `‘bmh’`
*   `‘classic’`
*   `‘dark_background’`
*   `‘fast’`
*   `‘fivethirtyeight’`
*   `‘ggplot’`
*   `‘grayscale’`
*   `‘seaborn-bright’`
*   `‘seaborn-colorblind’`
*   `‘seaborn-dark-palette’`
*   `‘seaborn-dark’`
*   `‘seaborn-darkgrid’`
*   `‘seaborn-deep’`
*   `‘seaborn-muted’`
*   `‘seaborn-notebook’`
*   `‘seaborn-paper’`
*   `‘seaborn-pastel’`
*   `‘seaborn-poster’`
*   `‘seaborn-talk’`
*   `‘seaborn-ticks’`
*   `‘seaborn-white’`
*   `‘seaborn-whitegrid’`
*   `‘seaborn’`
*   `‘Solarize_Light2’`
*   `‘tableau-colorblind10’`
*   `‘_classic_test’`

我们可以选择并使用一个样式。例如，使用`‘seaborn-dark-palette’`。

```python
plt.style.use(‘seaborn-dark-palette’)
```

![](img/8ea732c6f3047bbc90703421a5ce1adf_12.png)

---

![](img/8ea732c6f3047bbc90703421a5ce1adf_14.png)

## 创建示例数据

![](img/8ea732c6f3047bbc90703421a5ce1adf_16.png)

为了演示绘图功能，我们首先创建一个包含随机数据的DataFrame。设置随机种子可以确保每次运行代码时生成相同的数据。

![](img/8ea732c6f3047bbc90703421a5ce1adf_17.png)

```python
np.random.seed(123)
dates = pd.date_range(‘20230711’, periods=5)
df = pd.DataFrame(np.random.randn(5, 3), index=dates, columns=[‘A’, ‘B’, ‘C’])
print(df)
```

![](img/8ea732c6f3047bbc90703421a5ce1adf_18.png)

---

![](img/8ea732c6f3047bbc90703421a5ce1adf_20.png)

## 绘制基本图表

![](img/8ea732c6f3047bbc90703421a5ce1adf_21.png)

上一节我们创建了示例数据，本节中我们来看看如何使用DataFrame直接绘制图表。

![](img/8ea732c6f3047bbc90703421a5ce1adf_23.png)

### 绘制线图

![](img/8ea732c6f3047bbc90703421a5ce1adf_25.png)

我们可以直接调用DataFrame的`.plot()`方法来绘制线图。

![](img/8ea732c6f3047bbc90703421a5ce1adf_27.png)

```python
df.plot()
```

此方法会为DataFrame中的每一列绘制一条折线。在Jupyter Notebook中，图表通常是交互式的，可以缩放和保存。

![](img/8ea732c6f3047bbc90703421a5ce1adf_29.png)

![](img/8ea732c6f3047bbc90703421a5ce1adf_30.png)

### 绘制散点图

![](img/8ea732c6f3047bbc90703421a5ce1adf_32.png)

如果我们只想绘制特定列之间的关系，可以使用散点图。以下是绘制列‘A’和‘B’的散点图的示例。

```python
df.plot.scatter(x=‘A’, y=‘B’)
```

![](img/8ea732c6f3047bbc90703421a5ce1adf_34.png)

我们可以通过参数`c`（颜色）和`s`（大小）来引入第三维数据。例如，用列‘C’的值来映射点的颜色和大小。

![](img/8ea732c6f3047bbc90703421a5ce1adf_36.png)

```python
df.plot.scatter(x=‘A’, y=‘B’, c=‘C’, s=df[‘C’]*100, colormap=‘viridis’)
```

![](img/8ea732c6f3047bbc90703421a5ce1adf_38.png)

> **注意**：如果用于映射颜色或大小的列中包含零值或异常值，可能会导致图表显示异常。在实际操作中，需要检查数据。

![](img/8ea732c6f3047bbc90703421a5ce1adf_40.png)

### 使用Axes对象精细控制

![](img/8ea732c6f3047bbc90703421a5ce1adf_42.png)

通过`ax`参数，我们可以将图表绘制在特定的坐标轴对象上，从而进行更精细的控制，例如调整宽高比。

![](img/8ea732c6f3047bbc90703421a5ce1adf_44.png)

```python
fig, ax = plt.subplots()
df.plot.scatter(x=‘A’, y=‘B’, c=‘C’, s=df[‘C’]*100, colormap=‘viridis’, ax=ax)
ax.set_aspect(‘equal’)
```

这种方法生成的图表布局通常更加紧凑和规整。

![](img/8ea732c6f3047bbc90703421a5ce1adf_46.png)

![](img/8ea732c6f3047bbc90703421a5ce1adf_47.png)

---

![](img/8ea732c6f3047bbc90703421a5ce1adf_49.png)

## 绘制其他统计图表

除了线图和散点图，Pandas还支持绘制多种统计图表。

### 箱线图

箱线图用于展示数据分布情况。

![](img/8ea732c6f3047bbc90703421a5ce1adf_51.png)

```python
df.plot.box()
```

### 直方图

![](img/8ea732c6f3047bbc90703421a5ce1adf_53.png)

直方图用于展示数据的频率分布。我们可以通过`alpha`参数调整透明度。

```python
df.plot.hist(alpha=0.7)
```

---

![](img/8ea732c6f3047bbc90703421a5ce1adf_55.png)

## 使用真实数据集绘图

为了展示更复杂的图表，我们将使用一个经典的真实数据集——鸢尾花（Iris）数据集。

![](img/8ea732c6f3047bbc90703421a5ce1adf_57.png)

### 加载鸢尾花数据集

我们将使用`scikit-learn`库来加载数据集。如果尚未安装该库，可以使用`pip install scikit-learn`命令安装。

![](img/8ea732c6f3047bbc90703421a5ce1adf_59.png)

```python
from sklearn.datasets import load_iris
iris_data = load_iris()
```

![](img/8ea732c6f3047bbc90703421a5ce1adf_61.png)

加载的数据包含特征数据（`data`）和标签（`target`）。标签对应三种鸢尾花的品种。

![](img/8ea732c6f3047bbc90703421a5ce1adf_63.png)

```python
print(iris_data.target_names) # 输出: [‘setosa’ ‘versicolor’ ‘virginica’]
```

![](img/8ea732c6f3047bbc90703421a5ce1adf_65.png)

我们将其转换为一个完整的Pandas DataFrame。

![](img/8ea732c6f3047bbc90703421a5ce1adf_66.png)

```python
df_iris = pd.DataFrame(iris_data.data, columns=iris_data.feature_names)
df_iris[‘target’] = iris_data.target
df_iris[‘name’] = df_iris[‘target’].apply(lambda x: iris_data.target_names[x])
print(df_iris.head())
```

![](img/8ea732c6f3047bbc90703421a5ce1adf_68.png)

### 绘制散点图矩阵

散点图矩阵可以一次性展示数据集中所有数值型特征两两之间的关系。

```python
pd.plotting.scatter_matrix(df_iris.iloc[:, :4], figsize=(10, 10))
```

![](img/8ea732c6f3047bbc90703421a5ce1adf_70.png)

### 绘制平行坐标图

![](img/8ea732c6f3047bbc90703421a5ce1adf_72.png)

平行坐标图适用于展示具有多个特征的高维数据。每条线代表一个数据样本，特征值由在不同垂直轴上的位置表示。

![](img/8ea732c6f3047bbc90703421a5ce1adf_74.png)

```python
pd.plotting.parallel_coordinates(df_iris, ‘name’)
```

在这个图表中，不同颜色的线代表了不同品种的鸢尾花（例如，紫色代表‘virginica’，浅绿色代表‘setosa’），可以直观地比较不同品种在各个特征维度上的差异。

![](img/8ea732c6f3047bbc90703421a5ce1adf_76.png)

---

![](img/8ea732c6f3047bbc90703421a5ce1adf_78.png)

## 总结

![](img/8ea732c6f3047bbc90703421a5ce1adf_80.png)

本节课中我们一起学习了Pandas强大的数据可视化功能。我们从设置绘图样式开始，学习了如何创建线图、散点图、箱线图和直方图等基础图表。随后，我们使用真实的鸢尾花数据集，实践了绘制散点图矩阵和平行坐标图这两种用于探索高维数据关系的图表。掌握这些绘图技巧，能够帮助我们在量化分析和金融工程中更直观地理解和展示数据规律。