# Python金融分析与量化交易实战：P5：4-亚马逊股价趋势 📈

在本节课中，我们将学习如何评估和优化一个基础的股票价格预测模型。我们将以亚马逊（AMZN）的股价数据为例，分析模型预测的准确性，并探讨如何通过调整模型参数来提升预测效果。

---

上一节我们构建了一个基础的股价预测模型。本节中，我们来看看如何评估这个模型的性能，并思考如何让它变得更好。

![](img/143f27ac30ad13899dcddd3260bc0ec1_1.png)

首先，我们导入必要的库并加载亚马逊的股票数据。

![](img/143f27ac30ad13899dcddd3260bc0ec1_3.png)

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
from fbprophet import Prophet

![](img/143f27ac30ad13899dcddd3260bc0ec1_5.png)

![](img/143f27ac30ad13899dcddd3260bc0ec1_7.png)

# 加载亚马逊股票数据
df = pd.read_csv('AMZN.csv')
df['Date'] = pd.to_datetime(df['Date'])
df = df[['Date', 'Close']].rename(columns={'Date': 'ds', 'Close': 'y'})
```

![](img/143f27ac30ad13899dcddd3260bc0ec1_9.png)

亚马逊的股票代码是AMZN。其股价历史最低点出现在1997年5月22日。在我们执行分析的2023年1月23日，其收盘价为1362.54美元。下图展示了其股价的整体趋势。



![](img/143f27ac30ad13899dcddd3260bc0ec1_11.png)

![](img/143f27ac30ad13899dcddd3260bc0ec1_1.png)

观察其价格浮动情况，可以看到股价存在频繁的上升和下降波动。



![](img/143f27ac30ad13899dcddd3260bc0ec1_13.png)

![](img/143f27ac30ad13899dcddd3260bc0ec1_3.png)



![](img/143f27ac30ad13899dcddd3260bc0ec1_5.png)

![](img/143f27ac30ad13899dcddd3260bc0ec1_15.png)

但总体而言，股价保持着上升的趋势。



![](img/143f27ac30ad13899dcddd3260bc0ec1_17.png)

![](img/143f27ac30ad13899dcddd3260bc0ec1_7.png)

---

### 构建基础模型

我们使用Prophet库构建一个基础预测模型。黑色点代表真实股价，绿色点代表模型预测的股价。



![](img/143f27ac30ad13899dcddd3260bc0ec1_9.png)

观察发现，真实股价的突变点较多，而模型预测的曲线则更为平缓、均衡。这是我们期望的初步效果，因为一个过于拟合历史数据中所有突变点的模型可能泛化能力较差，无法准确预测未来趋势。我们更希望模型能把握住主要的平稳趋势。

![](img/143f27ac30ad13899dcddd3260bc0ec1_19.png)

![](img/143f27ac30ad13899dcddd3260bc0ec1_21.png)

接下来，我们使用这个基础模型预测未来90天的股价。这里我们暂时不调整任何模型参数，仅设置预测周期 `periods=90`。

![](img/143f27ac30ad13899dcddd3260bc0ec1_23.png)

```python
# 构建并拟合模型
model = Prophet()
model.fit(df)

![](img/143f27ac30ad13899dcddd3260bc0ec1_25.png)

![](img/143f27ac30ad13899dcddd3260bc0ec1_27.png)

# 创建未来日期数据框
future = model.make_future_dataframe(periods=90)
# 进行预测
forecast = model.predict(future)
```

![](img/143f27ac30ad13899dcddd3260bc0ec1_29.png)

![](img/143f27ac30ad13899dcddd3260bc0ec1_31.png)

预测结果显示，绿色预测线呈现平稳上升的趋势。



![](img/143f27ac30ad13899dcddd3260bc0ec1_13.png)

模型预测到2023年4月24日的股价约为1366美元。届时可以对比实际股价，验证预测的准确性。



![](img/143f27ac30ad13899dcddd3260bc0ec1_15.png)



![](img/143f27ac30ad13899dcddd3260bc0ec1_17.png)

这个基础模型显得比较保守。虽然亚马逊股价可能呈现更强劲的上升势头，但保守的模型有助于降低离群点和突变点对预测结果的过度影响。

![](img/143f27ac30ad13899dcddd3260bc0ec1_33.png)

---

![](img/143f27ac30ad13899dcddd3260bc0ec1_35.png)

### 模型评估方法

![](img/143f27ac30ad13899dcddd3260bc0ec1_37.png)

现在我们需要系统地评估模型性能。以下是两种主要的评估思路：

1.  **误差评估**：计算预测值与真实值之间的差异。我们可以将数据划分为训练集和测试集，分别计算两者上的平均绝对误差等指标。
2.  **趋势评估**：判断模型是否准确预测了股价的上升或下降趋势。即，当实际趋势为上升时，预测趋势是否也为上升；实际趋势下降时，预测是否也为下降。

为了进行量化评估，我们设定2017年之前的数据为训练集，2017年之后的数据为测试集。

```python
# 划分训练集和测试集
train = df[df['ds'] < '2017-01-01'] # 假设2017年前为训练集
test = df[df['ds'] >= '2017-01-01'] # 2017年及之后为测试集

