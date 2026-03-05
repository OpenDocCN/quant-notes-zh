# Python金融分析量化交易：P11：回归方程与相关系数实例 📈

![](img/703eb61e8dfae4786b78aa78bc73f8e9_1.png)

![](img/703eb61e8dfae4786b78aa78bc73f8e9_3.png)

在本节课中，我们将学习如何使用Python构建回归方程和计算相关系数，这是金融数据分析中量化两个变量间关系的核心技能。我们将通过具体的代码实例，展示如何从数据预处理到结果可视化的完整流程。

![](img/703eb61e8dfae4786b78aa78bc73f8e9_5.png)

---

## 构建回归方程

![](img/703eb61e8dfae4786b78aa78bc73f8e9_7.png)

上一节我们介绍了数据的基本处理，本节中我们来看看如何构建回归方程来描述两个变量间的线性关系。构建回归方程的方法很多，我们可以使用多种工具包。这里我们使用NumPy库中的`polyfit`函数来计算回归系数。

![](img/703eb61e8dfae4786b78aa78bc73f8e9_9.png)

![](img/703eb61e8dfae4786b78aa78bc73f8e9_11.png)

`polyfit`函数可以帮助我们计算回归模型的系数。其基本形式是拟合一个多项式，例如`Y = KX + B`，其中`K`是一次项系数，`B`是常数项（零次项系数）。

![](img/703eb61e8dfae4786b78aa78bc73f8e9_13.png)

![](img/703eb61e8dfae4786b78aa78bc73f8e9_15.png)

以下是使用`polyfit`函数的关键步骤：

![](img/703eb61e8dfae4786b78aa78bc73f8e9_17.png)

![](img/703eb61e8dfae4786b78aa78bc73f8e9_19.png)

1.  **准备数据**：确保用于计算的两个数据序列没有空值。
2.  **调用函数**：传入自变量`X`、因变量`Y`以及多项式的阶数`deg`。
3.  **理解参数**：参数`deg=1`表示拟合一个一次多项式（即直线`Y = KX + B`）。如果`deg=2`，则会拟合包含`X`二次项的曲线。

![](img/703eb61e8dfae4786b78aa78bc73f8e9_21.png)

![](img/703eb61e8dfae4786b78aa78bc73f8e9_23.png)

首先，我们需要处理数据中的空值，这是构建模型前的必要步骤。

```python
# 假设df是我们的DataFrame，包含‘X_column’和‘Y_column’两列
# 使用dropna处理空值
df.dropna(subset=['X_column', 'Y_column'], inplace=True)

![](img/703eb61e8dfae4786b78aa78bc73f8e9_25.png)

# 提取处理后的数据
x_data = df['X_column'].values
y_data = df['Y_column'].values
```

![](img/703eb61e8dfae4786b78aa78bc73f8e9_27.png)

接着，我们使用`np.polyfit`来计算回归系数。

![](img/703eb61e8dfae4786b78aa78bc73f8e9_29.png)

![](img/703eb61e8dfae4786b78aa78bc73f8e9_31.png)

```python
import numpy as np

![](img/703eb61e8dfae4786b78aa78bc73f8e9_33.png)

# 使用polyfit计算回归系数，deg=1表示线性回归
coefficients = np.polyfit(x_data, y_data, deg=1)
# coefficients 是一个数组，例如 [k, b]，对应 Y = k*X + b
k, b = coefficients
print(f"回归方程为: Y = {k:.4f} * X + {b:.4f}")
```

![](img/703eb61e8dfae4786b78aa78bc73f8e9_35.png)

---

![](img/703eb61e8dfae4786b78aa78bc73f8e9_37.png)

![](img/703eb61e8dfae4786b78aa78bc73f8e9_39.png)

## 可视化回归结果

![](img/703eb61e8dfae4786b78aa78bc73f8e9_41.png)

得到回归方程后，我们可以将其可视化，与原始数据散点图叠加，直观地观察拟合效果。

![](img/703eb61e8dfae4786b78aa78bc73f8e9_43.png)

以下是绘制散点图和回归线的步骤：

![](img/703eb61e8dfae4786b78aa78bc73f8e9_45.png)

![](img/703eb61e8dfae4786b78aa78bc73f8e9_47.png)

1.  绘制原始数据的散点图。
2.  根据回归方程，生成一组对应的预测Y值。
3.  在同一张图上绘制回归线。

![](img/703eb61e8dfae4786b78aa78bc73f8e9_49.png)

![](img/703eb61e8dfae4786b78aa78bc73f8e9_51.png)

```python
import matplotlib.pyplot as plt

![](img/703eb61e8dfae4786b78aa78bc73f8e9_53.png)

![](img/703eb61e8dfae4786b78aa78bc73f8e9_55.png)

# 1. 绘制散点图
plt.figure(figsize=(10, 6))
plt.scatter(x_data, y_data, s=16, label='原始数据点') # s参数控制点的大小

![](img/703eb61e8dfae4786b78aa78bc73f8e9_57.png)

![](img/703eb61e8dfae4786b78aa78bc73f8e9_59.png)

# 2. 生成回归线的点
# 利用求得的系数k, b，为x_data计算对应的预测y值
y_pred = k * x_data + b

![](img/703eb61e8dfae4786b78aa78bc73f8e9_61.png)

![](img/703eb61e8dfae4786b78aa78bc73f8e9_63.png)

# 3. 绘制回归线
plt.plot(x_data, y_pred, color='red', linewidth=2, label='回归线')

![](img/703eb61e8dfae4786b78aa78bc73f8e9_65.png)

![](img/703eb61e8dfae4786b78aa78bc73f8e9_67.png)

plt.xlabel('X 指标')
plt.ylabel('Y 指标')
plt.title('指标关系与回归线')
plt.legend()
plt.grid(True)
plt.show()
```

