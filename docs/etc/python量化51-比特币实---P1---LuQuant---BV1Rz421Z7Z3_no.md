# Python量化51：比特币实时交易 - P1：三均线趋势预警系统

在本教程中，我们将学习如何使用Python构建一个自定义的比特币交易指标预警系统。该系统通过比较三条指数移动平均线来判断市场趋势，并在检测到特定趋势信号时，自动发送电子邮件到您的手机进行提醒。整个过程涵盖了数据获取、指标计算、信号生成和自动通知的实现。

## 概述

我们将实现一个基于三条移动平均线的趋势检测策略。核心思想是：当快速、中等、慢速三条移动平均线呈现特定排列顺序时，代表市场处于明确的上升或下降趋势。我们的程序将自动监控市场，并在检测到这些信号时，通过电子邮件发送警报，帮助您及时把握交易机会，而无需时刻盯盘。

---

## 数据准备与移动平均线计算

首先，我们需要获取历史价格数据并计算三条不同周期的指数移动平均线。

我们将使用Pandas库来处理数据。假设我们已经有一个包含比特币历史价格（日期、开盘价、最高价、最低价、收盘价、成交量）的CSV文件。

![](img/ef8942187d5f3c02700ab647c62da2db_1.png)

```python
import pandas as pd

# 读取数据文件
df = pd.read_csv('bitcoin_prices.csv')
# 检查数据是否有缺失值
print(df.isnull().sum())
# 查看数据前几行，确保读取正确
print(df.head())
```

接下来，我们计算三条指数移动平均线。这里我们选择20、30和60作为周期参数，您可以根据自己的策略进行调整。

```python
import pandas_ta as ta

# 计算指数移动平均线
df['EMA_20'] = ta.ema(df['Close'], length=20)
df['EMA_30'] = ta.ema(df['Close'], length=30)
df['EMA_60'] = ta.ema(df['Close'], length=60)

# 查看添加了移动平均线后的数据
print(df.tail(10))
```

---

## 定义趋势信号生成函数

上一节我们计算了三条移动平均线，本节中我们来看看如何根据它们的排列顺序来定义交易信号。

我们将创建一个函数，根据三条EMA的排列关系返回信号值：
*   **+1**：代表买入信号（上升趋势）。条件：`EMA_20 > EMA_30` 且 `EMA_30 > EMA_60`。
*   **-1**：代表卖出信号（下降趋势）。条件：`EMA_20 < EMA_30` 且 `EMA_30 < EMA_60`。
*   **0**：代表无明确信号。

```python
def my_signal(row):
    """
    根据三条EMA的排列生成信号。
    +1: 买入信号 (EMA_20 > EMA_30 > EMA_60)
    -1: 卖出信号 (EMA_20 < EMA_30 < EMA_60)
     0: 无信号
    """
    if row['EMA_20'] < row['EMA_30'] and row['EMA_30'] < row['EMA_60']:
        return -1
    elif row['EMA_20'] > row['EMA_30'] and row['EMA_30'] > row['EMA_60']:
        return 1
    else:
        return 0

# 将信号函数应用到数据框的每一行
df['Signal'] = df.apply(my_signal, axis=1)
```

为了直观验证，我们可以绘制一小段数据内的K线图和移动平均线。

```python
import plotly.graph_objects as go

# 选取一小段数据进行绘图
df_slice = df.iloc[100:200]
fig = go.Figure(data=[go.Candlestick(x=df_slice['Date'],
                open=df_slice['Open'], high=df_slice['High'],
                low=df_slice['Low'], close=df_slice['Close'],
                name='K线')])
# 添加三条移动平均线
fig.add_trace(go.Scatter(x=df_slice['Date'], y=df_slice['EMA_20'], mode='lines', name='EMA 20', line=dict(color='blue')))
fig.add_trace(go.Scatter(x=df_slice['Date'], y=df_slice['EMA_30'], mode='lines', name='EMA 30', line=dict(color='orange')))
fig.add_trace(go.Scatter(x=df_slice['Date'], y=df_slice['EMA_60'], mode='lines', name='EMA 60', line=dict(color='black')))
fig.show()
```

在图表中，您可以观察到在上升趋势阶段，三条线会呈现 `EMA_20`（蓝）在上，`EMA_30`（橙）在中，`EMA_60`（黑）在下的顺序。当顺序反转时，可能意味着趋势转变。

---

## 整合实时数据与邮件预警功能

现在我们已经验证了指标的有效性，接下来我们将把逻辑应用到实时数据上，并添加自动邮件通知功能。这需要连接经纪商API获取实时数据，并使用SMTP协议发送邮件。

以下是实现该功能的核心步骤，我们将它们封装在一个函数中。

首先，导入必要的库并设置邮件参数。

```python
import smtplib
from email.mime.text import MIMEText
from email.mime.multipart import MIMEMultipart
# 假设使用Oanda API获取实时数据（此处仅为示例结构）
# from oandapyV20 import API
# from oandapyV20.endpoints import instruments

# 邮件配置 (请替换为您的实际信息)
sender_email = "your_email@gmail.com"
sender_password = "your_app_password"  # 注意：可能需要使用Gmail的应用专用密码
receiver_email = "your_phone_email@gmail.com"  # 发送到与手机同步的邮箱
```

