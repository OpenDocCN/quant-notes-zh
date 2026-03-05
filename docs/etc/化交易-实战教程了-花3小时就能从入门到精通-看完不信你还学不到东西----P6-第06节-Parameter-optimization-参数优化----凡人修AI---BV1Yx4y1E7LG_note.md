# Python量化交易实战教程：P6：参数优化 🎯

在本节课中，我们将学习如何实现并优化三种不同的量化交易策略。我们将从简单的移动平均交叉策略开始，逐步深入到更复杂的模型预测和配对交易策略，并最终探讨如何通过参数优化来提升策略表现。

## 移动平均交叉策略 📈

上一节我们介绍了事件驱动交易框架的基本结构，本节中我们来看看如何实现第一个具体的交易策略：移动平均交叉策略。

该策略的核心思想非常简单。我们需要设定两个回顾周期：一个短期窗口和一个长期窗口。在本例中，短期窗口设为100天，长期窗口设为400天。需要提醒的是，这两个窗口长度的设置是后续参数优化环节中需要调整的一对参数。我们将根据优化结果来讨论如何选择长短窗口，以实现收益最大化。

策略的核心是 `calculate_signal` 函数。它的主要功能是从数据处理器（`DataHandler`）的事件队列（`event_q`）中读取市场信息并作出反应。对于每一个交易标的（`symbol`），我们获取其最近N天的收盘价，这里的N对应上述的短期和长期窗口长度。我们分别计算短期和长期的移动平均线。

![](img/6d6796143a723efc398964af503dba82_1.png)

以下是该策略的交易原则：
*   当短期移动平均线超过长期移动平均线时，我们进入市场。
*   反之，当短期移动平均线低于长期移动平均线时，我们退出市场。

![](img/6d6796143a723efc398964af503dba82_3.png)

其原理很容易理解：当短期均值超过长期均值时，表明股票近期呈现上涨趋势，因此我们买入。反之，则表明市场疲软，我们卖出。根据条件判断后，我们更新持仓状态，并构造一个信号事件（`SignalEvent`）放入队列中，等待后续的投资组合管理器（`Portfolio`）读取并执行。

这就是我们重写策略对象的具体逻辑，我们将其命名为 `MovingAverageCrossStrategy`。

```python
# 策略初始化
class MovingAverageCrossStrategy(Strategy):
    def __init__(self, bars, events, short_window=100, long_window=400):
        self.bars = bars
        self.symbol_list = self.bars.symbol_list
        self.events = events
        self.short_window = short_window
        self.long_window = long_window
        self.bought = self._calculate_initial_bought()

    # 核心：计算交易信号的方法
    def calculate_signals(self, event):
        if event.type == 'MARKET':
            for s in self.symbol_list:
                bars = self.bars.get_latest_bars_values(s, "close", N=self.long_window)
                if bars is not None and len(bars) == self.long_window:
                    short_sma = np.mean(bars[-self.short_window:])
                    long_sma = np.mean(bars)
                    # 交易规则实施
                    if short_sma > long_sma and self.bought[s] == "OUT":
                        # 生成买入信号
                        signal = SignalEvent(...)
                        self.events.put(signal)
                        self.bought[s] = "LONG"
                    elif short_sma < long_sma and self.bought[s] == "LONG":
                        # 生成卖出信号
                        signal = SignalEvent(...)
                        self.events.put(signal)
                        self.bought[s] = "OUT"
```

![](img/6d6796143a723efc398964af503dba82_5.png)

下面的 `if __name__ == "__main__"` 函数是对整个事件驱动系统的整合调用。`csv_dict` 指定了本地历史数据文件的位置，`symbol` 设置为苹果公司股票，并定义了初始资本和开始时间。通过调用回测对象（`Backtest`）来模拟交易。

