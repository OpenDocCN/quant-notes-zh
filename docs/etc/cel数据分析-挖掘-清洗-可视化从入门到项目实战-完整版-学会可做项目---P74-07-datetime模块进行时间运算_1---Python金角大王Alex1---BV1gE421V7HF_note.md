Python数据分析：P74：datetime模块进行时间运算

在本节课中，我们将要学习Python的`datetime`模块，它主要用于处理日期和时间数据。我们将重点学习如何使用`datetime`模块进行日期的表达、时间运算以及时间替换。

---

![](img/44d93415df4a6ed28fd6158f03032308_1.png)

### **datetime模块简介**

![](img/44d93415df4a6ed28fd6158f03032308_3.png)

上一节我们介绍了`time`模块，它主要用于处理时间戳和日期格式字符串。本节中我们来看看`datetime`模块，它主要用来表达“年-月-日”这种到天的日期，并进行时间的运算和替换。

![](img/44d93415df4a6ed28fd6158f03032308_5.png)

`datetime`模块包含多个类，以下是几个核心的子类：

![](img/44d93415df4a6ed28fd6158f03032308_7.png)

*   `date`：表示日期（年、月、日）。
*   `time`：表示时间（时、分、秒、微秒）。
*   `datetime`：表示日期和时间的组合。
*   `timedelta`：表示两个日期或时间之间的间隔（时间差）。
*   `tzinfo`：处理时区信息。

![](img/44d93415df4a6ed28fd6158f03032308_9.png)

---

### **获取当前时间**

首先，我们学习如何获取当前的日期和时间。这是最基础的操作。

使用`datetime.now()`方法可以返回当前的日期和时间类型。

```python
import datetime

d = datetime.datetime.now()
print(d)
```
执行以上代码，会以默认格式打印出当前的日期和时间，例如：`2024-05-28 14:30:00.123456`。

![](img/44d93415df4a6ed28fd6158f03032308_11.png)

获取到这个`datetime`对象后，我们可以进行多种操作，例如将其转换为时间戳或时间元组，这与`time`模块的功能是相通的。

```python
# 转换为时间戳
timestamp = d.timestamp()
print(timestamp)

![](img/44d93415df4a6ed28fd6158f03032308_13.png)

# 转换为时间元组
time_tuple = d.timetuple()
print(time_tuple)
```

![](img/44d93415df4a6ed28fd6158f03032308_15.png)

此外，我们还可以使用`datetime.fromtimestamp()`方法将一个时间戳转换回`datetime`类型。

```python
dt_from_ts = datetime.datetime.fromtimestamp(1716885000)
print(dt_from_ts)
```

![](img/44d93415df4a6ed28fd6158f03032308_17.png)

---

![](img/44d93415df4a6ed28fd6158f03032308_18.png)

### **时间的运算**

![](img/44d93415df4a6ed28fd6158f03032308_20.png)

接下来，我们学习如何进行时间的加减运算，这是`datetime`模块非常实用的功能。

直接对两个`datetime`对象进行加减是不允许的。我们必须借助`timedelta`类来表示一个时间间隔，然后进行运算。

![](img/44d93415df4a6ed28fd6158f03032308_22.png)

`timedelta`可以指定天数、小时、分钟、秒等参数。

以下是进行时间运算的步骤：

![](img/44d93415df4a6ed28fd6158f03032308_24.png)

1.  获取一个当前时间的`datetime`对象。
2.  创建一个`timedelta`对象，定义要增加或减少的时间量。
3.  将两者相加或相减。

![](img/44d93415df4a6ed28fd6158f03032308_26.png)

```python
import datetime

![](img/44d93415df4a6ed28fd6158f03032308_28.png)

# 获取当前时间
now = datetime.datetime.now()
print("当前时间：", now)

![](img/44d93415df4a6ed28fd6158f03032308_30.png)

# 创建一个表示5天的时间间隔
delta = datetime.timedelta(days=5)

![](img/44d93415df4a6ed28fd6158f03032308_32.png)

# 计算5天后的时间
future_date = now + delta
print("5天后：", future_date)

# 计算5天前的时间
past_date = now - delta
print("5天前：", past_date)

# 创建一个表示5小时的时间间隔
delta_hours = datetime.timedelta(hours=5)
# 计算5小时后的时间
future_hours = now + delta_hours
print("5小时后：", future_hours)
```

---

### **时间的替换**

最后，我们学习如何替换一个`datetime`对象中的特定部分（如年、月、日），而不改变其他部分。

![](img/44d93415df4a6ed28fd6158f03032308_34.png)

这可以通过`replace()`方法来实现。该方法会返回一个新的`datetime`对象，原对象保持不变。

以下是使用`replace()`方法的示例：

![](img/44d93415df4a6ed28fd6158f03032308_36.png)

```python
import datetime

![](img/44d93415df4a6ed28fd6158f03032308_38.png)

# 获取当前时间
now = datetime.datetime.now()
print("原始时间：", now)

![](img/44d93415df4a6ed28fd6158f03032308_40.png)

# 将年份替换为2120年
new_time = now.replace(year=2120)
print("替换年份后：", new_time)

# 将年份替换为2120年，月份替换为8月
new_time2 = now.replace(year=2120, month=8)
print("替换年份和月份后：", new_time2)
```

---

### **总结**

本节课中我们一起学习了Python `datetime`模块的核心用法。

![](img/44d93415df4a6ed28fd6158f03032308_42.png)

我们首先了解了`datetime`模块与`time`模块的区别，它更侧重于完整的日期表达。然后，我们学习了如何获取当前时间，并将其与时间戳进行转换。接着，我们掌握了使用`timedelta`进行时间加减运算的方法，这是处理日程、计算期限的关键。最后，我们学习了使用`replace()`方法灵活地替换日期时间中的某个部分。

![](img/44d93415df4a6ed28fd6158f03032308_44.png)

记住`datetime.now()`、`timedelta`和`replace()`这几个核心方法，你就能应对大多数基本的日期时间处理需求了。