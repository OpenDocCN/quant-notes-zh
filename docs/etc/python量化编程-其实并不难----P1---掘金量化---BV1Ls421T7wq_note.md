# Python量化编程：P1：Python编程快速上手指南 🐍

![](img/2fc381bbffe702b3517bb30bd197d332_1.png)

![](img/2fc381bbffe702b3517bb30bd197d332_3.png)

![](img/2fc381bbffe702b3517bb30bd197d332_4.png)

![](img/2fc381bbffe702b3517bb30bd197d332_6.png)

![](img/2fc381bbffe702b3517bb30bd197d332_8.png)

![](img/2fc381bbffe702b3517bb30bd197d332_10.png)

![](img/2fc381bbffe702b3517bb30bd197d332_12.png)

![](img/2fc381bbffe702b3517bb30bd197d332_14.png)

![](img/2fc381bbffe702b3517bb30bd197d332_16.png)

![](img/2fc381bbffe702b3517bb30bd197d332_18.png)

![](img/2fc381bbffe702b3517bb30bd197d332_20.png)

![](img/2fc381bbffe702b3517bb30bd197d332_22.png)

![](img/2fc381bbffe702b3517bb30bd197d332_24.png)

![](img/2fc381bbffe702b3517bb30bd197d332_26.png)

![](img/2fc381bbffe702b3517bb30bd197d332_28.png)

在本节课中，我们将学习如何快速上手Python量化编程。课程将分为两部分：Python基础核心概念和量化中常用的第三方库。掌握这些内容，你就能开始编写自己的量化策略了。

![](img/2fc381bbffe702b3517bb30bd197d332_30.png)

![](img/2fc381bbffe702b3517bb30bd197d332_32.png)

![](img/2fc381bbffe702b3517bb30bd197d332_34.png)

![](img/2fc381bbffe702b3517bb30bd197d332_36.png)

![](img/2fc381bbffe702b3517bb30bd197d332_38.png)

![](img/2fc381bbffe702b3517bb30bd197d332_40.png)

![](img/2fc381bbffe702b3517bb30bd197d332_42.png)

## Python基础四大核心内容

![](img/2fc381bbffe702b3517bb30bd197d332_44.png)

![](img/2fc381bbffe702b3517bb30bd197d332_46.png)

![](img/2fc381bbffe702b3517bb30bd197d332_48.png)

![](img/2fc381bbffe702b3517bb30bd197d332_50.png)

![](img/2fc381bbffe702b3517bb30bd197d332_52.png)

![](img/2fc381bbffe702b3517bb30bd197d332_54.png)

![](img/2fc381bbffe702b3517bb30bd197d332_56.png)

![](img/2fc381bbffe702b3517bb30bd197d332_58.png)

Python基础主要包含四大内容：变量、数据类型、控制流和函数。理解这些是编写任何程序，包括量化策略的第一步。

![](img/2fc381bbffe702b3517bb30bd197d332_60.png)

![](img/2fc381bbffe702b3517bb30bd197d332_62.png)

![](img/2fc381bbffe702b3517bb30bd197d332_64.png)

![](img/2fc381bbffe702b3517bb30bd197d332_66.png)

![](img/2fc381bbffe702b3517bb30bd197d332_68.png)

![](img/2fc381bbffe702b3517bb30bd197d332_70.png)

![](img/2fc381bbffe702b3517bb30bd197d332_72.png)

![](img/2fc381bbffe702b3517bb30bd197d332_74.png)

### 变量

![](img/2fc381bbffe702b3517bb30bd197d332_76.png)

![](img/2fc381bbffe702b3517bb30bd197d332_78.png)

![](img/2fc381bbffe702b3517bb30bd197d332_80.png)

![](img/2fc381bbffe702b3517bb30bd197d332_82.png)

![](img/2fc381bbffe702b3517bb30bd197d332_84.png)

![](img/2fc381bbffe702b3517bb30bd197d332_86.png)

![](img/2fc381bbffe702b3517bb30bd197d332_87.png)

变量用于存储数据，在量化策略中，它常用于管理可调整的参数。例如，在MACD策略中，我们可以用变量来代表其参数，这样只需在程序开头修改变量的值，就能轻松调整整个策略的参数，而无需深入修改策略内部的每一处代码。

![](img/2fc381bbffe702b3517bb30bd197d332_89.png)

![](img/2fc381bbffe702b3517bb30bd197d332_90.png)

![](img/2fc381bbffe702b3517bb30bd197d332_92.png)

![](img/2fc381bbffe702b3517bb30bd197d332_94.png)

