# Python金融分析量化交易：P5：4-亚马逊股价趋势 📈

在本节课中，我们将学习如何评估和改进一个基础的股票价格预测模型。我们将以亚马逊（AMZN）的股价数据为例，分析模型预测的准确性，并探讨如何通过调整参数来提升模型性能。

## 导入库与数据准备

上一节我们构建了一个基础的预测模型。本节中，我们来看看如何评估和改进它。首先，我们需要导入必要的库并加载新的股票数据。

![](img/109eb14f4273383d7afebeddaaf7864f_1.png)

![](img/109eb14f4273383d7afebeddaaf7864f_3.png)

以下是导入所需库的代码：

![](img/109eb14f4273383d7afebeddaaf7864f_5.png)

![](img/109eb14f4273383d7afebeddaaf7864f_7.png)

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
from fbprophet import Prophet
```

![](img/109eb14f4273383d7afebeddaaf7864f_9.png)

我们更换股票数据，使用亚马逊（股票代码：AMZN）的股价进行分析。亚马逊作为一家全球领先的科技公司，其股价具有分析价值。

![](img/109eb14f4273383d7afebeddaaf7864f_11.png)

## 数据初步分析

加载数据后，我们首先观察其整体趋势和价格波动情况。

![](img/109eb14f4273383d7afebeddaaf7864f_13.png)

以下是亚马逊股价的整体趋势图：

![](img/109eb14f4273383d7afebeddaaf7864f_1.png)

![](img/109eb14f4273383d7afebeddaaf7864f_15.png)

股价整体呈现上升趋势。接着，我们观察其每日价格浮动情况。

![](img/109eb14f4273383d7afebeddaaf7864f_17.png)

以下是股价的浮动情况图：

![](img/109eb14f4273383d7afebeddaaf7864f_3.png)

![](img/109eb14f4273383d7afebeddaaf7864f_5.png)

![](img/109eb14f4273383d7afebeddaaf7864f_7.png)

图表显示，股价虽有短期波动，但长期趋势是上升的。

![](img/109eb14f4273383d7afebeddaaf7864f_19.png)

![](img/109eb14f4273383d7afebeddaaf7864f_21.png)

## 构建基础预测模型

![](img/109eb14f4273383d7afebeddaaf7864f_23.png)

![](img/109eb14f4273383d7afebeddaaf7864f_25.png)

基于历史数据，我们构建一个基础的Prophet模型来预测未来股价。

![](img/109eb14f4273383d7afebeddaaf7864f_27.png)

![](img/109eb14f4273383d7afebeddaaf7864f_29.png)

以下是基础模型的预测结果图：

![](img/109eb14f4273383d7afebeddaaf7864f_31.png)

![](img/109eb14f4273383d7afebeddaaf7864f_9.png)

图中黑色点代表真实股价，绿色点代表模型预测值。可以观察到，真实数据中存在更多突变点，而模型的预测曲线则更为平滑。这在一定程度上是合理的，因为过于拟合突变点可能导致模型过拟合，降低其泛化能力。我们更希望模型能捕捉主要趋势，保持一定的平稳性。

## 预测未来股价

接下来，我们使用模型预测未来90天的股价。在初始化模型时，我们仅指定预测周期 `periods=90`，不调整其他参数。

以下是未来90天的预测结果图：

![](img/109eb14f4273383d7afebeddaaf7864f_33.png)

![](img/109eb14f4273383d7afebeddaaf7864f_13.png)

![](img/109eb14f4273383d7afebeddaaf7864f_35.png)

![](img/109eb14f4273383d7afebeddaaf7864f_15.png)

![](img/109eb14f4273383d7afebeddaaf7864f_37.png)

绿色曲线为预测结果，显示股价将平稳上升。模型预测到特定日期（例如4月24日）的收盘价约为1366。请注意，实际股价会与此预测值存在偏差。

## 模型评估方法

预测完成后，我们需要评估模型的性能。评估主要从两个方面进行。

以下是评估模型时需要考虑的两个核心方面：

1.  **误差评估**：计算预测值与真实值之间的差异。通常将数据分为训练集和测试集，分别计算平均绝对误差等指标。公式可以表示为：
    `误差 = |真实值 - 预测值|`
2.  **趋势预测准确性**：评估模型是否能正确预测股价的上升或下降趋势。即，当真实趋势上升时，模型预测是否也为上升；当真实趋势下降时，模型预测是否也为下降。

## 代码实现与评估分析

现在，我们通过代码具体实现上述评估过程。我们将2014年至2017年的数据作为训练集，2017年至2018年的数据作为测试集。

以下是评估过程的关键步骤代码片段：

![](img/109eb14f4273383d7afebeddaaf7864f_39.png)

```python
# 划分训练集和测试集
train = data[‘2014-01-01‘:‘2017-01-23‘]
test = data[‘2017-01-24‘:‘2018-01-23‘]

![](img/109eb14f4273383d7afebeddaaf7864f_41.png)

![](img/109eb14f4273383d7afebeddaaf7864f_43.png)

# 创建并拟合模型
model = Prophet()
model.fit(train)

# 预测未来365天（即测试集范围）
future = model.make_future_dataframe(periods=365)
forecast = model.predict(future)

# 提取预测结果和真实值
predicted = forecast[‘yhat‘]
actual = test[‘y‘]
```

![](img/109eb14f4273383d7afebeddaaf7864f_45.png)

在评估趋势准确性时，我们使用 `np.sign` 函数来判断每日股价变化是正（上升）还是负（下降），然后比较预测趋势与真实趋势是否一致。

以下是趋势判断的代码逻辑：

```python
# 计算每日变化的方向（上升为1，下降为-1）
predicted_trend = np.sign(np.diff(predicted))
actual_trend = np.sign(np.diff(actual))

# 计算趋势预测正确的比例
trend_accuracy = np.mean(predicted_trend == actual_trend)
```

运行评估代码后，我们得到以下关键指标：

![](img/109eb14f4273383d7afebeddaaf7864f_47.png)

*   训练集平均误差：约18
*   测试集平均误差：约160
*   上升趋势预测正确率：56%
*   下降趋势预测正确率：43.81%

![](img/109eb14f4273383d7afebeddaaf7864f_49.png)

以下是模型预测结果与真实值的对比图：

![](img/109eb14f4273383d7afebeddaaf7864f_51.png)

![](img/109eb14f4273383d7afebeddaaf7864f_51.png)

![](img/109eb14f4273383d7afebeddaaf7864f_53.png)

![](img/109eb14f4273383d7afebeddaaf7864f_53.png)

图中，深蓝色线为预测值，黑色线为真实值。红色线标记了预测开始的时点。可以看出，当前模型的预测值过于平缓，未能充分捕捉真实股价的波动和上升幅度，导致预测误差较大。

## 总结与下节预告

本节课中，我们一起学习了如何评估一个股票价格预测模型。我们以亚马逊股价为例，计算了预测误差和趋势准确性，发现基础模型的表现有较大提升空间。模型的预测过于保守和平滑，未能有效反映真实的股价波动。

![](img/109eb14f4273383d7afebeddaaf7864f_55.png)

![](img/109eb14f4273383d7afebeddaaf7864f_57.png)

那么，如何改进模型呢？下一节，我们将聚焦于**模型调参**，学习如何调整Prophet模型的各项参数，使其能更好地捕捉数据中的趋势、季节性和突变点，从而提升预测的准确性。这将是我们优化模型性能的关键一步。