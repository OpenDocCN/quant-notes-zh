# 数据处理基础：P29：MAD法去极值教程

![](img/ed1021ba447461ad6845f7f999448e96_0.png)

![](img/ed1021ba447461ad6845f7f999448e96_2.png)

在本节课中，我们将学习第二种数据去极值方法——MAD法。我们将了解其核心概念、计算步骤，并通过代码演示其具体实现。

## 概述

上一节我们介绍了百分位法去极值，本节中我们来看看另一种稳健的方法：MAD法。MAD是“Median Absolute Deviation”（中位数绝对偏差）的缩写。该方法通过计算数据的中位数和绝对偏差来定义数据的正常范围，从而识别并处理异常值。其核心思想是利用数据自身的中位数特性来抵抗极端值的影响。

## MAD法核心概念与计算步骤

![](img/ed1021ba447461ad6845f7f999448e96_4.png)

MAD法的核心在于计算一个名为“中位数绝对偏差”的值，并用它来设定数据的上下边界。

![](img/ed1021ba447461ad6845f7f999448e96_6.png)

![](img/ed1021ba447461ad6845f7f999448e96_8.png)

**核心公式如下：**
1.  计算原始数据序列的中位数：`median = median(data)`
2.  计算每个数据点与中位数的绝对偏差：`abs_deviation = |data - median|`
3.  计算这些绝对偏差的中位数，即MAD值：`MAD = median(abs_deviation)`
4.  设定正常值范围的下界和上界：
    *   下界：`lower_bound = median - N * MAD`
    *   上界：`upper_bound = median + N * MAD`
    其中，`N` 是一个常数，通常取值为 **1.4826**。
5.  将超出 `[lower_bound, upper_bound]` 范围的数据视为极值，并进行处理（如截断或替换）。

## 代码实现

![](img/ed1021ba447461ad6845f7f999448e96_10.png)

![](img/ed1021ba447461ad6845f7f999448e96_12.png)

以下是使用Python和Pandas库实现MAD去极值函数的示例代码。

![](img/ed1021ba447461ad6845f7f999448e96_14.png)

![](img/ed1021ba447461ad6845f7f999448e96_16.png)

```python
import pandas as pd
import numpy as np

![](img/ed1021ba447461ad6845f7f999448e96_18.png)

![](img/ed1021ba447461ad6845f7f999448e96_20.png)

def mad_filter(series, n=1.4826):
    """
    使用MAD法对序列进行去极值处理。
    
    参数:
    series (pd.Series): 待处理的数据序列。
    n (float): 用于计算边界的倍数，默认为1.4826。
    
    返回:
    pd.Series: 处理后的数据序列。
    """
    # 1. 计算原始序列的中位数
    median = series.quantile(0.5)
    
    # 2. 计算绝对偏差序列
    abs_deviation = (series - median).abs()
    
    # 3. 计算绝对偏差的中位数 (MAD值)
    mad = abs_deviation.quantile(0.5)
    
    # 4. 计算上下边界
    upper_bound = median + n * mad
    lower_bound = median - n * mad
    
    # 5. 对超出边界的数据进行截断处理
    filtered_series = series.clip(lower=lower_bound, upper=upper_bound)
    
    return filtered_series

# 示例：使用函数处理数据
# 假设 `data` 是一个Pandas Series
# filtered_data = mad_filter(data, n=1.4826)
```

## 方法对比与应用

![](img/ed1021ba447461ad6845f7f999448e96_22.png)

![](img/ed1021ba447461ad6845f7f999448e96_24.png)

以下是MAD法与百分位法的主要特点对比：

![](img/ed1021ba447461ad6845f7f999448e96_26.png)

*   **稳健性**：MAD法对极端值不敏感，因为其基于中位数计算，比基于均值和标准差的方法更稳健。
*   **计算**：步骤清晰，涉及两次中位数计算。
*   **参数**：主要参数是倍数 `N`，通常使用标准值1.4826（目的是使MAD在正态分布下与标准差估计量一致）。
*   **适用场景**：适用于数据中存在显著异常值，且希望使用对异常值不敏感的统计量进行去极值的场景。

![](img/ed1021ba447461ad6845f7f999448e96_28.png)

![](img/ed1021ba447461ad6845f7f999448e96_30.png)

## 总结

![](img/ed1021ba447461ad6845f7f999448e96_32.png)

![](img/ed1021ba447461ad6845f7f999448e96_34.png)

本节课中我们一起学习了MAD去极值法。我们首先理解了MAD（中位数绝对偏差）的概念，然后逐步拆解了其计算过程：先求原始数据中位数，再求绝对偏差的中位数，最后利用一个常数倍数确定数据的正常范围。通过代码演示，我们看到了如何将这一过程转化为具体的程序。与百分位法相比，MAD法基于中位数，具有更强的稳健性，是处理包含异常值数据的有效工具之一。