![](img/2fc381bbffe702b3517bb30bd197d332_96.png)

![](img/2fc381bbffe702b3517bb30bd197d332_98.png)

![](img/2fc381bbffe702b3517bb30bd197d332_99.png)

![](img/2fc381bbffe702b3517bb30bd197d332_101.png)

**示例**：
```python
# 定义MACD策略的参数变量
short_window = 12   # 短期EMA周期
long_window = 26    # 长期EMA周期
signal_window = 9   # 信号线周期
```

![](img/2fc381bbffe702b3517bb30bd197d332_103.png)

![](img/2fc381bbffe702b3517bb30bd197d332_105.png)

![](img/2fc381bbffe702b3517bb30bd197d332_107.png)

![](img/2fc381bbffe702b3517bb30bd197d332_108.png)

![](img/2fc381bbffe702b3517bb30bd197d332_110.png)

![](img/2fc381bbffe702b3517bb30bd197d332_112.png)

![](img/2fc381bbffe702b3517bb30bd197d332_114.png)

![](img/2fc381bbffe702b3517bb30bd197d332_116.png)

### 数据类型

![](img/2fc381bbffe702b3517bb30bd197d332_118.png)

![](img/2fc381bbffe702b3517bb30bd197d332_120.png)

![](img/2fc381bbffe702b3517bb30bd197d332_122.png)

![](img/2fc381bbffe702b3517bb30bd197d332_124.png)

![](img/2fc381bbffe702b3517bb30bd197d332_126.png)

![](img/2fc381bbffe702b3517bb30bd197d332_128.png)

![](img/2fc381bbffe702b3517bb30bd197d332_130.png)

![](img/2fc381bbffe702b3517bb30bd197d332_131.png)

数据类型定义了数据的种类和可执行的操作。Python有丰富的数据类型，在量化中常用的包括：
*   **数值类型**：如整数（`int`）、浮点数（`float`），用于计算价格、指标等。
*   **字符串**（`str`）：用于表示股票代码、日期等文本信息。
*   **布尔值**（`bool`）：`True`或`False`，用于条件判断。

![](img/2fc381bbffe702b3517bb30bd197d332_133.png)

![](img/2fc381bbffe702b3517bb30bd197d332_135.png)

![](img/2fc381bbffe702b3517bb30bd197d332_137.png)

![](img/2fc381bbffe702b3517bb30bd197d332_139.png)

![](img/2fc381bbffe702b3517bb30bd197d332_141.png)

![](img/2fc381bbffe702b3517bb30bd197d332_143.png)

![](img/2fc381bbffe702b3517bb30bd197d332_144.png)

![](img/2fc381bbffe702b3517bb30bd197d332_146.png)

### 控制流

![](img/2fc381bbffe702b3517bb30bd197d332_148.png)

![](img/2fc381bbffe702b3517bb30bd197d332_150.png)

![](img/2fc381bbffe702b3517bb30bd197d332_152.png)

![](img/2fc381bbffe702b3517bb30bd197d332_154.png)

![](img/2fc381bbffe702b3517bb30bd197d332_156.png)

![](img/2fc381bbffe702b3517bb30bd197d332_157.png)

![](img/2fc381bbffe702b3517bb30bd197d332_159.png)

控制流用于控制程序的执行逻辑，主要包括条件语句和循环语句。

![](img/2fc381bbffe702b3517bb30bd197d332_161.png)

![](img/2fc381bbffe702b3517bb30bd197d332_163.png)

![](img/2fc381bbffe702b3517bb30bd197d332_165.png)

![](img/2fc381bbffe702b3517bb30bd197d332_167.png)

![](img/2fc381bbffe702b3517bb30bd197d332_169.png)

![](img/2fc381bbffe702b3517bb30bd197d332_171.png)

![](img/2fc381bbffe702b3517bb30bd197d332_173.png)

上一节我们介绍了如何用变量存储数据，本节中我们来看看如何根据数据做出决策和进行重复操作。

![](img/2fc381bbffe702b3517bb30bd197d332_175.png)

![](img/2fc381bbffe702b3517bb30bd197d332_177.png)

![](img/2fc381bbffe702b3517bb30bd197d332_179.png)

![](img/2fc381bbffe702b3517bb30bd197d332_181.png)

![](img/2fc381bbffe702b3517bb30bd197d332_183.png)

![](img/2fc381bbffe702b3517bb30bd197d332_185.png)

![](img/2fc381bbffe702b3517bb30bd197d332_187.png)