```python
if __name__ == "__main__":
    csv_dir = '/path/to/your/csv/files/'  # 更改为你的文件路径
    symbol_list = ['AAPL']
    initial_capital = 100000.0
    start_date = datetime.datetime(2010, 1, 1)

    backtest = Backtest(csv_dir, symbol_list, initial_capital, start_date,
                        HistoricalCSVDataHandler, SimulatedExecutionHandler,
                        Portfolio, MovingAverageCrossStrategy)
    backtest.simulate_trading()
```

通过调用性能评估类（`Performance`），我们可以得到详细的报表，包括收益率、夏普比率、最大回撤比率和回撤持续时间，以及生成的订单数量。在整个交易过程中，我们采取了10次行动。虽然夏普比率和收益率不算很高，但可以看到最大回撤相对较低，说明策略的风险较小。

## 基于模型预测的策略（以QDA为例）🔮

![](img/6d6796143a723efc398964af503dba82_7.png)

接下来我们看看另一种策略模型：基于标普500指数自身历史数据的预测模型。本例使用的是二次判别分析（QDA），我们在上节课初步提到过。我们同样可以通过 `scikit-learn` 库轻松使用线性判别分析（LDA）、支持向量机（SVM）或逻辑回归等方法，本例使用QDA进行演示。

我们依旧需要重写 `calculate_signals` 函数。核心想法是利用QDA模型进行预测。每次我们等待新的价格数据（`Bar`）到来时，使用其滞后期（例如5阶或10阶）作为特征变量（X），来预测下一期标普500价格是上涨还是下跌（Y）。

以下是信号生成逻辑：
*   如果模型预测为上涨，并且当前未持有市场头寸，则创建一个做多（`LONG`）的信号事件。
*   反之，如果预测为下跌，则通知执行处理器（`ExecutionHandler`）退出市场。

具体的执行代码位于 `calculate_signals` 函数中。

```python
def calculate_signals(self, event):
    if event.type == 'MARKET':
        # 获取历史数据并准备特征X（滞后项）和标签Y（涨跌）
        lags = self.bars.get_latest_bars_values(self.symbol, "close", N=self.lookback+1)
        if len(lags) >= self.lookback+1:
            # 准备训练数据...
            # 使用QDA模型进行拟合和预测
            self.model.fit(X_train, y_train)
            prediction = self.model.predict(X_current.reshape(1, -1))

            if prediction == 1 and self.bought == "OUT":
                # 生成买入信号
                signal = SignalEvent(...)
                self.events.put(signal)
                self.bought = "LONG"
            elif prediction == -1 and self.bought == "LONG":
                # 生成卖出信号
                signal = SignalEvent(...)
                self.events.put(signal)
                self.bought = "OUT"
```

与第一个移动平均策略一样，我们使用本地存储的历史数据（例如从雅虎财经下载的标普500数据）。开始时间设为2006年。使用QDA策略的结果显示，收益率为9.8%，明显高于移动平均策略，夏普比率为1.83。但需要注意的是，最大回撤显著提高，说明风险也相应增加。此外，信号和订单数量达到270个，换手率明显提高。如果考虑交易费用，这种方法会产生更高的成本。

## 均值回复配对交易策略 ⚖️

![](img/6d6796143a723efc398964af503dba82_9.png)

![](img/6d6796143a723efc398964af503dba82_11.png)

现在，我们来看第三种策略：均值回复配对交易。与上两种策略不同，配对交易使用日内价格数据，频率更高，可称为高频交易。由于在雅虎财经等平台只能获取日度数据，因此我们使用 `DTN IQFeed` 来获取日内数据。这需要在之前提到的执行处理器（`ExecutionHandler`）中的 `TWS` 连接处配置用户名和ID信息。

![](img/6d6796143a723efc398964af503dba82_13.png)

本例使用美国航空（AAL）和联合大陆航空（UAL）这对股票进行配对交易。具体流程如下：
1.  使用滚动窗口（例如一个月或一周）进行最小二乘回归，计算残差。这个窗口长度是需要优化的参数之一。
2.  根据上一步得到的残差，计算其Z分数（即标准化后的收益率，并经过风险缩放）。
3.  根据Z分数决定入场或退出。顾名思义，Z分数越高，表明这段时期的收益率越大。我们设定一个上限和下限。
    *   当Z分数超过上限且未持有头寸时，买入。
    *   当Z分数低于下限时，说明市场疲软、风险高或收益率低，应强制平仓退出市场。

