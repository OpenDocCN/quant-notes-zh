# 2025数据分析：P16：15.双均线策略-金叉&死叉 🚀💰

![](img/6c8c333a450e4beea1fa4bcdc4f9effe_1.png)

## 概述
在本节课中，我们将学习双均线策略的核心概念：金叉与死叉。我们将了解它们的定义，并通过Python代码，从股票数据中找出所有金叉和死叉发生的具体日期。这是实现量化交易策略的关键一步。

![](img/6c8c333a450e4beea1fa4bcdc4f9effe_3.png)

![](img/6c8c333a450e4beea1fa4bcdc4f9effe_5.png)

![](img/6c8c333a450e4beea1fa4bcdc4f9effe_7.png)

![](img/6c8c333a450e4beea1fa4bcdc4f9effe_9.png)

## 金叉与死叉的定义
上一节我们介绍了如何计算移动平均线。本节中我们来看看如何利用均线判断买卖点。

![](img/6c8c333a450e4beea1fa4bcdc4f9effe_11.png)

![](img/6c8c333a450e4beea1fa4bcdc4f9effe_13.png)

![](img/6c8c333a450e4beea1fa4bcdc4f9effe_15.png)

![](img/6c8c333a450e4beea1fa4bcdc4f9effe_17.png)

在股票技术分析中，金叉和死叉是分析指标中两根线交叉产生的信号。在我们的双均线策略中，这两根线分别是5日均线（MA5，短期均线）和30日均线（MA30，长期均线）。

![](img/6c8c333a450e4beea1fa4bcdc4f9effe_19.png)

*   **金叉**：当短期均线（MA5）从下方上穿长期均线（MA30）时，交叉点附近的位置称为金叉。
*   **死叉**：当短期均线（MA5）从上方下穿长期均线（MA30）时，交叉点附近的位置称为死叉。

金叉通常被视为买入信号，而死叉则被视为卖出信号。

![](img/6c8c333a450e4beea1fa4bcdc4f9effe_21.png)

![](img/6c8c333a450e4beea1fa4bcdc4f9effe_23.png)

![](img/6c8c333a450e4beea1fa4bcdc4f9effe_25.png)

![](img/6c8c333a450e4beea1fa4bcdc4f9effe_27.png)

![](img/6c8c333a450e4beea1fa4bcdc4f9effe_29.png)

## 寻找金叉与死叉日期的思路
理解了金叉和死叉的定义后，我们需要从数据中找到它们发生的具体时间。

观察均线交叉点，我们可以发现一个规律：在交叉点发生的前后，短期均线（MA5）和长期均线（MA30）的大小关系会发生变化。
*   在金叉点**左侧**，MA5 < MA30。
*   在金叉点**右侧**，MA5 > MA30。
*   在死叉点**左侧**，MA5 > MA30。
*   在死叉点**右侧**，MA5 < MA30。

因此，我们可以通过比较MA5和MA30序列的大小关系，并找到其关系发生“翻转”的时刻，来定位金叉和死叉。

![](img/6c8c333a450e4beea1fa4bcdc4f9effe_31.png)

## 代码实现：定位金叉与死叉日期
以下是具体的实现步骤和代码。

![](img/6c8c333a450e4beea1fa4bcdc4f9effe_33.png)

![](img/6c8c333a450e4beea1fa4bcdc4f9effe_35.png)

首先，我们需要确保用于比较的MA5和MA30序列长度一致，并且索引对齐。由于MA30的前29个值为空（NaN），我们统一从第30个数据开始切片。

```python
# 确保数据索引对齐，从第30个数据开始
ma5 = df[‘ma5’][30:]
ma30 = df[‘ma30’][30:]
df = df[30:] # 原始数据也做相应切片
```

![](img/6c8c333a450e4beea1fa4bcdc4f9effe_37.png)

![](img/6c8c333a450e4beea1fa4bcdc4f9effe_39.png)

接着，我们创建两个布尔序列，分别代表 `MA5 < MA30` 和 `MA5 > MA30` 的条件。

```python
# 创建条件序列
s1 = ma5 < ma30  # 条件：MA5 < MA30
s2 = ma5 > ma30  # 条件：MA5 > MA30
```

现在，`s1` 和 `s2` 是两组布尔值序列。`True` 表示条件成立，`False` 表示条件不成立。我们的目标是找到条件发生变化的转折点。

![](img/6c8c333a450e4beea1fa4bcdc4f9effe_41.png)

**寻找死叉日期**
死叉的特征是：由 `MA5 > MA30` 变为 `MA5 < MA30`。在布尔序列中，这对应着 `s1` 由 `False` 变为 `True`。
我们可以通过 `(s1) & (s2.shift(1))` 这个条件来捕捉这一变化。`s2.shift(1)` 表示将 `s2` 序列向下移动一位。

```python
# 判断死叉的条件：当前s1为True，且前一天的s2为True (即前一天MA5>MA30)
death_cross_condition = (s1) & (s2.shift(1))
# 获取死叉日期
death_dates = df.loc[death_cross_condition].index
```

![](img/6c8c333a450e4beea1fa4bcdc4f9effe_43.png)

**寻找金叉日期**
金叉的特征是：由 `MA5 < MA30` 变为 `MA5 > MA30`。在布尔序列中，这对应着 `s1` 由 `True` 变为 `False`。
我们可以通过 `~(s1 | s2.shift(1))` 这个条件来捕捉这一变化。这里 `~` 表示逻辑取反。

![](img/6c8c333a450e4beea1fa4bcdc4f9effe_45.png)

```python
# 判断金叉的条件：当前s1为False，且前一天的s2为False (即前一天MA5<MA30)
golden_cross_condition = ~(s1 | s2.shift(1))
# 获取金叉日期
golden_dates = df.loc[golden_cross_condition].index
```

![](img/6c8c333a450e4beea1fa4bcdc4f9effe_47.png)

![](img/6c8c333a450e4beea1fa4bcdc4f9effe_49.png)

![](img/6c8c333a450e4beea1fa4bcdc4f9effe_51.png)

![](img/6c8c333a450e4beea1fa4bcdc4f9effe_53.png)

## 总结
本节课中我们一起学习了双均线策略的核心——金叉与死叉。我们首先明确了它们的定义和交易意义，然后深入分析了其数据特征：即短期均线与长期均线大小关系发生变化的时刻。最后，我们利用Pandas的布尔索引和序列移位（`shift`）功能，编写代码精准地找出了所有金叉和死叉发生的日期。这为我们后续基于这些信号进行模拟交易和回测奠定了坚实的基础。