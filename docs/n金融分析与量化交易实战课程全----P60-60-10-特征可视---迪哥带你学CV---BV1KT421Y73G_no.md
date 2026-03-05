# Python金融分析与量化交易实战课程：P60：特征可视化展示 📊

![](img/0c4fd72a1b1ff363769ea2bda541ab02_0.png)

![](img/0c4fd72a1b1ff363769ea2bda541ab02_2.png)

在本节课中，我们将学习如何为金融时间序列数据创建滞后特征，并使用散点图结合颜色映射（colormap）来可视化这些特征与实际目标值之间的关系。我们将使用`shift`方法生成特征，并通过`matplotlib`库绘制图表。

![](img/0c4fd72a1b1ff363769ea2bda541ab02_4.png)

## 概述

![](img/0c4fd72a1b1ff363769ea2bda541ab02_6.png)

![](img/0c4fd72a1b1ff363769ea2bda541ab02_8.png)

上一节我们介绍了数据的基本处理。本节中，我们来看看如何构建用于预测的滞后特征，并对其进行可视化分析。我们将创建一个函数来生成特征，然后绘制一个散点图，其中点的颜色深浅代表当日的实际收益率。

![](img/0c4fd72a1b1ff363769ea2bda541ab02_10.png)

## 创建滞后特征

我们的目标是预测当日的回报率（Y）。为此，我们需要使用历史数据作为特征（X）。例如，我们可以使用前一天和前两天的回报率作为特征。

![](img/0c4fd72a1b1ff363769ea2bda541ab02_12.png)

![](img/0c4fd72a1b1ff363769ea2bda541ab02_14.png)

以下是创建滞后特征的具体步骤。我们将定义一个函数来完成这个操作。

![](img/0c4fd72a1b1ff363769ea2bda541ab02_16.png)

![](img/0c4fd72a1b1ff363769ea2bda541ab02_18.png)

![](img/0c4fd72a1b1ff363769ea2bda541ab02_20.png)

```python
def create_lag_features(data, num_lags=2):
    """
    为DataFrame创建滞后特征。
    参数:
        data: 包含‘returns’列的原始DataFrame。
        num_lags: 要创建的滞后特征数量（例如，2代表创建lag_1和lag_2）。
    返回:
        添加了滞后特征列的DataFrame。
    """
    for i in range(1, num_lags + 1):
        col_name = ‘lag_{}’.format(i)
        data[col_name] = data[‘returns’].shift(i)
    return data
```

![](img/0c4fd72a1b1ff363769ea2bda541ab02_22.png)

执行该函数后，我们的数据将新增两列：`lag_1`（前一天回报率）和`lag_2`（前两天回报率）。由于`shift`操作会在数据开头产生缺失值（NaN），我们需要将其删除。

![](img/0c4fd72a1b1ff363769ea2bda541ab02_24.png)

![](img/0c4fd72a1b1ff363769ea2bda541ab02_26.png)

![](img/0c4fd72a1b1ff363769ea2bda541ab02_28.png)

![](img/0c4fd72a1b1ff363769ea2bda541ab02_30.png)

```python
# 应用函数创建特征
data = create_lag_features(data, num_lags=2)
# 删除因创建滞后特征而产生的缺失值行
data = data.dropna()
```

![](img/0c4fd72a1b1ff363769ea2bda541ab02_32.png)

![](img/0c4fd72a1b1ff363769ea2bda541ab02_34.png)

## 特征可视化

![](img/0c4fd72a1b1ff363769ea2bda541ab02_36.png)

![](img/0c4fd72a1b1ff363769ea2bda541ab02_38.png)

![](img/0c4fd72a1b1ff363769ea2bda541ab02_40.png)

现在我们已经有了特征，接下来按照要求绘制散点图进行可视化。我们将以`lag_1`为X轴，`lag_2`为Y轴，并用点的颜色代表当日的实际回报率（`returns`）。

![](img/0c4fd72a1b1ff363769ea2bda541ab02_42.png)

以下是绘制该图表的代码：

```python
import matplotlib.pyplot as plt

![](img/0c4fd72a1b1ff363769ea2bda541ab02_44.png)

![](img/0c4fd72a1b1ff363769ea2bda541ab02_46.png)

plt.figure(figsize=(10, 6))
# 绘制散点图，c参数指定颜色映射的值（即当日的returns）
scatter = plt.scatter(data[‘lag_1’], data[‘lag_2’], c=data[‘returns’], cmap=‘RdYlBu’, s=16)
# 添加颜色条以解释颜色映射
plt.colorbar(scatter, label=‘Actual Return (returns)’)

![](img/0c4fd72a1b1ff363769ea2bda541ab02_48.png)

# 添加参考线
plt.axhline(y=0, color=‘r’, linestyle=‘--’, alpha=0.5) # 水平线
plt.axvline(x=0, color=‘r’, linestyle=‘--’, alpha=0.5) # 垂直线

# 添加轴标签和标题
plt.xlabel(‘Lag 1 (Previous Day Return)’)
plt.ylabel(‘Lag 2 (Return from Two Days Ago)’)
plt.title(‘Feature Visualization with Colormap’)
plt.grid(True, alpha=0.3)
plt.show()
```

**代码解释**：
*   `c=data[‘returns’]`：这指定了每个数据点的颜色由当日的实际回报率决定。
*   `cmap=‘RdYlBu’`：这是使用的颜色映射。`RdYlBu`代表从红色（Red）到黄色（Yellow）再到蓝色（Blue），通常红色表示正值（上涨），蓝色表示负值（下跌）。
*   `plt.colorbar()`：添加一个颜色条，方便查看颜色与实际数值的对应关系。
*   `plt.axhline` 和 `plt.axvline`：分别在Y=0和X=0处添加虚线，作为参考基准线。

![](img/0c4fd72a1b1ff363769ea2bda541ab02_50.png)

![](img/0c4fd72a1b1ff363769ea2bda541ab02_52.png)

通过这个图表，我们可以直观地观察前两天的回报率（特征）与当天实际回报率（目标）之间的关系。颜色越偏红，表示当天上涨越多；颜色越偏蓝，表示当天下跌越多。

![](img/0c4fd72a1b1ff363769ea2bda541ab02_54.png)

## 总结

![](img/0c4fd72a1b1ff363769ea2bda541ab02_56.png)

![](img/0c4fd72a1b1ff363769ea2bda541ab02_58.png)

![](img/0c4fd72a1b1ff363769ea2bda541ab02_60.png)

本节课中我们一起学习了构建和可视化金融时间序列特征的两个核心步骤。
1.  **创建滞后特征**：我们使用Pandas的`shift()`方法，通过一个自定义函数高效地生成了用于预测的历史特征（如`lag_1`, `lag_2`），并处理了由此产生的缺失值。
2.  **特征可视化**：我们利用`matplotlib`绘制了散点图，将两个滞后特征分别置于X轴和Y轴，并通过颜色映射（colormap）将第三个维度——当日的实际回报率——直观地展现出来，形成了一幅信息丰富的三维数据可视化图。

![](img/0c4fd72a1b1ff363769ea2bda541ab02_62.png)

![](img/0c4fd72a1b1ff363769ea2bda541ab02_64.png)

![](img/0c4fd72a1b1ff363769ea2bda541ab02_66.png)

这种方法不仅帮助我们理解特征与目标之间的关系，也为后续建立量化模型提供了清晰的数据洞察。