**条件语句**（如 `if/else`）帮助程序根据不同情况执行不同操作。例如，在MACD策略中，我们可以判断MACD值是否大于零（红柱），来决定是买入还是卖出。

![](img/2fc381bbffe702b3517bb30bd197d332_189.png)

![](img/2fc381bbffe702b3517bb30bd197d332_191.png)

![](img/2fc381bbffe702b3517bb30bd197d332_193.png)

![](img/2fc381bbffe702b3517bb30bd197d332_195.png)

![](img/2fc381bbffe702b3517bb30bd197d332_197.png)

![](img/2fc381bbffe702b3517bb30bd197d332_199.png)

![](img/2fc381bbffe702b3517bb30bd197d332_201.png)

![](img/2fc381bbffe702b3517bb30bd197d332_202.png)

**示例**：
```python
if macd_value > 0:
    # 执行买入操作
    place_order(buy)
else:
    # 执行卖出操作
    place_order(sell)
```

![](img/2fc381bbffe702b3517bb30bd197d332_203.png)

![](img/2fc381bbffe702b3517bb30bd197d332_205.png)

![](img/2fc381bbffe702b3517bb30bd197d332_207.png)

![](img/2fc381bbffe702b3517bb30bd197d332_208.png)

![](img/2fc381bbffe702b3517bb30bd197d332_210.png)

![](img/2fc381bbffe702b3517bb30bd197d332_212.png)

![](img/2fc381bbffe702b3517bb30bd197d332_214.png)

![](img/2fc381bbffe702b3517bb30bd197d332_216.png)

**循环语句**（如 `for` 循环）用于重复执行相同操作。如果你有一个包含100只股票的股票池，需要对每只股票进行相同的计算，使用循环可以避免重复编写100遍代码。

![](img/2fc381bbffe702b3517bb30bd197d332_218.png)

![](img/2fc381bbffe702b3517bb30bd197d332_219.png)

![](img/2fc381bbffe702b3517bb30bd197d332_221.png)

![](img/2fc381bbffe702b3517bb30bd197d332_223.png)

![](img/2fc381bbffe702b3517bb30bd197d332_225.png)

![](img/2fc381bbffe702b3517bb30bd197d332_227.png)

![](img/2fc381bbffe702b3517bb30bd197d332_229.png)

![](img/2fc381bbffe702b3517bb30bd197d332_231.png)

**示例**：
```python
stock_list = [‘AAPL‘, ‘GOOGL‘, ‘MSFT‘, ...] # 假设有100只股票
for stock in stock_list:
    # 对每只股票执行相同的计算逻辑
    calculate_indicator(stock)
```

![](img/2fc381bbffe702b3517bb30bd197d332_233.png)

![](img/2fc381bbffe702b3517bb30bd197d332_235.png)

![](img/2fc381bbffe702b3517bb30bd197d332_237.png)

![](img/2fc381bbffe702b3517bb30bd197d332_239.png)

![](img/2fc381bbffe702b3517bb30bd197d332_241.png)

![](img/2fc381bbffe702b3517bb30bd197d332_243.png)

![](img/2fc381bbffe702b3517bb30bd197d332_245.png)

### 函数

![](img/2fc381bbffe702b3517bb30bd197d332_247.png)

![](img/2fc381bbffe702b3517bb30bd197d332_249.png)

![](img/2fc381bbffe702b3517bb30bd197d332_251.png)

![](img/2fc381bbffe702b3517bb30bd197d332_253.png)

![](img/2fc381bbffe702b3517bb30bd197d332_255.png)

![](img/2fc381bbffe702b3517bb30bd197d332_257.png)

![](img/2fc381bbffe702b3517bb30bd197d332_259.png)

![](img/2fc381bbffe702b3517bb30bd197d332_261.png)

函数是一个可重复使用的代码块，用于执行特定任务。它可以将一系列操作封装起来，在需要时直接调用，使代码更简洁、易维护。

![](img/2fc381bbffe702b3517bb30bd197d332_263.png)

![](img/2fc381bbffe702b3517bb30bd197d332_265.png)

![](img/2fc381bbffe702b3517bb30bd197d332_267.png)

![](img/2fc381bbffe702b3517bb30bd197d332_269.png)

![](img/2fc381bbffe702b3517bb30bd197d332_271.png)

![](img/2fc381bbffe702b3517bb30bd197d332_273.png)

![](img/2fc381bbffe702b3517bb30bd197d332_275.png)

![](img/2fc381bbffe702b3517bb30bd197d332_277.png)

