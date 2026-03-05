# 量化交易实战课程：P59：特征可视化展示

![](img/b34b7b3a08dcf8775e6147d0e2a709ea_0.png)

![](img/b34b7b3a08dcf8775e6147d0e2a709ea_2.png)

在本节课中，我们将学习如何为时间序列数据创建滞后特征，并使用可视化方法直观地展示这些特征与目标变量之间的关系。我们将使用股票收益率数据，通过创建“前一天”和“前两天”的收益率作为特征，并绘制散点图来观察它们与“当天”实际收益率的关系。

![](img/b34b7b3a08dcf8775e6147d0e2a709ea_4.png)

## 概述与目标变量定义

![](img/b34b7b3a08dcf8775e6147d0e2a709ea_6.png)

![](img/b34b7b3a08dcf8775e6147d0e2a709ea_8.png)

上一节我们介绍了数据准备的基础。本节中，我们来看看如何构建用于预测的特征。首先，我们需要明确预测的目标。在我们的例子中，目标变量 `Y` 是股票当天的实际收益率。这个数据在我们的数据集中是现成的，对应 `returns` 列。

![](img/b34b7b3a08dcf8775e6147d0e2a709ea_10.png)

## 构建滞后特征

![](img/b34b7b3a08dcf8775e6147d0e2a709ea_12.png)

为了预测未来的走势，我们需要基于历史数据来构建特征。例如，如果我们想预测今天的收益率，一个自然的想法是使用过去几天的收益率数据。

![](img/b34b7b3a08dcf8775e6147d0e2a709ea_14.png)

![](img/b34b7b3a08dcf8775e6147d0e2a709ea_16.png)

以下是构建滞后特征的具体步骤。我们将创建一个函数，用于生成指定天数的历史收益率特征。

![](img/b34b7b3a08dcf8775e6147d0e2a709ea_18.png)

![](img/b34b7b3a08dcf8775e6147d0e2a709ea_20.png)

![](img/b34b7b3a08dcf8775e6147d0e2a709ea_22.png)

```python
def create_lag_features(data, n_lags=2):
    """
    为给定的DataFrame创建滞后特征。
    参数:
        data: 包含‘returns’列的DataFrame。
        n_lags: 要创建的滞后特征数量，例如2表示创建前一天和前两天的特征。
    返回:
        添加了滞后特征列的DataFrame。
    """
    # 初始化一个列表，用于存储新创建的列名
    new_columns = []
    # 循环创建每一个滞后特征
    for i in range(1, n_lags + 1):
        # 定义新列名，例如‘lag_1’，‘lag_2’
        col_name = ‘lag_{}’.format(i)
        # 使用shift函数将‘returns’列向后移动i天，得到滞后i天的特征
        data[col_name] = data[‘returns’].shift(i)
        # 将新列名加入列表
        new_columns.append(col_name)
    # 返回添加了新列的DataFrame和新列名列表
    return data, new_columns
```

![](img/b34b7b3a08dcf8775e6147d0e2a709ea_24.png)

![](img/b34b7b3a08dcf8775e6147d0e2a709ea_26.png)

执行这个函数后，我们的数据集中会增加两列：`lag_1`（代表前一天的收益率）和 `lag_2`（代表前两天的收益率）。由于使用了 `shift` 操作，数据的前几行会产生缺失值（NaN），我们需要将其删除以保证数据质量。

![](img/b34b7b3a08dcf8775e6147d0e2a709ea_28.png)

![](img/b34b7b3a08dcf8775e6147d0e2a709ea_30.png)

![](img/b34b7b3a08dcf8775e6147d0e2a709ea_32.png)

```python
# 应用函数，创建两个滞后特征
data, lag_columns = create_lag_features(data, n_lags=2)
# 删除因创建滞后特征而产生的缺失值行
data = data.dropna()
```

![](img/b34b7b3a08dcf8775e6147d0e2a709ea_34.png)

![](img/b34b7b3a08dcf8775e6147d0e2a709ea_36.png)

![](img/b34b7b3a08dcf8775e6147d0e2a709ea_38.png)

## 特征可视化

![](img/b34b7b3a08dcf8775e6147d0e2a709ea_40.png)

