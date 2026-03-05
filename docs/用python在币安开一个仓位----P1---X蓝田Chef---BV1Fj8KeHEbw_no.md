# Python币安交易教程：P1：使用Python在币安开仓 🐍

![](img/be88af94e0379c1ec871f650017a77f7_1.png)

在本节课中，我们将学习如何使用Python编程语言，通过币安（Binance）交易所的API接口创建一个期货仓位。整个过程将分为几个步骤，从查找API文档到最终执行交易，即使没有编程基础也能跟随操作。

## 查找API文档与确定交易模式

![](img/be88af94e0379c1ec871f650017a77f7_3.png)

首先，我们需要找到币安官方的API文档。这是与交易所服务器进行通信的规则手册。

![](img/be88af94e0379c1ec871f650017a77f7_5.png)

![](img/be88af94e0379c1ec871f650017a77f7_1.png)

假设我们计划在“单向持仓”模式下，下一个“市价”订单。这意味着我们直接以当前市场价格买入或卖出，并且只持有一个方向的仓位。

![](img/be88af94e0379c1ec871f650017a77f7_3.png)

![](img/be88af94e0379c1ec871f650017a77f7_7.png)

## 准备请求URL与参数

根据官方API文档，我们需要使用一个特定的URL来发送创建订单的请求。这是一个POST请求。

![](img/be88af94e0379c1ec871f650017a77f7_7.png)

![](img/be88af94e0379c1ec871f650017a77f7_9.png)

我们将这个URL复制下来，并创建一个新的Python项目文件。将URL粘贴到文件中。

接下来，我们需要确定请求必须携带哪些参数。以下是创建市价订单所需的核心参数：

*   **symbol**: 交易对，例如 `BTCUSDT`。
*   **side**: 买卖方向，`BUY` 或 `SELL`。
*   **type**: 订单类型，此处为 `MARKET`（市价）。
*   **quantity**: 购买或卖出的数量。
*   **timestamp**: 当前的时间戳，用于防止请求重放。

![](img/be88af94e0379c1ec871f650017a77f7_9.png)

除了这些，由于这是一个需要身份验证的请求，我们还需要生成一个**签名（signature）**。签名是通过将API密钥、请求参数和时间戳等组合起来，并使用密钥进行加密生成的，用于验证请求的合法性。

![](img/be88af94e0379c1ec871f650017a77f7_11.png)

## 填写参数与生成签名

现在，我们开始填写参数并生成签名。

![](img/be88af94e0379c1ec871f65e017a77f7_11.png)

首先，获取当前时间戳。在Python中，可以使用以下代码：
```python
timestamp = int(time.time() * 1000)
```

然后，我们需要生成签名。这需要用到你的API Key和Secret Key。以下是一个生成签名的函数示例：
```python
import hmac
import hashlib

def get_signature(api_secret, query_string):
    return hmac.new(api_secret.encode('utf-8'), query_string.encode('utf-8'), hashlib.sha256).hexdigest()
```

我们使用这个函数，将包含所有参数的查询字符串（query string）和你的Secret Key传递进去，就能得到最终的签名。

## 发起交易请求

准备好所有参数和签名后，我们就可以发起请求了。我们使用 `requests` 库来发送POST请求。

假设我们想交易 `SOLUSDT`，当前价格约为0.47美元，我们计划买入15个（价值约7美元）。我们将这些信息填入参数。

由于币安API对国内网络访问可能有限制，建议使用国外的云服务器来运行代码。

![](img/be88af94e0379c1ec871f650017a77f7_13.png)

![](img/be88af94e0379c1ec871f650017a77f7_13.png)

将代码部署到云服务器上并运行。

![](img/be88af94e0379c1ec871f650017a77f7_15.png)

![](img/be88af94e0379c1ec871f650017a77f7_15.png)

![](img/be88af94e0379c1ec871f650017a77f7_17.png)

发起下单请求。

![](img/be88af94e0379c1ec871f650017a77f7_17.png)

请求成功后，我们可以在币安账户中查看仓位，确认已经买入了15个 `SOLUSDT`。

![](img/be88af94e0379c1ec871f650017a77f7_19.png)

![](img/be88af94e0379c1ec871f650017a77f7_19.png)

## 优化代码与再次尝试

![](img/be88af94e0379c1ec871f650017a77f7_21.png)

为了使操作更清晰，我们可以优化代码，例如在开仓时打印一个提示信息。

![](img/be88af94e0379c1ec871f650017a77f7_21.png)

![](img/be88af94e0379c1ec871f650017a77f7_23.png)

再次运行开仓代码（例如开一个空单），这次控制台会显示“开空”等提示信息。

![](img/be88af94e0379c1ec871f650017a77f7_23.png)

![](img/be88af94e0379c1ec871f650017a77f7_25.png)

检查仓位，可以看到持仓数量增加了。

![](img/be88af94e0379c1ec871f650017a77f7_25.png)

最后，我们将代码整理得更简洁、更通用。将下单请求封装成一个函数，将交易对和数量作为参数传入，这样就能方便地交易不同的币种。

![](img/be88af94e0379c1ec871f650017a77f7_27.png)

![](img/be88af94e0379c1ec871f650017a77f7_27.png)

让我们用这个优化后的函数尝试交易另一个币种，例如 `ADAUSDT`，以市价开60个数量的空单。

![](img/be88af94e0379c1ec871f650017a77f7_29.png)

![](img/be88af94e0379c1ec871f650017a77f7_29.png)

执行代码。

![](img/be88af94e0379c1ec871f650017a77f7_31.png)

![](img/be88af94e0379c1ec871f650017a77f7_31.png)

查看仓位，确认已成功建立60个 `ADAUSDT` 的空头仓位。

![](img/be88af94e0379c1ec871f650017a77f7_33.png)

![](img/be88af94e0379c1ec871f650017a77f7_33.png)

---

本节课中我们一起学习了如何使用Python通过币安API开设期货仓位。核心步骤包括：查找API文档、准备请求URL与参数、生成安全签名、发送POST请求以及优化代码结构。本教程的示例代码已上传至GitHub。

![](img/be88af94e0379c1ec871f650017a77f7_35.png)

![](img/be88af94e0379c1ec871f650017a77f7_35.png)