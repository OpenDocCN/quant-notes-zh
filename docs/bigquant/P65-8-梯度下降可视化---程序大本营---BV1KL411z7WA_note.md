# 机器学习基础：P65：梯度下降可视化 🎯

![](img/35cc1e9dc02965cd00cdb1de63891075_0.png)

![](img/35cc1e9dc02965cd00cdb1de63891075_2.png)

![](img/35cc1e9dc02965cd00cdb1de63891075_4.png)

在本节课中，我们将学习如何将梯度下降算法的迭代过程进行可视化。通过绘制算法每一步的“足迹”，我们可以直观地理解它是如何一步步逼近函数最优解（最小值点）的。

![](img/35cc1e9dc02965cd00cdb1de63891075_6.png)

---

上一节我们介绍了梯度下降的核心算法实现。本节中，我们来看看如何将算法的每一步更新过程用图形展示出来。

![](img/35cc1e9dc02965cd00cdb1de63891075_8.png)

![](img/35cc1e9dc02965cd00cdb1de63891075_10.png)

![](img/35cc1e9dc02965cd00cdb1de63891075_12.png)

为了记录梯度下降的每一步，我们需要在代码中增加一个列表来存储每次更新后的 `x` 值。这个列表我们命名为 `x_`。

以下是具体实现步骤：

![](img/35cc1e9dc02965cd00cdb1de63891075_14.png)

![](img/35cc1e9dc02965cd00cdb1de63891075_16.png)

1.  **初始化记录列表**：在 `while True` 循环开始前，创建一个空列表 `x_ = []`，并将初始随机生成的 `x` 值存入其中。
2.  **记录更新过程**：在循环内部，每当 `x` 值根据梯度下降公式 `x = x - lr * grad` 更新后，就将新的 `x` 值追加到 `x_` 列表中。
3.  **添加计数器**：为了知道算法迭代了多少步，我们可以添加一个计数器 `count`，每次更新时加1。
4.  **转换数据类型**：由于我们后续绘图需要使用 `NumPy` 数组进行计算，需要将列表 `x_` 转换为 `NumPy` 数组：`x_ = np.array(x_)`。

![](img/35cc1e9dc02965cd00cdb1de63891075_18.png)

以下是实现上述记录功能的代码片段：

![](img/35cc1e9dc02965cd00cdb1de63891075_20.png)

![](img/35cc1e9dc02965cd00cdb1de63891075_22.png)

```python
import numpy as np
import matplotlib.pyplot as plt

![](img/35cc1e9dc02965cd00cdb1de63891075_24.png)

![](img/35cc1e9dc02965cd00cdb1de63891075_26.png)

# 假设已有函数 f(x) 和其导数 df(x) 的定义
# 学习率 lr 和初始 x 也已定义

![](img/35cc1e9dc02965cd00cdb1de63891075_28.png)

![](img/35cc1e9dc02965cd00cdb1de63891075_30.png)

![](img/35cc1e9dc02965cd00cdb1de63891075_32.png)

x_ = [x]  # 记录初始x值
count = 0

![](img/35cc1e9dc02965cd00cdb1de63891075_34.png)

![](img/35cc1e9dc02965cd00cdb1de63891075_36.png)

![](img/35cc1e9dc02965cd00cdb1de63891075_38.png)

while True:
    grad = df(x)
    new_x = x - lr * grad

    if abs(f(x) - f(new_x)) < 1e-6:
        break

    x = new_x
    x_.append(x)  # 记录更新后的x值
    count += 1

![](img/35cc1e9dc02965cd00cdb1de63891075_40.png)

x_ = np.array(x_)  # 转换为NumPy数组以便计算
print(f"梯度下降的次数: {count}")
```

![](img/35cc1e9dc02965cd00cdb1de63891075_42.png)

![](img/35cc1e9dc02965cd00cdb1de63891075_44.png)

---

