# Python金融时间序列分析与量化交易实战教程：P61：60.特征可视化展示 📊

![](img/b5474b7dbc60014c4a92f8443d78e89f_0.png)

![](img/b5474b7dbc60014c4a92f8443d78e89f_2.png)

在本节课中，我们将学习如何为时间序列预测模型创建特征，并利用可视化技术直观地展示这些特征与目标变量之间的关系。我们将使用股票收益率数据，通过`shift`操作构造历史特征，并绘制散点图进行观察。

![](img/b5474b7dbc60014c4a92f8443d78e89f_4.png)

---

![](img/b5474b7dbc60014c4a92f8443d78e89f_6.png)

![](img/b5474b7dbc60014c4a92f8443d78e89f_8.png)

![](img/b5474b7dbc60014c4a92f8443d78e89f_10.png)

## 特征工程：构造历史特征

上一节我们介绍了时间序列预测的基本概念。本节中，我们来看看如何为预测模型构造输入特征。在时间序列预测中，一个常见的做法是使用过去几期的数据来预测未来。

![](img/b5474b7dbc60014c4a92f8443d78e89f_12.png)

我们的目标是预测当天的收益率（Y）。Y是数据中直接存在的`returns`列。而输入特征（X）则需要我们从历史数据中构造。例如，我们可以使用前一天（t-1）和前两天的收益率（t-2）作为特征来预测今天的收益率（t）。

![](img/b5474b7dbc60014c4a92f8443d78e89f_14.png)

以下是构造特征的具体步骤：

![](img/b5474b7dbc60014c4a92f8443d78e89f_16.png)

![](img/b5474b7dbc60014c4a92f8443d78e89f_18.png)

![](img/b5474b7dbc60014c4a92f8443d78e89f_20.png)

1.  **定义特征构造函数**：我们将创建一个函数，用于自动生成指定滞后期的历史特征。
2.  **应用`shift`操作**：`shift`函数可以将数据向上或向下移动指定的行数，从而方便地获取前一天、前两天等历史数据。

![](img/b5474b7dbc60014c4a92f8443d78e89f_22.png)

![](img/b5474b7dbc60014c4a92f8443d78e89f_24.png)

```python
def create_features(data, n_features=2):
    """
    为时间序列数据创建滞后特征。
    参数:
        data: 包含目标列（如‘returns’）的DataFrame。
        n_features: 要创建的滞后特征数量，例如2代表创建前一天和前两天的特征。
    """
    for i in range(1, n_features + 1):
        # 定义列名，例如‘lag_1’，‘lag_2’
        col_name = ‘lag_{}’.format(i)
        # 使用shift(i)获取i天前的数据，并创建为新列
        data[col_name] = data[‘returns’].shift(i)
    # 删除因shift操作产生的缺失值行
    data = data.dropna()
    return data

![](img/b5474b7dbc60014c4a92f8443d78e89f_26.png)

![](img/b5474b7dbc60014c4a92f8443d78e89f_28.png)

![](img/b5474b7dbc60014c4a92f8443d78e89f_30.png)

# 应用函数，创建两个滞后特征
data = create_features(data, n_features=2)
```

![](img/b5474b7dbc60014c4a92f8443d78e89f_32.png)

![](img/b5474b7dbc60014c4a92f8443d78e89f_34.png)

执行上述代码后，我们的数据中会增加两列：`lag_1`（前一天收益率）和`lag_2`（前两天收益率）。注意，最初几行由于没有历史数据会变为`NaN`，我们使用`dropna()`将其删除。

![](img/b5474b7dbc60014c4a92f8443d78e89f_36.png)

![](img/b5474b7dbc60014c4a92f8443d78e89f_38.png)

![](img/b5474b7dbc60014c4a92f8443d78e89f_40.png)

---

![](img/b5474b7dbc60014c4a92f8443d78e89f_42.png)

## 特征可视化：散点图与颜色映射

![](img/b5474b7dbc60014c4a92f8443d78e89f_44.png)

![](img/b5474b7dbc60014c4a92f8443d78e89f_46.png)

特征构造完成后，下一步是直观地理解特征与目标变量之间的关系。我们可以通过绘制散点图来实现，并使用颜色来编码第三个维度——即当天的实际收益率。

![](img/b5474b7dbc60014c4a92f8443d78e89f_48.png)

![](img/b5474b7dbc60014c4a92f8443d78e89f_50.png)

以下是绘制特征可视化图的步骤：

1.  **绘制散点图**：以`lag_1`为X轴，`lag_2`为Y轴，绘制所有数据点。
2.  **使用颜色映射**：将每个点的颜色（`c`参数）设置为当天的实际`returns`值。颜色越偏向暖色（如红色），代表正收益越大；颜色越偏向冷色（如蓝色），代表负收益（下跌）越大。
3.  **添加参考线**：在`x=0`和`y=0`处添加参考线，便于观察数据分布。

![](img/b5474b7dbc60014c4a92f8443d78e89f_52.png)

```python
import matplotlib.pyplot as plt

plt.figure(figsize=(10, 6))
# 绘制散点图，颜色c由‘returns’列的值决定
scatter = plt.scatter(x=data[‘lag_1’],
                      y=data[‘lag_2’],
                      c=data[‘returns’],
                      cmap=‘coolwarm’, # 选择从蓝色到红色的渐变色系
                      alpha=0.7,
                      s=16)
# 添加颜色条，显示颜色与数值的对应关系
plt.colorbar(scatter, label=‘Actual Returns (Y)’)

![](img/b5474b7dbc60014c4a92f8443d78e89f_54.png)

![](img/b5474b7dbc60014c4a92f8443d78e89f_56.png)

# 添加参考线
plt.axhline(y=0, color=‘red’, linestyle=‘--’, alpha=0.5) # 水平线
plt.axvline(x=0, color=‘red’, linestyle=‘--’, alpha=0.5) # 垂直线

![](img/b5474b7dbc60014c4a92f8443d78e89f_58.png)

![](img/b5474b7dbc60014c4a92f8443d78e89f_60.png)

plt.xlabel(‘Lag 1 (t-1) Return’)
plt.ylabel(‘Lag 2 (t-2) Return’)
plt.title(‘Feature Visualization: Lagged Returns vs Actual Returns’)
plt.grid(True, alpha=0.3)
plt.show()
```

![](img/b5474b7dbc60014c4a92f8443d78e89f_62.png)

生成的图表中，每个点代表一个交易日。点的位置由前两天的收益率决定，而点的颜色则揭示了当天的实际表现。通过这种可视化方式，我们可以初步观察是否存在某种模式，例如，是否当`lag_1`和`lag_2`都为正值时，当天的收益率（颜色）也倾向于为暖色。

![](img/b5474b7dbc60014c4a92f8443d78e89f_64.png)

![](img/b5474b7dbc60014c4a92f8443d78e89f_66.png)

![](img/b5474b7dbc60014c4a92f8443d78e89f_68.png)

---

![](img/b5474b7dbc60014c4a92f8443d78e89f_70.png)

![](img/b5474b7dbc60014c4a92f8443d78e89f_72.png)

![](img/b5474b7dbc60014c4a92f8443d78e89f_74.png)

本节课中我们一起学习了时间序列预测中的特征工程与可视化。我们掌握了如何使用`shift`方法构造滞后特征，以及如何通过带有颜色映射的散点图，将特征（X：历史收益率）与目标变量（Y：当前收益率）的关系在一个二维平面上进行三维化的直观展示。这是构建和理解量化模型的重要基础步骤。