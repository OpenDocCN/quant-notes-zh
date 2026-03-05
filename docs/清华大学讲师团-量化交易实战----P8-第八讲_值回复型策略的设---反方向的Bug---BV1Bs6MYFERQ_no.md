# 量化交易实战：第八讲：均值回复型策略的设计与开发 📈

在本节课中，我们将学习均值回复型量化策略的原理、设计、开发与优化。我们将从理论验证开始，逐步构建一个完整的策略，并探讨如何通过多空组合、成本管理和头寸管理来改进策略表现。

---

## 概述

![](img/0f81e3704f1e21b26a24b9e99b174c4c_1.png)

![](img/0f81e3704f1e21b26a24b9e99b174c4c_3.png)

![](img/0f81e3704f1e21b26a24b9e99b174c4c_5.png)

均值回复效应是一种普遍存在的现象，指价格或收益率在偏离其长期平均水平后，倾向于回归到该水平。本节课将引导大家通过一个完整的量化研究流程——提出问题、分析问题、解决问题——来构建一个基于此效应的股票策略。我们将使用聚宽平台进行策略开发与回测。

![](img/0f81e3704f1e21b26a24b9e99b174c4c_7.png)

![](img/0f81e3704f1e21b26a24b9e99b174c4c_9.png)

![](img/0f81e3704f1e21b26a24b9e99b174c4c_11.png)

---

![](img/0f81e3704f1e21b26a24b9e99b174c4c_13.png)

![](img/0f81e3704f1e21b26a24b9e99b174c4c_15.png)

## 均值回复型策略的原理

上一节我们介绍了趋势型策略，本节中我们来看看另一种重要的策略类型——均值回复型策略。它与“高抛低吸”的概念类似，但有其独特的理论基础和应用方式。

均值回复效应在多个领域都有体现：
1.  **行为金融学**：投资者常对信息产生**过度反应**或**反应不足**，导致价格偏离内在价值，随后会向均值回归。
2.  **投资实践**：如巴菲特的“别人贪婪时我恐惧，别人恐惧时我贪婪”，本质上是利用市场情绪的极端值进行反向操作，期待均值回归。
3.  **市场周期**：市场常因短期消息产生波动，但长期会回归其基本面决定的趋势。

然而，对于单一个股（如贵州茅台、中国石油），在特定时期内可能呈现强烈的单边趋势，均值回复效应并不明显。因此，我们的策略将基于**全市场股票**的统计特性来构建，利用大数定律捕捉稳定的均值回复效应。

![](img/0f81e3704f1e21b26a24b9e99b174c4c_17.png)

![](img/0f81e3704f1e21b26a24b9e99b174c4c_19.png)

---

![](img/0f81e3704f1e21b26a24b9e99b174c4c_21.png)

![](img/0f81e3704f1e21b26a24b9e99b174c4c_23.png)

![](img/0f81e3704f1e21b26a24b9e99b174c4c_25.png)

## 验证市场中的均值回复效应

![](img/0f81e3704f1e21b26a24b9e99b174c4c_27.png)

![](img/0f81e3704f1e21b26a24b9e99b174c4c_29.png)

![](img/0f81e3704f1e21b26a24b9e99b174c4c_31.png)

![](img/0f81e3704f1e21b26a24b9e99b174c4c_33.png)

在构建策略之前，我们需要先验证A股市场是否存在稳定的、基于收益率的均值回复效应。以下是我们的验证思路：

![](img/0f81e3704f1e21b26a24b9e99b174c4c_35.png)

![](img/0f81e3704f1e21b26a24b9e99b174c4c_37.png)

![](img/0f81e3704f1e21b26a24b9e99b174c4c_39.png)

![](img/0f81e3704f1e21b26a24b9e99b174c4c_41.png)

![](img/0f81e3704f1e21b26a24b9e99b174c4c_43.png)

![](img/0f81e3704f1e21b26a24b9e99b174c4c_45.png)

我们设计一个实验，将时间划分为两个连续的时间段（如各3个月）。
1.  在**时间段一**，计算全市场每只股票的收益率，并进行排名。
2.  构建两个组合：
    *   **组合一**：收益率排名**最高**的前10%股票。
    *   **组合二**：收益率排名**最低**的前10%股票。
