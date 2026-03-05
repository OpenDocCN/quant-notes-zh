# Python金融分析+量化交易：P7：06 双均线策略-金叉&死叉 📈

在本节课中，我们将要学习双均线策略的核心概念：金叉与死叉。我们将理解它们的定义，并通过Python代码找出股票数据中所有金叉和死叉出现的日期。

![](img/f0bb3cef626cd32a153ec74bf4de4ded_1.png)

## 概述

上一节我们介绍了如何计算并绘制股票的移动平均线。本节中，我们来看看如何利用短期均线（如MA5）和长期均线（如MA30）的交叉点，即“金叉”和“死叉”，来制定交易策略。我们的核心目标是编写代码，自动从数据中找出这些关键的交叉日期。

![](img/f0bb3cef626cd32a153ec74bf4de4ded_3.png)

## 什么是金叉与死叉？

![](img/f0bb3cef626cd32a153ec74bf4de4ded_5.png)

![](img/f0bb3cef626cd32a153ec74bf4de4ded_7.png)

在股票技术分析中，金叉和死叉是分析指标中两根线交叉的形态。在我们的双均线策略中，这两根线分别是短期均线（如MA5）和长期均线（如MA30）。

![](img/f0bb3cef626cd32a153ec74bf4de4ded_9.png)

![](img/f0bb3cef626cd32a153ec74bf4de4ded_11.png)

*   **短期均线**：例如MA5，代表较短时间周期（5天）内收盘价的平均值。
*   **长期均线**：例如MA30，代表较长时间周期（30天）内收盘价的平均值。

![](img/f0bb3cef626cd32a153ec74bf4de4ded_13.png)

![](img/f0bb3cef626cd32a153ec74bf4de4ded_15.png)

以下是金叉和死叉的直观定义：

![](img/f0bb3cef626cd32a153ec74bf4de4ded_17.png)

![](img/f0bb3cef626cd32a153ec74bf4de4ded_1.png)

![](img/f0bb3cef626cd32a153ec74bf4de4ded_19.png)

*   **金叉**：当短期均线从下方**上穿**长期均线时，其交点附近的位置称为金叉。
    *   **公式**：`MA5` 上穿 `MA30`。
*   **死叉**：当短期均线从上方**下穿**长期均线时，其交点附近的位置称为死叉。
    *   **公式**：`MA5` 下穿 `MA30`。

![](img/f0bb3cef626cd32a153ec74bf4de4ded_3.png)

## 金叉与死叉的交易意义

理解金叉和死叉的交易含义是制定策略的基础。

![](img/f0bb3cef626cd32a153ec74bf4de4ded_21.png)

*   **金叉**通常被视为**买入信号**。它表明短期趋势开始强于长期趋势，市场可能进入上涨通道。
*   **死叉**通常被视为**卖出信号**。它表明短期趋势开始弱于长期趋势，市场可能进入下跌通道。

![](img/f0bb3cef626cd32a153ec74bf4de4ded_23.png)

![](img/f0bb3cef626cd32a153ec74bf4de4ded_25.png)

![](img/f0bb3cef626cd32a153ec74bf4de4ded_27.png)

![](img/f0bb3cef626cd32a153ec74bf4de4ded_5.png)

![](img/f0bb3cef626cd32a153ec74bf4de4ded_29.png)

因此，在双均线策略中，一个简单的操作逻辑是：**出现金叉时买入，出现死叉时卖出**。

![](img/f0bb3cef626cd32a153ec74bf4de4ded_7.png)

## 如何在数据中定位金叉与死叉？

我们的目标是找出金叉和死叉发生的具体日期。这需要分析`MA5`和`MA30`这两条均线数值的大小关系。

观察均线交叉点的特征：
*   在**金叉点**左侧：`MA5 < MA30`
*   在**金叉点**右侧：`MA5 > MA30`
*   在**死叉点**左侧：`MA5 > MA30`
*   在**死叉点**右侧：`MA5 < MA30`

![](img/f0bb3cef626cd32a153ec74bf4de4ded_11.png)

![](img/f0bb3cef626cd32a153ec74bf4de4ded_31.png)

核心思路是：**通过判断`MA5`和`MA30`大小关系序列的变化，来定位交叉点**。

![](img/f0bb3cef626cd32a153ec74bf4de4ded_33.png)

### 步骤一：数据准备与布尔序列生成

![](img/f0bb3cef626cd32a153ec74bf4de4ded_35.png)

首先，我们需要确保`MA5`和`MA30`两个序列具有相同的有效数据起点（避免NaN值干扰比较），并生成代表大小关系的布尔序列。

