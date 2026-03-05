# Python金融量化：P15：双均线策略-金叉&死叉 📈

![](img/390b3d77b2dfecf2e8b939ae7e6daff9_1.png)

在本节课中，我们将学习双均线策略的核心概念——金叉与死叉。我们将理解它们的定义，并通过Python代码找出股票数据中所有金叉和死叉出现的具体日期。

## 概述

![](img/390b3d77b2dfecf2e8b939ae7e6daff9_3.png)

![](img/390b3d77b2dfecf2e8b939ae7e6daff9_5.png)

![](img/390b3d77b2dfecf2e8b939ae7e6daff9_7.png)

上一节我们介绍了如何计算并绘制股票的移动平均线。本节中，我们来看看如何利用短期均线（如MA5）和长期均线（如MA30）的交叉关系，来识别市场中的买入（金叉）和卖出（死叉）信号。

![](img/390b3d77b2dfecf2e8b939ae7e6daff9_9.png)

![](img/390b3d77b2dfecf2e8b939ae7e6daff9_11.png)

## 什么是金叉与死叉？🤔

![](img/390b3d77b2dfecf2e8b939ae7e6daff9_13.png)

![](img/390b3d77b2dfecf2e8b939ae7e6daff9_15.png)

在股票技术分析中，金叉和死叉是分析指标中两根线交叉产生的信号。

![](img/390b3d77b2dfecf2e8b939ae7e6daff9_17.png)

![](img/390b3d77b2dfecf2e8b939ae7e6daff9_19.png)

*   **金叉**：当短期均线（如MA5）从下方上穿长期均线（如MA30）时，形成的交叉点附近区域称为金叉。金叉通常被视为买入信号。
*   **死叉**：当短期均线从上方下穿长期均线时，形成的交叉点附近区域称为死叉。死叉通常被视为卖出信号。

在我们的坐标系中，X轴代表时间，Y轴代表计算出的移动平均值（如5日或30日收盘价的均值）。金叉和死叉就是两条均线在坐标系中相交时产生的。

## 如何通过代码识别金叉与死叉？💻

![](img/390b3d77b2dfecf2e8b939ae7e6daff9_21.png)

![](img/390b3d77b2dfecf2e8b939ae7e6daff9_23.png)

我们的目标是找出所有金叉和死叉发生的日期。核心思路是分析短期均线（`ma5`）和长期均线（`ma30`）在每个时间点上的大小关系。

![](img/390b3d77b2dfecf2e8b939ae7e6daff9_25.png)

![](img/390b3d77b2dfecf2e8b939ae7e6daff9_27.png)

![](img/390b3d77b2dfecf2e8b939ae7e6daff9_29.png)

在交叉点附近，大小关系会发生反转：
*   **金叉点左侧**：`ma5 < ma30`
*   **金叉点右侧**：`ma5 > ma30`
*   **死叉点左侧**：`ma5 > ma30`
*   **死叉点右侧**：`ma5 < ma30`

因此，我们可以通过判断 `ma5` 和 `ma30` 序列的大小关系变化来定位交叉点。

### 数据准备

首先，我们需要确保用于比较的 `ma5` 和 `ma30` 序列长度一致且没有空值（NaN）。通常从第30个数据点开始截取，以保证 `ma30` 也有有效值。

![](img/390b3d77b2dfecf2e8b939ae7e6daff9_31.png)

```python
# 假设 df 是包含 ‘ma5’ 和 ‘ma30’ 列的DataFrame
# 从第30个数据点开始截取，保证数据有效且索引对齐
ma5 = df[‘ma5‘][30:]
ma30 = df[‘ma30‘][30:]
# 同时，原数据 df 也需要做同样的截取，以便后续索引对齐
df = df[30:]
```

![](img/390b3d77b2dfecf2e8b939ae7e6daff9_33.png)

![](img/390b3d77b2dfecf2e8b939ae7e6daff9_35.png)

### 定义大小关系序列

接着，我们创建两个布尔序列，分别表示 `ma5 < ma30` 和 `ma5 > ma30` 的条件。

```python
# s1 序列：ma5 是否小于 ma30 (True表示小于)
s1 = ma5 < ma30
# s2 序列：ma5 是否大于 ma30 (True表示大于)
s2 = ma5 > ma30
# 注意：s2 本质上等于 ~s1 (对s1取反)
```

### 定位死叉日期

![](img/390b3d77b2dfecf2e8b939ae7e6daff9_37.png)

