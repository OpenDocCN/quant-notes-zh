# Python数据分析实战：P73：06：时间处理模块time 📅

![](img/5ae9eb30a11bd4c746483abc43194f8d_0.png)

![](img/5ae9eb30a11bd4c746483abc43194f8d_2.png)

![](img/5ae9eb30a11bd4c746483abc43194f8d_4.png)

在本节课中，我们将要学习Python中用于处理时间的基础模块——`time`模块。时间处理在编程中无处不在，例如记录日志、显示时间、进行日期运算等。掌握时间处理是数据分析乃至所有编程任务中的重要一环。

## 时间处理的三种场景

![](img/5ae9eb30a11bd4c746483abc43194f8d_6.png)

在编程中，处理时间主要涉及以下三种场景：

![](img/5ae9eb30a11bd4c746483abc43194f8d_8.png)

![](img/5ae9eb30a11bd4c746483abc43194f8d_10.png)

![](img/5ae9eb30a11bd4c746483abc43194f8d_12.png)

*   **时间显示**：例如在屏幕上显示当前时间，或将时间记录到日志文件中。
*   **时间转换**：在不同时间格式之间进行转换。例如，将人类可读的字符串（如 `"2024-05-29"`）转换为程序可以运算的日期对象，或者反过来。
*   **时间运算**：对时间进行比较和计算。例如，计算两个日期之间的天数差，或判断某个特定日期是否即将到来。

## Python中的时间表示形式

![](img/5ae9eb30a11bd4c746483abc43194f8d_14.png)

在Python中，时间主要有三种表示形式：

![](img/5ae9eb30a11bd4c746483abc43194f8d_16.png)

![](img/5ae9eb30a11bd4c746483abc43194f8d_18.png)

![](img/5ae9eb30a11bd4c746483abc43194f8d_20.png)

1.  **时间戳 (Timestamp)**：一个浮点数，表示从**1970年1月1日00:00:00 UTC**开始到现在的秒数。这是计算机内部存储时间的基础方式。
    *   **公式**：`time.time()` 返回当前时间戳。
    *   **代码示例**：
        ```python
        import time
        current_timestamp = time.time()
        print(current_timestamp)  # 输出类似 1716961234.56789
        ```

2.  **格式化时间字符串 (Formatted Time String)**：人类可读的字符串，如 `"2024-05-29 15:30:00"`。它本质上是字符串，不能直接用于计算。

![](img/5ae9eb30a11bd4c746483abc43194f8d_22.png)

![](img/5ae9eb30a11bd4c746483abc43194f8d_24.png)

![](img/5ae9eb30a11bd4c746483abc43194f8d_26.png)

3.  **时间元组/结构体时间 (struct_time)**：一个包含9个元素的元组，结构化地表示时间。这9个元素分别是：
    *   `tm_year` (年，如2024)
    *   `tm_mon` (月，1-12)
    *   `tm_mday` (日，1-31)
    *   `tm_hour` (时，0-23)
    *   `tm_min` (分，0-59)
    *   `tm_sec` (秒，0-59)
    *   `tm_wday` (星期几，0-6，0代表周一)
    *   `tm_yday` (一年中的第几天，1-366)
    *   `tm_isdst` (是否为夏令时，-1, 0, 1)

![](img/5ae9eb30a11bd4c746483abc43194f8d_28.png)

![](img/5ae9eb30a11bd4c746483abc43194f8d_30.png)

![](img/5ae9eb30a11bd4c746483abc43194f8d_32.png)

## UTC时间简介

UTC（Coordinated Universal Time，世界协调时），也就是格林威治标准时间。全球被划分为不同的时区，中国使用的是**UTC+8**（东八区）时间。在处理跨国应用或需要统一时间基准时，需要考虑UTC时间。

![](img/5ae9eb30a11bd4c746483abc43194f8d_34.png)

![](img/5ae9eb30a11bd4c746483abc43194f8d_36.png)

![](img/5ae9eb30a11bd4c746483abc43194f8d_38.png)

![](img/5ae9eb30a11bd4c746483abc43194f8d_40.png)

## time模块核心方法详解

![](img/5ae9eb30a11bd4c746483abc43194f8d_42.png)

![](img/5ae9eb30a11bd4c746483abc43194f8d_44.png)

上一节我们介绍了时间的三种表示形式，本节中我们来看看`time`模块提供的核心方法，它们主要用于在这些形式之间进行转换。

![](img/5ae9eb30a11bd4c746483abc43194f8d_46.png)

![](img/5ae9eb30a11bd4c746483abc43194f8d_48.png)

以下是`time`模块中几个关键函数的使用方法：

![](img/5ae9eb30a11bd4c746483abc43194f8d_50.png)

![](img/5ae9eb30a11bd4c746483abc43194f8d_52.png)

*   **`time.time()`**：获取当前时间的时间戳。
    ```python
    import time
    start = time.time()
    time.sleep(3)  # 让程序暂停3秒
    end = time.time()
    print(f"程序运行了 {end - start} 秒")  # 输出：程序运行了 3.000... 秒
    ```