3.  在**时间段二**，重新计算这两个组合中每只股票的收益率及其在全市场的新排名。
4.  计算两个组合在时间段二的平均排名。

![](img/0f81e3704f1e21b26a24b9e99b174c4c_47.png)

![](img/0f81e3704f1e21b26a24b9e99b174c4c_49.png)

![](img/0f81e3704f1e21b26a24b9e99b174c4c_51.png)

![](img/0f81e3704f1e21b26a24b9e99b174c4c_53.png)

![](img/0f81e3704f1e21b26a24b9e99b174c4c_55.png)

![](img/0f81e3704f1e21b26a24b9e99b174c4c_57.png)

![](img/0f81e3704f1e21b26a24b9e99b174c4c_59.png)

**核心逻辑**：如果存在均值回复效应，那么：
*   组合一（之前表现最好）在时间段二的平均排名应**接近市场平均水平（如0.5）**。
*   组合二（之前表现最差）在时间段二的平均排名也应**接近市场平均水平（如0.5）**。

我们使用Python和Tushare数据源编写程序进行验证。关键公式是计算股票在观察窗口内的收益率：

![](img/0f81e3704f1e21b26a24b9e99b174c4c_61.png)

`收益率 = (时间段末期股价 - 时间段初期股价) / 时间段初期股价`

![](img/0f81e3704f1e21b26a24b9e99b174c4c_63.png)

![](img/0f81e3704f1e21b26a24b9e99b174c4c_65.png)

验证结果显示，组合一和组合二在第二个时间段的平均排名确实都接近0.5，这证实了全市场范围内存在基于收益率的均值回复效应。

![](img/0f81e3704f1e21b26a24b9e99b174c4c_67.png)

![](img/0f81e3704f1e21b26a24b9e99b174c4c_69.png)

---

## 编写均值回复型股票量化策略

基于上述验证，我们可以设计一个“短期市场反转策略”。

### 策略核心逻辑
*   **做多端**：买入过去一段时间（观察窗口）内**收益率最低**的一篮子股票。
*   **做空端**：卖空过去一段时间内**收益率最高**的一篮子股票。
*   **调仓**：每隔固定的周期（如每月），清空旧持仓，按照最新的收益率排名重新构建上述多空组合。

![](img/0f81e3704f1e21b26a24b9e99b174c4c_71.png)

![](img/0f81e3704f1e21b26a24b9e99b174c4c_73.png)

![](img/0f81e3704f1e21b26a24b9e99b174c4c_75.png)

### 在聚宽平台实现策略（V1.1：纯多头版本）

![](img/0f81e3704f1e21b26a24b9e99b174c4c_77.png)

![](img/0f81e3704f1e21b26a24b9e99b174c4c_79.png)

![](img/0f81e3704f1e21b26a24b9e99b174c4c_81.png)

![](img/0f81e3704f1e21b26a24b9e99b174c4c_83.png)

![](img/0f81e3704f1e21b26a24b9e99b174c4c_85.png)

首先，我们实现一个只做多收益率最低股票的简化版本，以理解基础框架。

![](img/0f81e3704f1e21b26a24b9e99b174c4c_87.png)

![](img/0f81e3704f1e21b26a24b9e99b174c4c_89.png)

![](img/0f81e3704f1e21b26a24b9e99b174c4c_91.png)

![](img/0f81e3704f1e21b26a24b9e99b174c4c_93.png)

![](img/0f81e3704f1e21b26a24b9e99b174c4c_95.png)

以下是策略实现的关键步骤与代码片段：

![](img/0f81e3704f1e21b26a24b9e99b174c4c_97.png)

![](img/0f81e3704f1e21b26a24b9e99b174c4c_99.png)

1.  **初始化设置**：设置基准、复权模式、账户类型（信用账户用于后续融券）和交易成本（初始设为0）。
    ```python
    def initialize(context):
        set_benchmark('000300.XSHG') # 沪深300基准
        set_option('use_real_price', True) # 使用真实价格
        # 设置为信用账户，以便使用融资融券函数
        set_option('account_type', 'stock_margin')
        # 初始设置交易成本为0
        set_order_cost(...)
    ```

![](img/0f81e3704f1e21b26a24b9e99b174c4c_101.png)

![](img/0f81e3704f1e21b26a24b9e99b174c4c_103.png)