根据之前的分析，死叉对应着大小关系从 `ma5 > ma30` (在s1中为False) 过渡到 `ma5 < ma30` (在s1中为True)。我们可以通过 `s1 & (s2.shift(1))` 这个条件来捕捉这种 `False -> True` 的过渡。

![](img/390b3d77b2dfecf2e8b939ae7e6daff9_39.png)

以下是定位死叉日期的步骤：

1.  将 `s2` 序列向下移动一位（`s2.shift(1)`），这样每个位置的值就代表了**前一个交易日** `ma5 > ma30` 是否成立。
2.  将移动后的 `s2` 与当前的 `s1` 进行逻辑与（`&`）操作。
3.  当 `(s1 == True) & (前一日s2 == True)` 成立时，即 `(当前ma5 < ma30) & (前一日ma5 > ma30)`，这正好标识了死叉点。
4.  用这个布尔序列作为索引，从原始数据 `df` 中提取出对应行，其索引即为死叉日期。

```python
# 判断死叉的条件：当前s1为True (ma5<ma30)，且前一日s2为True (前一日ma5>ma30)
death_cross_condition = s1 & (s2.shift(1))
# 获取死叉对应的行数据
death_cross_data = df.loc[death_cross_condition]
# 提取死叉日期
death_dates = death_cross_data.index
```

### 定位金叉日期

![](img/390b3d77b2dfecf2e8b939ae7e6daff9_41.png)

同理，金叉对应着大小关系从 `ma5 < ma30` (在s1中为True) 过渡到 `ma5 > ma30` (在s1中为False)。我们可以通过 `~(s1 | (s2.shift(1)))` 这个条件来捕捉这种 `True -> False` 的过渡。

以下是定位金叉日期的步骤：

1.  将 `s2` 序列向下移动一位（`s2.shift(1)`）。
2.  将当前的 `s1` 与移动后的 `s2` 进行逻辑或（`|`）操作。`s1 | s2.shift(1)` 为True表示：`当前ma5<ma30` **或** `前一日ma5>ma30` 至少一个成立。
3.  在交叉点，`当前ma5<ma30 (True)` 且 `前一日ma5>ma30 (False)`，`True | False` 结果为 `True`。但我们需要的是 `True -> False` 的过渡点，即结果应为 `False` 的点。因此，我们需要对 `s1 | s2.shift(1)` 的结果取反（`~`）。
4.  取反后，`~(True | False)` 得到 `False`，`~(False | True)` 得到 `False`，而 `~(True | True)` 得到 `False`，只有 `~(False | False)` 得到 `True`。这个 `True` 对应的是 `当前ma5>ma30 (s1=False)` 且 `前一日ma5<ma30 (s2.shift(1)=False)`，即金叉点。
5.  用这个布尔序列作为索引，从 `df` 中提取金叉日期。

![](img/390b3d77b2dfecf2e8b939ae7e6daff9_43.png)

```python
# 判断金叉的条件：对 (s1为True 或 前一日s2为True) 的结果取反
# 等价于：当前s1为False (ma5>ma30) 且 前一日s2为False (前一日ma5<ma30)
golden_cross_condition = ~(s1 | (s2.shift(1)))
# 获取金叉对应的行数据
golden_cross_data = df.loc[golden_cross_condition]
# 提取金叉日期
golden_dates = golden_cross_data.index
```

![](img/390b3d77b2dfecf2e8b939ae7e6daff9_45.png)

## 总结

本节课中我们一起学习了双均线策略的关键信号——金叉与死叉。我们不仅理解了它们作为买入和卖出信号的理论含义，更重要的是，我们掌握了如何通过Python代码，利用短期均线和长期均线的数值关系变化，来精确地识别并提取出这些信号发生的具体日期。这是构建自动化交易策略非常基础且重要的一步。

![](img/390b3d77b2dfecf2e8b939ae7e6daff9_47.png)

![](img/390b3d77b2dfecf2e8b939ae7e6daff9_49.png)

![](img/390b3d77b2dfecf2e8b939ae7e6daff9_51.png)

![](img/390b3d77b2dfecf2e8b939ae7e6daff9_53.png)

核心方法总结如下：
*   **数据对齐**：确保用于比较的均线数据长度一致、无空值且索引对齐。
*   **关系判断**：创建布尔序列记录 `ma5 < ma30` 和 `ma5 > ma30` 的条件。
*   **死叉定位**：使用条件 **`s1 & (s2.shift(1))`** 找出从 `ma5 > ma30` 过渡到 `ma5 < ma30` 的日期。
*   **金叉定位**：使用条件 **`~(s1 | (s2.shift(1)))`** 找出从 `ma5 < ma30` 过渡到 `ma5 > ma30` 的日期。