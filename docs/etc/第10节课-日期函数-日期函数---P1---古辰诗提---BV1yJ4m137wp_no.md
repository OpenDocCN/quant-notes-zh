# 量化编程基础：第10课：日期与时间函数详解

在本节课中，我们将深入学习EasyLanguage中的日期与时间函数。我们将重点讲解如何在不同格式（如字符串、EL日期、儒略日）之间进行转换，以及如何提取日期和时间的各个组成部分。掌握这些函数是处理交易数据中时间戳信息的基础。

![](img/7c1df76f7d1341ee72600d05494b8fcd_1.png)

![](img/7c1df76f7d1341ee72600d05494b8fcd_3.png)

## 📅 日期函数详解

上一节我们介绍了`StringToDate`函数，本节我们来看看`ELDateToString`函数。

### ELDateToString 函数

`ELDateToString`函数用于将EasyLanguage的标准日期格式转换为字符串格式。其返回值类型为字符串（String）。

**函数公式**：
```
ELDateToString(Day, Month, Year)
```

以下是参数说明：
*   `Day`：日期（天），输入1或01均可。
*   `Month`：月份。
*   `Year`：年份。

**代码示例**：
```easylanguage
Print(ELDateToString(1, 1, 2022));
```
执行上述代码将输出字符串：`“2022年1月1日”`。

**重要提示**：EasyLanguage是强类型语言，必须注意数据类型。`ELDateToString`返回的是字符串，不能直接赋值给数值型变量。

### FormatDate 函数

`FormatDate`函数用于将指定的儒略日（整数部分为日期，小数部分为时间）转换为特定格式的字符串表达式。

**函数公式**：
```
FormatDate(“Template”, JulianDate)
```

以下是格式字符说明：
*   `D`：日期（不补零）。`DD`：日期（补零）。`DDD`：星期缩写（如“周一”）。`DDDD`：星期全称（如“星期一”）。
*   `M`：月份（不补零）。`MM`：月份（补零）。`MMM`：月份缩写（如“7月”）。`MMMM`：月份全称（如“七月”）。
*   `YY`：年份后两位。`YYYY`：年份四位。

**代码示例**：
```easylanguage
Print(FormatDate(“DD/MM/YYYY”, 40000));
```
执行后可能输出：`“06/07/2009”`，表示2009年7月6日。你可以在模板中加入任意分隔符或文字。

### 日期信息提取函数

以下函数用于从日期中提取特定信息。

**DayOfMonth 与 DayFromJulian**：
*   `DayOfMonth(ELDate)`：返回指定EL日期是当月的第几天。
*   `DayFromJulian(JulianDate)`：从儒略日日期中提取“天”的信息。

**代码示例**：
```easylanguage
Print(DayOfMonth(1202202)); // 输出：2
Print(DayFromJulian(40000)); // 输出：6
```

**DayOfWeek 与 DayOfWeekFromJulian**：
*   `DayOfWeek(ELDate)`：返回指定日期是周几（0=周日，1=周一，…，6=周六）。
*   `DayOfWeekFromJulian(JulianDate)`：从儒略日中提取星期信息。

**Month/Year 及其 FromJulian 版本**：
*   `Month(ELDate)` / `Year(ELDate)`：从EL日期中提取月份/年份。
*   `MonthFromJulian(JulianDate)` / `YearFromJulian(JulianDate)`：从儒略日中提取月份/年份。

### IncMonth 函数

![](img/7c1df76f7d1341ee72600d05494b8fcd_5.png)

`IncMonth`函数用于将给定的儒略日向前或向后移动指定的月数。

**函数公式**：
```
IncMonth(JulianDate, NumberOfMonths)
```
*   `NumberOfMonths`为正数则向后移动，为负数则向前移动。

**代码示例**：
```easylanguage
Print(IncMonth(40000, 1));  // 输出：40031（后移一月）
Print(IncMonth(40000, -1)); // 输出：39970（前移一月）
```

## 🕐 时间函数详解

理解了日期函数后，本节我们来看看时间函数。EasyLanguage中的标准时间格式称为`ELTime`，通常是`HHMM`格式的数值（24小时制，精确到分钟）。若要精确到秒，函数名常包含`_S`后缀。在儒略日中，时间用小数部分表示。

### 获取当前与最后计算时间

以下是获取当前时间和最后一根K线计算时间的函数。

