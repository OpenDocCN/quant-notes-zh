# 量化交易实战：4.1：策略信号对接券商自动下单 📈

![](img/8be2686e012ee8ec0eb90ab8f05f497c_1.png)

在本节课中，我们将学习如何将量化策略生成的交易信号，自动对接到券商的QMT软件进行下单，实现策略的自动化交易执行。

![](img/8be2686e012ee8ec0eb90ab8f05f497c_3.png)

---

![](img/8be2686e012ee8ec0eb90ab8f05f497c_5.png)

## 概述

![](img/8be2686e012ee8ec0eb90ab8f05f497c_7.png)

上一节我们介绍了如何快速生成策略信号。本节中，我们来看看如何将这些信号与券商的交易系统连接起来，实现自动下单。整个过程主要分为三个步骤：建立交易连接、整合策略代码、以及设置定时运行程序。

![](img/8be2686e012ee8ec0eb90ab8f05f497c_9.png)

---

![](img/8be2686e012ee8ec0eb90ab8f05f497c_11.png)

## 第一步：导入并测试交易功能包

![](img/8be2686e012ee8ec0eb90ab8f05f497c_13.png)

首先，我们需要导入一个封装好的交易功能包，以便与QMT软件进行通信。只需一行代码即可完成导入。

![](img/8be2686e012ee8ec0eb90ab8f05f497c_15.png)

```python
import mikmt_trade
```

![](img/8be2686e012ee8ec0eb90ab8f05f497c_17.png)

这个功能包已经将交易所需的所有功能封装好，可以直接使用。使用前，请确保您本机已登录券商的QMT软件。

![](img/8be2686e012ee8ec0eb90ab8f05f497c_19.png)

接下来，我们运行代码测试连接。首先查询账户资金。

![](img/8be2686e012ee8ec0eb90ab8f05f497c_21.png)

运行后，如果成功查询到资金信息，则表明交易连接已建立。

![](img/8be2686e012ee8ec0eb90ab8f05f497c_23.png)

接着，我们尝试进行买入下单操作。

![](img/8be2686e012ee8ec0eb90ab8f05f497c_25.png)

```python
# 示例：买入下单
order_id = mikmt_trade.buy(stock_code='000001', price=10.0, volume=100)
```

![](img/8be2686e012ee8ec0eb90ab8f05f497c_27.png)

由于当前是周末（非交易时间），在模拟测试环境中下单会失败，但会返回一个委托编号。这个委托编号表明下单指令已成功发送至交易系统，只是因非交易时段而未能成交。这证明了交易连接是成功的。

![](img/8be2686e012ee8ec0eb90ab8f05f497c_29.png)

---

## 第二步：整合策略代码

![](img/8be2686e012ee8ec0eb90ab8f05f497c_31.png)

要将“反包大阴线”策略的信号用于自动交易，还需要进行一些准备工作。

![](img/8be2686e012ee8ec0eb90ab8f05f497c_33.png)

以下是需要完成的工作列表：

![](img/8be2686e012ee8ec0eb90ab8f05f497c_35.png)

![](img/8be2686e012ee8ec0eb90ab8f05f497c_37.png)

1.  **创建策略文件**：新建一个Python文件，例如 `fanbao_dayin.py`。将“反包大阴线”策略的完整代码（包括策略框架）复制到这个文件中。
2.  **创建定时任务文件**：再新建一个Python文件，例如 `scheduler_main.py`。这个文件将用于存放所有需要定时运行的程序和功能。

![](img/8be2686e012ee8ec0eb90ab8f05f497c_39.png)

---

## 第三步：设置定时运行程序

![](img/8be2686e012ee8ec0eb90ab8f05f497c_41.png)

在 `scheduler_main.py` 文件中，我们将设置几个关键的定时任务。

### 1. 盘后数据补充

为了保证日K线行情数据的完整性，需要在每个交易日收盘后（例如15:31分）自动补充当天的行情数据到历史数据库中。我们可以使用 `schedule` 包来实现定时任务。

```python
import schedule
import time

![](img/8be2686e012ee8ec0eb90ab8f05f497c_43.png)

def supplement_daily_data():
    # 补充当日所有个股行情数据的代码
    pass

![](img/8be2686e012ee8ec0eb90ab8f05f497c_45.png)

# 每个交易日15:31执行
schedule.every().day.at("15:31").do(supplement_daily_data)
```

![](img/8be2686e012ee8ec0eb90ab8f05f497c_47.png)

### 2. 盘中信号监控与下单