![](img/6d6796143a723efc398964af503dba82_15.png)

这里需要优化的参数有三个：滚动回归的窗口长度、Z分数的上限和下限。

让我们看一下 `calculate_signals` 方法的核心实现步骤。

```python
def calculate_signals(self, event):
    if event.type == 'MARKET':
        # 1. 获取两只股票的最新价格序列
        bars_a = self.bars.get_latest_bars_values(self.pair[0], "close", N=self.lookback)
        bars_b = self.bars.get_latest_bars_values(self.pair[1], "close", N=self.lookback)

        if len(bars_a) == self.lookback and len(bars_b) == self.lookback:
            # 2. 进行滚动回归，计算残差
            # (使用 statsmodels 或 numpy 进行OLS回归)
            # model = OLS(bars_a, bars_b)
            # residuals = bars_a - model.predict(bars_b)

            # 3. 计算残差的Z分数
            zscore = (residuals[-1] - np.mean(residuals)) / np.std(residuals)

            # 4. 根据Z分数生成交易信号
            if zscore > self.entry_threshold and self.bought == "OUT":
                # 生成买入A/卖出B的信号 (配对交易)
                signal = SignalEvent(...)
                self.events.put(signal)
                self.bought = "LONG"
            elif zscore < -self.exit_threshold and self.bought == "LONG":
                # 生成平仓信号
                signal = SignalEvent(...)
                self.events.put(signal)
                self.bought = "OUT"
```

由于不再使用CSV数据处理器，而是直接连接交易所，我们需要对框架中的其他类进行修改：
1.  修改 `data.py` 中的 `HistoricalCSVDataHandler`，将其改为高频数据处理器。
2.  修改列名：之前从雅虎财经获取的数据调整后收盘价列名为 `Adj Close`，而从 `DTN IQFeed` 获取的数据对应列名可能为 `Close`。在投资组合（`Portfolio`）的 `update_timeindex` 等方法中都需要同步修改列名。
3.  修改夏普比率的计算：之前基于日度收益率计算，现在基于10分钟频率。需要相应调整计算中的时间参数（例如，年化交易日数、每日交易分钟数等）。

该策略的表现如下：总收益率比QDA策略有明显提高，夏普比率从1.8提升到2.3。值得注意的是，最大回撤比上一个方法还要低，说明这种方法在提高收益率的同时还能降低风险。但需要注意的是，换手率从270激增到7000多，交易频率大大提高。因此，交易成本是需要重点考虑的因素，高交易费用可能会显著影响最终收益。

## 策略表现可视化 📊

![](img/6d6796143a723efc398964af503dba82_17.png)

`plot_performance` 函数的主要功能是将上述提到的权益曲线、收益率和回撤等指标进行绘图，以便更好地进行动态监控。

由于回测运行时间较长，这里直接展示结果截图。
*   **移动平均交叉策略**：投资组合价值在很长一段时间内下降，在1998年亚洲金融危机后大幅上升，但很快又回落。收益率和回撤率在复苏期也较高，表明该策略并非非常理想。
*   **QDA预测策略**：投资组合价值几乎呈震荡上升趋势。收益率虽然波动，但整体为正。需要注意的是，在获得较高收益率时，面临的最大回撤也很大，表明高风险伴随高收益。
*   **均值回复配对交易策略**：在经历2008年短暂的熊市后，投资组合价值几乎持续大幅提升，收益率相当高。同时，最大回撤仅约3%，明显优于上述两种策略。

## 模型参数优化 🛠️

