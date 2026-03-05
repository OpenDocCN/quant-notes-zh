# Python金融分析与量化交易实战课程：P27：3Sigma方法实例 📊

在本节课中，我们将要学习一种在金融数据预处理中常用的去极值方法——3Sigma方法。我们将从正态分布的基本概念入手，理解3Sigma方法的原理，并基于Python实现该方法，最后将其应用于示例数据。

## 正态分布与3Sigma原理

上一节我们介绍了分位数和中位数绝对偏差去极值法，本节中我们来看看基于统计学原理的3Sigma方法。

3Sigma方法的原理基于正态分布。正态分布是一种常见的概率分布，其形状呈对称的钟形曲线。在正态分布中，大多数数据点会聚集在均值附近，而远离均值的数据点出现的概率则较低。

为了便于理解，可以想象一个银行贷款的场景：银行多借或少借你少量金额是正常情况，但多借或少借你巨额款项则属于异常情况。在数据中，这些“异常”的巨额款项就相当于我们需要处理的极值或离群点。

在标准正态分布中，均值（μ）为0，标准差（σ）为1。数据偏离均值的程度可以用“几倍的标准差”来衡量。下图展示了这个概念：

![](img/ea3f386912a58855569f9018ae4dedcb_1.png)

![](img/ea3f386912a58855569f9018ae4dedcb_1.png)

图中，`X`代表均值。从均值向两侧扩展：
*   **均值 ± 1σ**：这个区间包含了大约68.3%的数据。
*   **均值 ± 2σ**：这个区间包含了大约95.4%的数据。
*   **均值 ± 3σ**：这个区间包含了大约99.7%的数据。

3Sigma方法的核心思想是：假设数据经过某种变换后服从正态分布，那么我们可以认为，落在`均值 ± 3倍标准差`区间内的数据（约99.7%）是“正常”数据，而落在这个区间外的数据（约0.3%）则被视为“极值”。

因此，我们的目标是计算数据的**均值（μ）**和**标准差（σ）**，然后根据以下公式确定正常值的上下限：
*   **上限**：`μ + n * σ`
*   **下限**：`μ - n * σ`

其中，`n`通常取3，代表3倍标准差。之后，将所有超出此范围的数据“规范”到边界上，而不是直接删除。

## 3Sigma方法代码实现

理解了原理后，我们开始动手实现3Sigma方法。我们将编写一个函数，它接收一列数据和指定的`n`值（即几倍标准差），并返回处理后的数据序列以及计算出的上下限。

![](img/ea3f386912a58855569f9018ae4dedcb_3.png)

以下是实现步骤：

首先，我们需要计算输入数据的均值和标准差。

![](img/ea3f386912a58855569f9018ae4dedcb_5.png)

```python
def three_sigma(data_series, n=3):
    # 计算均值
    mean = data_series.mean()
    # 计算标准差
    std = data_series.std()
```

接着，利用均值和标准差计算数据的上下限。

![](img/ea3f386912a58855569f9018ae4dedcb_7.png)

```python
    # 计算上限
    upper_limit = mean + n * std
    # 计算下限
    lower_limit = mean - n * std
```

![](img/ea3f386912a58855569f9018ae4dedcb_9.png)

最后，对原始数据进行规范：将所有大于上限的值设置为上限，将所有小于下限的值设置为下限。

```python
    # 规范数据：将超出范围的值拉回边界
    processed_series = data_series.clip(lower=lower_limit, upper=upper_limit)
    
    return processed_series, lower_limit, upper_limit
```

![](img/ea3f386912a58855569f9018ae4dedcb_11.png)

完整的函数代码如下：

![](img/ea3f386912a58855569f9018ae4dedcb_13.png)

```python
def three_sigma(data_series, n=3):
    """
    使用3Sigma方法处理数据极值。
    参数:
        data_series: pandas Series, 待处理的数据列。
        n: int, 标准差的倍数，默认为3。
    返回:
        processed_series: 处理后的数据序列。
        lower_limit: 计算出的下限。
        upper_limit: 计算出的上限。
    """
    mean = data_series.mean()
    std = data_series.std()
    
    upper_limit = mean + n * std
    lower_limit = mean - n * std
    
    processed_series = data_series.clip(lower=lower_limit, upper=upper_limit)
    
    return processed_series, lower_limit, upper_limit
```

![](img/ea3f386912a58855569f9018ae4dedcb_15.png)

## 方法应用与对比

![](img/ea3f386912a58855569f9018ae4dedcb_17.png)

![](img/ea3f386912a58855569f9018ae4dedcb_19.png)

现在，我们可以使用这个函数来处理实际数据。为了更直观地展示效果，我们暂时将`n`设为1（即使用1Sigma，包含约68.3%的数据），这样上下限的范围会更窄，极值处理的效果更明显。

![](img/ea3f386912a58855569f9018ae4dedcb_21.png)

```python
# 假设 `data` 是包含我们感兴趣列（例如‘price’）的DataFrame
# 应用1Sigma方法
processed_data, lower, upper = three_sigma(data['price'], n=1)

![](img/ea3f386912a58855569f9018ae4dedcb_23.png)

print(f"下限: {lower:.2f}")
print(f"上限: {upper:.2f}")
# 可以绘制处理前后的数据分布图进行对比
```

![](img/ea3f386912a58855569f9018ae4dedcb_25.png)

执行代码后，我们可以通过绘图来观察处理效果。图中，绿色线通常代表原始数据的整体趋势或中位数（根据绘图代码而定），而蓝色线则代表根据3Sigma（本例为1Sigma）计算出的上限或下限。

![](img/ea3f386912a58855569f9018ae4dedcb_27.png)

![](img/ea3f386912a58855569f9018ae4dedcb_27.png)

![](img/ea3f386912a58855569f9018ae4dedcb_29.png)

如图所示，蓝色线标识了正常值的边界。所有超出边界的原始数据点，在`processed_data`中都被规范到了边界值上。

![](img/ea3f386912a58855569f9018ae4dedcb_31.png)

需要指出的是，不同的去极值方法（如分位数法、MAD法、3Sigma法）计算出的边界可能略有不同。例如，某种方法得到的下限可能在60左右，另一种可能在50左右。选择哪种方法取决于具体的任务需求和数据特性，它们都是数据预处理中可供选择的策略。

![](img/ea3f386912a58855569f9018ae4dedcb_33.png)

## 本章总结

![](img/ea3f386912a58855569f9018ae4dedcb_35.png)

本节课中我们一起学习了数据预处理“三步走”中的第一步——去极值。我们重点探讨了3Sigma方法。

![](img/ea3f386912a58855569f9018ae4dedcb_37.png)

我们首先从正态分布入手，理解了3Sigma方法基于的统计学原理：即认为大部分正常数据应落在`均值 ± n倍标准差`的区间内。接着，我们一步步实现了该方法的Python代码，核心是计算**均值（mean）**、**标准差（std）**，并利用公式 **`mean ± n * std`** 确定边界，最后使用`.clip()`函数规范数据。

![](img/ea3f386912a58855569f9018ae4dedcb_39.png)

关键点在于，去极值操作**并非直接删除**异常数据，而是通过定义合理的**下界（lower_limit）**和**上界（upper_limit）**，将所有数据规范到我们预设的合理范围之内，从而减少极端值对后续分析的干扰。

![](img/ea3f386912a58855569f9018ae4dedcb_41.png)

至此，我们已介绍了分位数、MAD和3Sigma三种常用的去极值方法，大家可以根据实际情况进行实验和选择。