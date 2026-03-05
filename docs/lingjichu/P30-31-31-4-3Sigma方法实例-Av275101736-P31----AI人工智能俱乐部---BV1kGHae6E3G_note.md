# 数据预处理：P31：3-Sigma方法实例 📊

在本节课中，我们将要学习一种基于统计学原理的数据去极值方法——3-Sigma方法。我们将理解其核心思想，并通过代码实现来掌握如何应用此方法处理数据中的异常值。

## 概述

上一节我们介绍了去极值的概念，本节中我们来看看一种基于正态分布假设的经典方法：3-Sigma方法。它的原理是利用数据的均值和标准差来定义一个“正常”数据的范围，并将超出此范围的值视为极值进行处理。

## 3-Sigma方法原理 📈

3-Sigma方法基于一个核心假设：数据服从或近似服从正态分布（也称为高斯分布）。

如果大家理解正态分布比较麻烦，可以将其想象为一个“正常”的分布。例如，你去银行贷款，银行多借或少借你几块钱是正常情况；但如果多借或少借你十万八万，这就显得不正常了。在数据中，大多数“正常”的数据点会聚集在均值附近，而极值则出现在远离均值的位置。

我们来看下面这张正态分布图。图中，越靠近中心（均值）的区域，阴影面积越大，代表事件发生的概率越高。越向两侧延伸，概率则越低。极值，即离群点，通常就落在这些概率很低的偏远区域。

![](img/bb26a03d0fb6b456033ed804c713dba4_1.png)

所谓“3-Sigma”，就是指以均值为中心，向左右各扩展3倍标准差（σ）所构成的区间。标准差是衡量数据波动大小的指标。

![](img/bb26a03d0fb6b456033ed804c713dba4_1.png)

从图中可以看到，区间范围越大（例如±3σ），数据点落在这个区间外的可能性就越小。根据统计学知识，在标准正态分布中：
*   **均值 ± 1σ** 的区间包含了约 **68.3%** 的数据。
*   **均值 ± 2σ** 的区间包含了约 **95.4%** 的数据。
*   **均值 ± 3σ** 的区间包含了约 **99.7%** 的数据。

因此，3-Sigma方法的思路是：假设数据经过变换后服从正态分布，那么我们可以认为落在 **均值 ± 3倍标准差** 区间内的数据（约99.7%）是“正常”的，而落在该区间外的数据（约0.3%）则被视为“极值”并进行规范处理。

![](img/bb26a03d0fb6b456033ed804c713dba4_3.png)

## 代码实现 💻

理解了原理后，我们来看看如何用代码实现3-Sigma方法。其核心是计算数据的均值（mean）和标准差（std），然后根据指定的倍数N来确定上下限。

以下是实现步骤：

首先，我们需要计算选定数据列的均值和标准差。

![](img/bb26a03d0fb6b456033ed804c713dba4_3.png)

```python
# 计算均值和标准差
mean = data['column_name'].mean()
std = data['column_name'].std()
```

![](img/bb26a03d0fb6b456033ed804c713dba4_5.png)

接着，根据均值和标准差计算数据的上下限。上限为 `均值 + N * 标准差`，下限为 `均值 - N * 标准差`。

```python
# 设定倍数N，例如N=3代表3-Sigma
N = 3

# 计算上限和下限
upper_limit = mean + N * std
lower_limit = mean - N * std
```

![](img/bb26a03d0fb6b456033ed804c713dba4_5.png)

![](img/bb26a03d0fb6b456033ed804c713dba4_7.png)

![](img/bb26a03d0fb6b456033ed804c713dba4_7.png)

最后，对原始数据进行规范：将所有大于上限的值设置为上限值，将所有小于下限的值设置为下限值。这样，极值就被“拉回”到正常范围内，而不是被删除。

```python
# 进行去极值处理
data['column_name_processed'] = data['column_name'].clip(lower=lower_limit, upper=upper_limit)
```

![](img/bb26a03d0fb6b456033ed804c713dba4_9.png)

![](img/bb26a03d0fb6b456033ed804c713dba4_9.png)

将以上步骤封装成一个函数，便于调用。

![](img/bb26a03d0fb6b456033ed804c713dba4_11.png)