![](img/8be2686e012ee8ec0eb90ab8f05f497c_49.png)

这是核心部分，我们需要设置一个每秒钟运行一次的任务，来实时检测策略信号并触发下单。

![](img/8be2686e012ee8ec0eb90ab8f05f497c_51.png)

```python
def fanbao_trading_task():
    # 判断当前是否为交易日及交易时段
    if not is_trading_day() or not is_trading_hours():
        return

    # 运行策略，获取信号DataFrame: df_signal
    df_signal = run_fanbao_strategy()

    # 如果df_signal不为空，说明有信号产生
    if df_signal is not None and not df_signal.empty:
        for index, row in df_signal.iterrows():
            stock_code = row['股票代码']
            trade_date = row['日期']

            # 避免重复下单：检查此股票代码+日期组合是否已存在于历史记录中
            if (stock_code, trade_date) not in executed_orders_history:
                # 以卖五价下单，金额可自定义
                order_price = get_sell_five_price(stock_code)
                order_volume = calculate_volume(order_price, capital=10000) # 示例：使用1万元
                mikmt_trade.buy(stock_code=stock_code, price=order_price, volume=order_volume)

                # 下单后，将此记录加入历史，防止同一信号重复下单
                executed_orders_history.append((stock_code, trade_date))

![](img/8be2686e012ee8ec0eb90ab8f05f497c_53.png)

# 每秒钟运行一次监控任务
schedule.every(1).seconds.do(fanbao_trading_task)
```

![](img/8be2686e012ee8ec0eb90ab8f05f497c_55.png)

**关键点说明**：
*   **避免重复下单**：通过维护一个已执行订单的历史记录（`executed_orders_history`），确保同一交易日内对同一只股票的同一信号只下单一次。
*   **执行频率**：`schedule.every(1).seconds` 表示每秒执行一次，保证了信号捕捉的及时性。您可以根据需要调整间隔。

![](img/8be2686e012ee8ec0eb90ab8f05f497c_57.png)

### 3. 盘后预选股

![](img/8be2686e012ee8ec0eb90ab8f05f497c_59.png)

“反包大阴线”策略为了盘中快速出信号，需要在盘后预先筛选出符合条件的股票池（例如，前15日内有涨停且当日跌幅超过-5%的股票）。我们可以在收盘后（例如15:35分）运行这个预选任务。

![](img/8be2686e012ee8ec0eb90ab8f05f497c_61.png)

```python
def pre_select_stocks():
    # 运行预选股逻辑，生成并保存预选股票列表
    pre_selected_list = run_fanbao_preselection()
    save_preselection(pre_selected_list)

![](img/8be2686e012ee8ec0eb90ab8f05f497c_63.png)

# 每个交易日15:35执行预选
schedule.every().day.at("15:35").do(pre_select_stocks)
```

![](img/8be2686e012ee8ec0eb90ab8f05f497c_65.png)

---

![](img/8be2686e012ee8ec0eb90ab8f05f497c_67.png)

## 实盘运行

![](img/8be2686e012ee8ec0eb90ab8f05f497c_69.png)

![](img/8be2686e012ee8ec0eb90ab8f05f497c_71.png)

完成以上所有代码整合后，在实盘环境中，您只需要启动 `scheduler_main.py` 这个定时任务程序。

```bash
python scheduler_main.py
```

![](img/8be2686e012ee8ec0eb90ab8f05f497c_73.png)

程序运行后，会输出类似“开始运行实盘反包大阴线策略”的提示。此后，您无需进行任何手动操作。程序将永久在后台运行，自动处理盘后数据补充、盘中信号监控与下单、以及盘后预选股等所有任务。一旦策略产生交易信号，系统便会自动执行买入操作。

![](img/8be2686e012ee8ec0eb90ab8f05f497c_75.png)

---

![](img/8be2686e012ee8ec0eb90ab8f05f497c_77.png)

## 总结

![](img/8be2686e012ee8ec0eb90ab8f05f497c_79.png)

本节课中，我们一起学习了将量化策略信号自动对接到券商QMT系统进行下单的全过程。我们首先测试了交易连接，然后整合了策略代码，最后通过设置定时任务程序，实现了盘后数据维护、盘中实时监控下单以及盘后预选股的自动化流程。完成这些步骤后，您的策略就可以在实盘环境中自动运行了。

![](img/8be2686e012ee8ec0eb90ab8f05f497c_81.png)

下一节，我们将介绍另一种需求：当您不需要自动下单，而是希望策略信号能通过钉钉等工具免费发送消息提醒时，该如何实现。