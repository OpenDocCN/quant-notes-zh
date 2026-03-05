# Python量化交易：10：外汇交易的技巧 - P1

![](img/a89c7575b9f9fa11eaa0f4d45eaa85d5_1.png)

在本教程中，我们将学习如何构建一个自动化系统，用于流式传输实时市场数据、分析交易信号，并在发现交易机会时自动向您的手机或电子邮件发送警报。整个过程使用Python实现，旨在帮助您摆脱手动盯盘的困扰。

## 概述
我们将通过几个步骤构建这个系统：首先，获取实时市场数据；其次，编写一个函数来识别特定的价格模式（例如看涨吞没或看跌吞没）；最后，当检测到模式时，通过电子邮件自动发送警报。我们还会学习如何将这个过程安排为定时任务，以便每日自动运行。

![](img/a89c7575b9f9fa11eaa0f4d45eaa85d5_3.png)

---

![](img/a89c7575b9f9fa11eaa0f4d45eaa85d5_5.png)

### 1. 获取市场数据
首先，我们需要获取股票或外汇的实时历史数据。我们将使用 `yfinance` 库来下载数据。

```python
import yfinance as yf

def get_data(symbol='AAPL'):
    data = yf.download(tickers=symbol, period='2d', interval='1d')
    return data
```

以上代码定义了一个函数 `get_data`，它接收一个交易品种符号（默认为苹果股票AAPL），并下载最近两天的每日数据。函数返回一个包含开盘价、最高价、最低价、收盘价等信息的DataFrame。

为了验证函数是否正常工作，我们可以调用它并打印结果。

```python
historical_data = get_data()
print(historical_data)
```

运行后，您将看到包含最近两个交易日数据的数据框。

---

### 2. 识别交易信号
上一节我们介绍了如何获取数据，本节中我们来看看如何分析这些数据以识别交易信号。我们将编写一个函数来检测“看涨吞没”和“看跌吞没”模式。

以下是检测吞没模式的函数逻辑：

```python
def test_engulfing(data):
    # 获取最近两根K线的数据
    prev_open = data['Open'].iloc[-2]
    prev_close = data['Close'].iloc[-2]
    curr_open = data['Open'].iloc[-1]
    curr_close = data['Close'].iloc[-1]

    # 检测看跌吞没模式
    if (prev_close > prev_open) and (curr_open > curr_close) and (curr_open >= prev_close) and (curr_close <= prev_open):
        return 1  # 看跌信号
    # 检测看涨吞没模式
    elif (prev_close < prev_open) and (curr_open < curr_close) and (curr_open <= prev_close) and (curr_close >= prev_open):
        return 2  # 看涨信号
    else:
        return 0  # 无信号
```

该函数检查最近两根K线是否形成吞没形态。如果检测到看跌吞没模式，则返回 `1`；如果检测到看涨吞没模式，则返回 `2`；否则返回 `0`。

我们可以用之前下载的数据测试这个函数：

```python
signal = test_engulfing(historical_data)
print(f"当前信号: {signal}")
```

![](img/a89c7575b9f9fa11eaa0f4d45eaa85d5_7.png)

---

### 3. 设置电子邮件警报
在识别出交易信号后，下一步是设置自动通知。我们将使用Gmail的SMTP服务来发送电子邮件。

首先，需要准备好Gmail账户和应用程序专用密码。以下是发送电子邮件的代码部分：

![](img/a89c7575b9f9fa11eaa0f4d45eaa85d5_9.png)

```python
import smtplib
from email.mime.text import MIMEText
from email.mime.multipart import MIMEMultipart
import ssl

def send_email(signal_message):
    # 邮件配置
    sender_email = "your_email@gmail.com"
    receiver_email = "your_email@gmail.com"
    password = "your_app_specific_password"  # Gmail应用专用密码

    subject = "交易信号提醒"
    body = signal_message

    # 创建邮件
    message = MIMEMultipart()
    message["From"] = sender_email
    message["To"] = receiver_email
    message["Subject"] = subject
    message.attach(MIMEText(body, "plain"))

    # 发送邮件
    context = ssl.create_default_context()
    with smtplib.SMTP_SSL("smtp.gmail.com", 465, context=context) as server:
        server.login(sender_email, password)
        server.sendmail(sender_email, receiver_email, message.as_string())
```

**注意**：请务必将 `your_email@gmail.com` 和 `your_app_specific_password` 替换为您自己的信息。

---

### 4. 整合功能与定时任务
现在，我们将获取数据、检测信号和发送邮件这三个步骤整合到一个主函数中。此外，为了每日自动运行，我们将使用 `schedule` 库来安排任务。

以下是整合后的主函数：

```python
import schedule
import time

def main_job():
    # 定义要监控的交易品种列表
    symbols = ['AAPL', 'NVDA', 'PYPL']
    all_messages = []

    for symbol in symbols:
        # 获取数据
        data = get_data(symbol)
        # 检测信号
        signal = test_engulfing(data)

        if signal == 1:
            all_messages.append(f"{symbol}: 看跌吞没信号")
        elif signal == 2:
            all_messages.append(f"{symbol}: 看涨吞没信号")

    # 如果有信号，则发送邮件
    if all_messages:
        email_body = "\n".join(all_messages)
        send_email(email_body)
        print("检测到信号，邮件已发送。")
    else:
        print("未检测到任何信号。")

# 安排任务每天UTC时间0点（午夜）运行一次
schedule.every().day.at("00:00").do(main_job)

print("程序已启动，等待执行计划任务...")
while True:
    schedule.run_pending()
    time.sleep(60)  # 每分钟检查一次是否有任务需要执行
```

这个 `main_job` 函数会遍历股票列表，检查每只股票是否有吞没信号，并将所有信号汇总到一封邮件中发送。通过调度器，该函数会在每天午夜自动运行一次。

---

### 5. 扩展与自定义
您可以根据自己的交易策略对此系统进行扩展。例如：
*   在识别吞没模式时，加入对**支撑位**、**阻力位**或**移动平均线**的考量。
*   监控更多交易品种或不同时间框架的数据。
*   将警报方式扩展至短信或其他即时通讯工具。

只需修改 `test_engulfing` 函数或 `main_job` 中的逻辑即可实现更复杂的策略。

---

![](img/a89c7575b9f9fa11eaa0f4d45eaa85d5_11.png)

## 总结
在本教程中，我们一起学习了如何构建一个基础的自动化交易信号警报系统。我们掌握了以下核心技能：
1.  使用 `yfinance` **获取实时市场数据**。
2.  编写函数**识别特定的K线形态**（如吞没模式）。
3.  利用SMTP协议**通过Gmail自动发送电子邮件警报**。
4.  使用 `schedule` 库**将整个流程安排为每日定时任务**。

![](img/a89c7575b9f9fa11eaa0f4d45eaa85d5_12.png)

这个系统提供了一个强大的起点，让您能够自动监控市场并在出现潜在交易机会时获得通知，从而节省大量手动分析时间。您可以根据所学知识，进一步定制和强化这个系统以适应更复杂的交易策略。