```python
# 假设 df 是包含‘MA5’和‘MA30’列的DataFrame
# 统一从第30个数据开始（确保MA30没有NaN值）
ma5 = df[‘MA5‘][30:]
ma30 = df[‘MA30‘][30:]

# 生成布尔序列
# s1: 代表 MA5 < MA30 的条件
s1 = ma5 < ma30
# s2: 代表 MA5 > MA30 的条件 (s1的相反情况)
s2 = ma5 > ma30
```

此时，`s1`是一个布尔值序列，其中`True`表示当天`MA5 < MA30`，`False`表示当天`MA5 > MA30`。`s2`则与之相反。

### 步骤二：定位死叉日期

![](img/f0bb3cef626cd32a153ec74bf4de4ded_37.png)

根据之前的分析，死叉点对应`s1`序列中从`False`过渡到`True`的位置（即左侧`MA5 > MA30`，右侧`MA5 < MA30`）。

![](img/f0bb3cef626cd32a153ec74bf4de4ded_39.png)

我们可以利用序列移位和逻辑运算来捕捉这种变化：

```python
# 判断死叉的条件：s1（当前为False）且 s2.shift(1)（前一天为True）
# 即：前一天 MA5 > MA30 (s2=True)，当天 MA5 < MA30 (s1=True)
death_cross_condition = s1 & s2.shift(1)

# 获取死叉日期
# 注意：原数据df也需要做同样的切片以保持索引对齐
df_aligned = df[30:]
death_dates = df_aligned.loc[death_cross_condition].index
```

**代码解释**：
1.  `s2.shift(1)`将`s2`序列向下移动一位，代表“前一天”的`MA5 > MA30`状态。
2.  `s1 & s2.shift(1)`进行逻辑与操作。只有当“当天`s1`为True”且“前一天`s2`为True”同时满足时，结果才为`True`，这正好对应了从`False`（`s1`为False等价于`s2`为True）到`True`（`s1`为True）的过渡点，即死叉点。
3.  用这个布尔序列作为索引，从对齐后的数据中提取出行，其索引就是死叉日期。

### 步骤三：定位金叉日期

![](img/f0bb3cef626cd32a153ec74bf4de4ded_41.png)

同理，金叉点对应`s1`序列中从`True`过渡到`False`的位置（即左侧`MA5 < MA30`，右侧`MA5 > MA30`）。

捕捉这种变化的代码如下：

```python
# 判断金叉的条件：s1（当前为False）且 s1.shift(1)（前一天为True）
# 即：前一天 MA5 < MA30 (s1=True)，当天 MA5 > MA30 (s1=False)
# 也可以写成：~(s1 | s2.shift(1))，即对 (s1为True 或 前一天s2为True) 的情况取反
golden_cross_condition = ~(s1 | s2.shift(1))

# 获取金叉日期
golden_dates = df_aligned.loc[golden_cross_condition].index
```

**代码解释**：
1.  `s1 | s2.shift(1)`进行逻辑或操作。当“当天`s1`为True”或“前一天`s2`为True”任一满足时，结果为`True`。这涵盖了金叉点**两侧**的状态（左侧`s1=True`，右侧前一天`s2=True`）。
2.  对这个结果取反`~`，得到`True`的位置就恰好是金叉点（既不是左侧状态，也不是右侧状态，而是变化的瞬间）。
3.  用此布尔序列提取出的索引，即为金叉日期。

![](img/f0bb3cef626cd32a153ec74bf4de4ded_43.png)

![](img/f0bb3cef626cd32a153ec74bf4de4ded_47.png)

## 总结

![](img/f0bb3cef626cd32a153ec74bf4de4ded_45.png)

本节课中我们一起学习了双均线策略的关键信号——金叉与死叉。

1.  **理解了概念**：金叉是短期均线上穿长期均线，视为买入信号；死叉是短期均线下穿长期均线，视为卖出信号。
2.  **掌握了核心方法**：通过分析`MA5`和`MA30`大小关系序列(`s1`)的变化来定位交叉点。
    *   死叉对应 `s1` 从 `False` 变为 `True`，使用条件 `s1 & s2.shift(1)` 捕捉。
    *   金叉对应 `s1` 从 `True` 变为 `False`，使用条件 `~(s1 | s2.shift(1))` 捕捉。
3.  **实现了代码**：我们编写了Python代码，通过`pandas`的布尔索引和`shift()`方法，自动化地找出了股票数据中所有金叉和死叉发生的日期。

![](img/f0bb3cef626cd32a153ec74bf4de4ded_47.png)

至此，我们已经能够精准识别交易信号点。在接下来的章节中，我们将利用这些信号日期，进一步完成交易策略的模拟与回测。

![](img/f0bb3cef626cd32a153ec74bf4de4ded_49.png)

![](img/f0bb3cef626cd32a153ec74bf4de4ded_51.png)

![](img/f0bb3cef626cd32a153ec74bf4de4ded_53.png)

![](img/f0bb3cef626cd32a153ec74bf4de4ded_53.png)