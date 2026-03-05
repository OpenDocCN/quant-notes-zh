# Python金融分析与量化交易实战教程：P63：11-11-构建回归方程 📈

在本节课中，我们将学习如何构建一个回归方程模型，用于预测股票市场的每日走势（上涨或下跌）。我们将使用Python的`scikit-learn`库来实现线性回归，并基于预测结果制定简单的交易策略。

---

## 第一步：导入必要的工具包

首先，我们需要借助一些工具包。这里选择的是`scikit-learn`，它是Python中一个非常常见的机器学习库，包含了几乎所有常规的机器学习算法。

我们将使用其中的线性回归模型。

```python
from sklearn.linear_model import LinearRegression
```

---

## 第二步：实例化模型

在`scikit-learn`中，构建任何模型的第一步都是对导入的类进行实例化。

```python
model = LinearRegression()
```

---

## 第三步：准备数据并训练模型

模型实例化后，我们需要用数据来训练它。训练操作在`scikit-learn`中通过`.fit()`方法完成。该方法需要传入两个参数：特征数据`X`和目标变量`Y`。

以下是准备数据和训练模型的步骤：

1.  **准备特征数据 (X)**：我们使用之前定义好的特定列作为特征。为了在函数外部也能调用这些列名，我们将其声明为全局变量。
2.  **准备目标变量 (Y)**：我们将进行一个对比实验。第一个目标变量使用实际的日收益率 (`returns`)，第二个目标变量使用涨跌方向 (`direction`)。
3.  **训练模型**：分别用两组`(X, Y)`数据训练两个模型。

```python
# 假设 `data` 是包含特征的DataFrame，`column_names` 是特征列名的全局变量列表
X = data[column_names]

# 对比实验一：以实际收益率作为目标
Y_returns = data['returns']
model_returns = LinearRegression()
model_returns.fit(X, Y_returns)

# 对比实验二：以涨跌方向作为目标
Y_direction = data['direction']
model_direction = LinearRegression()
model_direction.fit(X, Y_direction)
```

---

## 第四步：进行预测

模型训练完成后，我们可以用它来对数据进行预测。预测使用`.predict()`方法。

我们将分别用两个模型对特征`X`进行预测，并将预测结果添加到原始数据中。

```python
# 使用第一个模型（基于收益率）进行预测
data['pred_returns'] = model_returns.predict(X)

# 使用第二个模型（基于涨跌方向）进行预测
data['pred_direction'] = model_direction.predict(X)
```

执行后，可以查看`data.head()`来观察预测结果。初步观察可能发现预测值（例如`pred_direction`）的符号与真实值不完全一致，这是正常的，因为线性回归模型无法100%准确地捕捉复杂市场趋势。

---

## 第五步：将预测值转换为交易信号

对于交易策略而言，我们更关心的是“买”或“卖”的信号，而不是具体的预测数值。因此，我们需要将连续的预测值转换为离散的交易标志位。

转换逻辑很简单：如果预测值大于0，则视为看涨信号（标记为1）；如果小于等于0，则视为看跌信号（标记为-1或0，根据策略定义）。这里我们采用1和-1。

```python
# 将第一个模型的预测值转换为交易信号
data['signal_returns'] = (data['pred_returns'] > 0).astype(int) * 2 - 1  # 将True/False映射为1/-1

# 将第二个模型的预测值转换为交易信号
data['signal_direction'] = (data['pred_direction'] > 0).astype(int) * 2 - 1
```

转换后，`signal_returns`和`signal_direction`列的值将为1（买入）或-1（卖出），代表了模型给出的每日操作建议。

---

## 第六步：基于策略计算模拟收益

现在，我们可以根据生成的交易信号来模拟策略的收益情况。基本思路是：当信号为1（买入）时，我们获得当日的实际收益率；当信号为-1（卖出）时，我们获得当日实际收益率的相反数（即做空或避开损失）。

以下是计算两种策略收益的方法：

```python
# 策略一：基于收益率模型信号的收益
data['strategy_returns'] = data['signal_returns'] * data['returns']

# 策略二：基于涨跌方向模型信号的收益
data['strategy_direction'] = data['signal_direction'] * data['returns']
```

计算完成后，`strategy_returns`和`strategy_direction`列就分别代表了遵循对应模型信号所获得的日收益率。通过分析这些收益率序列（例如计算累计收益、夏普比率等），我们可以对比哪个模型的预测对交易更有指导意义。

**注意**：在Jupyter Notebook等交互式环境中重复执行单元格时，可能会因为变量重复赋值而导致列名混乱。如果出现此情况，建议重启内核并从头执行代码，以确保流程清晰。

---

![](img/0e16dfeb6cf790cf67563ad1306554ba_1.png)

## 总结

![](img/0e16dfeb6cf790cf67563ad1306554ba_3.png)

本节课中，我们一起学习了如何构建一个基础的量化交易预测模型。

1.  我们引入了`scikit-learn`库并实例化了线性回归模型。
2.  我们准备了特征数据和两种不同的目标变量（收益率和涨跌方向）来训练模型，并进行对比实验。
3.  我们使用训练好的模型进行预测，并将连续的预测值转换为“买入/卖出”的交易信号。
4.  最后，我们根据交易信号模拟了策略的收益，为后续评估模型效果奠定了基础。

![](img/0e16dfeb6cf790cf67563ad1306554ba_5.png)

这个过程展示了从数据准备、模型训练到策略回测的基本闭环。请注意，本例使用的是简单的线性回归，实际市场更为复杂。后续可以通过尝试更复杂的模型、优化特征工程以及严格的风险管理来提升策略性能。