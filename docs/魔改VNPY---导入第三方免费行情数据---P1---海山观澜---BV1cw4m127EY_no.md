# 魔改VN.PY：P1：导入第三方免费行情数据 🎼

在本节课中，我们将学习如何为VN.PY框架导入第三方免费的行情数据源。VN.PY是一个基于Python的开源量化交易框架，但其默认的行情数据接口可能有限或需要付费。通过本教程，你将掌握扩展VN.PY，使其能够接入自定义的免费数据源，从而丰富你的策略回测和实盘交易的数据基础。

![](img/3e280036e9e4ca5761d566272e12543a_1.png)

## 概述与准备工作

![](img/3e280036e9e4ca5761d566272e12543a_2.png)

在开始修改代码之前，需要进行一些准备工作。核心任务是创建一个新的数据服务接口（Datafeed），并将其集成到VN.PY的引擎中。

首先，确保你已安装VN.PY。你可以使用pip进行安装：
```bash
pip install vnpy
```

![](img/3e280036e9e4ca5761d566272e12543a_3.png)

接下来，我们需要规划新数据接口的代码结构。VN.PY的数据接口通常位于 `vnpy_datarecorder` 或类似的模块中。我们将创建一个新的Python文件来定义我们的数据网关（Gateway）。

## 创建自定义数据网关类

![](img/3e280036e9e4ca5761d566272e12543a_4.png)

上一节我们介绍了准备工作，本节中我们来看看如何创建核心的数据网关类。这个类将继承VN.PY的基类，并实现连接市场、订阅行情、查询历史数据等必要方法。

创建一个新文件，例如 `my_free_datafeed.py`。首先导入必要的模块：

![](img/3e280036e9e4ca5761d566272e12543a_5.png)

```python
from vnpy.trader.gateway import BaseGateway
from vnpy.trader.constant import Exchange, Interval
from vnpy.trader.object import HistoryRequest, TickData, BarData
from datetime import datetime
import requests  # 假设我们使用requests库获取网络数据
```

接着，定义我们的网关类。类名可以定为 `MyFreeDataGateway`。

![](img/3e280036e9e4ca5761d566272e12543a_6.png)

```python
class MyFreeDataGateway(BaseGateway):
    """
    自定义免费行情数据网关。
    """
    # 网关名称，显示在VN Station中
    default_name = "MyFreeData"
    
    # 支持的交易所，这里以沪深交易所为例
    default_setting = {
        "token": "",  # 如果需要API令牌
        "server": "https://api.example.com"  # 数据服务器地址
    }
    
    # 支持的K线周期
    supported_interval = [
        Interval.MINUTE,
        Interval.HOUR,
        Interval.DAILY
    ]
    
    def __init__(self, event_engine):
        """构造函数"""
        super().__init__(event_engine, "MyFreeData")
        self.settings = self.default_setting.copy()
        
    def connect(self, setting: dict):
        """
        连接数据源。
        """
        self.settings.update(setting)
        # 这里实现具体的连接逻辑，例如验证API token
        try:
            # 示例：发送一个测试请求
            test_url = f"{self.settings['server']}/ping"
            response = requests.get(test_url)
            if response.status_code == 200:
                self.write_log("免费数据源连接成功")
                self.status.update({"connected": True})
            else:
                self.write_log("连接测试失败")
        except Exception as e:
            self.write_log(f"连接过程中发生错误：{e}")
    
    def subscribe(self, req: SubscribeRequest):
        """
        订阅行情数据。
        对于纯历史数据源，此方法可能无需实现。
        """
        self.write_log(f"该数据源暂不支持实时行情订阅")
    
    def query_history(self, req: HistoryRequest):
        """
        查询历史数据，这是最核心的方法。
        """
        # 构建请求参数
        params = {
            "symbol": req.symbol,
            "exchange": req.exchange.value,
            "interval": self._interval_to_str(req.interval),
            "start_date": req.start.strftime("%Y%m%d"),
            "end_date": req.end.strftime("%Y%m%d")
        }
        
        # 发送请求到第三方API
        url = f"{self.settings['server']}/kline"
        try:
            resp = requests.get(url, params=params)
            data_list = resp.json()
        except Exception as e:
            self.write_log(f"获取历史数据失败：{e}")
            return []
        
        # 将API返回的数据解析成VN.PY的BarData列表
        bars = []
        for data in data_list:
            bar = BarData(
                symbol=req.symbol,
                exchange=req.exchange,
                datetime=datetime.strptime(data['time'], '%Y-%m-%d %H:%M:%S'),
                interval=req.interval,
                open_price=float(data['open']),
                high_price=float(data['high']),
                low_price=float(data['low']),
                close_price=float(data['close']),
                volume=float(data['volume']),
                gateway_name=self.gateway_name
            )
            bars.append(bar)
        
        return bars
    
    def _interval_to_str(self, interval: Interval):
        """将VN.PY的Interval枚举转换为第三方API识别的字符串"""
        map_dict = {
            Interval.MINUTE: "1min",
            Interval.HOUR: "1h",
            Interval.DAILY: "1d"
        }
        return map_dict.get(interval, "1d")
```

