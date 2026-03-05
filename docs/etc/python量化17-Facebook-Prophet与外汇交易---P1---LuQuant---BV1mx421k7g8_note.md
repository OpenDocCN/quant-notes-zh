# Python量化17：1 - Facebook Prophet与外汇交易 🧮

在本节课中，我们将学习如何使用Facebook开发的Prophet模型进行时间序列预测，并将其应用于欧元/美元外汇交易数据。我们将通过Python代码实现，分析预测结果，并探讨其在交易策略中的潜在应用。

![](img/b8eb291618f57b782fcbc475e34fab77_1.png)

---

![](img/b8eb291618f57b782fcbc475e34fab77_3.png)

## 概述 📋

Prophet是Facebook开发的一种算法，专门用于处理时间序列数据并提供未来值的预测。该模型能够自动处理缺失数据和异常值，并考虑年度、每周和每日的季节性趋势，包括节假日的影响。对于交易者而言，这些特性使得Prophet成为一个值得探索的工具。

![](img/b8eb291618f57b782fcbc475e34fab77_5.png)

上一节我们介绍了Prophet模型的基本概念，本节中我们来看看如何将其应用于外汇价格预测。

---

## 数据准备与预处理 📊

![](img/b8eb291618f57b782fcbc475e34fab77_7.png)

首先，我们需要准备数据。我们使用欧元/美元外汇对的小时级别历史价格数据，时间范围从2003年到2023年。为了演示，我们仅选取其中的一部分数据以加快计算速度。

以下是数据预处理的关键步骤：

1.  **导入库并读取数据**：使用Pandas库读取CSV格式的历史价格数据。
2.  **数据清洗**：过滤掉交易量为零的蜡烛图（代表市场休市时段），并检查缺失值。
3.  **数据切片**：为了快速测试，我们仅选取1000根蜡烛图进行分析。
4.  **列名重命名**：Prophet模型要求输入数据框必须包含两列：`ds`（日期时间戳）和`y`（目标值）。我们将原始数据中的`GMT time`列重命名为`ds`，并创建新列`y`，其值等于收盘价`close`。
5.  **日期格式转换**：将`ds`列转换为正确的日期时间格式，以确保Prophet能够正确解析。

![](img/b8eb291618f57b782fcbc475e34fab77_9.png)

![](img/b8eb291618f57b782fcbc475e34fab77_10.png)

以下是核心的代码片段：

![](img/b8eb291618f57b782fcbc475e34fab77_11.png)

```python
import pandas as pd
from prophet import Prophet

![](img/b8eb291618f57b782fcbc475e34fab77_13.png)

# 读取数据
df = pd.read_csv('eurusd_hourly.csv')
# 过滤交易量为零的数据
df = df[df['Volume'] != 0].reset_index(drop=True)
# 选取部分数据
df_slice = df.iloc[1000:2000].copy()

![](img/b8eb291618f57b782fcbc475e34fab77_15.png)

# 准备Prophet所需列
df_slice = df_slice.rename(columns={'GMT time': 'ds'})
df_slice['ds'] = df_slice['ds'].str[:-4]  # 移除描述性后缀
df_slice['ds'] = pd.to_datetime(df_slice['ds'], format='%d.%m.%Y %H:%M:%S')
df_slice['y'] = df_slice['close']
```

![](img/b8eb291618f57b782fcbc475e34fab77_17.png)

---

## 构建预测函数 🔧

![](img/b8eb291618f57b782fcbc475e34fab77_19.png)

接下来，我们构建一个核心函数 `prophet_signal`，用于封装Prophet模型的拟合与预测过程。该函数接受以下参数：
*   `df_candles`：包含`ds`和`y`列的数据框。
*   `back_candles`：用于拟合模型的历史蜡烛图数量。
*   `front_predictions`：需要预测的未来蜡烛图数量。
*   `diff_limit`：用于生成分类信号的阈值（稍后讨论）。
*   `signal`：布尔值，决定是输出连续预测值还是分类信号。

函数的主要步骤如下：

1.  **数据复制**：创建数据的本地副本，避免修改原始数据。
2.  **模型初始化与拟合**：实例化Prophet模型，并使用指定数量的历史数据（`back_candles`）进行拟合。
3.  **创建未来时间点**：使用 `make_future_dataframe` 方法生成一个包含未来时间戳的数据框。
4.  **进行预测**：调用 `predict` 方法获取未来值的预测结果，包括预测值（`yhat`）以及置信区间（`yhat_lower`, `yhat_upper`）。

![](img/b8eb291618f57b782fcbc475e34fab77_21.png)

以下是函数的核心代码结构：

![](img/b8eb291618f57b782fcbc475e34fab77_23.png)

```python
def prophet_signal(df_candles, back_candles, front_predictions, diff_limit=0.008, signal=False):
    df_local = df_candles.copy()
    model = Prophet()
    model.fit(df_local[['ds', 'y']].tail(back_candles))

    future = model.make_future_dataframe(periods=front_predictions, freq='H', include_history=False)
    forecast = model.predict(future)

    yhat = forecast['yhat'].iloc[0]
    yhat_lower = forecast['yhat_lower'].iloc[0]
    yhat_upper = forecast['yhat_upper'].iloc[0]

    if signal:
        # 分类逻辑将在下一节讨论
        pass
    else:
        return yhat, yhat_lower, yhat_upper
```

