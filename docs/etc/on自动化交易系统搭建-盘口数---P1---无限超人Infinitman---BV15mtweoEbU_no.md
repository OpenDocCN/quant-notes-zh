# Python自动化交易系统搭建：P1：盘口数据同步与信号提醒

在本节课中，我们将学习如何使用Python搭建一个基础的自动化交易系统。该系统将实现从数据源获取实时盘口数据，并根据预设策略生成交易信号，最后通过即时通讯工具发送提醒。整个过程将涉及数据获取、策略逻辑和消息通知三个核心模块。

## 概述

一个自动化交易系统通常由数据流、策略引擎和执行器三部分组成。本节课，我们将聚焦于前两部分，即如何同步获取市场盘口数据，并基于这些数据计算交易信号。我们不会涉及真实的订单执行，而是通过发送提醒消息来模拟信号的输出。这为后续接入真实交易接口打下基础。

## 数据获取：连接市场信息源

上一节我们概述了系统的基本构成，本节中我们来看看如何获取实时的市场数据。盘口数据，即买卖双方的报价和挂单量，是许多交易策略的基础。我们将使用一个模拟的数据源来代表真实的行情API。

在Python中，我们通常使用`requests`库来访问网络API，或使用专门的金融数据库（如`ccxt`用于加密货币）。为了简化演示，这里我们将创建一个函数来模拟获取数据。

```python
import time
import random

def fetch_market_data(symbol):
    """
    模拟获取指定交易对的盘口数据。
    在实际应用中，此处应替换为真实的API调用。
    """
    # 模拟数据延迟
    time.sleep(0.1)
    # 生成模拟的买一、卖一价和量
    bid_price = round(random.uniform(100, 101), 2)
    ask_price = round(bid_price + random.uniform(0.1, 0.5), 2)
    bid_volume = round(random.uniform(1, 10), 2)
    ask_volume = round(random.uniform(1, 10), 2)

    market_data = {
        'symbol': symbol,
        'timestamp': time.time(),
        'bid_price': bid_price,   # 买一价
        'ask_price': ask_price,   # 卖一价
        'bid_volume': bid_volume, # 买一量
        'ask_volume': ask_volume  # 卖一量
    }
    return market_data
```

## 策略逻辑：从数据到信号

成功获取数据后，下一步是让计算机根据这些数据做出决策。这就是策略引擎的作用。策略可以非常简单，比如当买价高于某个阈值时发出买入信号；也可以非常复杂，涉及多种技术指标和机器学习模型。

我们以一个简单的均值回归思想为例：假设价格会围绕一个中心值波动。当当前买价显著低于近期平均价时，可能意味着价格被低估，考虑买入；反之则考虑卖出。

以下是计算简单移动平均和生成信号的函数：

```python
def calculate_sma(prices, window):
    """计算简单移动平均线。"""
    if len(prices) < window:
        return None
    return sum(prices[-window:]) / window

![](img/5592daf7fb50cef292d3822a15009be4_0.png)

def generate_trading_signal(market_data, price_history, window=5, threshold=0.1):
    """
    基于价格与移动平均线的偏离生成交易信号。
    """
    current_price = (market_data['bid_price'] + market_data['ask_price']) / 2
    price_history.append(current_price)

    if len(price_history) < window:
        return "WAIT", current_price, None

    sma = calculate_sma(price_history, window)
    deviation = (current_price - sma) / sma

    if deviation < -threshold:
        signal = "BUY"
    elif deviation > threshold:
        signal = "SELL"
    else:
        signal = "HOLD"

    return signal, current_price, sma
```

## 消息通知：实时提醒交易信号

当策略引擎生成一个交易信号后，我们需要及时获知它。将信号发送到手机或电脑上的即时通讯软件是一个常见做法。这里我们以发送邮件为例，因为它相对通用且易于设置。在实际操作中，你也可以替换为微信、Telegram或钉钉的机器人通知。

以下是使用Python的`smtplib`库发送邮件提醒的示例函数。你需要提前配置好发件邮箱的SMTP服务。

```python
import smtplib
from email.mime.text import MIMEText
from email.header import Header

def send_email_alert(signal, symbol, price, sma):
    """
    通过电子邮件发送交易信号提醒。
    """
    # 邮件服务器配置（以QQ邮箱为例，需使用授权码）
    mail_host = "smtp.qq.com"
    mail_user = "your_email@qq.com"
    mail_pass = "your_authorization_code"  # 注意是授权码，不是登录密码

    sender = mail_user
    receivers = ['receiver_email@example.com']  # 接收邮箱

    # 邮件内容
    content = f"""
    交易信号提醒
    交易对：{symbol}
    当前价格：{price}
    移动平均价（{window}期）：{sma}
    生成信号：**{signal}**
    时间：{time.strftime('%Y-%m-%d %H:%M:%S')}
    """
    message = MIMEText(content, 'plain', 'utf-8')
    message['From'] = Header("自动化交易系统", 'utf-8')
    message['To'] = Header("交易员", 'utf-8')
    message['Subject'] = Header(f"交易信号：{signal} - {symbol}", 'utf-8')

    try:
        smtpObj = smtplib.SMTP_SSL(mail_host, 465)
        smtpObj.login(mail_user, mail_pass)
        smtpObj.sendmail(sender, receivers, message.as_string())
        print("邮件发送成功")
    except smtplib.SMTPException as e:
        print(f"邮件发送失败: {e}")
```

![](img/5592daf7fb50cef292d3822a15009be4_1.png)

## 系统整合与循环运行

现在，我们已经拥有了系统的所有核心部件：数据获取、策略逻辑和消息通知。最后一步是将它们整合到一个主循环中，让系统能够持续运行，定期检查市场并做出反应。

以下是主程序的逻辑流程：

1.  初始化变量（如价格历史列表）。
2.  进入一个无限循环。
3.  在每次循环中，获取最新的市场数据。
4.  将新数据输入策略函数，生成信号。
5.  如果信号不是“HOLD”或“WAIT”，则触发通知。
6.  等待一段时间后，开始下一次循环。

```python
if __name__ == "__main__":
    symbol = "BTC/USDT"
    price_history = []
    window = 5
    threshold = 0.001  # 0.1%的偏离阈值

    print("自动化交易监控系统启动...")
    try:
        while True:
            # 1. 获取数据
            data = fetch_market_data(symbol)
            # 2. 生成信号
            signal, price, sma = generate_trading_signal(data, price_history, window, threshold)
            # 3. 记录日志
            print(f"[{time.strftime('%H:%M:%S')}] 价格: {price:.2f}, SMA: {sma:.2f}, 信号: {signal}")
            # 4. 发送提醒（例如，当出现买入或卖出信号时）
            if signal in ["BUY", "SELL"]:
                send_email_alert(signal, symbol, price, sma)
            # 5. 等待一段时间再进行下一次查询（避免过于频繁请求API）
            time.sleep(5)
    except KeyboardInterrupt:
        print("\n系统已手动停止。")
```

## 总结

本节课中，我们一起学习了自动化交易系统前端流程的搭建。我们从**连接模拟数据源**开始，学会了如何获取盘口数据。接着，我们设计了一个基于**简单移动平均线偏离**的策略逻辑，将原始数据转化为明确的交易信号。最后，我们实现了通过**电子邮件发送提醒**的功能，确保信号能被及时感知。通过将这些模块整合到一个循环中，我们完成了一个能够持续监控市场并发出警报的简易系统。这为后续加入更复杂的策略、真实行情接口以及订单执行模块奠定了坚实的基础。