# 数据分析+金融量化+数据清洗：P19：2.清洗空值面试题 📊

![](img/3efceeb4c4b548fd7eab71105f442b61_1.png)

![](img/3efceeb4c4b548fd7eab71105f442b61_3.png)

在本节课中，我们将通过一个真实的面试题，学习如何处理数据集中的空值（缺失值）。我们将使用一个冷库温度数据的例子，演示两种核心的空值处理方法：删除和填充。

![](img/3efceeb4c4b548fd7eab71105f442b61_5.png)

![](img/3efceeb4c4b548fd7eab71105f442b61_7.png)

![](img/3efceeb4c4b548fd7eab71105f442b61_9.png)

上一节我们介绍了数据清洗的基本概念，本节中我们来看看如何具体应用这些方法来处理一个实际问题。

![](img/3efceeb4c4b548fd7eab71105f442b61_11.png)

![](img/3efceeb4c4b548fd7eab71105f442b61_13.png)

## 数据加载与查看

![](img/3efceeb4c4b548fd7eab71105f442b61_15.png)

![](img/3efceeb4c4b548fd7eab71105f442b61_17.png)

首先，我们需要将数据加载到Python环境中进行查看。数据存储在一个名为`test_data.xlsx`的Excel文件中。

![](img/3efceeb4c4b548fd7eab71105f442b61_19.png)

```python
import pandas as pd
df = pd.read_excel('./data/test_data.xlsx')
```

加载后，我们查看数据的基本结构。数据包含7列（1-7号温度采集设备）和多行（每分钟采集一次）。我们的目标是使用1-4号设备的数据来预测5-7号设备的温度。因此，数据清洗的重点是处理前四列中的空值。

![](img/3efceeb4c4b548fd7eab71105f442b61_21.png)

![](img/3efceeb4c4b548fd7eab71105f442b61_23.png)

## 核心处理方法：删除空值行

![](img/3efceeb4c4b548fd7eab71105f442b61_25.png)

![](img/3efceeb4c4b548fd7eab71105f442b61_27.png)

第一种处理空值的方法是直接删除包含空值的整行数据。这种方法简单直接，适用于空值较少、删除后对整体数据影响不大的情况。

![](img/3efceeb4c4b548fd7eab71105f442b61_29.png)

![](img/3efceeb4c4b548fd7eab71105f442b61_31.png)

![](img/3efceeb4c4b548fd7eab71105f442b61_33.png)

以下是删除空值行的两种实现方式：

![](img/3efceeb4c4b548fd7eab71105f442b61_35.png)

![](img/3efceeb4c4b548fd7eab71105f442b61_37.png)

1.  **使用 `dropna()` 方法**：
    这是最直接的方法。`axis=0` 参数表示按行删除（`axis=1`表示按列删除）。
    ```python
    df_cleaned_drop = df[['1', '2', '3', '4']].dropna(axis=0)
    ```

2.  **使用布尔索引结合 `notna()` 和 `all()`**：
    这种方法先创建一个布尔掩码，标识出那些在所有指定列上都没有空值的行，然后利用这个掩码来筛选数据。
    ```python
    # 检查前四列，找出没有任何空值的行
    mask = df[['1', '2', '3', '4']].notna().all(axis=1)
    # 使用布尔掩码筛选数据
    df_cleaned_bool = df.loc[mask, ['1', '2', '3', '4']]
    ```

![](img/3efceeb4c4b548fd7eab71105f442b61_39.png)

![](img/3efceeb4c4b548fd7eab71105f442b61_41.png)

原始数据有1060行，删除空值行后剩余982行。删除比例不高，因此在此案例中删除法是可行的。

![](img/3efceeb4c4b548fd7eab71105f442b61_43.png)

## 核心处理方法：填充空值

![](img/3efceeb4c4b548fd7eab71105f442b61_45.png)

第二种处理空值的方法是使用邻近值进行填充。这适用于数据具有连续性（如时间序列数据）的场景，填充后能保留更多的数据样本。

![](img/3efceeb4c4b548fd7eab71105f442b61_47.png)

![](img/3efceeb4c4b548fd7eab71105f442b61_49.png)

![](img/3efceeb4c4b548fd7eab71105f442b61_51.png)

对于温度数据，同一设备相邻时间点的读数通常接近，因此适合用前向或后向填充。

![](img/3efceeb4c4b548fd7eab71105f442b61_53.png)

![](img/3efceeb4c4b548fd7eab71105f442b61_55.png)

以下是填充空值的步骤：

![](img/3efceeb4c4b548fd7eab71105f442b61_57.png)

![](img/3efceeb4c4b548fd7eab71105f442b61_59.png)

1.  **前向填充 (`ffill`)**：
    使用同一列中上一个有效值来填充当前空值。`axis=0` 表示沿列方向（垂直方向）查找。
    ```python
    df_filled = df[['1', '2', '3', '4']].fillna(method='ffill', axis=0)
    ```

2.  **后向填充 (`bfill`)**：
    前向填充后，数据开头可能仍有空值（因为前面没有值可参考）。此时可以再进行一次后向填充，使用下一个有效值进行填充。
    ```python
    df_filled = df_filled.fillna(method='bfill', axis=0)
    ```

![](img/3efceeb4c4b548fd7eab71105f442b61_61.png)

![](img/3efceeb4c4b548fd7eab71105f442b61_63.png)

## 验证清洗结果

填充操作完成后，必须验证是否所有空值都已被处理。

![](img/3efceeb4c4b548fd7eab71105f442b61_65.png)

![](img/3efceeb4c4b548fd7eab71105f442b61_67.png)

我们可以使用 `isna()` 结合 `any()` 方法来检查：

![](img/3efceeb4c4b548fd7eab71105f442b61_69.png)

```python
# 检查每一列是否还有空值
columns_check = df_filled.isna().any(axis=0)
print(columns_check)

![](img/3efceeb4c4b548fd7eab71105f442b61_71.png)

![](img/3efceeb4c4b548fd7eab71105f442b61_73.png)

# 检查每一行是否还有空值
rows_check = df_filled.isna().any(axis=1)
print(rows_check.any()) # 如果为False，则表示所有行都已无空值
```

![](img/3efceeb4c4b548fd7eab71105f442b61_75.png)

如果输出显示所有列均为 `False`，则表明数据中的空值已被成功清洗。

![](img/3efceeb4c4b548fd7eab71105f442b61_77.png)

![](img/3efceeb4c4b548fd7eab71105f442b61_79.png)

![](img/3efceeb4c4b548fd7eab71105f442b61_81.png)

本节课中我们一起学习了如何处理数据中的空值。我们掌握了两种核心方法：**删除包含空值的行** 和 **使用前向/后向填充法填补空值**。并通过一个冷库温度数据的面试题，实践了如何加载数据、选择目标列、应用清洗方法，并最终验证清洗结果。选择哪种方法取决于数据特性和业务需求，在数据丢失不多时可选择删除，在需要保持数据连续性时则适合填充。