**获取当前时间**：
*   `CurrentTime`：获取当前时间（精确到分钟）。
*   `CurrentTime_S`：获取当前时间（精确到秒）。

**获取最后计算时间**：
*   `LastCalcTime`：返回最后一根已完成K线的`ELTime`格式时间。
*   `LastCalcTime_S`：返回最后一根已完成K线的`ELTime`格式时间（精确到秒）。
*   `LastCalcMMTime`：返回从当日凌晨到最后一根K线所经历的**分钟数**。
*   `LastCalcSSTime`：返回从当日凌晨到最后一根K线所经历的**秒数**。

**代码示例**：
```easylanguage
Print(LastCalcTime);    // 例如：1545 (表示15:45)
Print(LastCalcSSTime);  // 例如：56700 (表示从凌晨起过了56700秒)
```

### 时间格式转换函数

以下函数用于时间格式之间的转换。

**StringToTime 函数**：
将时间字符串转换为儒略日格式（小数部分）。模板中可用`HH`（24小时制）或`hh`（12小时制），并用`TT`表示AM/PM。

**函数公式**：
```
StringToTime(“TimeString”)
```

![](img/7c1df76f7d1341ee72600d05494b8fcd_7.png)

**代码示例**：
```easylanguage
Print(StringToTime(“15:30:00”)); // 输出儒略日小数部分，如 0.645833
```

**TimeToString 函数**：
将儒略日格式的时间（小数部分）转换为字符串。输出为24小时制。

**函数公式**：
```
TimeToString(JulianDateTime)
```

**代码示例**：
```easylanguage
Print(TimeToString(40000.33)); // 可能输出 “07:54:00”
```

**DateTimeToELTime 与 ELTimeToDateTime**：
*   `DateTimeToELTime(JulianDateTime)`：将儒略日日期时间转换为`ELTime`格式（`HHMM`）。
*   `ELTimeToDateTime(ELTime)`：将`ELTime`格式时间转换为儒略日时间（小数部分，日期为0）。

### FormatTime 函数

`FormatTime`函数用于将儒略日时间格式化为自定义字符串，功能与`FormatDate`类似，但专注于时间部分。

**函数公式**：
```
FormatTime(“Template”, JulianDateTime)
```

以下是格式字符说明：
*   `h` / `hh`：12小时制的小时（不补零/补零）。
*   `H` / `HH`：24小时制的小时（不补零/补零）。
*   `m` / `mm`：分钟（不补零/补零）。
*   `s` / `ss`：秒（不补零/补零）。
*   `t` / `tt`：AM/PM标识符（单字母/双字母）。

**代码示例**：
```easylanguage
Print(FormatTime(“HH:mm:ss”, 40000.33)); // 输出 “07:54:00”
Print(FormatTime(“hh:mm:ss tt”, 40000.75)); // 输出 “06:00:00 PM”
```

### 时间信息提取函数

以下函数用于从儒略日时间中提取具体的时、分、秒、毫秒。

**提取时间单位**：
*   `HoursFromDateTime(JulianDateTime)`：提取小时数（0-23）。
*   `MinutesFromDateTime(JulianDateTime)`：提取分钟数（0-59）。
*   `SecondsFromDateTime(JulianDateTime)`：提取秒数（0-59）。
*   `MilliSecondsFromDateTime(JulianDateTime)`：提取毫秒数。

**代码示例**：
```easylanguage
Print(HoursFromDateTime(40000.33)); // 输出 7
Print(MinutesFromDateTime(40000.33)); // 输出 54
```

## 📝 本节总结

本节课我们一起学习了EasyLanguage中日期与时间函数的核心内容。

我们主要掌握了：
1.  **关键转换**：`ELDateToString`、`FormatDate`、`StringToTime`、`TimeToString`、`FormatTime`等函数，用于在字符串、EL日期/时间和儒略日格式之间进行转换。
2.  **信息提取**：使用`DayOfMonth`、`Month`、`Year`、`HoursFromDateTime`、`MinutesFromDateTime`等函数从日期时间中提取特定部分。
3.  **日期运算**：使用`IncMonth`函数对日期进行加减操作。
4.  **时间获取**：使用`CurrentTime`、`LastCalcTime`等函数获取当前或K线计算时间。

![](img/7c1df76f7d1341ee72600d05494b8fcd_9.png)

理解并熟练运用这些函数，是处理交易策略中一切时间相关逻辑的基石。下一节课，我们将学习结合日期与时间的综合函数。