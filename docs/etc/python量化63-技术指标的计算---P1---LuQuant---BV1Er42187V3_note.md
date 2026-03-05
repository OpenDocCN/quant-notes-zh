# Python量化63：技术指标的计算 - P1 - LuQuant

![](img/d8b193a6fe81cf0f9e2a62e845a452f7_1.png)

## 概述
在本节课中，我们将学习如何使用Python分析外汇市场数据，并探索技术指标与未来价格方向之间是否存在相关性。我们将从下载数据、清理数据开始，然后计算并分析几个常见的技术指标，最后尝试定义并可视化价格趋势。

---

## 1：数据获取与初步处理

首先，我们需要获取外汇市场数据。我们将从特定网站下载美元对瑞士法郎（USD/CHF）的历史价格数据，时间范围选择1小时和4小时K线图。

以下是获取数据后的初步处理步骤：

1.  **导入库并加载数据**：使用`pandas`库的`read_csv`函数加载下载的CSV文件。
    ```python
    import pandas as pd
    df = pd.read_csv('your_downloaded_file.csv')
    ```

![](img/d8b193a6fe81cf0f9e2a62e845a452f7_3.png)

2.  **检查数据**：使用`tail()`函数查看数据框的最后几行，了解数据结构。数据通常包含时间、开盘价、最高价、最低价、收盘价和交易量等列。

3.  **数据清理**：
    *   删除交易量为零的行，这些可能代表非交易时段（如周末）。
        ```python
        zero_volume_indices = df[df['Volume'] == 0].index
        df = df.drop(zero_volume_indices)
        ```
    *   检查并处理缺失值。
        ```python
        missing_values = df.isna().sum()
        ```

完成这些步骤后，我们就得到了一个相对干净、可用于分析的数据集。

---

## 2：计算技术指标

上一节我们准备好了数据，本节中我们来看看如何为数据添加技术指标。我们将使用`pandas_ta`库来计算几个常见指标。

以下是需要计算的指标及其含义：

*   **平均真实波幅（ATR）**：衡量市场波动性的指标。我们设置参数`length=20`，表示使用最近20根K线计算。
    ```python
    df['ATR'] = df.ta.atr(length=20)
    ```
*   **相对强弱指数（RSI）**：衡量价格变动速度和幅度的动量指标。默认参数`length=14`。
    ```python
    df['RSI'] = df.ta.rsi()
    ```
*   **中间价**：每根K线的（最高价+最低价）/2。
    ```python
    df['Average'] = (df['High'] + df['Low']) / 2
    ```
*   **移动平均线（MA）**：我们计算三条不同周期的简单移动平均线，以捕捉不同时间尺度的趋势。
    *   `MA_40`: 对价格变动最敏感。
    *   `MA_80`: 敏感性中等。
    *   `MA_160`: 最不敏感，代表长期趋势。
    ```python
    df['MA_40'] = df.ta.sma(length=40)
    df['MA_80'] = df.ta.sma(length=80)
    df['MA_160'] = df.ta.sma(length=160)
    ```

为了进一步分析趋势，我们定义一个函数来计算任意序列的**斜率**。

```python
import numpy as np

def get_slope(points):
    """计算一组点的平均斜率"""
    x = np.arange(len(points))
    slope, _ = np.polyfit(x, points, 1) # 线性拟合，返回斜率和截距
    return slope
```

然后，我们将这个函数应用到中间价、RSI和三条移动平均线上，计算它们最近6个值的斜率，以此判断短期趋势方向。

```python
window = 6
df['Slope_Avg'] = df['Average'].rolling(window=window).apply(get_slope)
df['Slope_RSI'] = df['RSI'].rolling(window=window).apply(get_slope)
df['Slope_MA40'] = df['MA_40'].rolling(window=window).apply(get_slope)
# ... 同样计算 Slope_MA80, Slope_MA160
```

现在，我们的数据框中包含了原始价格数据和多个衍生技术指标，为后续分析奠定了基础。

---

## 3：定义分析目标（价格趋势）

有了技术指标，我们需要明确分析的目标。本节我们将定义一个将未来价格走势分类的方法。

我们采用一种基于固定止盈止损规则的分类方法：
1.  选定一个K线作为分析的起点。
2.  观察其后续一定数量（例如15-20根）K线的价格行为。
3.  设定一个止盈点（如+100点）和止损点（如-50点），形成2:1的盈亏比。
4.  根据价格在这段时间内最先触及的边界进行分类：
    *   **上升趋势（类别 1）**：价格先触及+100点止盈线，未触及-50点止损线。
    *   **下降趋势（类别 -1）**：价格先触及-100点止盈线，未触及+50点止损线。
    *   **无明确趋势（类别 0）**：价格在±50点之间震荡，未触及任何止盈或止损边界。

