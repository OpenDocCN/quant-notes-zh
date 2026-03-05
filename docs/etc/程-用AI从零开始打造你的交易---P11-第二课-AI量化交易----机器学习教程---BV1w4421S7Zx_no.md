# AI量化交易：P11：第二课【AI量化交易】第二章 交易数据形成的价格

![](img/12d14db7cd7a337cb35d5838541b418b_1.png)

![](img/12d14db7cd7a337cb35d5838541b418b_2.png)

![](img/12d14db7cd7a337cb35d5838541b418b_3.png)

## 概述
在本节课中，我们将学习如何从交易所获取原始的tick数据，并理解整个交易系统数据的产生过程。我们将重点介绍上海期货交易所的综合交易平台（CTP），学习如何使用其API来获取市场行情数据，为后续构建回测系统和交易策略打下基础。

---

## 课程反馈与作业回顾
上一节我们介绍了市场微观结构和tick数据。本节中，我们首先回顾一下第一次课程的反馈和作业情况。

以下是针对第一次作业的几点总结与改进建议：

![](img/12d14db7cd7a337cb35d5838541b418b_5.png)

![](img/12d14db7cd7a337cb35d5838541b418b_7.png)

1.  **作业难度与时间评估**：建议大家在提交作业时，对作业难度进行0-10分的评分，并注明所花费的时间。这有助于课程调整，确保核心内容不被过难的作业占用过多时间。
2.  **编程基础**：对于编程基础较弱的同学，起步可以慢一些，但最重要的是跟上课程节奏。课程前期会打好量化投资的基础，后续再逐步引入AI模型。
3.  **数据处理经验**：量化交易中，很多数据处理规则是经验性的。例如，在处理数据时需要注意集合竞价、夜盘时间、涨跌停等特殊情况的处理。这些规则需要大家在实践中不断积累和完善。

![](img/12d14db7cd7a337cb35d5838541b418b_9.png)

![](img/12d14db7cd7a337cb35d5838541b418b_11.png)

![](img/12d14db7cd7a337cb35d5838541b418b_13.png)

我们挑选了两位同学的作业进行展示，他们的代码结构清晰，逻辑完整，值得大家参考学习。特别是刘宁谦同学，他是第一个提交作业的，这种积极性值得鼓励。

![](img/12d14db7cd7a337cb35d5838541b418b_15.png)

![](img/12d14db7cd7a337cb35d5838541b418b_17.png)

![](img/12d14db7cd7a337cb35d5838541b418b_19.png)

![](img/12d14db7cd7a337cb35d5838541b418b_21.png)

---

![](img/12d14db7cd7a337cb35d5838541b418b_23.png)

![](img/12d14db7cd7a337cb35d5838541b418b_25.png)

![](img/12d14db7cd7a337cb35d5838541b418b_27.png)

![](img/12d14db7cd7a337cb35d5838541b418b_29.png)

![](img/12d14db7cd7a337cb35d5838541b418b_31.png)

![](img/12d14db7cd7a337cb35d5838541b418b_33.png)

![](img/12d14db7cd7a337cb35d5838541b418b_35.png)

![](img/12d14db7cd7a337cb35d5838541b418b_37.png)

![](img/12d14db7cd7a337cb35d5838541b418b_39.png)

## 市场数据：从Tick到Bar的进阶
上一节我们介绍了如何将tick数据合成为分钟Bar。本节中我们来看看其他几种合成Bar的方式，它们可能为机器学习提供更好的数据特征。

![](img/12d14db7cd7a337cb35d5838541b418b_41.png)

![](img/12d14db7cd7a337cb35d5838541b418b_43.png)

![](img/12d14db7cd7a337cb35d5838541b418b_45.png)

![](img/12d14db7cd7a337cb35d5838541b418b_47.png)

![](img/12d14db7cd7a337cb35d5838541b418b_49.png)

除了按时间切分的Tick Bar，还有两种常见的Bar合成方式：

![](img/12d14db7cd7a337cb35d5838541b418b_51.png)

