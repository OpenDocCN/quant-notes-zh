# Python金融分析与量化交易实战：P58：特征可视化展示

![](img/5c10797b89ce8be217485050455422bf_0.png)

![](img/5c10797b89ce8be217485050455422bf_2.png)

在本节课中，我们将学习如何为时间序列数据创建滞后特征，并使用散点图结合颜色映射（colormap）来可视化这些特征与实际目标值之间的关系。我们将使用股票收益率数据作为示例。

![](img/5c10797b89ce8be217485050455422bf_4.png)

上一节我们介绍了数据的基础处理，本节中我们来看看如何构建用于预测的滞后特征并进行可视化。

![](img/5c10797b89ce8be217485050455422bf_6.png)

![](img/5c10797b89ce8be217485050455422bf_8.png)

## 创建滞后特征

![](img/5c10797b89ce8be217485050455422bf_10.png)

在时间序列预测中，我们经常使用过去的数据（滞后特征）来预测未来的值。例如，我们可以使用前一天和前两天的收益率来预测今天的收益率。

![](img/5c10797b89ce8be217485050455422bf_12.png)

以下是创建滞后特征的具体步骤：

![](img/5c10797b89ce8be217485050455422bf_14.png)

1.  **定义特征列名**：我们计划创建两个特征，分别代表“前一天收益率”和“前两天收益率”。
2.  **使用`shift`函数**：Pandas的`shift()`函数可以将数据沿时间轴移动指定的步数，从而方便地创建滞后序列。
3.  **处理缺失值**：由于数据移动，序列开头会产生缺失值（NaN），我们需要将其删除。

![](img/5c10797b89ce8be217485050455422bf_16.png)

![](img/5c10797b89ce8be217485050455422bf_18.png)

我们将这些步骤封装在一个函数中，以便重复使用。

![](img/5c10797b89ce8be217485050455422bf_20.png)

![](img/5c10797b89ce8be217485050455422bf_22.png)

![](img/5c10797b89ce8be217485050455422bf_24.png)

```python
def create_lag_features(data, column, n_lags=2):
    """
    为指定列创建滞后特征。
    参数:
        data: 包含原始数据的DataFrame。
        column: 需要创建滞后特征的列名。
        n_lags: 要创建的滞后特征数量，默认为2。
    返回:
        添加了滞后特征列的DataFrame。
    """
    # 初始化一个列表，用于存储新创建的列名
    col_names = []
    # 循环创建指定数量的滞后特征
    for i in range(1, n_lags + 1):
        # 生成列名，例如‘lag_1’， ‘lag_2’
        col_name = f‘lag_{i}’
        # 使用shift函数创建滞后序列
        data[col_name] = data[column].shift(i)
        # 将新列名加入列表
        col_names.append(col_name)
    # 删除因创建滞后特征而产生的缺失值行
    data = data.dropna()
    return data, col_names

![](img/5c10797b89ce8be217485050455422bf_26.png)

![](img/5c10797b89ce8be217485050455422bf_28.png)

# 假设‘data’是包含‘returns’列的DataFrame
# 调用函数，为‘returns’列创建2个滞后特征
data, lag_cols = create_lag_features(data, ‘returns’, n_lags=2)
```

![](img/5c10797b89ce8be217485050455422bf_30.png)

![](img/5c10797b89ce8be217485050455422bf_32.png)

执行上述代码后，我们的`data` DataFrame 中会增加两列：`lag_1`（前一天收益率）和 `lag_2`（前两天收益率）。

![](img/5c10797b89ce8be217485050455422bf_34.png)

![](img/5c10797b89ce8be217485050455422bf_36.png)

![](img/5c10797b89ce8be217485050455422bf_38.png)

## 特征可视化

![](img/5c10797b89ce8be217485050455422bf_40.png)

![](img/5c10797b89ce8be217485050455422bf_42.png)

创建好特征后，我们可以通过可视化来直观地观察特征与目标值之间的关系。我们将绘制一个散点图：
*   **X轴**：`lag_1`（前一天收益率）
*   **Y轴**：`lag_2`（前两天收益率）
*   **点的颜色**：`returns`（当天实际收益率），使用颜色映射（colormap）来区分数值大小。

![](img/5c10797b89ce8be217485050455422bf_44.png)

以下是绘制该图的代码：

![](img/5c10797b89ce8be217485050455422bf_46.png)

```python
import matplotlib.pyplot as plt

![](img/5c10797b89ce8be217485050455422bf_48.png)

![](img/5c10797b89ce8be217485050455422bf_50.png)

# 设置图形大小
plt.figure(figsize=(16, 10))

# 绘制散点图
# c参数指定颜色依据的数据列，cmap参数指定颜色映射方案（这里使用‘coolwarm’）
scatter = plt.scatter(data[‘lag_1’], data[‘lag_2’], c=data[‘returns’], cmap=‘coolwarm’, alpha=0.7)

![](img/5c10797b89ce8be217485050455422bf_52.png)

# 添加颜色条，显示颜色与数值的对应关系
plt.colorbar(scatter, label=‘Actual Return (returns)’)

![](img/5c10797b89ce8be217485050455422bf_54.png)

# 添加参考线（零线）
plt.axhline(y=0, color=‘r’, linestyle=‘--’, alpha=0.5) # 水平零线
plt.axvline(x=0, color=‘r’, linestyle=‘--’, alpha=0.5) # 垂直零线

![](img/5c10797b89ce8be217485050455422bf_56.png)

# 添加标题和坐标轴标签
plt.title(‘Feature Visualization: Lagged Returns vs Actual Return’)
plt.xlabel(‘Lag 1 (Previous Day Return)’)
plt.ylabel(‘Lag 2 (Return from Two Days Ago)’)

![](img/5c10797b89ce8be217485050455422bf_58.png)

![](img/5c10797b89ce8be217485050455422bf_60.png)

# 显示图形
plt.show()
```

![](img/5c10797b89ce8be217485050455422bf_62.png)

**图形解读**：
*   在这个散点图中，每个点代表一个交易日。
*   点的位置由该交易日的前一天(`lag_1`)和前两天的收益率(`lag_2`)决定。
*   点的颜色深浅（从蓝色到红色）代表了该交易日**当天**的实际收益率(`returns`)。
    *   **红色越深**：表示当天实际收益率**为正且数值较大**（上涨强劲）。
    *   **蓝色越深**：表示当天实际收益率为**负且绝对值较大**（下跌严重）。
    *   **接近白色**：表示当天实际收益率接近零。
*   图中的红色虚线是X轴和Y轴的零线，有助于划分象限。

![](img/5c10797b89ce8be217485050455422bf_64.png)

![](img/5c10797b89ce8be217485050455422bf_66.png)

![](img/5c10797b89ce8be217485050455422bf_68.png)

这种可视化方法相当于在一个二维平面上展示了三维信息（X， Y， 颜色代表的Z），使我们能够直观地探索历史特征与当前目标值之间的潜在模式。

![](img/5c10797b89ce8be217485050455422bf_70.png)

![](img/5c10797b89ce8be217485050455422bf_72.png)

![](img/5c10797b89ce8be217485050455422bf_74.png)

本节课中我们一起学习了如何为金融时间序列数据创建滞后特征，并利用散点图与颜色映射技术对特征和目标变量进行了有效的可视化分析。这有助于我们在构建量化模型前，直观理解数据的内在结构和关系。