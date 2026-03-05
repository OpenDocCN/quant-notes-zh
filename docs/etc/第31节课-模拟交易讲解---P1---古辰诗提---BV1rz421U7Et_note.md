# VNPY量化课程：第31课：模拟交易与无界面应用开发

在本节课中，我们将学习如何基于VN.PY框架进行更高级的应用开发，主要包括两部分：如何构建一个无界面（No-UI）的自动化交易程序，以及理解VN.PY内置的模拟交易模块（Paper Account）的核心原理。通过本节课，你将能够将所学知识融会贯通，搭建自己的自动化交易系统。

![](img/7346dd3d15aaaca2a64ead55328b42da_1.png)

## 框架回顾与进阶思路

![](img/7346dd3d15aaaca2a64ead55328b42da_3.png)

上一节我们介绍了CTA策略的框架与引擎。现在，我们来回顾并深化对VN.PY整体架构的理解。

VN.PY的核心框架由几个关键部分组成：
*   **事件引擎（Event Engine）**：作为数据流的核心，负责所有事件（如行情、成交、日志）的驱动和分发。
*   **主引擎（Main Engine）**：框架的主体，封装了大量方法，用于连接上游的**网关（Gateway）**和下游的**应用（App）**。
*   **应用（App）**：例如CTA策略引擎（实盘）、CTA回测引擎等，实现具体的交易或分析功能。

理解这个框架后，你可以做很多事情，例如查看VN.PY的其他应用模块，或者根据需求自定义组件。编程就像搭积木，你已经掌握了基础的积木块（Python知识、VN.PY框架），现在需要学习如何将它们组合起来解决实际问题。

## 构建无界面（No-UI）自动化程序

一个常见的需求是开发一个无需图形界面、能自动定时启动和关闭的交易程序。这能避免手动操作的麻烦，实现真正的程序化。以下是构建这样一个程序的核心思路和步骤。

首先，你需要理清主要任务：搭建框架、导入策略、加载并运行策略。以下是关键代码结构的演示：

1.  **导入必要的模块并初始化核心引擎**
    ```python
    from vnpy.event import EventEngine
    from vnpy.trader.engine import MainEngine
    from vnpy_ctp import CtpGateway
    from vnpy_ctastrategy import CtaStrategyApp
    from vnpy_ctastrategy.base import EVENT_CTA_LOG
    import time

    # 创建事件引擎和主引擎
    event_engine = EventEngine()
    main_engine = MainEngine(event_engine)
    main_engine.write_log("主引擎创建成功")
    ```

2.  **添加交易接口和策略应用**
    ```python
    # 添加CTP交易接口
    ctp_gateway = main_engine.add_gateway(CtpGateway)
    main_engine.write_log("CTP接口创建成功")

    # 添加CTA策略应用
    cta_engine = main_engine.add_app(CtaStrategyApp)
    main_engine.write_log("CTA引擎创建成功")
    ```

![](img/7346dd3d15aaaca2a64ead55328b42da_5.png)

![](img/7346dd3d15aaaca2a64ead55328b42da_7.png)

3.  **注册CTA日志到主引擎的日志引擎**
    CTA引擎内部的日志类型（`EVENT_CTA_LOG`）需要单独注册到主引擎的日志引擎才能统一输出。
    ```python
    log_engine = main_engine.get_engine("log")
    log_engine.register(EVENT_CTA_LOG, log_engine.process_log_event)
    main_engine.write_log("CTA日志监听事件加载成功")
    ```

4.  **连接交易接口并初始化策略**
    ```python
    # 连接CTP接口（此处需要填写你的账户配置字典）
    settings = {
        "用户名": "your_username",
        "密码": "your_password",
        "经纪商代码": "your_broker",
        "交易服务器": "tcp://xxx.xxx.xxx.xxx:yyyy",
        "行情服务器": "tcp://xxx.xxx.xxx.xxx:zzzz",
        "产品名称": "your_product",
        "授权编码": "your_auth_code"
    }
    ctp_gateway.connect(settings)
    time.sleep(10)  # 等待连接
    main_engine.write_log("CTP连接成功")

    # 初始化CTA引擎和数据
    cta_engine.init_engine()
    main_engine.write_log("CTA引擎初始化完成")

    # 初始化所有策略（这里可能需要较长时间，取决于策略数量和数据下载）
    cta_engine.init_all_strategies()
    time.sleep(60)  # 等待策略初始化，实际应用中建议使用更智能的等待方式
    main_engine.write_log("所有策略初始化完成")

    # 启动所有策略
    cta_engine.start_all_strategies()
    main_engine.write_log("所有策略启动")
    ```

5.  **保持主进程运行并实现定时控制**
    程序的主线程或进程必须持续运行，否则事件循环会停止。通常我们会将其放入一个循环中，并结合时间判断来实现定时开关。
    ```python
    # 定义交易时间段
    TRADING_PERIODS = [
        ("09:00:00", "11:30:00"),
        ("13:00:00", "15:00:00"),
        ("21:00:00", "23:00:00")
    ]

    def is_trading_time():
        """检查当前时间是否在交易时段内"""
        from datetime import datetime
        now = datetime.now().time()
        for start, end in TRADING_PERIODS:
            if datetime.strptime(start, "%H:%M:%S").time() <= now <= datetime.strptime(end, "%H:%M:%S").time():
                return True
        return False

    # 主循环
    try:
        while True:
            if is_trading_time():
                # 确保策略在运行
                if not cta_engine.active:
                    cta_engine.start_all_strategies()
            else:
                # 非交易时间停止策略
                if cta_engine.active:
                    cta_engine.stop_all_strategies()
            time.sleep(60)  # 每分钟检查一次
    except KeyboardInterrupt:
        # 程序退出前清理
        cta_engine.stop_all_strategies()
        cta_engine.clear()
        main_engine.write_log("程序安全退出")
    ```