*   **成交量Bar (Volume Bar)**：不按时间，而是按固定的成交量进行切分。例如，每成交10万手合成一个Bar。其动机在于，一个Tick内成交100手与十个Tick内各成交10手，在时间序列上可能被视为不同的样本，Volume Bar试图平衡这种差异。
*   **成交额Bar (Dollar Bar)**：按固定的成交额进行切分。其公式为：`成交额 = 价格 × 成交量`。相比Volume Bar，它进一步考虑了价格因素，因为同样成交100手，在3500元和3600元价位流入的市场资金是不同的。

研究表明，Volume Bar和Dollar Bar的收益率序列可能比Tick Bar更接近独立同分布的高斯分布，这有助于缓解金融时间序列中常见的自相关问题。有兴趣的同学可以自行验证这一特性。

**核心概念公式**：
*   Tick Bar: 按固定时间间隔 `Δt` 切分。
*   Volume Bar: 按固定成交量阈值 `V_threshold` 切分。
*   Dollar Bar: 按固定成交额阈值 `D_threshold` 切分，其中 `D = Price × Volume`。

关于Bar的合成，特别是在开盘、收盘边界或当日成交量未达到阈值时如何处理，并没有固定答案。这取决于研究目的和个人习惯，需要大家在实践中探索和验证。

---

## 理解交易平台：CTP架构
在掌握了数据本身后，我们需要了解数据是如何从交易所传递到我们手中的。接下来，我们将深入讲解上海期货交易所的综合交易平台（CTP）。

![](img/12d14db7cd7a337cb35d5838541b418b_53.png)

![](img/12d14db7cd7a337cb35d5838541b418b_55.png)

![](img/12d14db7cd7a337cb35d5838541b418b_57.png)

![](img/12d14db7cd7a337cb35d5838541b418b_59.png)

CTP平台主要包含三大部分：

1.  **交易系统 (Trading System)**：这是我们量化交易程序直接交互的部分。它负责接收行情(Market Data)和报单/撤单(Order Management)。本节课我们主要关注如何通过它获取行情数据。
2.  **风控系统 (Risk Management)**：由期货公司负责。主要功能是验资（检查账户资金是否足够）、监控保证金比例和杠杆水平，防止过度交易和穿仓风险，保护投资者。
3.  **结算系统 (Settlement)**：由交易所负责。每日收盘后提供结算单，清晰记录所有成交明细和持仓汇总。交易者需要做“对账”(Reconcile)，确保自己系统的记录与交易所的结算单一致，这是风险控制中至关重要的一环。

![](img/12d14db7cd7a337cb35d5838541b418b_61.png)

![](img/12d14db7cd7a337cb35d5838541b418b_62.png)

![](img/12d14db7cd7a337cb35d5838541b418b_63.png)

![](img/12d14db7cd7a337cb35d5838541b418b_64.png)

![](img/12d14db7cd7a337cb35d5838541b418b_65.png)

![](img/12d14db7cd7a337cb35d5838541b418b_66.png)

![](img/12d14db7cd7a337cb35d5838541b418b_67.png)

![](img/12d14db7cd7a337cb35d5838541b418b_68.png)

![](img/12d14db7cd7a337cb35d5838541b418b_69.png)

![](img/12d14db7cd7a337cb35d5838541b418b_70.png)

![](img/12d14db7cd7a337cb35d5838541b418b_71.png)

![](img/12d14db7cd7a337cb35d5838541b418b_72.png)

![](img/12d14db7cd7a337cb35d5838541b418b_73.png)

![](img/12d14db7cd7a337cb35d5838541b418b_74.png)

![](img/12d14db7cd7a337cb35d5838541b418b_75.png)

![](img/12d14db7cd7a337cb35d5838541b418b_76.png)

![](img/12d14db7cd7a337cb35d5838541b418b_78.png)

---

![](img/12d14db7cd7a337cb35d5838541b418b_80.png)

![](img/12d14db7cd7a337cb35d5838541b418b_82.png)

## CTP数据流与通信模式
要使用CTP API，需要理解其数据交换的几种模式：

*   **对话模式 (Dialogue Mode)**：由客户端主动发起请求(Request)，服务器响应(Response)。例如查询合约信息、查询资金等。
*   **私有模式 (Private Mode)**：由服务器主动向特定客户端推送消息。例如报单成交回报、资金变动等，这些信息是私密的。
*   **广播模式 (Broadcast Mode)**：由服务器向所有订阅的客户端广播消息。**市场行情数据(Market Data)** 就是通过这种模式推送的，以保证公平性。

