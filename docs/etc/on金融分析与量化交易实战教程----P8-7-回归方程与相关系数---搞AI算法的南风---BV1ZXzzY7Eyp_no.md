# Python金融分析与量化交易实战教程：P8：回归方程与相关系数实例 📈

![](img/814c096636b393a8c4476fa4f5dc82b9_1.png)

![](img/814c096636b393a8c4476fa4f5dc82b9_3.png)

在本节课中，我们将学习如何使用Python构建回归方程并计算相关系数，这是金融数据分析中衡量两个变量关系的基础技能。我们将通过实际代码演示，从数据预处理到结果可视化，一步步完成分析。

![](img/814c096636b393a8c4476fa4f5dc82b9_5.png)

---

## 构建回归方程

上一节我们介绍了数据的基本处理，本节中我们来看看如何构建回归方程来描述两个变量之间的关系。构建回归方程的方法很多，我们可以使用多种工具包。这里我们使用NumPy库中的`polyfit`函数来计算回归系数。

![](img/814c096636b393a8c4476fa4f5dc82b9_7.png)

![](img/814c096636b393a8c4476fa4f5dc82b9_9.png)

`polyfit`函数可以帮助我们计算回归系数，其核心公式为：
**Y = KX + B**
其中，K是斜率，B是截距。

![](img/814c096636b393a8c4476fa4f5dc82b9_11.png)

![](img/814c096636b393a8c4476fa4f5dc82b9_13.png)

以下是使用`polyfit`函数的关键步骤：

![](img/814c096636b393a8c4476fa4f5dc82b9_15.png)

![](img/814c096636b393a8c4476fa4f5dc82b9_17.png)

1.  **准备数据**：首先，我们需要两个指标的数据。例如，我们有两个变量`X`和`Y`。
2.  **调用函数**：使用`np.polyfit(X, Y, deg)`函数。参数`deg`指定了回归方程的次数。`deg=1`表示我们构建一个一次线性方程（即Y=KX+B）。
3.  **处理异常**：在计算前，需要确保数据中没有空值（NaN），否则可能导致计算错误。

![](img/814c096636b393a8c4476fa4f5dc82b9_19.png)

以下是构建回归方程的示例代码：
```python
import numpy as np
import pandas as pd

![](img/814c096636b393a8c4476fa4f5dc82b9_21.png)

# 假设df是一个包含‘X’和‘Y’两列的DataFrame
# 首先，删除可能存在的空值
df.dropna(inplace=True)

![](img/814c096636b393a8c4476fa4f5dc82b9_23.png)

# 提取X和Y的数据
X = df['X'].values
Y = df['Y'].values

![](img/814c096636b393a8c4476fa4f5dc82b9_25.png)

# 使用polyfit计算回归系数，deg=1表示线性回归
coefficients = np.polyfit(X, Y, deg=1)
# coefficients是一个数组，[K, B]
K, B = coefficients
print(f"回归方程为: Y = {K:.4f} * X + {B:.4f}")
```

![](img/814c096636b393a8c4476fa4f5dc82b9_27.png)

![](img/814c096636b393a8c4476fa4f5dc82b9_29.png)

---

![](img/814c096636b393a8c4476fa4f5dc82b9_31.png)

## 可视化回归结果

![](img/814c096636b393a8c4476fa4f5dc82b9_33.png)

得到回归方程后，我们可以将其可视化，与原始数据点一起展示，以便直观地观察拟合效果。

![](img/814c096636b393a8c4476fa4f5dc82b9_35.png)

以下是绘制散点图及回归线的步骤：

![](img/814c096636b393a8c4476fa4f5dc82b9_37.png)

1.  **绘制散点图**：首先，使用`matplotlib`绘制原始数据`(X, Y)`的散点图。
2.  **计算回归线**：利用上一步得到的系数`K`和`B`，为`X`值计算对应的预测`Y`值，生成回归线上的点。
3.  **绘制回归线**：将计算出的回归线点绘制在同一个图上。

![](img/814c096636b393a8c4476fa4f5dc82b9_39.png)

以下是可视化代码示例：
```python
import matplotlib.pyplot as plt

![](img/814c096636b393a8c4476fa4f5dc82b9_41.png)

![](img/814c096636b393a8c4476fa4f5dc82b9_43.png)

# 绘制原始数据散点图
plt.figure(figsize=(16, 9))
plt.scatter(X, Y, label='原始数据点')

![](img/814c096636b393a8c4476fa4f5dc82b9_45.png)

![](img/814c096636b393a8c4476fa4f5dc82b9_47.png)

# 计算回归线上的Y值
Y_pred = K * X + B

![](img/814c096636b393a8c4476fa4f5dc82b9_49.png)

![](img/814c096636b393a8c4476fa4f5dc82b9_51.png)

# 绘制回归线
plt.plot(X, Y_pred, color='red', linewidth=2, label='回归线')

![](img/814c096636b393a8c4476fa4f5dc82b9_53.png)

![](img/814c096636b393a8c4476fa4f5dc82b9_55.png)

plt.xlabel('X')
plt.ylabel('Y')
plt.title('回归方程可视化')
plt.legend()
plt.show()
```