![](img/7346dd3d15aaaca2a64ead55328b42da_9.png)

你可以将上述逻辑封装成一个类或脚本，并部署在服务器上实现7x24小时运行。VN.PY官方Git仓库（`vnpy`）中也提供了`no_ui`的示例代码，可供参考。

![](img/7346dd3d15aaaca2a64ead55328b42da_11.png)

## 理解模拟交易（Paper Account）模块

模拟交易（Paper Trading）是策略开发中不可或缺的一环。它与回测（Backtesting）的主要区别在于：**模拟交易接收实时的行情数据（Tick）进行模拟撮合**，而回测使用历史数据。

VN.PY的模拟交易模块位于 `vnpy_paperaccount`。其核心原理是拦截本应发送给实盘网关（如CTP）的委托订单，在本地根据实时行情进行模拟撮合，并维护一个虚拟的账户和持仓状态。

### 核心流程剖析

模拟交易模块本质上是一个特殊的App（`PaperAccountApp`），它内部有一个引擎（`PaperEngine`）。其工作流程如下：

1.  **拦截委托**：当策略通过主引擎发送委托（`send_order`）时，PaperAccount模块会将其截获，而不是转发给真实的交易接口。
2.  **存储与撮合**：将委托存储在活跃订单列表（`active_orders`）中。每当新的行情Tick到达时，遍历所有活跃委托，检查是否满足成交条件。
3.  **模拟成交逻辑**：成交逻辑与回测中的撮合逻辑类似，主要判断委托价格与市场行情价格的关系：
    *   **市价单（MARKET）**：通常以当前对手价（加/减滑点）立即成交。
    *   **限价单（LIMIT）**：当市场价格达到或优于委托价时成交。
    *   **停止单（STOP）**：当市场价格突破设定价位时，转为市价单成交。
    ```python
    # 简化的限价多单成交判断逻辑
    if order.direction == Direction.LONG and tick.ask_price_1 <= order.price:
        trade_price = tick.ask_price_1  # 以卖一价成交
        # ... 生成成交记录并更新持仓
    ```
4.  **维护虚拟账户**：模块内部维护一个虚拟持仓字典（`positions`），以 `(vt_symbol, direction)` 为键。当有模拟成交发生时，更新对应的持仓数量、盈亏等数据。
5.  **推送事件**：将模拟成交（`TradeData`）、持仓更新（`PositionData`）等事件推送到事件引擎，从而通知策略，使策略状态与模拟账户同步。

### 关键代码结构浏览

在 `paper_engine.py` 中，你可以看到以下关键方法，它们构成了模拟交易的核心：

*   `__init__`: 初始化，设置映射关系（`gateway_map`，用于支持多底层接口），并加载本地保存的模拟账户设置（`settings`）和数据（`data`，如持仓）。
*   `register_event`: 注册监听合约事件（`EVENT_CONTRACT`）、行情事件（`EVENT_TICK`）和定时器事件（`EVENT_TIMER`）。
*   `process_tick_event`: 处理行情，更新最新Tick，并尝试对活跃订单进行撮合（`cross_order`）。
*   `send_order`: 处理策略发来的委托请求，进行风控检查（如是否超仓），然后存入活跃订单列表。
*   `cross_order`: 核心撮合函数，根据订单类型和当前Tick价格判断是否成交。
*   `update_position`: 成交后，更新虚拟持仓数据。
*   `process_timer_event`: 定时计算持仓浮动盈亏（`calculate_pnl`）并推送更新。

### 需要注意的细节

*   **数据持久化**：模拟账户的持仓和资金信息会通过 `save_data` 方法保存到本地文件（如 `paper_account_data.json`），确保程序重启后状态得以恢复。
*   **深拷贝（Deep Copy）**：在将订单对象放入事件引擎前，代码常使用 `copy` 方法。这是因为Python中对象赋值是引用传递，防止在后续处理中意外修改原始订单对象的状态。
*   **多接口支持**：代码中通过 `gateway_map` 来处理策略可能连接多个实盘接口（如一个CTP，一个易盛）的情况，确保模拟订单能正确映射到其原本的合约和接口上下文。

## 总结

本节课我们一起学习了两个重要的进阶主题：**无界面自动化程序的搭建**和**模拟交易模块的原理**。

*   通过构建No-UI程序，我们实践了如何将VN.PY框架作为“积木”，组合出满足特定需求（定时自动运行）的应用程序，这是将策略投入实际生产环境的关键一步。
*   通过剖析Paper Account模块，我们理解了模拟交易如何通过截获委托、基于实时行情撮合、维护虚拟状态来模拟真实交易过程。这不仅能用于策略验证，其代码本身也是学习如何扩展VN.PY功能的优秀范例。

![](img/7346dd3d15aaaca2a64ead55328b42da_13.png)

至此，你已经掌握了从策略编写、回测、模拟交易到自动化部署的完整链条。接下来，你可以尝试独立实现一个简单的模拟交易模块，或者优化No-UI程序的时间控制逻辑，从而深化对VN.PY框架和程序化交易系统的理解。