```python
def three_sigma(data_series, n=3):
    """
    使用3-Sigma方法去极值
    :param data_series: 输入的数据序列（Pandas Series）
    :param n: 标准差的倍数，默认为3
    :return: 处理后的数据序列
    """
    mean = data_series.mean()
    std = data_series.std()
    upper_limit = mean + n * std
    lower_limit = mean - n * std
    return data_series.clip(lower=lower_limit, upper=upper_limit)
```

![](img/bb26a03d0fb6b456033ed804c713dba4_13.png)

![](img/bb26a03d0fb6b456033ed804c713dba4_11.png)

![](img/bb26a03d0fb6b456033ed804c713dba4_15.png)

## 方法应用与对比 🔍

![](img/bb26a03d0fb6b456033ed804c713dba4_17.png)

我们可以应用这个函数，并可视化处理效果。为了更清晰地展示，我们先将倍数N设为1（即68.3%的区间）。

![](img/bb26a03d0fb6b456033ed804c713dba4_19.png)

```python
# 应用函数，N=1
processed_data = three_sigma(original_data, n=1)

![](img/bb26a03d0fb6b456033ed804c713dba4_21.png)

# 可以绘制处理前后的数据分布图进行对比
import matplotlib.pyplot as plt
fig, axes = plt.subplots(1, 2, figsize=(12, 4))
axes[0].hist(original_data, bins=50, color='skyblue')
axes[0].set_title('原始数据分布')
axes[1].hist(processed_data, bins=50, color='lightcoral')
axes[1].set_title('3-Sigma (N=1) 处理后分布')
plt.show()
```

![](img/bb26a03d0fb6b456033ed804c713dba4_23.png)

![](img/bb26a03d0fb6b456033ed804c713dba4_23.png)

执行代码后，可以看到处理后的数据分布。图中，绿色基线通常保持不变，而蓝色的阈值线（上下限）会根据不同的N值和数据本身发生变化。

![](img/bb26a03d0fb6b456033ed804c713dba4_25.png)

![](img/bb26a03d0fb6b456033ed804c713dba4_27.png)

![](img/bb26a03d0fb6b456033ed804c713dba4_27.png)

在实际任务中，上下限的具体数值会因数据不同而略有差异。例如，在这个任务中上限可能在60左右，在另一个任务中可能不到50。

![](img/bb26a03d0fb6b456033ed804c713dba4_29.png)

![](img/bb26a03d0fb6b456033ed804c713dba4_29.png)

![](img/bb26a03d0fb6b456033ed804c713dba4_31.png)

选择哪种去极值方法（如分位数法、MAD法、3-Sigma法）以及设定何种参数（如N的取值），取决于实际的数据特性和分析需求。它们都是数据预处理中常用的不同策略，大家可以多进行实验来选择最合适的一种。

![](img/bb26a03d0fb6b456033ed804c713dba4_31.png)

![](img/bb26a03d0fb6b456033ed804c713dba4_33.png)

## 总结

![](img/bb26a03d0fb6b456033ed804c713dba4_35.png)

本节课中我们一起学习了3-Sigma去极值方法。我们来回顾一下核心要点：

![](img/bb26a03d0fb6b456033ed804c713dba4_37.png)

1.  **核心思想**：该方法基于数据服从正态分布的假设，认为距离均值超过3倍标准差的数据点属于小概率事件，应视为极值。
2.  **关键步骤**：计算数据的**均值（μ）**和**标准差（σ）**，然后根据公式 **上限 = μ + Nσ** 和 **下限 = μ - Nσ** 确定正常值范围。
3.  **处理方式**：去极值**不是删除数据**，而是通过“截断”将超出上下限的值规范到边界上，使所有数据落在指定范围内。
4.  **方法定位**：这是数据预处理“三步走”（去极值、标准化、缺失值处理）中第一步“去极值”的常用方法之一。

主要就是这三种去极值方法（分位数、MAD、3-Sigma）我们都介绍完了。掌握它们，能帮助我们在数据分析前有效地平滑数据，减少异常值对模型的干扰。

![](img/bb26a03d0fb6b456033ed804c713dba4_39.png)

![](img/bb26a03d0fb6b456033ed804c713dba4_39.png)