![](img/5ae9eb30a11bd4c746483abc43194f8d_54.png)

![](img/5ae9eb30a11bd4c746483abc43194f8d_56.png)

*   **`time.localtime([secs])`**：将一个时间戳转换为当前时区的`struct_time`（时间元组）。如果不传入参数`secs`，则使用当前时间。
    ```python
    import time
    # 获取当前时间的结构体表示
    local_struct = time.localtime()
    print(local_struct)
    # 输出：time.struct_time(tm_year=2024, tm_mon=5, tm_mday=29, tm_hour=15, ...)

    # 将特定时间戳转换为结构体
    some_timestamp = 0  # 1970-01-01 08:00:00 (UTC+8)
    struct_at_epoch = time.localtime(some_timestamp)
    print(struct_at_epoch.tm_year)  # 输出：1970
    ```

![](img/5ae9eb30a11bd4c746483abc43194f8d_58.png)

![](img/5ae9eb30a11bd4c746483abc43194f8d_60.png)

![](img/5ae9eb30a11bd4c746483abc43194f8d_61.png)

![](img/5ae9eb30a11bd4c746483abc43194f8d_63.png)

![](img/5ae9eb30a11bd4c746483abc43194f8d_65.png)

*   **`time.gmtime([secs])`**：与`localtime()`类似，但将时间戳转换为**UTC时区**的`struct_time`。
    ```python
    import time
    utc_struct = time.gmtime()
    print(utc_struct.tm_hour)  # 输出UTC时间的小时数，比北京时间晚8小时
    ```

*   **`time.mktime(t)`**：将一个`struct_time`（本地时间）转换为时间戳。它是`localtime()`的逆操作。
    ```python
    import time
    t = time.localtime()
    timestamp = time.mktime(t)
    print(timestamp)  # 输出对应的时间戳
    ```

![](img/5ae9eb30a11bd4c746483abc43194f8d_67.png)

![](img/5ae9eb30a11bd4c746483abc43194f8d_69.png)

*   **`time.strftime(format[, t])`**：将一个`struct_time`（如`localtime()`的返回结果）格式化为指定的字符串。`format`参数定义了输出格式。
    *   `%Y`：四位数的年份
    *   `%m`：两位数的月份（01-12）
    *   `%d`：两位数的日期（01-31）
    *   `%H`：24小时制的小时（00-23）
    *   `%M`：分钟（00-59）
    *   `%S`：秒（00-59）
    ```python
    import time
    t = time.localtime()
    formatted_time = time.strftime("%Y-%m-%d %H:%M:%S", t)
    print(formatted_time)  # 输出：2024-05-29 15:30:00

    custom_format = time.strftime("今天是%Y年%m月%d日，%H点%M分", t)
    print(custom_format)
    ```

*   **`time.strptime(string[, format])`**：将一个格式化的时间字符串解析为`struct_time`。它是`strftime()`的逆操作，必须指定与字符串匹配的`format`。
    ```python
    import time
    time_str = "2024-05-29 15:30:00"
    struct_time = time.strptime(time_str, "%Y-%m-%d %H:%M:%S")
    print(struct_time)
    # 现在struct_time可以被用于计算或转换
    ```

## 时间格式转换关系图

为了方便记忆这些转换关系，可以参考下图：

```
        时间戳 (Timestamp)
            │
            │ time.localtime() / time.gmtime()
            ▼
        时间元组 (struct_time)
            │
            │ time.strftime()        time.strptime()
            ▼                           ▲
        格式化字符串 (String) ------------┘
            │
            │ (通常需要先strptime)
            ▼
        时间元组 (struct_time)
            │
            │ time.mktime()
            ▼
        时间戳 (Timestamp)
```

**核心路径**：
1.  **时间戳 -> 时间元组**：使用 `localtime()` 或 `gmtime()`。
2.  **时间元组 -> 格式化字符串**：使用 `strftime()`。
3.  **格式化字符串 -> 时间元组**：使用 `strptime()`。
4.  **时间元组 -> 时间戳**：使用 `mktime()`。

## 总结

![](img/5ae9eb30a11bd4c746483abc43194f8d_71.png)

本节课中我们一起学习了Python `time`模块的基础知识。我们首先了解了编程中处理时间的三种主要场景：显示、转换和运算。然后，我们认识了Python中时间的三种核心表示形式：**时间戳**、**格式化字符串**和**时间元组(struct_time)**。接着，我们详细讲解了`time`模块的关键函数，包括获取时间戳(`time()`)、暂停程序(`sleep()`)、以及在不同时间格式间进行转换的方法(`localtime()`, `gmtime()`, `mktime()`, `strftime()`, `strptime()`)。最后，通过一张转换关系图梳理了这些方法之间的联系。掌握这些内容是进行更复杂日期时间处理和后续学习`datetime`模块的重要基础。