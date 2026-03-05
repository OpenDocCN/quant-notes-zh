# Python金融分析与量化交易实战教程：P62：10-10-特征可视化展示 🎯

![](img/c5b9762ee9a0b569aa610bedebf7b4a5_0.png)

![](img/c5b9762ee9a0b569aa610bedebf7b4a5_2.png)

在本节课中，我们将学习如何为量化交易模型创建历史特征，并使用可视化方法直观地展示这些特征与目标值之间的关系。我们将重点掌握使用`shift`方法构建特征，以及通过散点图和颜色映射进行数据可视化的技巧。

![](img/c5b9762ee9a0b569aa610bedebf7b4a5_4.png)

![](img/c5b9762ee9a0b569aa610bedebf7b4a5_6.png)

![](img/c5b9762ee9a0b569aa610bedebf7b4a5_8.png)

上一节我们介绍了数据处理的基础，本节中我们来看看如何构建用于预测的历史特征。

![](img/c5b9762ee9a0b569aa610bedebf7b4a5_10.png)

## 构建历史特征

![](img/c5b9762ee9a0b569aa610bedebf7b4a5_12.png)

在量化交易中，我们常常使用过去的数据来预测未来的走势。例如，我们想预测今天的回报率，可以使用前一天或前几天的数据作为特征。

![](img/c5b9762ee9a0b569aa610bedebf7b4a5_14.png)

![](img/c5b9762ee9a0b569aa610bedebf7b4a5_16.png)

![](img/c5b9762ee9a0b569aa610bedebf7b4a5_18.png)

以下是创建历史特征的具体步骤：

![](img/c5b9762ee9a0b569aa610bedebf7b4a5_20.png)

![](img/c5b9762ee9a0b569aa610bedebf7b4a5_22.png)

1.  **定义特征列名**：首先，我们需要确定要创建哪些特征。例如，我们计划使用前一天（lag_1）和前两天的数据（lag_2）。
    ```python
    lags = [‘lag_1‘, ‘lag_2‘]
    ```

![](img/c5b9762ee9a0b569aa610bedebf7b4a5_24.png)

![](img/c5b9762ee9a0b569aa610bedebf7b4a5_26.png)

![](img/c5b9762ee9a0b569aa610bedebf7b4a5_28.png)

![](img/c5b9762ee9a0b569aa610bedebf7b4a5_30.png)

2.  **创建特征生成函数**：为了灵活地创建不同时间跨度的特征，我们定义一个函数。这个函数接收原始数据，并为其添加指定数量的滞后特征列。
    ```python
    def create_lags(data, n_lags=2):
        # 初始化一个列表，用于存储新创建的列名
        new_columns = []
        # 循环创建每一个滞后特征
        for i in range(1, n_lags + 1):
            # 生成列名，例如 ‘lag_1‘, ‘lag_2‘
            col_name = ‘lag_{}‘.format(i)
            # 使用 shift 方法将 ‘returns‘ 列的数据向后移动 i 个位置，得到滞后 i 期的特征
            data[col_name] = data[‘returns‘].shift(i)
            # 将新列名加入列表
            new_columns.append(col_name)
        # 返回添加了新特征列的数据
        return data
    ```

![](img/c5b9762ee9a0b569aa610bedebf7b4a5_32.png)

![](img/c5b9762ee9a0b569aa610bedebf7b4a5_34.png)

![](img/c5b9762ee9a0b569aa610bedebf7b4a5_36.png)

![](img/c5b9762ee9a0b569aa610bedebf7b4a5_38.png)

3.  **应用函数并处理数据**：调用上述函数为我们的数据集创建特征。由于使用`shift`操作会在数据开头产生空值（NaN），我们需要将其删除。
    ```python
    # 应用函数，创建两个滞后特征
    data = create_lags(data, n_lags=2)
    # 删除因 shift 操作产生的缺失值行
    data = data.dropna()
    ```

![](img/c5b9762ee9a0b569aa610bedebf7b4a5_40.png)

执行以上步骤后，我们的数据集中就新增了`lag_1`（前一天回报率）和`lag_2`（前两天回报率）两列特征。

![](img/c5b9762ee9a0b569aa610bedebf7b4a5_42.png)

![](img/c5b9762ee9a0b569aa610bedebf7b4a5_44.png)

## 特征可视化展示

![](img/c5b9762ee9a0b569aa610bedebf7b4a5_46.png)

特征构建完成后，我们可以通过可视化来直观理解特征与目标变量（即当天的实际回报率`returns`）之间的关系。

![](img/c5b9762ee9a0b569aa610bedebf7b4a5_48.png)

以下是绘制特征可视化散点图的步骤：

1.  **绘制基础散点图**：我们以`lag_1`为X轴，`lag_2`为Y轴绘制散点图。每个点的颜色（`c`参数）由当天的实际回报率`returns`决定，这样就将第三个维度的信息（目标值）通过颜色映射（`cmap`）表现了出来。
    ```python
    import matplotlib.pyplot as plt

    plt.figure(figsize=(10, 6))
    # 绘制散点图，颜色映射表示当天的实际回报率
    scatter = plt.scatter(data[‘lag_1‘], data[‘lag_2‘],
                          c=data[‘returns‘], cmap=‘coolwarm‘,
                          s=16, alpha=0.7)
    # 添加颜色条，用于解读颜色对应的数值大小
    plt.colorbar(scatter, label=‘Actual Returns‘)
    plt.xlabel(‘Lag 1 (Previous Day)‘)
    plt.ylabel(‘Lag 2 (Two Days Ago)‘)
    plt.title(‘Feature Visualization with Color-Mapped Target‘)
    ```

![](img/c5b9762ee9a0b569aa610bedebf7b4a5_50.png)

![](img/c5b9762ee9a0b569aa610bedebf7b4a5_52.png)

![](img/c5b9762ee9a0b569aa610bedebf7b4a5_54.png)

2.  **添加参考线**：为了更清晰地观察数据点相对于原点（0，0）的分布，我们可以在图中添加水平和垂直的参考线。
    ```python
    # 添加垂直参考线 (x=0)
    plt.axvline(x=0, color=‘red‘, linestyle=‘--‘, linewidth=0.5)
    # 添加水平参考线 (y=0)
    plt.axhline(y=0, color=‘red‘, linestyle=‘--‘, linewidth=0.5)
    plt.show()
    ```

![](img/c5b9762ee9a0b569aa610bedebf7b4a5_56.png)

![](img/c5b9762ee9a0b569aa610bedebf7b4a5_58.png)

生成的图表中，X轴代表前一天的特征值，Y轴代表前两天的特征值。每个点的颜色深浅由当天的实际回报率决定：越偏向暖色（如红色）表示正收益越大，越偏向冷色（如蓝色）表示负收益（下跌）越大。这相当于一个三维数据的二维投影，使得数据模式更加直观。

![](img/c5b9762ee9a0b569aa610bedebf7b4a5_60.png)

![](img/c5b9762ee9a0b569aa610bedebf7b4a5_62.png)

![](img/c5b9762ee9a0b569aa610bedebf7b4a5_64.png)

![](img/c5b9762ee9a0b569aa610bedebf7b4a5_66.png)

本节课中我们一起学习了如何为时间序列预测问题构建历史特征，以及如何通过带有颜色映射的散点图将特征与目标变量的复杂关系进行可视化展示。掌握这些技能有助于我们更好地理解数据，并为后续的模型训练打下基础。