2.  **准备交易日历**：获取所有交易日并建立日期与索引的映射，便于快速回溯N个交易日。
    ```python
    trade_dates = get_trade_days()
    date_index_map = {date: idx for idx, date in enumerate(trade_dates)}
    ```

3.  **定时调仓逻辑**：在`before_market_open`函数中判断是否为调仓日，并计算股票池。
    ```python
    def before_market_open(context):
        current_date = context.current_dt.date()
        # 判断是否为调仓日（例如每20个交易日）
        if context.elapse_days % adjust_interval == 0:
            rebalance_today = True
            # 计算观察窗口的起止日期
            tail_idx = date_index_map[current_date] - 1
            head_idx = tail_idx - check_period
            head_date = trade_dates[head_idx]
            tail_date = trade_dates[tail_idx]
            # 计算全市场股票在观察窗口内的收益率并排序
            stock_data_list = []
            for stock in all_stocks:
                # 获取head_date和tail_date的收盘价，计算收益率
                # ...
                stock_data_list.append((stock, return_rate, tail_price))
            # 按收益率升序排序（收益率低的在前）
            sorted_stocks = sorted(stock_data_list, key=lambda x: x[1], reverse=False)
            # 选取收益率最低的前10%作为新的做多股票池
            context.new_long_position = sorted_stocks[:num_to_keep]
    ```

![](img/0f81e3704f1e21b26a24b9e99b174c4c_105.png)

4.  **执行交易**：在`market_open`函数中，如果是调仓日，则先平旧仓，再按等资金分配方式开新仓。
    ```python
    def market_open(context):
        if context.rebalance_today:
            # 平掉所有旧的多头仓位
            for stock in context.portfolio.long_positions:
                order_target(stock, 0)
            # 等资金分配买入新的股票池
            cash_per_stock = context.portfolio.available_cash / len(context.new_long_position)
            for stock, _, price in context.new_long_position:
                # 计算可买股数（100的整数倍）
                shares_to_buy = int(cash_per_stock / price / 100) * 100
                order(stock, shares_to_buy)
    ```

![](img/0f81e3704f1e21b26a24b9e99b174c4c_107.png)

![](img/0f81e3704f1e21b26a24b9e99b174c4c_109.png)

![](img/0f81e3704f1e21b26a24b9e99b174c4c_111.png)

![](img/0f81e3704f1e21b26a24b9e99b174c4c_113.png)

![](img/0f81e3704f1e21b26a24b9e99b174c4c_115.png)

纯多头策略的回测表现平平，这符合预期，因为均值回复效应需要同时利用“赢家组合”的回落和“输家组合”的反弹。

![](img/0f81e3704f1e21b26a24b9e99b174c4c_117.png)

---

## 策略改进一：构建多空双向策略

上一节我们实现了纯多头策略，本节中我们来看看如何将其改进为多空双向策略，以更完整地捕捉均值回复效应。

![](img/0f81e3704f1e21b26a24b9e99b174c4c_119.png)

![](img/0f81e3704f1e21b26a24b9e99b174c4c_121.png)

![](img/0f81e3704f1e21b26a24b9e99b174c4c_123.png)

### 策略逻辑调整
在每次调仓时：
1.  同时持有两个组合：
    *   **多头组合**：做多收益率排名**最低**的N只股票。
    *   **空头组合**：做空收益率排名**最高**的N只股票。
2.  调仓时，同时平掉旧的多头和空头仓位，并建立新的多空仓位。

![](img/0f81e3704f1e21b26a24b9e99b174c4c_125.png)

![](img/0f81e3704f1e21b26a24b9e99b174c4c_127.png)

![](img/0f81e3704f1e21b26a24b9e99b174c4c_129.png)

### 代码修改要点（V2.1版本）
与纯多头版本相比，主要修改在于股票池的构建和交易执行部分：

![](img/0f81e3704f1e21b26a24b9e99b174c4c_131.png)

![](img/0f81e3704f1e21b26a24b9e99b174c4c_133.png)

![](img/0f81e3704f1e21b26a24b9e99b174c4c_135.png)

1.  **构建多空股票池**：
    ```python
    # 排序后，收益率低的在前，高的在后
    sorted_stocks = sorted(stock_data_list, key=lambda x: x[1], reverse=False)
    # 多头池：取前10%（表现最差）
    context.new_long_position = sorted_stocks[:num_to_keep]
    # 空头池：取后10%（表现最好）
    context.new_short_position = sorted_stocks[-num_to_keep:]
    ```

