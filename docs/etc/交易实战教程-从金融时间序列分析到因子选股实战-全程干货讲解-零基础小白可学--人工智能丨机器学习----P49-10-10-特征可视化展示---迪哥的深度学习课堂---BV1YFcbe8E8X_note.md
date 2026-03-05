# Python金融分析与量化交易实战教程：P49：10-10-特征可视化展示 📊

![](img/71d586458f35dba6abd7b909d585bf9b_0.png)

![](img/71d586458f35dba6abd7b909d585bf9b_2.png)

在本节课中，我们将学习如何为金融时间序列数据创建滞后特征，并通过可视化图表直观地展示这些特征与实际回报率之间的关系。我们将使用`shift`函数来构建特征，并利用散点图和颜色映射来呈现数据。

---

![](img/71d586458f35dba6abd7b909d585bf9b_4.png)

![](img/71d586458f35dba6abd7b909d585bf9b_6.png)

![](img/71d586458f35dba6abd7b909d585bf9b_8.png)

上一节我们介绍了时间序列数据的基本处理，本节中我们来看看如何构建用于预测的滞后特征并进行可视化。

## 创建滞后特征

![](img/71d586458f35dba6abd7b909d585bf9b_10.png)

我们的目标是预测当天的回报率（Y）。为此，我们需要使用历史数据作为特征（X）。例如，我们可以使用前一天和前两天的回报率来预测今天。

以下是创建滞后特征的具体步骤：

![](img/71d586458f35dba6abd7b909d585bf9b_12.png)

![](img/71d586458f35dba6abd7b909d585bf9b_14.png)

1.  **定义特征列名**：我们将创建两个新列，分别代表“前一天回报率”和“前两天回报率”。
2.  **使用`shift`函数**：`shift`函数可以将数据沿时间轴移动指定的步数，从而方便地获取历史数据。

![](img/71d586458f35dba6abd7b909d585bf9b_16.png)

![](img/71d586458f35dba6abd7b909d585bf9b_18.png)

我们通过一个函数来完成这个操作：

![](img/71d586458f35dba6abd7b909d585bf9b_20.png)

![](img/71d586458f35dba6abd7b909d585bf9b_22.png)

![](img/71d586458f35dba6abd7b909d585bf9b_24.png)

```python
def create_lag_features(data, n_lags=2):
    """
    为DataFrame创建滞后特征。
    参数:
        data: 包含‘returns’列的原始DataFrame。
        n_lags: 要创建的滞后特征数量。
    返回:
        添加了滞后特征列的DataFrame。
    """
    for i in range(1, n_lags + 1):
        # 定义列名，例如‘lag_1’，‘lag_2’
        col_name = f‘lag_{i}’
        # 使用shift(i)获取i天前的数据，并赋值给新列
        data[col_name] = data[‘returns’].shift(i)
    # 删除因创建滞后特征而产生的缺失值行
    data = data.dropna()
    return data

![](img/71d586458f35dba6abd7b909d585bf9b_26.png)

![](img/71d586458f35dba6abd7b909d585bf9b_28.png)

# 应用函数，创建两个滞后特征
data = create_lag_features(data, n_lags=2)
```

![](img/71d586458f35dba6abd7b909d585bf9b_30.png)

![](img/71d586458f35dba6abd7b909d585bf9b_32.png)

![](img/71d586458f35dba6abd7b909d585bf9b_34.png)

执行上述代码后，我们的`data` DataFrame中会增加两列：`lag_1`（前一天回报率）和`lag_2`（前两天回报率）。注意，前两行数据会因为缺少历史值而成为缺失值，我们使用`dropna()`将其删除。

![](img/71d586458f35dba6abd7b909d585bf9b_36.png)

## 特征可视化展示

![](img/71d586458f35dba6abd7b909d585bf9b_38.png)

特征创建完成后，我们可以通过可视化来观察特征与目标变量之间的关系。任务要求我们绘制散点图，其中：
*   X轴：`lag_1`（前一天回报率）
*   Y轴：`lag_2`（前两天回报率）
*   点的颜色：`returns`（当天实际回报率），用不同颜色深浅表示数值大小。

![](img/71d586458f35dba6abd7b909d585bf9b_40.png)

以下是绘制该图的代码：

![](img/71d586458f35dba6abd7b909d585bf9b_42.png)

```python
import matplotlib.pyplot as plt

![](img/71d586458f35dba6abd7b909d585bf9b_44.png)

# 绘制散点图
plt.figure(figsize=(10, 6))
# c参数指定颜色映射的数据源，cmap指定颜色方案（‘coolwarm’是红蓝热度图）
scatter = plt.scatter(data[‘lag_1’], data[‘lag_2’], c=data[‘returns’], cmap=‘coolwarm’, s=16)
plt.colorbar(scatter, label=‘Actual Returns (Y)’) # 添加颜色条，说明颜色含义

# 添加参考线
plt.axhline(y=0, color=‘r’, linestyle=‘--’, alpha=0.5) # 水平线 (y=0)
plt.axvline(x=0, color=‘r’, linestyle=‘--’, alpha=0.5) # 垂直线 (x=0)

# 添加标题和坐标轴标签
plt.title(‘Feature Visualization: Lag_1 vs Lag_2 Colored by Actual Returns’)
plt.xlabel(‘Lag_1 (Previous Day Return)’)
plt.ylabel(‘Lag_2 (Two Days Ago Return)’)
plt.grid(True, alpha=0.3)
plt.show()
```

![](img/71d586458f35dba6abd7b909d585bf9b_46.png)

![](img/71d586458f35dba6abd7b909d585bf9b_48.png)

**图表解读**：
*   图中每个点代表一个交易日。
*   点的位置由`lag_1`和`lag_2`的值决定。
*   点的颜色代表当天的实际回报率(`returns`)：
    *   **红色**（越深）：表示当天实际回报率为**正**，且数值越大颜色越深（红）。
    *   **蓝色**（越深）：表示当天实际回报率为**负**，且数值越小（负得越多）颜色越深（蓝）。
*   中间的红色虚线是X轴和Y轴的零点参考线。

![](img/71d586458f35dba6abd7b909d585bf9b_50.png)

这种可视化方法相当于用二维平面展示了三个维度的信息（`lag_1`, `lag_2`, `returns`），通过颜色映射直观地揭示了历史回报率与当前回报率之间的潜在关系。

![](img/71d586458f35dba6abd7b909d585bf9b_52.png)

![](img/71d586458f35dba6abd7b909d585bf9b_54.png)

![](img/71d586458f35dba6abd7b909d585bf9b_56.png)

---

![](img/71d586458f35dba6abd7b909d585bf9b_58.png)

![](img/71d586458f35dba6abd7b909d585bf9b_60.png)

![](img/71d586458f35dba6abd7b909d585bf9b_62.png)

本节课中我们一起学习了如何为金融时间序列构建滞后特征，这是量化建模中准备特征数据的关键一步。我们还掌握了使用`matplotlib`绘制散点图并结合颜色映射（`cmap`）来可视化多变量关系的方法，这有助于我们直观地探索和理解特征与目标变量之间的模式。