接下来，定义主工作函数。该函数会执行以下操作：
1.  从经纪商平台（如Oanda）下载最新的K线数据。
2.  计算三条移动平均线。
3.  应用信号函数判断最新K线的趋势。
4.  如果检测到新的买入或卖出信号（与上一根K线信号不同），则发送邮件警报。

```python
def main_job():
    """
    主工作函数：获取数据、计算指标、检查信号并发送邮件。
    """
    # 1. 从API获取实时K线数据 (此处为伪代码，需根据具体API实现)
    # 例如：获取最近61根4小时K线，因为我们只需要最后60根已关闭的K线进行计算
    # candles = fetch_candles(instrument="BTC_USD", granularity="H4", count=61)
    
    # 2. 将数据转换为Pandas DataFrame
    df_live = pd.DataFrame(columns=['Open', 'High', 'Low', 'Close'])
    # for candle in candles:
    #     ... 解析并填充df_live ...
    
    # 3. 计算移动平均线 (使用最后60根已关闭的K线)
    df_live['EMA_20'] = df_live['Close'].rolling(window=20).mean()
    df_live['EMA_30'] = df_live['Close'].rolling(window=30).mean()
    df_live['EMA_60'] = df_live['Close'].rolling(window=60).mean()
    
    # 4. 生成信号
    df_live['Signal'] = df_live.apply(my_signal, axis=1)
    
    # 5. 检查信号变化（假设索引-2是前一根K线，索引-1是当前最新已关闭K线）
    last_signal = df_live.iloc[-1]['Signal']
    prev_signal = df_live.iloc[-2]['Signal']
    
    message = ""
    if last_signal == 1 and prev_signal != 1:
        message = "🚀 买入信号：检测到上升趋势！(EMA20 > EMA30 > EMA60)"
    elif last_signal == -1 and prev_signal != -1:
        message = "📉 卖出信号：检测到下降趋势！(EMA20 < EMA30 < EMA60)"
    
    # 6. 如果生成了新信号，则发送邮件
    if message:
        send_email_notification(message)

def send_email_notification(body):
    """发送邮件通知的函数"""
    msg = MIMEMultipart()
    msg['From'] = sender_email
    msg['To'] = receiver_email
    msg['Subject'] = "比特币交易信号预警"
    
    msg.attach(MIMEText(body, 'plain'))
    
    try:
        server = smtplib.SMTP('smtp.gmail.com', 587)
        server.starttls()
        server.login(sender_email, sender_password)
        server.send_message(msg)
        server.quit()
        print("邮件发送成功！")
    except Exception as e:
        print(f"邮件发送失败: {e}")
```

**重要提示**：使用Gmail发送邮件可能需要启用“两步验证”并生成“应用专用密码”，而不是直接使用您的登录密码。请根据Gmail的安全设置进行调整。

---

## 设置定时任务自动运行

我们不希望手动运行这个监控程序，而是希望它能够定时自动执行。为此，我们将使用`APScheduler`库来安排任务。

我们将设置任务从周一到周五，每4小时运行一次（对应4小时K线周期），并加入一些容错机制。

```python
from apscheduler.schedulers.blocking import BlockingScheduler
from apscheduler.triggers.cron import CronTrigger
import datetime

# 创建调度器
scheduler = BlockingScheduler()

# 定义定时任务：周一到周五，每4小时过5分钟运行一次（避免整点网络拥堵）
trigger = CronTrigger(
    day_of_week='mon-fri',
    hour='*/4',
    minute=5,
    start_date=datetime.datetime.now(),
    timezone='Asia/Shanghai'  # 请根据您的时区修改
)

# 添加任务到调度器
scheduler.add_job(
    func=main_job,
    trigger=trigger,
    id='bitcoin_trend_check',
    misfire_grace_time=900,  # 允许15分钟的失火宽限时间
    coalesce=True,  # 合并多次未执行的作业
    max_instances=1
)

print("调度器已启动，开始监控...")
try:
    scheduler.start()
except (KeyboardInterrupt, SystemExit):
    print("调度器已停止。")
```

这样，程序就会在后台按照设定好的计划运行，自动检查市场并发送预警邮件。

---

## 总结

在本教程中，我们一起学习并实现了一个完整的比特币趋势预警系统。我们首先介绍了基于三条指数移动平均线（20， 30， 60周期）的趋势判断原理。然后，我们使用历史数据验证了指标的有效性，并定义了信号生成函数。

接着，我们将逻辑扩展到实时应用场景，整合了从经纪商API获取实时数据、计算指标、判断信号以及通过SMTP协议发送电子邮件警报的功能。最后，我们使用`APScheduler`库创建了一个定时任务，使整个监控过程能够自动化运行，在检测到新的趋势信号时及时通知您。

![](img/ef8942187d5f3c02700ab647c62da2db_3.png)

![](img/ef8942187d5f3c02700ab647c62da2db_4.png)

这个系统是一个基础框架，您可以根据自己的交易策略，轻松修改移动平均线的参数、添加其他技术指标（如RSI、MACD），或者对接不同的数据源和通知方式（如Telegram Bot、短信）。希望这个教程能帮助您迈出自动化交易分析的第一步。