![](img/0f81e3704f1e21b26a24b9e99b174c4c_137.png)

![](img/0f81e3704f1e21b26a24b9e99b174c4c_139.png)

2.  **执行多空交易**：
    ```python
    def market_open(context):
        if context.rebalance_today:
            # 平旧仓
            for stock in context.portfolio.long_positions:
                margin_close(stock) # 平多仓
            for stock in context.portfolio.short_positions:
                margin_close(stock) # 平空仓（买回证券）
            # 开新仓
            cash_per_long = total_cash * 0.5 / len(context.new_long_position)
            cash_per_short = total_cash * 0.5 / len(context.new_short_position)
            for stock, _, price in context.new_long_position:
                shares = int(cash_per_long / price / 100) * 100
                margin_open(stock, shares) # 融资买入
            for stock, _, price in context.new_short_position:
                shares = int(cash_per_short / price / 100) * 100
                margin_open(stock, -shares) # 融券卖出（做空）
    ```

![](img/0f81e3704f1e21b26a24b9e99b174c4c_141.png)

多空双向策略的回测曲线表现稳健，在震荡市中表现优异。在单边牛市中，由于做空了持续上涨的“赢家组合”，策略会跑输指数；在单边熊市中，做空“赢家组合”则能带来显著收益。这印证了策略逻辑的合理性。

---

![](img/0f81e3704f1e21b26a24b9e99b174c4c_143.png)

## 策略改进二：考虑交易成本的影响

上一节我们构建了多空策略，但忽略了交易成本。本节中我们来看看加入真实交易成本后，策略表现会发生什么变化。

### 主要交易成本
在股票融资融券交易中，成本主要包括：
1.  **佣金与印花税**：券商佣金（如万分之三）、交易所规费、卖出时的印花税（千分之一）。
2.  **融资融券成本**：融券的年化利息（约8%-10%），融资的利息（若使用）。
3.  **保证金要求**：融券通常需要更高的保证金比例（如150%）。
4.  **滑点与冲击成本**：大额订单对市场价格的瞬时影响。

### 在聚宽中设置成本（V2.2版本）
```python
def initialize(context):
    # 设置交易佣金、印花税等
    set_order_cost(OrderCost(open_tax=0, close_tax=0.001, # 印花税
                             open_commission=0.0003, close_commission=0.0003, # 佣金
                             min_commission=5),
                   type='stock')
    # 设置融资融券利率和保证金比例
    set_option('margin_interest_rate', 0.00) # 融资利率，普通做多可设为0
    set_option('short_margin_interest_rate', 0.10) # 融券利率，年化10%
    set_option('short_margin_ratio', 1.5) # 融券保证金比例 150%
```

![](img/0f81e3704f1e21b26a24b9e99b174c4c_145.png)

![](img/0f81e3704f1e21b26a24b9e99b174c4c_147.png)

![](img/0f81e3704f1e21b26a24b9e99b174c4c_149.png)

加入成本后，策略年化收益率从约10%下降至约5.5%。**主要原因**是融券产生了年化约10%的利息成本。由于策略约一半资金用于融券做空，仅此一项就侵蚀了约5%的年化收益。这个结果符合理论估算，说明成本分析至关重要。

![](img/0f81e3704f1e21b26a24b9e99b174c4c_151.png)

![](img/0f81e3704f1e21b26a24b9e99b174c4c_153.png)

![](img/0f81e3704f1e21b26a24b9e99b174c4c_155.png)

---

## 策略改进三：基于波动率的头寸管理优化

上一节我们考虑了成本，本节我们从头寸管理的角度进一步优化策略。我们将把固定的等资金分配，改为基于波动率的动态资金分配。

### 头寸管理原理
目标是使每只股票承担的风险（以波动率衡量）对总资金的贡献相等。我们使用**平均真实波幅（ATR）** 作为波动率的度量。
*   **ATR计算**：首先计算每日的真实波幅 `TR = Max(当日最高-最低, |当日最高-前收|, |当日最低-前收|)`，然后在N日（如20日）窗口内求平均，得到ATR。

![](img/0f81e3704f1e21b26a24b9e99b174c4c_157.png)

