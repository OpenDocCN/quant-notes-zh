# 时间序列预测：4：亚马逊股价趋势预测与模型评估 📈

在本节课中，我们将学习如何对一个基本的股价预测模型进行评估，并分析其预测效果。我们将以亚马逊（AMZN）的股票数据为例，通过计算误差和趋势预测准确率来量化模型的性能，并观察其预测结果与实际走势的差异。

![](img/c6c1bc395ed13c3dd858473ce8bd9c7a_1.png)

![](img/c6c1bc395ed13c3dd858473ce8bd9c7a_3.png)

## 数据准备与模型构建

上一节我们介绍了基础模型的构建方法，本节中我们来看看如何将其应用于新的数据。首先，我们更换股票数据，使用亚马逊（AMZN）的股价进行分析。数据时间跨度从1997年5月22日的最低点，到我们执行代码时的近期高点。

以下是构建基础模型的核心步骤代码：

```python
# 导入必要的库（假设已安装）
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
from prophet import Prophet

# 1. 加载并准备亚马逊股票数据
# df 应包含两列：'ds' (日期) 和 'y' (股价，如收盘价)
# 此处为示例，实际数据需从文件或API获取
# df = pd.read_csv('amzn_stock.csv')

# 2. 初始化并拟合Prophet模型
model = Prophet()
model.fit(df)

![](img/c6c1bc395ed13c3dd858473ce8bd9c7a_5.png)

![](img/c6c1bc395ed13c3dd858473ce8bd9c7a_7.png)

![](img/c6c1bc395ed13c3dd858473ce8bd9c7a_9.png)

# 3. 构建未来时间框架并进行预测
future = model.make_future_dataframe(periods=90) # 预测未来90天
forecast = model.predict(future)
```

![](img/c6c1bc395ed13c3dd858473ce8bd9c7a_11.png)

![](img/c6c1bc395ed13c3dd858473ce8bd9c7a_13.png)

![](img/c6c1bc395ed13c3dd858473ce8bd9c7a_15.png)

模型拟合后，我们可以得到预测结果。基础的预测模型倾向于产生更平滑、更保守的趋势线，这有助于避免对历史数据中的噪声和突变点过度拟合。

## 模型评估方法

仅仅得到预测结果是不够的，我们需要一套方法来评估模型的优劣。评估主要围绕两个核心方面进行：预测数值的误差以及预测趋势方向的准确性。

![](img/c6c1bc395ed13c3dd858473ce8bd9c7a_17.png)

![](img/c6c1bc395ed13c3dd858473ce8bd9c7a_19.png)

以下是评估模型时需要考虑的两个主要方面：

![](img/c6c1bc395ed13c3dd858473ce8bd9c7a_21.png)

1.  **预测误差**：计算预测值与真实值之间的差异。通常，我们会将数据集划分为训练集和测试集。在训练集上评估模型的学习能力，在测试集（模型未见过的数据）上评估其泛化能力。误差可以用平均绝对误差等公式表示：
    `error = |y_true - y_pred|`
2.  **趋势预测准确率**：股票预测中，判断价格是“上涨”还是“下跌”往往比精确的数值更重要。因此，我们需要统计模型正确预测趋势方向（上升或下降）的比例。

## 实施评估与结果分析

现在，我们将上述评估方法付诸实践。我们以2017年1月24日作为分界点，将此日期之前三年的数据作为训练集，之后一年的数据作为测试集。

以下是实施评估的关键步骤代码片段：

```python
# 划分训练集和测试集
train = df[df['ds'] < '2017-01-24']
test = df[df['ds'] >= '2017-01-24']

![](img/c6c1bc395ed13c3dd858473ce8bd9c7a_23.png)

![](img/c6c1bc395ed13c3dd858473ce8bd9c7a_25.png)

![](img/c6c1bc395ed13c3dd858473ce8bd9c7a_27.png)

# 在训练集上拟合模型
model = Prophet()
model.fit(train)

# 为测试集日期构建未来数据框并预测
future_test = model.make_future_dataframe(periods=len(test))
forecast_test = model.predict(future_test)
# 从预测结果中提取测试集对应的部分
predicted_test = forecast_test[forecast_test['ds'].isin(test['ds'])]['yhat'].values
true_test = test['y'].values

![](img/c6c1bc395ed13c3dd858473ce8bd9c7a_29.png)

# 计算测试集平均绝对误差
test_error = np.mean(np.abs(true_test - predicted_test))
print(f"测试集平均绝对误差: {test_error}")

# 计算趋势预测准确率
def calculate_trend_accuracy(true_series, pred_series):
    # 计算每日变化的方向（1为上涨，0为下跌）
    true_trend = np.sign(np.diff(true_series)) # np.sign: 正数为1，负数为-1，零为0
    pred_trend = np.sign(np.diff(pred_series))
    # 只比较有变化的日子（忽略变化为0的情况）
    mask = true_trend != 0
    accuracy = np.mean((true_trend[mask] == pred_trend[mask]).astype(float))
    return accuracy

trend_accuracy = calculate_trend_accuracy(true_test, predicted_test)
print(f"趋势方向预测准确率: {trend_accuracy:.2%}")
```

![](img/c6c1bc395ed13c3dd858473ce8bd9c7a_31.png)

运行评估代码后，我们得到了量化的结果。例如，测试集的平均绝对误差可能达到160美元以上，而趋势预测的准确率可能在50%左右徘徊，这接近随机猜测的水平。可视化图表也清晰地显示，模型的预测线（如蓝色线）较为平缓，而真实的股价走势（黑色线）波动更大且整体位置更高，两者存在显著差距。

![](img/c6c1bc395ed13c3dd858473ce8bd9c7a_33.png)

## 总结与后续方向

本节课中我们一起学习了如何对一个时间序列预测模型进行基本评估。我们以亚马逊股价为例，实施了从数据划分、模型拟合到计算预测误差和趋势准确率的完整流程。评估结果表明，我们的基础模型虽然能捕捉大致趋势，但在数值精确度和波动性捕捉上表现不佳，预测结果过于保守和平滑。

![](img/c6c1bc395ed13c3dd858473ce8bd9c7a_35.png)

![](img/c6c1bc395ed13c3dd858473ce8bd9c7a_37.png)

这个结果明确指出了模型的不足，也为我们指明了下一步的方向：**模型调参与优化**。我们需要调整模型的参数，使其能够更好地捕捉数据中的变化模式和季节性，从而提升预测的准确性和对趋势转折点的判断能力。这正是我们下一节课将要深入探讨的内容。