# 基于Python的股票分析与量化交易入门到实践：P41：10.3 Python量化择时--技术指标函数_趋向指标上--MACD、EMV、UOS (二) 📈

在本节课中，我们将要学习三种重要的趋向指标：MACD、EMV和UOS。我们将了解它们的基本概念、核心参数，并通过Python代码实战演示如何在聚宽量化平台上调用这些指标函数。课程的重点是理解指标的原理和应用场景，为后续构建量化策略打下基础。

![](img/87d3a5d841667f09cc09f1a95955d9cd_1.png)

## 代码实战：调用技术指标函数

上一节我们介绍了趋向指标的基本概念，本节中我们来看看如何在聚宽量化平台上具体调用MACD、EMV和UOS的函数。

以下是调用MACD指标函数的示例代码。MACD函数需要传入股票代码、计算日期以及快线、慢线和信号线的周期参数。

```python
# 计算MACD指标示例
# 导入必要的库，这里假设已连接聚宽平台
# 定义股票代码和日期
security = ‘000001.XSHE‘  # 平安银行
check_date = ‘2022-09-01‘
# 定义MACD参数
short_period = 12
long_period = 26
mid_period = 9
# 调用MACD函数
macd_result = TECH_ANALYSIS.MACD(security, check_date, short=short_period, long=long_period, mid=mid_period)
# 打印结果
print(“快线DIF:“, macd_result[‘dif‘])
print(“慢线DEA:“, macd_result[‘dea‘])
print(“MACD柱:“, macd_result[‘macd‘])
```

运行以上代码，可以得到平安银行在指定日期的MACD值。单日的数值难以判断金叉或死叉，这需要在后续的量化策略中结合连续的数据进行分析。

接下来我们实现EMV指标的计算。EMV函数需要股票列表、日期以及参数N和M。

```python
# 计算EMV指标示例
# 定义多只股票
security_list = [‘000002.XSHE‘, ‘601211.XSHG‘, ‘000001.XSHE‘]  # 万科A, 国泰君安, 平安银行
check_date = ‘2022-09-01‘
# 定义EMV参数
N = 14
M = 9
# 调用EMV函数
emv_result = TECH_ANALYSIS.EMV(security_list, check_date, N=N, M=M)
# 打印结果
print(“EMV值:“, emv_result[‘emv‘])
print(“MAEMV值:“, emv_result[‘maemv‘])
```

此代码返回了多只股票在指定日期的EMV及其移动平均线值。应用时需关注指标是否上穿或下穿零轴。

最后，我们来看UOS指标。UOS函数有特定的参数N1, N2, N3和M。

```python
# 计算UOS指标示例
security = ‘000001.XSHE‘
check_date = ‘2022-09-01‘
# 定义UOS参数
N1 = 7
N2 = 14
N3 = 28
M = 6
# 调用UOS函数
uos_result = TECH_ANALYSIS.UOS(security, check_date, N1=N1, N2=N2, N3=N3, M=M)
# 打印结果
print(“UOS值:“, uos_result[‘uos‘])
print(“MAUOS值:“, uos_result[‘mauos‘])
```

![](img/87d3a5d841667f09cc09f1a95955d9cd_3.png)

运行后，我们得到了平安银行的UOS及其移动平均值。根据其相对于阈值（如50和65）的位置，可以辅助判断短线买卖点。

虽然聚宽平台已封装好这些函数，但鼓励有兴趣的同学根据指标公式自行实现，以加深理解。

## 本章小结 📝

本节课中我们一起学习了三种关键的趋向指标。

首先，我们了解了趋向指标（或称动向指标DMI）的核心是分析股价供需关系变动导致的均衡与失衡循环，并借此研判趋势。

其次，我们详细介绍了MACD指标。它通过计算平滑移动平均线的差异来研判股价变化的方向、趋势和强度。其核心在于识别“金叉”（快线上穿慢线，红柱出现，买入信号）和“死叉”（快线下穿慢线，绿柱出现，卖出信号）。公式上，MACD由DIF（差离值）、DEA（信号线）和MACD柱构成。

接着，我们学习了EMV（简易波动指标）。该指标关注股票中间价的相对波动幅度，其计算基于价格变化与成交量的关系。EMV上升通常伴随价升量增，下跌则伴随缩量下跌。应用上，EMV由下向上穿过零轴为买入信号，由上向下穿过零轴为卖出信号。

最后，我们探讨了UOS（终极波动指标）。它是一个兼顾短、中、长线的指标，既能确认趋势，也能发出超买超卖信号。其算法相对复杂，但应用规则明确：
*   **短线交易**：UOS上穿50为买点，下穿65为卖点。
*   **中长线交易**：UOS上穿35为买点，下穿70为卖点。

在代码实战部分，我们演示了如何利用聚宽量化平台便捷地调用这些指标函数。虽然调用过程简单，但理解每个指标背后的原理和适用场景才是关键。建议初学者在掌握函数调用后，可以尝试根据公开的公式自行实现这些指标，以巩固学习成果。

![](img/87d3a5d841667f09cc09f1a95955d9cd_5.png)

好，以上就是本章的全部内容。我是meta，我们下期再见。