![](img/814c096636b393a8c4476fa4f5dc82b9_57.png)

![](img/814c096636b393a8c4476fa4f5dc82b9_59.png)

---

![](img/814c096636b393a8c4476fa4f5dc82b9_61.png)

## 计算相关系数

![](img/814c096636b393a8c4476fa4f5dc82b9_63.png)

除了回归方程，相关系数是另一个衡量两个变量线性关系强度和方向的常用指标。在Pandas中，计算相关系数非常简单。

![](img/814c096636b393a8c4476fa4f5dc82b9_65.png)

以下是计算相关系数的步骤：

![](img/814c096636b393a8c4476fa4f5dc82b9_67.png)

1.  **选择数据**：从DataFrame中选择需要计算相关性的两列数据。
2.  **调用函数**：使用`.corr()`方法即可计算出皮尔逊相关系数矩阵。

![](img/814c096636b393a8c4476fa4f5dc82b9_69.png)

以下是计算相关系数的代码示例：
```python
# 计算两列数据之间的相关系数
correlation_matrix = df[['X', 'Y']].corr()
print(correlation_matrix)
```
相关系数矩阵是一个对称矩阵，对角线上的值均为1（变量与自身的相关性为完全正相关），非对角线上的值就是两个变量之间的相关系数，其值介于-1到1之间。

![](img/814c096636b393a8c4476fa4f5dc82b9_71.png)

---

![](img/814c096636b393a8c4476fa4f5dc82b9_73.png)

![](img/814c096636b393a8c4476fa4f5dc82b9_75.png)

## 附加任务：滑动窗口相关系数分析

![](img/814c096636b393a8c4476fa4f5dc82b9_77.png)

在金融时间序列分析中，我们常常关心关系是否随时间变化。因此，我们可以计算滑动窗口内的相关系数。

![](img/814c096636b393a8c4476fa4f5dc82b9_79.png)

以下是计算滑动窗口相关系数的思路：

1.  **定义窗口**：设定一个窗口大小（例如250个交易日）。
2.  **滑动计算**：让这个窗口在时间序列上滑动，对于每一个窗口，计算窗口内两个指标的相关系数。
3.  **结果序列**：这样我们就得到了一个随时间变化的相关系数序列。

![](img/814c096636b393a8c4476fa4f5dc82b9_81.png)

以下是实现代码示例：
```python
# 设定窗口大小
window_size = 250

![](img/814c096636b393a8c4476fa4f5dc82b9_83.png)

# 计算X序列在滑动窗口内与Y序列的相关系数
rolling_corr = df['X'].rolling(window=window_size).corr(df['Y'])

![](img/814c096636b393a8c4476fa4f5dc82b9_85.png)

![](img/814c096636b393a8c4476fa4f5dc82b9_87.png)

# 可视化结果
plt.figure(figsize=(16, 6))
rolling_corr.plot()
plt.title(f'滑动窗口（大小={window_size}）相关系数变化')
plt.xlabel('时间')
plt.ylabel('相关系数')
plt.axhline(y=0, color='black', linestyle='--', linewidth=0.5) # 添加y=0参考线
plt.show()
```

![](img/814c096636b393a8c4476fa4f5dc82b9_89.png)

---

![](img/814c096636b393a8c4476fa4f5dc82b9_91.png)

![](img/814c096636b393a8c4476fa4f5dc82b9_93.png)

## 总结

![](img/814c096636b393a8c4476fa4f5dc82b9_95.png)

![](img/814c096636b393a8c4476fa4f5dc82b9_97.png)

本节课中我们一起学习了金融数据分析中的两个核心工具：**回归方程**与**相关系数**。

![](img/814c096636b393a8c4476fa4f5dc82b9_99.png)

*   **回归方程**（`np.polyfit`）帮助我们量化一个变量如何随另一个变量变化，并用一条直线（或曲线）进行拟合。
*   **相关系数**（`.corr()`）则简洁地告诉我们两个变量线性关系的强度和方向。
*   通过**滑动窗口分析**，我们可以观察这种关系如何随时间动态变化，这在金融市场的实践中尤为重要。

![](img/814c096636b393a8c4476fa4f5dc82b9_101.png)

![](img/814c096636b393a8c4476fa4f5dc82b9_103.png)

建议大家在课后亲自动手实践一遍代码，尝试修改参数（如窗口大小、回归方程次数），以加深对概念和方法的理解。这些技能是进行更复杂量化分析的基础。