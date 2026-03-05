# Python金融分析与量化交易实战教程：P50：11-11-构建回归方程 📈

在本节课中，我们将学习如何构建一个回归方程模型，用于预测股票每日的涨跌走势。我们将使用Python的`scikit-learn`库来实现线性回归，并基于预测结果制定简单的交易策略。

---

## 数据准备与模型引入

上一节我们完成了数据的预处理。本节中，我们将利用这些数据来构建预测模型。

首先，我们需要引入必要的工具包。这里选择`scikit-learn`，它是Python中一个非常常用的机器学习库，包含了各种常规的机器学习算法。

我们将使用其中的线性回归模型。

```python
from sklearn.linear_model import LinearRegression
```

导入了线性回归包之后，下一步是创建模型实例。在`scikit-learn`中，使用任何模型的第一步都是对其进行实例化。

```python
model = LinearRegression()
```

---

## 训练回归模型

模型实例化后，我们需要用数据来训练它。训练过程在`scikit-learn`中通过`.fit()`方法完成。

以下是`.fit()`方法的关键参数：
*   **X**：特征数据，即用于预测的自变量。
*   **Y**：标签数据，即我们想要预测的目标变量。

在本例中，我们将进行一个对比实验，使用两个不同的目标变量`Y`来训练模型，以观察哪种预测效果更好。

```python
# 假设 ‘feature_columns‘ 是之前定义好的特征列名列表
# 假设 ‘data‘ 是我们的主数据DataFrame

# 第一个模型，使用实际收益率 ‘returns‘ 作为目标变量
model1 = LinearRegression()
model1.fit(data[feature_columns], data[‘returns‘])

# 第二个模型，使用涨跌方向 ‘direction‘ 作为目标变量
model2 = LinearRegression()
model2.fit(data[feature_columns], data[‘direction‘])
```

---

## 进行预测并解读结果

模型训练完成后，就可以用它来对数据进行预测了。我们使用`.predict()`方法。

```python
# 使用两个模型分别进行预测
data[‘pred_returns‘] = model1.predict(data[feature_columns])
data[‘pred_direction‘] = model2.predict(data[feature_columns])
```

查看预测结果，可能会发现预测值并不完美，例如实际值为正时预测值为负。这是因为线性回归模型无法100%捕捉复杂的市场波动，我们的例子也相对简单。

预测值本身的具体数值（如-0.02或0.03）对于交易决策可能不是最直接的。我们更关心的是它的**方向**，即预示着上涨（正）还是下跌（负）。

因此，我们需要将连续的预测值转换为代表买卖信号的标志位。

以下是处理步骤：
1.  判断预测值是否大于0。
2.  大于0则标记为1（代表“买入/看涨”），否则标记为-1（代表“卖出/看跌”）。

```python
# 将回归预测值转换为交易信号（1 或 -1）
data[‘signal_returns‘] = (data[‘pred_returns‘] > 0).astype(int) * 2 - 1  # 将True/False映射为1/-1
data[‘signal_direction‘] = (data[‘pred_direction‘] > 0).astype(int) * 2 - 1
```

---

## 基于策略回测

得到了交易信号后，我们就可以模拟一个简单的策略：在信号为1时，按照当日实际收益率交易；在信号为-1时，进行反向操作（即做空，收益率为负）。

这个策略的收益可以简单地用 **信号 * 当日收益率** 来计算。

```python
# 策略一：基于收益率预测信号的策略收益
data[‘strategy_returns‘] = data[‘signal_returns‘] * data[‘returns‘]

# 策略二：基于涨跌方向预测信号的策略收益
data[‘strategy_direction‘] = data[‘signal_direction‘] * data[‘returns‘]
```

![](img/620233d646d2e98903c723a0fdd03f21_1.png)

![](img/620233d646d2e98903c723a0fdd03f21_3.png)

执行完代码后，我们可以查看`data`表，其中新增的列就记录了按照我们构建的回归方程策略所获得的每日收益。通过对比这两个策略列与原始的`returns`列，可以初步评估策略是否比单纯持有（或不操作）有更好的表现。

---

![](img/620233d646d2e98903c723a0fdd03f21_5.png)

本节课中我们一起学习了如何从金融时间序列数据构建线性回归预测模型。核心步骤包括：导入`scikit-learn`库、实例化模型、用`.fit()`方法训练、用`.predict()`方法进行预测。随后，我们将连续的预测值转化为离散的交易信号（1或-1），并基于此信号计算了一个简单策略的收益，为后续的策略评估奠定了基础。这是一个基础的模型应用流程，在实际操作中，还需要进行更严谨的回测和评估。