对于量化交易，降低延迟是关键。延迟主要产生在：`客户端 <-> 券商柜台 <-> 交易所` 这几个环节。将交易程序部署在券商托管机房（即“托管”），可以极大缩短物理距离，降低网络延迟。

![](img/12d14db7cd7a337cb35d5838541b418b_84.png)

![](img/12d14db7cd7a337cb35d5838541b418b_86.png)

---

## 实战：使用CTP API获取行情数据
理论铺垫完成后，本节我们将进入实战环节，学习如何使用CTP的行情API（MD API）来获取实时tick数据。

![](img/12d14db7cd7a337cb35d5838541b418b_88.png)

![](img/12d14db7cd7a337cb35d5838541b418b_90.png)

CTP API以C++动态库的形式提供。我们主要需要关注两个头文件：
*   `ThostFtdcMdApi.h` (行情接口)
*   `ThostFtdcTraderApi.h` (交易接口)

![](img/12d14db7cd7a337cb35d5838541b418b_92.png)

![](img/12d14db7cd7a337cb35d5838541b418b_94.png)

今天我们先专注于行情接口。使用流程可以概括为以下几个步骤：

![](img/12d14db7cd7a337cb35d5838541b418b_96.png)

1.  **创建并继承C++类**：我们需要创建一个类（例如 `CMyMdHandler`），继承自CTP提供的 `CThostFtdcMdSpi` 类。这个类是回调函数的接口。
2.  **实现回调函数**：在子类中实现关键的虚函数。对我们获取行情而言，最重要的是 `OnRtnDepthMarketData` 函数。每当有新的行情tick到来，CTP就会自动调用这个函数。
    ```cpp
    // 示例回调函数结构
    void CMyMdHandler::OnRtnDepthMarketData(CThostFtdcDepthMarketDataField *pDepthMarketData) {
        // 在这里处理接收到的行情数据 pDepthMarketData
        // 例如：打印、存入数据库或写入文件
        std::cout << “合约代码:” << pDepthMarketData->InstrumentID << “, 最新价:” << pDepthMarketData->LastPrice << std::endl;
    }
    ```
3.  **初始化与连接**：
    *   创建 `CThostFtdcMdApi` 实例。
    *   用我们自定义的 `CMyMdHandler` 实例向API注册回调接口。
    *   调用 `RegisterFront` 函数连接券商提供的行情前置服务器地址。
    *   调用 `Init` 初始化，然后调用 `ReqUserLogin` 登录。
4.  **订阅行情**：登录成功后，调用 `SubscribeMarketData` 函数订阅我们感兴趣的合约代码。
5.  **接收与处理数据**：订阅成功后，行情数据就会通过回调函数 `OnRtnDepthMarketData` 源源不断地推送过来。

![](img/12d14db7cd7a337cb35d5838541b418b_98.png)

![](img/12d14db7cd7a337cb35d5838541b418b_100.png)

![](img/12d14db7cd7a337cb35d5838541b418b_102.png)

**核心步骤代码框架**：
```cpp
// 1. 创建API实例
CThostFtdcMdApi* pUserApi = CThostFtdcMdApi::CreateFtdcMdApi();
// 2. 创建事件处理实例（自定义类）
CMyMdHandler myHandler;
// 3. 注册事件处理实例
pUserApi->RegisterSpi(&myHandler);
// 4. 注册前置机地址并初始化
pUserApi->RegisterFront(“tcp://xxx.xxx.xxx.xxx:xxxx”);
pUserApi->Init();
// 登录等后续操作...
```

**作业目标**：请大家根据以上流程，实现一个简单的CTP行情接收程序，至少能接收并保存一个期货合约（如螺纹钢RB）的tick数据。将接收到的数据与之前课程提供的第三方数据进行对比，验证一致性。

![](img/12d14db7cd7a337cb35d5838541b418b_104.png)

---

## 面试题与概率问题解析
课程最后，我们回顾并解析两道与量化金融相关的面试题。

![](img/12d14db7cd7a337cb35d5838541b418b_106.png)