例如，你可以将“筛选低风险股票”的一系列操作写成一个函数。之后每次需要筛选时，只需调用这个函数即可，无需重复编写代码。

![](img/2fc381bbffe702b3517bb30bd197d332_279.png)

![](img/2fc381bbffe702b3517bb30bd197d332_281.png)

![](img/2fc381bbffe702b3517bb30bd197d332_283.png)

![](img/2fc381bbffe702b3517bb30bd197d332_285.png)

![](img/2fc381bbffe702b3517bb30bd197d332_287.png)

![](img/2fc381bbffe702b3517bb30bd197d332_289.png)

![](img/2fc381bbffe702b3517bb30bd197d332_291.png)

**示例**：
```python
def is_first_trading_day_of_month(date):
    """
    判断给定日期是否是当月的第一个交易日。
    这是一个功能集合的例子。
    """
    # 这里是具体的判断逻辑...
    # 返回 True 或 False
    return result

![](img/2fc381bbffe702b3517bb30bd197d332_293.png)

![](img/2fc381bbffe702b3517bb30bd197d332_294.png)

![](img/2fc381bbffe702b3517bb30bd197d332_296.png)

![](img/2fc381bbffe702b3517bb30bd197d332_298.png)

![](img/2fc381bbffe702b3517bb30bd197d332_300.png)

![](img/2fc381bbffe702b3517bb30bd197d332_302.png)

![](img/2fc381bbffe702b3517bb30bd197d332_304.png)

![](img/2fc381bbffe702b3517bb30bd197d332_306.png)

![](img/2fc381bbffe702b3517bb30bd197d332_308.png)

# 在策略中调用函数
if is_first_trading_day_of_month(current_date):
    rebalance_portfolio()
```

![](img/2fc381bbffe702b3517bb30bd197d332_310.png)

![](img/2fc381bbffe702b3517bb30bd197d332_312.png)

![](img/2fc381bbffe702b3517bb30bd197d332_314.png)

![](img/2fc381bbffe702b3517bb30bd197d332_316.png)

![](img/2fc381bbffe702b3517bb30bd197d332_318.png)

![](img/2fc381bbffe702b3517bb30bd197d332_320.png)

![](img/2fc381bbffe702b3517bb30bd197d332_322.png)

## Python进阶：常用第三方库

![](img/2fc381bbffe702b3517bb30bd197d332_324.png)

![](img/2fc381bbffe702b3517bb30bd197d332_326.png)

![](img/2fc381bbffe702b3517bb30bd197d332_328.png)

![](img/2fc381bbffe702b3517bb30bd197d332_330.png)

![](img/2fc381bbffe702b3517bb30bd197d332_332.png)

![](img/2fc381bbffe702b3517bb30bd197d332_334.png)

![](img/2fc381bbffe702b3517bb30bd197d332_336.png)

![](img/2fc381bbffe702b3517bb30bd197d332_338.png)

掌握了Python基础后，进阶的关键在于学会使用强大的第三方库。它们提供了现成的工具和函数，能极大提升开发效率，避免重复“造轮子”。

![](img/2fc381bbffe702b3517bb30bd197d332_340.png)

![](img/2fc381bbffe702b3517bb30bd197d332_342.png)

![](img/2fc381bbffe702b3517bb30bd197d332_344.png)

![](img/2fc381bbffe702b3517bb30bd197d332_346.png)

![](img/2fc381bbffe702b3517bb30bd197d332_348.png)

![](img/2fc381bbffe702b3517bb30bd197d332_350.png)

![](img/2fc381bbffe702b3517bb30bd197d332_352.png)

![](img/2fc381bbffe702b3517bb30bd197d332_354.png)

以下是量化编程中几个至关重要的库：

![](img/2fc381bbffe702b3517bb30bd197d332_355.png)

![](img/2fc381bbffe702b3517bb30bd197d332_357.png)

![](img/2fc381bbffe702b3517bb30bd197d332_359.png)

![](img/2fc381bbffe702b3517bb30bd197d332_361.png)

![](img/2fc381bbffe702b3517bb30bd197d332_363.png)

![](img/2fc381bbffe702b3517bb30bd197d332_365.png)

![](img/2fc381bbffe702b3517bb30bd197d332_367.png)

![](img/2fc381bbffe702b3517bb30bd197d332_369.png)