![](img/703eb61e8dfae4786b78aa78bc73f8e9_69.png)

**注意**：在金融分析中，构建回归方程时需注意数据的预处理。例如，本教程中使用的是指标的增长率数据，而非原始价格。选择合适的预处理数据（如收益率、对数收益率等）对分析结果至关重要。

![](img/703eb61e8dfae4786b78aa78bc73f8e9_71.png)

---

![](img/703eb61e8dfae4786b78aa78bc73f8e9_73.png)

![](img/703eb61e8dfae4786b78aa78bc73f8e9_75.png)

## 计算相关系数

除了回归方程，相关系数是另一个衡量两个变量线性关系强度和方向的常用指标。在Pandas中，计算相关系数非常简单。

![](img/703eb61e8dfae4786b78aa78bc73f8e9_77.png)

![](img/703eb61e8dfae4786b78aa78bc73f8e9_79.png)

我们可以直接计算整个序列的相关系数矩阵。

![](img/703eb61e8dfae4786b78aa78bc73f8e9_81.png)

![](img/703eb61e8dfae4786b78aa78bc73f8e9_83.png)

```python
import pandas as pd

# 假设df包含‘col1’和‘col2’两列数据
correlation_matrix = df[['col1', 'col2']].corr()
print(correlation_matrix)
```
该矩阵对角线上的值均为1（变量与自身的完全相关），非对角线上的值即为两个变量间的相关系数，其值在-1到1之间。

![](img/703eb61e8dfae4786b78aa78bc73f8e9_85.png)

---

![](img/703eb61e8dfae4786b78aa78bc73f8e9_87.png)

![](img/703eb61e8dfae4786b78aa78bc73f8e9_89.png)

## 进阶应用：滑动窗口相关系数

![](img/703eb61e8dfae4786b78aa78bc73f8e9_91.png)

在时间序列分析中，我们常常关心关系如何随时间变化。这时，可以计算滑动窗口内的相关系数。

![](img/703eb61e8dfae4786b78aa78bc73f8e9_93.png)

以下是实现滑动窗口相关系数的思路：
1.  设定一个窗口大小（例如250个交易日）。
2.  在时间序列上滑动这个窗口。
3.  在每个窗口内，计算两个序列的相关系数。

![](img/703eb61e8dfae4786b78aa78bc73f8e9_95.png)

![](img/703eb61e8dfae4786b78aa78bc73f8e9_97.png)

```python
# 设置窗口大小
window_size = 250

![](img/703eb61e8dfae4786b78aa78bc73f8e9_99.png)

# 计算col1相对于col2的滑动窗口相关系数
rolling_corr = df['col1'].rolling(window=window_size).corr(df['col2'])

![](img/703eb61e8dfae4786b78aa78bc73f8e9_101.png)

![](img/703eb61e8dfae4786b78aa78bc73f8e9_103.png)

# 可视化结果
plt.figure(figsize=(12, 6))
rolling_corr.plot()
plt.title(f'{window_size}期滑动窗口相关系数变化')
plt.xlabel('时间')
plt.ylabel('相关系数')
plt.grid(True)
plt.show()
```
通过这个图表，我们可以观察两个金融指标间的相关性是稳定的，还是在特定时期发生了显著变化。

![](img/703eb61e8dfae4786b78aa78bc73f8e9_105.png)

![](img/703eb61e8dfae4786b78aa78bc73f8e9_107.png)

![](img/703eb61e8dfae4786b78aa78bc73f8e9_109.png)

---

![](img/703eb61e8dfae4786b78aa78bc73f8e9_111.png)

![](img/703eb61e8dfae4786b78aa78bc73f8e9_113.png)

## 总结

![](img/703eb61e8dfae4786b78aa78bc73f8e9_115.png)

![](img/703eb61e8dfae4786b78aa78bc73f8e9_117.png)

本节课中我们一起学习了金融数据分析中的两个核心关系量化工具：
1.  **构建回归方程**：使用`np.polyfit`函数可以方便地计算线性回归系数，并通过可视化将拟合线与数据对比。
2.  **计算相关系数**：使用`.corr()`方法可以快速计算变量间的静态相关系数。通过`.rolling().corr()`方法，我们可以进一步分析相关系数随时间动态变化的情况，这对于理解市场不同阶段的结构性变化非常有帮助。

![](img/703eb61e8dfae4786b78aa78bc73f8e9_119.png)

![](img/703eb61e8dfae4786b78aa78bc73f8e9_121.png)

建议大家在课后对照代码实践一遍，并尝试修改参数（如窗口大小、多项式阶数），以加深对代码功能和应用场景的理解。这些技能是进行更复杂金融量化分析的基础。