**1. 数字猜测游戏（博弈论）**
问题：你从1-100中随机选一个数N。我猜一个数K。如果我猜对了(K=N)，你付我K元；如果猜错了，我不付钱。我应该采取什么策略最大化期望收益？

![](img/12d14db7cd7a337cb35d5838541b418b_108.png)

![](img/12d14db7cd7a337cb35d5838541b418b_110.png)

![](img/12d14db7cd7a337cb35d5838541b418b_112.png)

![](img/12d14db7cd7a337cb35d5838541b418b_114.png)

![](img/12d14db7cd7a337cb35d5838541b418b_116.png)

![](img/12d14db7cd7a337cb35d5838541b418b_118.png)

![](img/12d14db7cd7a337cb35d5838541b418b_119.png)

**解析**：这是一个博弈问题。关键在于找到一个策略，使得我的期望收益与你选择的N无关。最优策略是以概率 `p(K) ∝ 1/K` 去猜数字K，并进行归一化。这样，无论你选哪个N，我的期望收益都是相同的。这避免了因对方选择策略而带来的不确定性。

**2. 计算最大回撤（动态规划）**
问题：给定一个价格序列 `prices[]`，计算其最大回撤(Maximum Drawdown)。

![](img/12d14db7cd7a337cb35d5838541b418b_121.png)

![](img/12d14db7cd7a337cb35d5838541b418b_123.png)

![](img/12d14db7cd7a337cb35d5838541b418b_124.png)

![](img/12d14db7cd7a337cb35d5838541b418b_125.png)

![](img/12d14db7cd7a337cb35d5838541b418b_127.png)

![](img/12d14db7cd7a337cb35d5838541b418b_129.png)

**解析**：最大回撤是投资过程中资产净值从峰值到谷底的最大跌幅。高效的计算方法是使用一次遍历（动态规划思想）：
*   维护一个变量 `peak`，记录到当前位置的历史最高价。
*   维护一个变量 `max_dd`，记录历史最大回撤。
*   遍历价格序列，更新：`current_dd = (peak - prices[i]) / peak`，然后 `max_dd = max(max_dd, current_dd)`，同时 `peak = max(peak, prices[i])`。
*   时间复杂度为O(n)。

![](img/12d14db7cd7a337cb35d5838541b418b_131.png)

---

![](img/12d14db7cd7a337cb35d5838541b418b_133.png)

![](img/12d14db7cd7a337cb35d5838541b418b_135.png)

## 本周作业与思考题
**主要作业**：
1.  实现CTP行情API的对接程序，收集指定合约的tick数据。
2.  将收集的数据与已有数据源进行对比验证。

![](img/12d14db7cd7a337cb35d5838541b418b_137.png)

![](img/12d14db7cd7a337cb35d5838541b418b_139.png)

![](img/12d14db7cd7a337cb35d5838541b418b_141.png)

![](img/12d14db7cd7a337cb35d5838541b418b_143.png)

![](img/12d14db7cd7a337cb35d5838541b418b_145.png)

**思考题**：
1.  **随机游走停时问题**：一个随机游走在区间[0, 1000]内，从80开始，每一步向上/向下移动1的概率各为1/2。当到达0或1000时停止。求最终停在0的概率。（提示：考虑鞅(Martingale)或蒙特卡洛模拟）
2.  **协方差矩阵性质**：证明任意随机向量对应的协方差矩阵是半正定(Semi-positive Definite)的。复习线性代数和统计学的基本概念。

---

![](img/12d14db7cd7a337cb35d5838541b418b_147.png)

![](img/12d14db7cd7a337cb35d5838541b418b_149.png)

## 总结
本节课中我们一起学习了量化交易数据流的源头。我们从回顾作业和数据处理经验开始，探讨了Tick Bar之外的Volume Bar和Dollar Bar等概念。接着，我们深入了解了国内主流量化交易接口CTP的架构、通信模式和数据流。最后，我们详细讲解了如何使用CTP的行情API（MD API）来编程获取实时的市场tick数据，并解析了相关的面试题。

![](img/12d14db7cd7a337cb35d5838541b418b_151.png)

通过本节课，大家应该对“市场数据如何从交易所产生并到达我们手中”有了完整的认识。这是构建自动化交易系统最基础也是最重要的一步。接下来两周，我们将基于已有的数据，开始构建回测系统，敬请期待。