最后，让我们介绍模型参数优化。如前所述，在第二种QDA策略中，如果换成SVM，则需要优化其惩罚参数C。在第一种移动平均和第三种配对交易策略中，都存在回顾窗口（`lookback window`）需要选择。对于移动平均，是短期和长期窗口；对于配对交易，是滚动回归的窗口长度。我们需要通过优化这些参数来最大化策略收益。

![](img/6d6796143a723efc398964af503dba82_19.png)

![](img/6d6796143a723efc398964af503dba82_20.png)

以下是两种常用的优化方法：

**1. 交叉验证**
交叉验证是将一个长度为N的数据集分成训练集和测试集进行内部测试的方法。如果是K=5的交叉验证，流程如下图所示：依次将数据分成5份，每次用其中4份建模，用剩下的1份测试，计算测试均方误差（MSE）。对5次的结果取平均，得到整个数据集的样本外MSE。对于不同的参数（例如λ从1到10），我们计算平均MSE，选择使MSE最小的参数值作为最优参数。

![](img/6d6796143a723efc398964af503dba82_22.png)

以下是对第二种策略（标普500的SVM模型）进行5折交叉验证的示例。我们对分类正确率取平均进行比较。

```python
from sklearn.svm import SVC
from sklearn.model_selection import cross_val_score

# 假设 X, y 已经准备好
svc = SVC(C=1.0, kernel='rbf')
scores = cross_val_score(svc, X, y, cv=5, scoring='accuracy')
print("交叉验证正确率: ", scores)
print("平均正确率: ", scores.mean())
```

![](img/6d6796143a723efc398964af503dba82_24.png)

通过改变参数C，会得到不同的平均击中率（`hit rate`）。我们选择能使击中率达到最高的惩罚参数C，作为最终交易策略的最优参数。

**2. 网格搜索**
网格搜索比交叉验证更全面，但速度更慢，它可以同时优化多个参数。例如，对于SVM，我们可以同时优化惩罚参数C和核函数参数gamma。我们为每个参数设定一个取值范围和步长，从而构造一个参数网格。调用网格搜索包（`GridSearchCV`）后，会返回最优参数组合的结果。

![](img/6d6796143a723efc398964af503dba82_26.png)

```python
from sklearn.model_selection import GridSearchCV

![](img/6d6796143a723efc398964af503dba82_28.png)

![](img/6d6796143a723efc398964af503dba82_30.png)

param_grid = {'C': [0.1, 1, 10, 100, 1000],
              'gamma': [0.001, 0.0001]}
grid = GridSearchCV(SVC(kernel='rbf'), param_grid, cv=5, scoring='accuracy')
grid.fit(X_train, y_train)
print("最优参数: ", grid.best_params_)
print("最高正确率: ", grid.best_score_)
```

![](img/6d6796143a723efc398964af503dba82_32.png)

![](img/6d6796143a723efc398964af503dba82_34.png)

我们可以看到对于不同的C和gamma组合所能获得的正确率。我们选取正确率最高的参数组作为交易策略的输入。

![](img/6d6796143a723efc398964af503dba82_36.png)

![](img/6d6796143a723efc398964af503dba82_38.png)

## 课程总结与作业 📝

本节课中，我们一起学习了三种量化交易策略的具体实现：移动平均交叉策略、基于模型预测的策略以及均值回复配对交易策略。我们分析了它们各自的原理、代码实现和表现，并探讨了如何使用交叉验证和网格搜索进行参数优化，以提升策略性能。

![](img/6d6796143a723efc398964af503dba82_40.png)

**本节课作业如下：**
请复习并整理本堂课所讲的事件驱动交易框架。根据讲解，理清每个类之间的依赖关系。先不要查看完整代码，尝试自己用伪代码写出框架，包括：
*   每个类如何初始化。
*   每个类包含哪些方法，以及每个方法的输入和输出应该是什么。
*   最后，请画出每个类之间的联系关系图。

![](img/6d6796143a723efc398964af503dba82_42.png)

通过完成这个作业，你将能更深刻地理解整个量化交易系统的运作机制。