# Python金融时间序列分析与量化交易实战教程：P62：61.构建回归方程 🧮

在本节课中，我们将学习如何利用线性回归模型构建一个预测方程，用于判断股票每日的涨跌走势。我们将使用Python的`scikit-learn`库，通过简单的步骤完成模型的训练与预测，并对比不同预测目标的效果。

## 导入必要的工具包

首先，我们需要借助一个强大的Python机器学习库——`scikit-learn`。这个库包含了几乎所有常规的机器学习算法。本节课我们将使用其中的线性回归模型。

```python
from sklearn.linear_model import LinearRegression
```

## 实例化模型

在`scikit-learn`中，使用任何模型的第一步都是对其进行实例化。我们将创建一个线性回归模型的实例。

```python
model = LinearRegression()
```

## 准备数据并训练模型

上一节我们导入了模型，本节中我们来看看如何使用数据来训练它。训练模型需要使用`.fit()`方法，该方法需要传入两个参数：特征数据`X`和预测目标`Y`。

以下是准备数据的步骤：
1.  首先，我们需要指定作为特征`X`的列。这些列名之前定义在一个函数内，为了能在函数外部调用，我们将其声明为全局变量。
2.  接着，我们定义预测目标`Y`。为了进行对比实验，我们将尝试两种不同的`Y`：
    *   `returns`：代表股票的实际日收益率。
    *   `direction`：代表股票是上涨（1）还是下跌（-1）的标签。

```python
# 假设 data 是包含特征和标签的DataFrame
# features 是一个包含特征列名的列表
X = data[features]

# 实验一：以实际收益率作为预测目标
Y_returns = data[‘returns‘]
model.fit(X, Y_returns)

# 实验二：以涨跌方向作为预测目标
Y_direction = data[‘direction‘]
model2 = LinearRegression()
model2.fit(X, Y_direction)
```

## 进行预测并评估结果

模型训练完成后，我们可以用它来对数据进行预测。预测结果是一个连续的数值，但为了制定交易策略，我们更关心它的正负方向（代表涨跌）。

以下是处理预测结果的步骤：
1.  使用训练好的模型对特征`X`进行预测，得到预测值。
2.  将连续的预测值转换为代表买卖操作的标志位（例如，大于0为买入/持有，小于0为卖出/做空）。

```python
# 使用模型进行预测
pred_returns = model.predict(X)
pred_direction = model2.predict(X)

# 将预测值转换为交易信号（1代表看多，-1代表看空）
signal_returns = (pred_returns > 0).astype(int) * 2 - 1  # 将True/False映射为1/-1
signal_direction = (pred_direction > 0).astype(int) * 2 - 1
```

## 基于策略计算模拟收益

最后，我们基于生成的交易信号来模拟策略的收益情况。这可以通过将交易信号与实际的日收益率相乘来实现。

以下是计算策略收益的方法：
1.  策略一的收益 = 基于`returns`预测的信号 * 实际`returns`。
2.  策略二的收益 = 基于`direction`预测的信号 * 实际`returns`。

```python
# 计算策略收益
data[‘strategy_returns_1‘] = signal_returns * data[‘returns‘]
data[‘strategy_returns_2‘] = signal_direction * data[‘returns‘]

![](img/97a263a0245fa6444966674dd28a9970_1.png)

# 查看结果
print(data[[‘returns‘, ‘strategy_returns_1‘, ‘strategy_returns_2‘]].head())
```

![](img/97a263a0245fa6444966674dd28a9970_3.png)

通过对比`strategy_returns`与原始`returns`的累计收益或其他指标，我们可以评估哪个预测目标构建的回归方程更有效。需要注意的是，线性回归是一个相对简单的模型，预测结果可能并不完美，但这为我们提供了一个量化策略开发的基础框架。

![](img/97a263a0245fa6444966674dd28a9970_5.png)

本节课中我们一起学习了如何使用`scikit-learn`构建线性回归方程，并将其应用于金融时间序列数据以生成交易信号。我们掌握了从导入模型、训练、预测到最终形成策略的完整流程，并了解了通过对比实验来优化模型选择的基本思路。