头寸分配公式推导如下：
设总资金为 `C`，每只股票承担的风险比例为 `R`，股票i的价格为 `P_i`，波动率（ATR）为 `V_i`。
若全仓买入所有M只候选股票，则有：
`C = Σ_{i=1}^{M} [ (C * R) / V_i ] * P_i`
可解得：
`R = 1 / Σ_{i=1}^{M} (P_i / V_i)`
则每只股票应买入的股数 `K_i` 为：
`K_i = int( (C * R / V_i) / 100 ) * 100` （A股需整手交易）

![](img/0f81e3704f1e21b26a24b9e99b174c4c_159.png)

![](img/0f81e3704f1e21b26a24b9e99b174c4c_161.png)

### 代码实现修改（V3.1版本：纯多头波动率管理）
主要修改在计算股票池和计算买入股数部分：

![](img/0f81e3704f1e21b26a24b9e99b174c4c_163.png)

1.  **计算每只股票的ATR**：
    ```python
    # 获取最近20个交易日的价格数据
    prices = get_price(stock, end_date=tail_date, count=atr_window+1, fields=['high','low','close'])
    # 计算TR
    tr = prices.apply(lambda row: max(row['high']-row['low'],
                                      abs(row['high']-row['close'].shift(1)),
                                      abs(row['low']-row['close'].shift(1))), axis=1)
    atr_value = tr[-atr_window:].mean() # 计算最后20日的ATR均值
    ```

![](img/0f81e3704f1e21b26a24b9e99b174c4c_165.png)

![](img/0f81e3704f1e21b26a24b9e99b174c4c_167.png)

![](img/0f81e3704f1e21b26a24b9e99b174c4c_169.png)

![](img/0f81e3704f1e21b26a24b9e99b174c4c_171.png)

![](img/0f81e3704f1e21b26a24b9e99b174c4c_173.png)

2.  **计算总风险比例R和每只股票头寸**：
    ```python
    # 计算 Σ(P_i / V_i)
    sum_p_over_v = sum(price / atr for _, _, price, atr in candidate_stocks)
    risk_per_stock = 1.0 / sum_p_over_v # 即 R
    # 计算每只股票应买入股数
    for stock, _, price, atr in candidate_stocks:
        shares_to_buy = int((total_cash * risk_per_stock / atr) / 100) * 100
        order(stock, shares_to_buy)
    ```

![](img/0f81e3704f1e21b26a24b9e99b174c4c_175.png)

使用基于波动率的头寸管理后，与等资金分配相比，策略在年化收益、夏普比率、盈亏比和最大回撤等关键指标上均获得了改善。这证明了精细化的资金管理能有效提升策略风险调整后收益。

---

## 总结与展望

本节课中，我们一起学习了均值回复型量化策略从理论到实战的全过程：
1.  **提出并验证假设**：通过编写程序验证了A股全市场存在基于收益率的均值回复效应。
2.  **策略开发**：在聚宽平台实现了从纯多头到多空双向的均值回复策略。
3.  **成本分析**：评估了交易成本（尤其是融券成本）对策略绩效的重大影响。
4.  **策略优化**：引入了基于ATR波动率的头寸管理方法，有效提升了策略的各项绩效指标。

![](img/0f81e3704f1e21b26a24b9e99b174c4c_177.png)

这是一个策略迭代研发的典型范例。一个策略思想需要经过严谨的验证、细致的实现、全面的成本考量以及持续的参数与风险管理优化，才可能具备实战价值。

![](img/0f81e3704f1e21b26a24b9e99b174c4c_179.png)

### 课后思考与作业
1.  **策略优化**：尝试对本课的策略进行改进，例如：调整调仓频率、加入技术指标择时、实现重叠调仓以减少不必要的交易、或按行业板块细分股票池。
2.  **深入验证**：修改验证程序中的参数（如时间段长度、起始时间），观察均值回复效应在不同市场周期和观察尺度下的稳定性。

### 下节预告
下节课我们将更换平台，使用 **MultiCharts** 来学习**配对交易/套利策略**的设计与开发。请同学们提前下载软件并熟悉其基本操作。

![](img/0f81e3704f1e21b26a24b9e99b174c4c_181.png)

---
*提示：量化策略研发复杂，本教程内容仅作为学习参考。实际应用需充分考虑市场环境、交易制度、个人风险承受能力等因素，并进行充分的模拟测试。*