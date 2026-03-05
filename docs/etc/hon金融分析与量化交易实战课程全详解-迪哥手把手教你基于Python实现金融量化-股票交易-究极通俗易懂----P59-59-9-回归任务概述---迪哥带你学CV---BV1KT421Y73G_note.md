# Python金融分析与量化交易实战课程：P59：回归任务概述 📈

在本节课中，我们将学习如何构建一个基于历史数据的回归策略，用于预测金融指标（如汇率或股价）的未来走势。我们将从理解回归策略的概念开始，逐步完成数据预处理、特征构建、模型训练与评估的完整流程。

---

## 什么是回归策略？🤔

上一节我们介绍了分类任务，本节中我们来看看回归策略。回归策略的核心目标是预测一个连续的未来值。例如，在股票交易中，我们不仅想知道明天是涨还是跌，更想知道具体会涨多少或跌多少。

所谓的回归策略，就是利用我们手中已有的历史数据来预测未来的指标。例如，要预测明天的指标，我们可以参考今天、前一天甚至更早的历史数据。这里提到的“前一天”或“前两天”并非固定选择，我们可以利用更多的历史数据特征，如前三、前四、前五天的数据。通常，利用的特征越多，预测效果可能越好。我们要做的就是在历史数据中分析过去的走势，以预测其未来的走势。

---

## 数据准备与预处理 📊

![](img/81a65b48fd6b5e1e734fe7ed5f4ed0a8_1.png)

首先，我们需要导入必要的工具包并加载数据。这一步与之前课程的操作一致。

![](img/81a65b48fd6b5e1e734fe7ed5f4ed0a8_3.png)

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
```

本次我们使用欧元兑美元（EUR/USD）的汇率历史数据文件 `data.csv`。汇率数据是连续的数值型数据，非常适合用于回归分析。

![](img/81a65b48fd6b5e1e734fe7ed5f4ed0a8_5.png)

```python
# 读取数据
data = pd.read_csv('data.csv')
# 指定分析EUR/USD汇率列
data = data[['EUR/USD']]
```

![](img/81a65b48fd6b5e1e734fe7ed5f4ed0a8_7.png)

接下来对数据进行基本的预处理操作。为了使结果具有累加性，我们通常对价格数据取对数来计算回报率。

```python
# 计算对数回报率
data['returns'] = np.log(data['EUR/USD'] / data['EUR/USD'].shift(1))
# 处理缺失值
data.dropna(inplace=True)
```

![](img/81a65b48fd6b5e1e734fe7ed5f4ed0a8_9.png)

除了具体的回报率数值，在交易中我们有时更关心价格变动的方向（涨或跌）。因此，我们在数据中新增一个“方向”列。

```python
# 创建方向列：上涨为1，下跌为-1
data['direction'] = np.sign(data['returns']).astype(int)
```

我们可以绘制回报率的直方图来观察其分布。

![](img/81a65b48fd6b5e1e734fe7ed5f4ed0a8_11.png)

```python
data['returns'].hist(bins=50)
plt.show()
```

观察直方图可以发现，回报率大致以0为均值，在左右两侧波动。这是金融时间序列数据的典型特征。

---

## 构建回归任务 🎯

本节我们将具体构建回归预测任务。回归是机器学习中的有监督任务，意味着我们需要定义特征（X）和目标变量（Y）。

![](img/81a65b48fd6b5e1e734fe7ed5f4ed0a8_13.png)

对于我们的任务，目标变量Y有两种常见的定义方式：
1.  **预测具体的回报率数值**：将 `returns` 列作为Y。这是一个标准的回归问题。
2.  **预测价格变动的方向**：将 `direction` 列作为Y。这可以看作一个回归问题（预测+1或-1），也可以转化为分类问题。

![](img/81a65b48fd6b5e1e734fe7ed5f4ed0a8_15.png)

我们将进行两组对比实验，分别使用 `Y1`（回报率）和 `Y2`（方向）作为目标。

```python
# 定义两种目标变量
Y1 = data['returns'].values
Y2 = data['direction'].values
```

![](img/81a65b48fd6b5e1e734fe7ed5f4ed0a8_17.png)

特征X的构建是关键。我们不能使用未来的数据预测过去，因此需要用历史窗口的数据来预测下一个时间点。以下是构建特征的一种方法：

![](img/81a65b48fd6b5e1e734fe7ed5f4ed0a8_19.png)

```python
# 假设我们使用过去5天的数据作为特征
lookback_period = 5
features = []
for i in range(len(data) - lookback_period):
    # 获取过去5天的回报率作为特征
    features.append(data['returns'].iloc[i:i+lookback_period].values)
# 将特征列表转换为数组
X = np.array(features)
# 目标变量Y需要与特征X对齐（从第5天之后开始）
Y1_for_model = Y1[lookback_period:]
Y2_for_model = Y2[lookback_period:]
```

---

![](img/81a65b48fd6b5e1e734fe7ed5f4ed0a8_21.png)

## 模型训练与评估 ⚙️

![](img/81a65b48fd6b5e1e734fe7ed5f4ed0a8_23.png)

特征和目标准备就绪后，我们就可以选择回归模型进行训练和评估了。以下是一个使用线性回归模型的示例流程。

```python
from sklearn.linear_model import LinearRegression
from sklearn.model_selection import train_test_split
from sklearn.metrics import mean_squared_error, accuracy_score

# 实验一：预测回报率数值 (Y1)
X_train, X_test, y_train, y_test = train_test_split(X, Y1_for_model, test_size=0.2, shuffle=False)
model_reg = LinearRegression()
model_reg.fit(X_train, y_train)
predictions_reg = model_reg.predict(X_test)
mse = mean_squared_error(y_test, predictions_reg)
print(f"回报率预测的均方误差(MSE)为: {mse}")

![](img/81a65b48fd6b5e1e734fe7ed5f4ed0a8_25.png)

# 实验二：预测变动方向 (Y2)
# 注意：虽然用回归模型预测方向，但评估时我们看分类准确率
X_train2, X_test2, y_train2, y_test2 = train_test_split(X, Y2_for_model, test_size=0.2, shuffle=False)
model_dir = LinearRegression()
model_dir.fit(X_train2, y_train2)
predictions_dir_raw = model_dir.predict(X_test2)
# 将回归预测值转换为方向（>0为1，<=0为-1）
predictions_dir = np.where(predictions_dir_raw > 0, 1, -1)
accuracy = accuracy_score(y_test2, predictions_dir)
print(f"方向预测的准确率为: {accuracy}")
```

---

## 总结 📝

本节课中我们一起学习了金融量化中的回归策略分析。我们从回归策略的概念入手，理解了其目标是利用历史数据预测未来连续的金融指标。接着，我们完成了数据加载、预处理（计算回报率、生成方向标签）以及特征工程的构建。最后，我们通过构建两种不同的目标变量（具体回报率和变动方向），演示了如何使用线性回归模型进行训练和初步评估。

![](img/81a65b48fd6b5e1e734fe7ed5f4ed0a8_27.png)

通过本课的学习，你已经掌握了构建一个基础回归预测模型的核心步骤。在实际应用中，可以尝试更复杂的模型（如决策树、神经网络）、更丰富的特征（如技术指标）以及更严谨的回测方法来优化策略效果。