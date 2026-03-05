# Python金融分析与量化交易实战教程：P59：2-特征可视化展示 📊

![](img/be9bdf8c4ffd49b195a2b78c14270eff_0.png)

![](img/be9bdf8c4ffd49b195a2b78c14270eff_2.png)

在本节课中，我们将学习如何为金融时间序列数据创建滞后特征，并通过可视化图表直观地展示这些特征与目标变量之间的关系。我们将使用`shift`函数生成特征，并用散点图结合颜色映射来呈现数据。

![](img/be9bdf8c4ffd49b195a2b78c14270eff_4.png)

![](img/be9bdf8c4ffd49b195a2b78c14270eff_6.png)

![](img/be9bdf8c4ffd49b195a2b78c14270eff_8.png)

## 概述
上一节我们介绍了数据的基本处理，本节中我们来看看如何构建用于预测的特征并进行可视化。我们将创建“前一天回报率”和“前两天回报率”作为特征，并以“当天实际回报率”作为目标变量，通过图表展示三者关系。

![](img/be9bdf8c4ffd49b195a2b78c14270eff_10.png)

## 创建滞后特征
我们的目标是预测当天的回报率（Y）。为此，我们需要使用历史数据作为特征（X）。例如，可以使用前一天、前两天甚至更早的数据。本节我们将创建“前一天回报率”和“前两天回报率”两个特征。

![](img/be9bdf8c4ffd49b195a2b78c14270eff_12.png)

![](img/be9bdf8c4ffd49b195a2b78c14270eff_14.png)

以下是创建滞后特征的具体步骤：

![](img/be9bdf8c4ffd49b195a2b78c14270eff_16.png)

![](img/be9bdf8c4ffd49b195a2b78c14270eff_18.png)

![](img/be9bdf8c4ffd49b195a2b78c14270eff_20.png)

1.  **定义特征列名**：首先，我们定义两个特征列的名称，例如 `lag_1` 和 `lag_2`。
    ```python
    lags = [‘lag_1‘, ‘lag_2‘]
    ```

![](img/be9bdf8c4ffd49b195a2b78c14270eff_22.png)

![](img/be9bdf8c4ffd49b195a2b78c14270eff_24.png)

![](img/be9bdf8c4ffd49b195a2b78c14270eff_26.png)

2.  **编写特征创建函数**：我们将编写一个函数，自动为数据框添加指定数量的滞后特征列。
    ```python
    def create_lags(data, n_lags=2):
        # 初始化一个列表，用于存储新创建的列名
        new_columns = []
        for i in range(1, n_lags + 1):
            # 生成列名，例如 ‘lag_1‘, ‘lag_2‘
            col_name = ‘lag_{}‘.format(i)
            # 使用 shift 函数创建滞后特征，shift(i) 表示将数据向下移动 i 行
            data[col_name] = data[‘returns‘].shift(i)
            # 将新列名加入列表
            new_columns.append(col_name)
        # 返回添加了新列的数据框
        return data, new_columns
    ```

![](img/be9bdf8c4ffd49b195a2b78c14270eff_28.png)

![](img/be9bdf8c4ffd49b195a2b78c14270eff_30.png)

![](img/be9bdf8c4ffd49b195a2b78c14270eff_32.png)

![](img/be9bdf8c4ffd49b195a2b78c14270eff_34.png)

![](img/be9bdf8c4ffd49b195a2b78c14270eff_36.png)

3.  **应用函数并处理缺失值**：调用函数为数据添加特征。由于使用`shift`操作后，前几行会产生缺失值（NaN），我们需要将其删除。
    ```python
    # 假设 data 是包含 ‘returns‘ 列的数据框
    data, lag_columns = create_lags(data, n_lags=2)
    # 删除因创建滞后特征而产生的缺失值行
    data = data.dropna()
    ```

![](img/be9bdf8c4ffd49b195a2b78c14270eff_38.png)

执行上述步骤后，数据框中将新增 `lag_1`（前一天回报率）和 `lag_2`（前两天回报率）两列，数据已准备好用于后续分析和建模。

![](img/be9bdf8c4ffd49b195a2b78c14270eff_40.png)

![](img/be9bdf8c4ffd49b195a2b78c14270eff_42.png)

## 特征可视化展示
特征创建完成后，我们需要直观地理解特征与目标变量之间的关系。任务要求绘制散点图，其中X轴为`lag_1`，Y轴为`lag_2`，并用不同颜色表示当天的实际回报率（`returns`）。

![](img/be9bdf8c4ffd49b195a2b78c14270eff_44.png)

以下是绘制特征可视化图的步骤：

1.  **绘制基础散点图**：使用`DataFrame.plot.scatter`方法绘制以`lag_1`为X轴、`lag_2`为Y轴的散点图。
    ```python
    ax = data.plot.scatter(x=‘lag_1‘, y=‘lag_2‘, c=data[‘returns‘], cmap=‘RdYlBu‘, figsize=(10, 8), colorbar=True)
    ```
    *   **c=data[‘returns‘]**：此参数指定散点图中每个点的颜色由`returns`列的数值决定。
    *   **cmap=‘RdYlBu‘**：这是颜色映射（colormap），它定义了数值如何映射到颜色。‘RdYlBu‘表示从红色（高正值）到黄色（中间值）再到蓝色（高负值）的渐变。
    *   **colorbar=True**：显示颜色条，方便查看颜色与具体数值的对应关系。

![](img/be9bdf8c4ffd49b195a2b78c14270eff_46.png)

![](img/be9bdf8c4ffd49b195a2b78c14270eff_48.png)

2.  **添加参考线**：为了更清晰地观察数据分布相对于原点（0，0）的情况，我们可以在图表中添加水平和垂直的参考线。
    ```python
    # 添加垂直参考线 (x=0)
    ax.axvline(x=0, color=‘red‘, linestyle=‘--‘, alpha=0.5)
    # 添加水平参考线 (y=0)
    ax.axhline(y=0, color=‘red‘, linestyle=‘--‘, alpha=0.5)
    ```
    *   `axvline`和`axhline`分别用于绘制垂直和水平线。
    *   `linestyle=‘--‘` 指定为虚线。

![](img/be9bdf8c4ffd49b195a2b78c14270eff_50.png)

![](img/be9bdf8c4ffd49b195a2b78c14270eff_52.png)

![](img/be9bdf8c4ffd49b195a2b78c14270eff_54.png)

生成的图表效果如下：每个点代表一个交易日，其位置由前两天的回报率决定，颜色则反映了当天的实际回报率。红色越深表示当天大涨，蓝色越深表示当天大跌。通过参考线，可以快速判断特征值（历史回报率）为正或负的区域。

![](img/be9bdf8c4ffd49b195a2b78c14270eff_56.png)

![](img/be9bdf8c4ffd49b195a2b78c14270eff_58.png)

![](img/be9bdf8c4ffd49b195a2b78c14270eff_60.png)

![](img/be9bdf8c4ffd49b195a2b78c14270eff_62.png)

## 总结
本节课中我们一起学习了金融数据分析中特征工程与可视化的关键一步。我们掌握了如何使用 **`shift()`** 函数为时间序列数据创建滞后特征，并利用 **散点图** 和 **颜色映射（colormap）** 将特征（`lag_1`, `lag_2`）与目标变量（`returns`）在一个二维图中进行三维信息的直观展示。这种方法能帮助我们初步判断历史数据与未来走势之间可能存在的模式，为后续构建量化交易模型打下基础。