---

## 预测结果分析 📈

我们使用100根历史蜡烛图来预测下一根蜡烛的收盘价。调用函数后，我们得到了预测值及其置信区间。

例如，一次预测可能返回：
*   预测值 (`yhat`)：1.11300
*   预测下限 (`yhat_lower`)：1.11280
*   预测上限 (`yhat_upper`)：1.11315

为了评估效果，我们在1000根蜡烛图的切片上滚动运行此预测，并将预测值（向前移动一个单位以便对齐）与实际收盘价绘制在同一图表中进行比较。

**分析发现**：虽然在某些时段预测值与实际价格走势吻合，但也存在明显的分歧时刻。例如，模型预测价格上涨时，实际价格可能下跌。这种方向性的错误对于交易策略是致命的，因为基于错误预测的交易决策会导致亏损。

Prophet模型在具有**强烈季节性模式**的数据上表现最佳，例如零售销售额或网站流量。然而，外汇市场受多种复杂因素驱动，其季节性模式相对较弱且不稳定，这可能是导致预测不准的主要原因。

![](img/b8eb291618f57b782fcbc475e34fab77_25.png)

---

![](img/b8eb291618f57b782fcbc475e34fab77_27.png)

## 尝试分类预测策略 🎯

鉴于直接预测价格值（回归任务）效果不理想，我们尝试一个不同的思路：将预测问题转化为分类问题。我们不关心具体的预测价格，而是关心下一根蜡烛是上涨还是下跌。

我们在 `prophet_signal` 函数中增加了分类逻辑。当参数 `signal=True` 时，函数执行以下操作：

1.  获取当前蜡烛的收盘价（`current_close`）和模型对下一根蜡烛的预测价（`yhat`）。
2.  计算预测价与当前价的差值。
3.  根据阈值 `diff_limit`（例如0.008）判断趋势：
    *   如果 `yhat - current_close >= diff_limit`，则返回 `2`，代表**看涨信号**。
    *   如果 `yhat - current_close <= -diff_limit`，则返回 `1`，代表**看跌信号**。
    *   如果差值在 `[-diff_limit, diff_limit]` 区间内，则返回 `0`，代表**横盘或信号不明**。

以下是实现此逻辑的代码补充：

```python
def prophet_signal(df_candles, back_candles, front_predictions, diff_limit=0.008, signal=False):
    # ... (前面的拟合和预测代码不变)
    yhat = forecast['yhat'].iloc[0]

    if signal:
        current_close = df_local['y'].iloc[-1]
        diff = yhat - current_close
        if diff >= diff_limit:
            return 2  # 上涨信号
        elif diff <= -diff_limit:
            return 1  # 下跌信号
        else:
            return 0  # 无明确信号
    else:
        return yhat, yhat_lower, yhat_upper
```

![](img/b8eb291618f57b782fcbc475e34fab77_29.png)

**请注意**：这只是一个初步想法。通过调整 `diff_limit` 阈值，可以改变信号的敏感度。但基于之前回归分析的结果，这种分类方法的有效性也需要进一步回测验证。

![](img/b8eb291618f57b782fcbc475e34fab77_31.png)

---

## 其他工具与性能考量 ⚙️

Facebook还提供了另一个名为 **NeuralProphet** 的工具，它在后端使用神经网络算法。理论上，神经网络可能捕捉更复杂的非线性关系。

然而，**NeuralProphet** 存在一个显著缺点：模型拟合需要**大量时间**。对于历史数据的批量回测（例如1000次以上的拟合预测），其计算成本极高，在普通个人电脑上可能难以实现。因此，它更适用于实时或低频的预测场景，而不适合快速的策略研发和回测。

![](img/b8eb291618f57b782fcbc475e34fab77_33.png)

---

![](img/b8eb291618f57b782fcbc475e34fab77_35.png)

## 总结 🎓

本节课中我们一起学习了：
1.  **Prophet模型原理**：一个用于时间序列预测的开源库，擅长处理季节性数据和异常值。
2.  **数据预处理**：如何将外汇数据整理成Prophet所需的格式（`ds`和`y`列）。
3.  **预测实现**：编写了 `prophet_signal` 函数，能够进行滚动预测并输出连续值或分类信号。
4.  **结果评估**：发现Prophet直接应用于外汇价格预测时，效果有限，主要原因是金融市场缺乏Prophet所擅长的强季节性模式。
5.  **策略变体**：探索了将回归预测转化为分类问题的思路。
6.  **工具对比**：简要了解了NeuralProphet及其在回测中的性能限制。

![](img/b8eb291618f57b782fcbc475e34fab77_37.png)

总而言之，虽然Prophet是一个强大的时间序列预测工具，但其在外汇交易这类弱季节性、高噪声领域的直接应用需要谨慎。交易者可能需要结合其他技术指标或市场信息，并对模型进行更深入的定制和优化。鼓励大家下载代码进行实验，并分享你的想法和发现。