# 在训练集上拟合模型
model = Prophet()
model.fit(train)

# 创建包含测试集日期的未来数据框
future = model.make_future_dataframe(periods=len(test))
forecast = model.predict(future)
```

以下是评估过程的核心代码逻辑：

```python
# 计算预测值与真实值的差异
train_error = np.mean(np.abs(train['y'] - forecast.loc[:len(train)-1, 'yhat']))
test_error = np.mean(np.abs(test['y'] - forecast.loc[len(train):len(train)+len(test)-1, 'yhat']))

# 计算趋势预测准确率
# 1. 计算每日变化（今日-昨日）
pred_diff = np.diff(forecast['yhat'].values)
actual_diff = np.diff(df['y'].values)
# 2. 将变化转为趋势信号：上升为1，下降为0
pred_trend = np.where(pred_diff > 0, 1, 0)
actual_trend = np.where(actual_diff > 0, 1, 0)
# 3. 计算趋势匹配的准确率（需对齐数据长度）
trend_accuracy = np.mean(pred_trend[:len(actual_trend)] == actual_trend)
```

![](img/143f27ac30ad13899dcddd3260bc0ec1_39.png)

在评估过程中，如果遇到不熟悉的函数（如 `np.sign`），可以查阅官方文档。`np.sign(x)` 函数的作用是：当 `x > 0` 时返回 `1`，`x < 0` 时返回 `-1`，`x == 0` 时返回 `0`。



![](img/143f27ac30ad13899dcddd3260bc0ec1_41.png)

![](img/143f27ac30ad13899dcddd3260bc0ec1_39.png)



![](img/143f27ac30ad13899dcddd3260bc0ec1_43.png)

![](img/143f27ac30ad13899dcddd3260bc0ec1_41.png)



![](img/143f27ac30ad13899dcddd3260bc0ec1_43.png)



![](img/143f27ac30ad13899dcddd3260bc0ec1_45.png)

![](img/143f27ac30ad13899dcddd3260bc0ec1_45.png)

运行评估代码后，我们得到了以下关键指标：

*   **训练集平均误差**：约18美元
*   **测试集平均误差**：约160美元
*   **上升趋势预测正确率**：约56%
*   **下降趋势预测正确率**：约44%

这些指标表明，当前模型在测试集上的误差远大于训练集，且趋势预测准确率接近随机猜测（50%），说明模型存在过拟合，泛化能力不足。



![](img/143f27ac30ad13899dcddd3260bc0ec1_47.png)

下图直观展示了预测结果（深蓝色线）与真实股价（黑色线）的对比。红色竖线标识了预测开始的时点。



![](img/143f27ac30ad13899dcddd3260bc0ec1_47.png)

![](img/143f27ac30ad13899dcddd3260bc0ec1_49.png)



![](img/143f27ac30ad13899dcddd3260bc0ec1_49.png)

![](img/143f27ac30ad13899dcddd3260bc0ec1_51.png)

可以明显看到，模型的预测曲线过于平缓，未能有效捕捉真实股价的波动和上升趋势，预测值与真实值相差数百美元。



![](img/143f27ac30ad13899dcddd3260bc0ec1_51.png)

![](img/143f27ac30ad13899dcddd3260bc0ec1_53.png)



![](img/143f27ac30ad13899dcddd3260bc0ec1_53.png)

![](img/143f27ac30ad13899dcddd3260bc0ec1_55.png)



![](img/143f27ac30ad13899dcddd3260bc0ec1_57.png)

---

### 总结

![](img/143f27ac30ad13899dcddd3260bc0ec1_55.png)

![](img/143f27ac30ad13899dcddd3260bc0ec1_57.png)

本节课中我们一起学习了如何评估一个时间序列预测模型的性能。我们以亚马逊股价预测为例，计算了模型在训练集和测试集上的误差，并分析了其预测股价上升/下降趋势的准确率。评估结果显示，我们的基础模型预测效果不理想，存在过拟合且无法捕捉股价的真实波动。这引出了我们下一节的核心任务：**如何通过调整模型参数来优化预测效果，使其能更准确地反映股价的变化趋势。**