现在我们已经有了特征（`lag_1`, `lag_2`）和目标变量（`returns`）。接下来，我们将通过可视化来探索它们之间的关系。

![](img/b34b7b3a08dcf8775e6147d0e2a709ea_42.png)

![](img/b34b7b3a08dcf8775e6147d0e2a709ea_44.png)

我们的目标是绘制一个散点图：
*   **X轴**：代表特征 `lag_1`（前一天的收益率）。
*   **Y轴**：代表特征 `lag_2`（前两天的收益率）。
*   **点的颜色**：代表目标变量 `returns`（当天的实际收益率）。我们使用颜色映射（colormap）来区分数值大小，例如，红色越深表示正收益越大，蓝色越深表示负收益越大。

![](img/b34b7b3a08dcf8775e6147d0e2a709ea_46.png)

![](img/b34b7b3a08dcf8775e6147d0e2a709ea_48.png)

以下是实现可视化的代码：

![](img/b34b7b3a08dcf8775e6147d0e2a709ea_50.png)

![](img/b34b7b3a08dcf8775e6147d0e2a709ea_52.png)

```python
import matplotlib.pyplot as plt

![](img/b34b7b3a08dcf8775e6147d0e2a709ea_54.png)

plt.figure(figsize=(10, 8))
# 绘制散点图，c参数指定颜色依据returns列的值变化
scatter = plt.scatter(data[‘lag_1’], data[‘lag_2’], c=data[‘returns’], cmap=‘coolwarm’, alpha=0.6)
# 添加颜色条，显示颜色与数值的对应关系
plt.colorbar(scatter, label=‘Actual Return (returns)’)

# 添加参考线
plt.axhline(y=0, color=‘r’, linestyle=‘--’, alpha=0.3) # 水平线 (Y=0)
plt.axvline(x=0, color=‘r’, linestyle=‘--’, alpha=0.3) # 垂直线 (X=0)

![](img/b34b7b3a08dcf8775e6147d0e2a709ea_56.png)

plt.xlabel(‘Lag 1 Return (Previous Day)’)
plt.ylabel(‘Lag 2 Return (Two Days Ago)’)
plt.title(‘Feature Visualization: Lag Returns vs. Actual Return’)
plt.grid(True, alpha=0.3)
plt.show()
```

![](img/b34b7b3a08dcf8775e6147d0e2a709ea_58.png)

![](img/b34b7b3a08dcf8775e6147d0e2a709ea_60.png)

![](img/b34b7b3a08dcf8775e6147d0e2a709ea_62.png)

通过这张图，我们可以直观地看到：
1.  点的分布情况，了解 `lag_1` 和 `lag_2` 特征的大致范围与相关性。
2.  通过颜色，可以快速识别出在特定的历史收益率组合下（即图中某个位置），当天的实际收益率是正（暖色调）还是负（冷色调），以及幅度的大小。

![](img/b34b7b3a08dcf8775e6147d0e2a709ea_64.png)

这相当于一个三维数据的二维投影，第三个维度（实际收益率）通过颜色来体现，使得图形更加清晰易懂。

![](img/b34b7b3a08dcf8775e6147d0e2a709ea_66.png)

![](img/b34b7b3a08dcf8775e6147d0e2a709ea_68.png)

![](img/b34b7b3a08dcf8775e6147d0e2a709ea_70.png)

## 总结

![](img/b34b7b3a08dcf8775e6147d0e2a709ea_72.png)

![](img/b34b7b3a08dcf8775e6147d0e2a709ea_74.png)

![](img/b34b7b3a08dcf8775e6147d0e2a709ea_76.png)

本节课中我们一起学习了量化分析中特征工程与可视化的一个关键环节：
1.  **构建滞后特征**：我们使用 `shift()` 函数，基于时间序列创建了历史特征（如前一天、前两天的收益率），这是预测未来值的基础。
2.  **数据清洗**：我们处理了因创建滞后特征而产生的缺失数据。
3.  **特征可视化**：我们绘制了以两个滞后特征为坐标轴的散点图，并通过颜色映射将目标变量（当天实际收益率）直观地展示出来。这种可视化方法有助于我们初步判断特征与目标之间的关系，为后续的模型选择与分析提供直观依据。