## 将网关注册到VN.PY

![](img/3e280036e9e4ca5761d566272e12543a_7.png)

创建好网关类后，需要将其注册到VN.PY中，以便在VN Station中加载和使用。

![](img/3e280036e9e4ca5761d566272e12543a_8.png)

在同一个 `my_free_datafeed.py` 文件的末尾，添加以下注册代码：

```python
# 注册网关到VN.PY
from vnpy.trader.gateway import GatewayMeta
GatewayMeta("MyFreeData", "My Free Datafeed", "1.0.0", MyFreeDataGateway)
```

![](img/3e280036e9e4ca5761d566272e12543a_9.png)

然后，需要修改VN.PY的入口配置。找到你的VN.PY项目中的 `main.py` 或启动脚本，在导入部分添加：

```python
import my_free_datafeed  # 这行代码会执行文件中的注册语句
```

![](img/3e280036e9e4ca5761d566272e12543a_10.png)

## 在VN Station中配置与使用

完成代码编写和注册后，就可以在VN Station中使用了。

![](img/3e280036e9e4ca5761d566272e12543a_11.png)

以下是启动和配置步骤：

1.  启动VN Station。
2.  在“交易”模块中，点击“连接行情”。
3.  在网关下拉列表中，你应该能看到新出现的“MyFreeData”。
4.  点击连接，并填入你在代码 `default_setting` 中定义的配置项（如服务器地址和Token）。
5.  连接成功后，即可在“数据”模块中查询和下载历史K线数据。

![](img/3e280036e9e4ca5761d566272e12543a_12.png)

## 注意事项与优化建议

在实现基本功能后，还有一些细节需要考虑以提升稳定性和可用性。

![](img/3e280036e9e4ca5761d566272e12543a_13.png)

以下是几个关键的优化点：

*   **错误处理**：在网络请求、数据解析等环节增加更细致的异常捕获和日志记录。
*   **速率限制**：遵守第三方API的调用频率限制，在代码中添加 `time.sleep()` 避免请求过快。
*   **数据缓存**：对于频繁查询的数据，可以考虑在本地建立缓存数据库（如SQLite），避免重复请求。
*   **时区处理**：确保API返回的时间戳与VN.PY内部使用的时区（通常为UTC+8）正确转换。

![](img/3e280036e9e4ca5761d566272e12543a_14.png)

## 总结

本节课中我们一起学习了如何为VN.PY框架导入第三方免费行情数据。我们从创建自定义数据网关类开始，逐步实现了连接、查询历史数据等核心功能，并将其成功注册到VN.PY生态中。通过这种方法，你可以灵活地接入任何提供HTTP API的免费或付费数据源，极大地扩展了VN.PY的数据获取能力，为后续的策略开发和回测打下坚实的基础。