![](img/35cc1e9dc02965cd00cdb1de63891075_46.png)

![](img/35cc1e9dc02965cd00cdb1de63891075_48.png)

![](img/35cc1e9dc02965cd00cdb1de63891075_50.png)

有了记录每一步 `x` 值的数据后，我们就可以开始绘制可视化图形了。

![](img/35cc1e9dc02965cd00cdb1de63891075_52.png)

![](img/35cc1e9dc02965cd00cdb1de63891075_54.png)

以下是绘图步骤：

![](img/35cc1e9dc02965cd00cdb1de63891075_56.png)

![](img/35cc1e9dc02965cd00cdb1de63891075_58.png)

1.  **绘制原函数曲线**：首先，我们需要绘制出目标函数 `f(x)` 的曲线，作为背景。这通过生成一组连续的 `x` 值（如 `x1 = np.linspace(0, 11.5, 100)`）并计算对应的 `y1 = f(x1)` 来实现，然后使用 `plt.plot(x1, y1)` 画出曲线。
2.  **绘制梯度下降路径**：接着，我们将 `x_` 列表中的每个点绘制在函数曲线上。横坐标是 `x_`，纵坐标是 `f(x_)`。我们使用散点图 `plt.scatter` 来绘制，并设置醒目的颜色（如红色）和点的大小以便观察。
3.  **调整图形**：为了使图形更清晰，可以使用 `plt.figure(figsize=(12,9))` 来调整画布大小。

![](img/35cc1e9dc02965cd00cdb1de63891075_60.png)

![](img/35cc1e9dc02965cd00cdb1de63891075_62.png)

以下是完整的绘图代码：

```python
# 绘制函数曲线
x1 = np.linspace(0, 11.5, 100)
y1 = f(x1)
plt.figure(figsize=(12, 9))
plt.plot(x1, y1)

![](img/35cc1e9dc02965cd00cdb1de63891075_64.png)

![](img/35cc1e9dc02965cd00cdb1de63891075_66.png)

![](img/35cc1e9dc02965cd00cdb1de63891075_68.png)

# 绘制梯度下降的每一步
plt.scatter(x_, f(x_), color='red', s=30)  # s 参数控制点的大小
plt.show()
```

![](img/35cc1e9dc02965cd00cdb1de63891075_70.png)

![](img/35cc1e9dc02965cd00cdb1de63891075_72.png)

运行代码后，你将看到一幅图：蓝色的函数曲线上，有一串红色的点。这些红点展示了梯度下降算法从初始随机点开始，每一步是如何根据梯度（斜率）方向移动的。通常，在斜率较大的地方，步长也会较大；随着接近最低点（斜率趋近于0），步长越来越小，最终密集地汇聚在最优点附近。

![](img/35cc1e9dc02965cd00cdb1de63891075_74.png)

![](img/35cc1e9dc02965cd00cdb1de63891075_76.png)

你可以通过调整学习率 `lr` 的值（例如从 `0.2` 改为 `0.3`），观察红点的分布和迭代次数 `count` 的变化，从而更深刻地理解学习率对算法收敛速度和稳定性的影响。

![](img/35cc1e9dc02965cd00cdb1de63891075_78.png)

![](img/35cc1e9dc02965cd00cdb1de63891075_80.png)

![](img/35cc1e9dc02965cd00cdb1de63891075_82.png)

---

![](img/35cc1e9dc02965cd00cdb1de63891075_84.png)

![](img/35cc1e9dc02965cd00cdb1de63891075_86.png)

本节课中我们一起学习了梯度下降算法的可视化方法。我们通过在代码中记录每次迭代的 `x` 值，并使用 `Matplotlib` 库将函数曲线和迭代路径绘制出来，从而直观地看到了梯度下降是如何“一步一步”找到函数最小值的。理解这段可视化代码对于掌握梯度下降的动态过程至关重要。请务必亲手运行并尝试修改参数，观察其变化，以加深理解。