以下是实现该逻辑的函数示例：

```python
def define_target(row_index, df, bars_to_check=20, take_profit_pips=100, risk_reward_ratio=2):
    stop_loss_pips = take_profit_pips / risk_reward_ratio
    current_close = df.loc[row_index, 'Close']
    future_prices = df.loc[row_index+1: row_index+bars_to_check, 'Close']
    # 计算未来价格相对于起点的点数变化
    pips_changes = (future_prices - current_close) * 10000 # 假设汇率小数点后4位
    # 检查是否先触及止盈或止损
    for change in pips_changes:
        if change >= take_profit_pips:
            return 1  # 上升趋势
        elif change <= -take_profit_pips:
            return -1 # 下降趋势
        elif change <= -stop_loss_pips or change >= stop_loss_pips:
            # 如果先触及止损区域，则趋势失败，归为无趋势
            # 更复杂的逻辑可能需要提前终止
            pass
    return 0 # 无明确趋势

# 将此函数应用到数据框的每一行（注意处理边界）
df['Target'] = df.index.to_series().apply(lambda x: define_target(x, df) if x + 20 < len(df) else None)
```

![](img/d8b193a6fe81cf0f9e2a62e845a452f7_5.png)

为数据添加了`Target`列后，我们就有了一个监督学习或统计分析所需的标签。

---

## 4：数据探索与可视化

在构建复杂模型之前，先对数据进行探索性分析至关重要。本节我们将通过绘制直方图来观察特征和目标的分布。

以下是需要可视化的关键内容：

![](img/d8b193a6fe81cf0f9e2a62e845a452f7_7.png)

*   **特征分布**：查看`Volume`（交易量）、`ATR`、`RSI`、`Average`（中间价）以及各`Slope`（斜率）的分布情况，检查是否存在异常值。
*   **目标分布**：查看`Target`列中三个类别（-1， 0， 1）的频率。通常“无明确趋势”的样本会更多。

```python
import matplotlib.pyplot as plt
features_to_plot = ['Volume', 'ATR', 'RSI', 'Average', 'Slope_Avg', 'Target']
df[features_to_plot].hist(bins=50, figsize=(15, 10))
plt.show()
```

通过直方图，我们可以快速了解数据的整体情况，确认数据清洗是否有效，并观察不同趋势类别下特征的初步分布差异。

---

## 5：初步分析：RSI与趋势的关系

最后，我们来做一个简单的分析：单独使用RSI能否预测趋势？

我们按照`Target`列将数据分为三个子集：上升趋势、下降趋势和无趋势。然后分别绘制这三个子集中RSI值的分布直方图。

```python
df_up = df[df['Target'] == 1]
df_down = df[df['Target'] == -1]
df_flat = df[df['Target'] == 0]

plt.figure(figsize=(10,6))
plt.hist(df_up['RSI'].dropna(), bins=100, alpha=0.5, label='Up Trend', density=True)
plt.hist(df_down['RSI'].dropna(), bins=100, alpha=0.5, label='Down Trend', density=True)
plt.hist(df_flat['RSI'].dropna(), bins=100, alpha=0.5, label='No Clear Trend', density=True)
plt.legend()
plt.title('RSI Distribution for Different Trend Classes')
plt.xlabel('RSI')
plt.ylabel('Density')
plt.show()
```

如果三个趋势类别的RSI分布高度重叠，没有明显区别，则说明**仅凭RSI一个指标难以有效区分未来的价格趋势**。这提示我们可能需要结合更多指标或使用更复杂的模型。

---

![](img/d8b193a6fe81cf0f9e2a62e845a452f7_9.png)

## 总结
本节课中我们一起学习了量化分析的基础流程：
1.  **获取与清洗数据**：从外部源下载数据，并进行清理（删除零交易量、处理缺失值）。
2.  **计算技术指标**：使用`pandas_ta`库计算ATR、RSI、移动平均线等指标，并计算其斜率以捕捉趋势变化。
3.  **定义分析目标**：基于固定的止盈止损规则，将未来的价格走势分类为上升、下降或无明确趋势。
4.  **数据探索**：通过直方图可视化数据和目标分布，进行初步了解。
5.  **简单分析**：通过对比不同趋势类别下RSI的分布，我们发现单一技术指标的预测能力有限。

![](img/d8b193a6fe81cf0f9e2a62e845a452f7_10.png)

这为后续使用多个指标组合或机器学习模型进行预测打下了坚实的基础。