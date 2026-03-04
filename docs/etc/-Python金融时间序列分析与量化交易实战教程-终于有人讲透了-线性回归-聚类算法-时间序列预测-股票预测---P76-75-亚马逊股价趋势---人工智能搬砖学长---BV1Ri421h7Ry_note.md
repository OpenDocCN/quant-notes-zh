# Python金融时间序列分析与量化交易实战教程：P76：75.亚马逊股价趋势 📈

![](img/9a2c27b3ecf3ae3f099b8c6d1982c2da_1.png)

![](img/9a2c27b3ecf3ae3f099b8c6d1982c2da_3.png)

在本节课中，我们将学习如何对亚马逊（AMZN）的股价数据应用时间序列模型进行预测，并初步评估模型的性能。我们将使用Prophet模型，并分析其预测结果与实际股价之间的差异。

![](img/9a2c27b3ecf3ae3f099b8c6d1982c2da_5.png)

![](img/9a2c27b3ecf3ae3f099b8c6d1982c2da_7.png)

![](img/9a2c27b3ecf3ae3f099b8c6d1982c2da_9.png)

![](img/9a2c27b3ecf3ae3f099b8c6d1982c2da_11.png)

## 模型构建与数据准备

![](img/9a2c27b3ecf3ae3f099b8c6d1982c2da_13.png)

![](img/9a2c27b3ecf3ae3f099b8c6d1982c2da_15.png)

上一节我们介绍了基础模型，本节中我们来看看如何将其应用于亚马逊的股票数据。

![](img/9a2c27b3ecf3ae3f099b8c6d1982c2da_17.png)

首先，我们导入必要的库并加载亚马逊的股票数据。股票代码为 `AMZN`。

![](img/9a2c27b3ecf3ae3f099b8c6d1982c2da_19.png)

![](img/9a2c27b3ecf3ae3f099b8c6d1982c2da_21.png)

![](img/9a2c27b3ecf3ae3f099b8c6d1982c2da_23.png)

```python
import pandas as pd
from prophet import Prophet
import matplotlib.pyplot as plt
```

![](img/9a2c27b3ecf3ae3f099b8c6d1982c2da_25.png)

![](img/9a2c27b3ecf3ae3f099b8c6d1982c2da_27.png)

亚马逊的股价历史数据跨度较长，从1997年至今，整体呈现上升趋势。我们关注其价格波动情况，为后续建模做准备。

![](img/9a2c27b3ecf3ae3f099b8c6d1982c2da_29.png)

![](img/9a2c27b3ecf3ae3f099b8c6d1982c2da_31.png)

## 基础模型预测

![](img/9a2c27b3ecf3ae3f099b8c6d1982c2da_33.png)

![](img/9a2c27b3ecf3ae3f099b8c6d1982c2da_35.png)

![](img/9a2c27b3ecf3ae3f099b8c6d1982c2da_37.png)

以下是构建基础Prophet模型并进行预测的步骤。

![](img/9a2c27b3ecf3ae3f099b8c6d1982c2da_39.png)

![](img/9a2c27b3ecf3ae3f099b8c6d1982c2da_41.png)

![](img/9a2c27b3ecf3ae3f099b8c6d1982c2da_43.png)

1.  初始化一个Prophet模型对象。
2.  将历史股价数据（包含日期`ds`和收盘价`y`两列）传入模型进行拟合。
3.  创建一个包含未来90天日期的数据框。
4.  使用拟合好的模型对这个未来数据框进行预测。

![](img/9a2c27b3ecf3ae3f099b8c6d1982c2da_45.png)

![](img/9a2c27b3ecf3ae3f099b8c6d1982c2da_47.png)

```python
# 假设 df 是包含‘ds’（日期）和‘y’（收盘价）的DataFrame
model = Prophet()
model.fit(df)

future = model.make_future_dataframe(periods=90)
forecast = model.predict(future)
```

![](img/9a2c27b3ecf3ae3f099b8c6d1982c2da_49.png)

![](img/9a2c27b3ecf3ae3f099b8c6d1982c2da_51.png)

模型预测结果显示，未来股价呈现平缓上升的趋势。然而，对比历史数据中实际的剧烈波动，基础模型的预测显得过于“平滑”和“保守”。这种特性虽然有助于避免过拟合，但可能无法准确捕捉股价的快速变化。

![](img/9a2c27b3ecf3ae3f099b8c6d1982c2da_53.png)

## 模型性能评估

为了量化模型的预测能力，我们需要对其进行评估。评估主要关注两个方面：预测值的误差以及预测趋势方向的准确性。

我们将数据集划分为训练集和测试集。例如，使用2014年至2017年的数据作为训练集，2017年至2018年的数据作为测试集。

![](img/9a2c27b3ecf3ae3f099b8c6d1982c2da_55.png)

以下是评估模型的具体计算步骤：

![](img/9a2c27b3ecf3ae3f099b8c6d1982c2da_57.png)

![](img/9a2c27b3ecf3ae3f099b8c6d1982c2da_59.png)

1.  **计算预测误差**：计算预测值与真实值之间的平均绝对误差（MAE）。分别计算训练集和测试集的误差，以判断模型是否存在过拟合或欠拟合。
    *   训练集误差（`train_error`）
    *   测试集误差（`test_error`）

![](img/9a2c27b3ecf3ae3f099b8c6d1982c2da_61.png)

2.  **评估趋势预测准确性**：判断模型是否准确预测了股价的上涨或下跌趋势。
    *   计算每日股价相对于前一日的变化（差值）。
    *   使用符号函数（`np.sign`）将变化转为趋势：上涨为1，下跌为-1。
    *   比较预测趋势与实际趋势是否一致，并计算正确预测的百分比。
        *   上升趋势预测正确率
        *   下降趋势预测正确率

对亚马逊股价基础模型的评估结果如下：
*   训练集平均误差约为18美元。
*   测试集平均误差约为160美元，显著增大。
*   上升趋势预测正确率约为56%。
*   下降趋势预测正确率约为44%。

这些指标表明，当前的基础模型在测试集上表现不佳，预测误差较大，且对涨跌趋势的预测准确率接近随机猜测（50%）。可视化结果也清晰显示，预测曲线（蓝色）过于平缓，无法跟上真实股价（黑色）的波动。

![](img/9a2c27b3ecf3ae3f099b8c6d1982c2da_63.png)

![](img/9a2c27b3ecf3ae3f099b8c6d1982c2da_65.png)

## 总结与展望

![](img/9a2c27b3ecf3ae3f099b8c6d1982c2da_67.png)

本节课中我们一起学习了如何对亚马逊股价建立Prophet预测模型，并完成了初步的预测与评估。我们发现，使用默认参数的基础模型虽然运行简单，但其预测结果过于平滑，在测试集上误差较大，对股价波动趋势的捕捉能力有限。

![](img/9a2c27b3ecf3ae3f099b8c6d1982c2da_69.png)

![](img/9a2c27b3ecf3ae3f099b8c6d1982c2da_71.png)

核心问题在于模型参数未经过优化，无法适应股票数据的高波动性特征。在下一节中，我们将探讨如何调整Prophet模型的参数，例如增长趋势、季节性和节假日效应等，以期提升模型对股价序列的拟合与预测精度。