*   **数据处理**：`NumPy` 和 `Pandas` 是数据处理的基石。`Pandas` 的 `DataFrame` 结构非常适合处理金融时间序列数据，如股票价格和交易量。
*   **指标计算**：`TA-Lib` 或 `mplfinance` 等库包含了大量技术指标（如MACD、RSI、布林带）的计算函数。你可以直接将价格数据传入库中的函数，一行代码就能计算出结果，无需自己从头实现复杂的公式。
*   **日期处理**：`datetime` 库用于处理时间和日期。例如，判断某个日期是否为当月第一个交易日，就需要用到这个库。
*   **数据可视化**：`Matplotlib` 和 `Seaborn` 用于绘制图表。例如，你可以将MACD指标与股票未来收益的关系画成散点图，直观地观察它们之间的相关性。

![](img/2fc381bbffe702b3517bb30bd197d332_371.png)

![](img/2fc381bbffe702b3517bb30bd197d332_373.png)

![](img/2fc381bbffe702b3517bb30bd197d332_375.png)

![](img/2fc381bbffe702b3517bb30bd197d332_376.png)

![](img/2fc381bbffe702b3517bb30bd197d332_378.png)

![](img/2fc381bbffe702b3517bb30bd197d332_380.png)

![](img/2fc381bbffe702b3517bb30bd197d332_382.png)

![](img/2fc381bbffe702b3517bb30bd197d332_383.png)

**示例（使用库计算MACD）**：
```python
import pandas as pd
import talib

![](img/2fc381bbffe702b3517bb30bd197d332_384.png)

![](img/2fc381bbffe702b3517bb30bd197d332_385.png)

![](img/2fc381bbffe702b3517bb30bd197d332_386.png)

![](img/2fc381bbffe702b3517bb30bd197d332_387.png)

![](img/2fc381bbffe702b3517bb30bd197d332_388.png)

![](img/2fc381bbffe702b3517bb30bd197d332_390.png)

![](img/2fc381bbffe702b3517bb30bd197d332_392.png)

![](img/2fc381bbffe702b3517bb30bd197d332_394.png)

![](img/2fc381bbffe702b3517bb30bd197d332_396.png)

# 假设df是一个包含‘close‘价格的DataFrame
close_prices = df[‘close‘].values
# 一行代码计算MACD
macd, signal, hist = talib.MACD(close_prices, fastperiod=12, slowperiod=26, signalperiod=9)
```

![](img/2fc381bbffe702b3517bb30bd197d332_398.png)

![](img/2fc381bbffe702b3517bb30bd197d332_400.png)

![](img/2fc381bbffe702b3517bb30bd197d332_402.png)

![](img/2fc381bbffe702b3517bb30bd197d332_404.png)

![](img/2fc381bbffe702b3517bb30bd197d332_406.png)

![](img/2fc381bbffe702b3517bb30bd197d332_408.png)

![](img/2fc381bbffe702b3517bb30bd197d332_409.png)

![](img/2fc381bbffe702b3517bb30bd197d332_411.png)

![](img/2fc381bbffe702b3517bb30bd197d332_413.png)

## 课程总结与预告

![](img/2fc381bbffe702b3517bb30bd197d332_415.png)

![](img/2fc381bbffe702b3517bb30bd197d332_417.png)

![](img/2fc381bbffe702b3517bb30bd197d332_419.png)

![](img/2fc381bbffe702b3517bb30bd197d332_421.png)

![](img/2fc381bbffe702b3517bb30bd197d332_422.png)

![](img/2fc381bbffe702b3517bb30bd197d332_424.png)

![](img/2fc381bbffe702b3517bb30bd197d332_426.png)

![](img/2fc381bbffe702b3517bb30bd197d332_428.png)

本节课中我们一起学习了Python量化编程的快速上手路径。核心在于掌握**变量、数据类型、控制流和函数**四大基础概念，并学会调用如`Pandas`、`TA-Lib`等**第三方库**来高效处理数据和计算指标。

![](img/2fc381bbffe702b3517bb30bd197d332_430.png)

![](img/2fc381bbffe702b3517bb30bd197d332_432.png)

![](img/2fc381bbffe702b3517bb30bd197d332_434.png)

![](img/2fc381bbffe702b3517bb30bd197d332_435.png)

![](img/2fc381bbffe702b3517bb30bd197d332_436.png)

![](img/2fc381bbffe702b3517bb30bd197d332_437.png)

![](img/2fc381bbffe702b3517bb30bd197d332_439.png)

![](img/2fc381bbffe702b3517bb30bd197d332_441.png)

在接下来的课程中，我们将进入实战环节，详细讲解**如何构建一个完整的量化策略**，以及**如何阅读和理解策略的历史回测报告**。敬请关注！