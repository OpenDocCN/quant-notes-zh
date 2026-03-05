# Python金融量化投资分析与股票交易：P21：Series缺失值处理 📊

![](img/32478fa354e43b5da2ac7d7f89733a91_1.png)

在本节课中，我们将学习如何处理Pandas Series中的缺失数据。缺失数据是数据分析中常见的问题，有效处理它们对于后续的计算和可视化至关重要。

上一节我们介绍了Series的基本概念和创建方法。本节中我们来看看如何处理Series中可能出现的缺失值。

![](img/32478fa354e43b5da2ac7d7f89733a91_3.png)

## 缺失值的概念

在Series中可能会出现缺失数据，即`NaN`值。出现缺失数据时，有时可以放任不管，但有时为了进行进一步运算或生成图表，我们需要将其处理掉。

![](img/32478fa354e43b5da2ac7d7f89733a91_5.png)

## 缺失值处理方法

![](img/32478fa354e43b5da2ac7d7f89733a91_7.png)

处理缺失数据主要有两种方法：删除缺失值或填充缺失值。

![](img/32478fa354e43b5da2ac7d7f89733a91_9.png)

### 方法一：删除缺失值

![](img/32478fa354e43b5da2ac7d7f89733a91_11.png)

![](img/32478fa354e43b5da2ac7d7f89733a91_13.png)

第一种方法是将含有缺失数据的行直接删除。

![](img/32478fa354e43b5da2ac7d7f89733a91_15.png)

以下是判断和删除缺失值的相关函数：

*   **`SR.isnull()`**：此函数返回一个布尔型Series，用于判断每个位置是否为缺失值。是`NaN`则返回`True`，否则返回`False`。
*   **`SR.notnull()`**：此函数与`isnull()`相反，不是缺失值的地方返回`True`，是缺失值则返回`False`。可以利用这个特性进行布尔索引过滤。
    ```python
    SR_filtered = SR[SR.notnull()]
    ```
*   **`SR.dropna()`**：此函数直接删除所有包含缺失值的行，是处理缺失值的便捷方法。
    ```python
    SR_dropped = SR.dropna()
    ```

### 方法二：填充缺失值

第二种方法是为缺失数据赋予一个具体的值。

![](img/32478fa354e43b5da2ac7d7f89733a91_17.png)

以下是填充缺失值的函数：

![](img/32478fa354e43b5da2ac7d7f89733a91_19.png)

![](img/32478fa354e43b5da2ac7d7f89733a91_21.png)

*   **`SR.fillna(value)`**：此函数将所有的`NaN`值填充为指定的`value`。例如，填充为0：
    ```python
    SR_filled_zero = SR.fillna(0)
    ```
    **注意**：Pandas的数据处理操作（如`fillna`）默认不会修改原Series，而是返回一个新的Series。如需保存结果，必须进行赋值操作。

除了填充固定值，还可以根据实际情况选择更合理的填充方式。例如，将缺失值填充为该Series的平均值，可以使数据趋势更连续。
```python
SR_filled_mean = SR.fillna(SR.mean())
```
`SR.mean()`在计算平均值时会自动忽略`NaN`值，这为数据处理提供了极大的便利。

![](img/32478fa354e43b5da2ac7d7f89733a91_23.png)

本节课中我们一起学习了Pandas Series中缺失值的两种核心处理方法：删除（`dropna`）与填充（`fillna`）。掌握这些方法，是进行数据清洗、保证数据质量的重要步骤，为后续的金融数据分析和建模打下坚实基础。