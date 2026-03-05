# 从零开始量化：14：基本流程控制

在本节课中，我们将学习EasyLanguage中的基本流程控制。流程控制决定了代码的执行顺序和逻辑，是编写任何程序的基础。掌握了这些，你就能开始构建自己的交易逻辑了。

## 概述

流程控制语句是编程的骨架，它允许我们根据条件执行不同的代码块，或者重复执行某些操作。本节课将详细介绍六种基本的流程控制结构：`if-then`、`begin-end`、`once`、`switch-case`、`for`循环和`while`循环。

---

![](img/491e701777ae6ef227367c3c84530847_1.png)

![](img/491e701777ae6ef227367c3c84530847_3.png)

## if-then 条件判断 🧐

`if-then`是最常用、最普遍的控制语句。它的逻辑非常直观：**如果**某个条件成立，**那么**就执行相应的代码。

它有四种基本形态：

1.  **简单判断**：`if` 条件成立，则 `then` 执行代码。
    ```easyLanguage
    if (condition) then
        DoSomething;
    ```

2.  **二选一**：`if` 条件成立，则执行代码块A；`else` 执行代码块B。
    ```easyLanguage
    if (condition) then
        DoSomethingA
    else
        DoSomethingB;
    ```

3.  **多条件判断**：使用 `else if` 串联多个条件。
    ```easyLanguage
    if (condition1) then
        DoSomething1
    else if (condition2) then
        DoSomething2
    else
        DoSomethingElse;
    ```

4.  **三元运算符**：`iff` 函数可以简化简单的 `if-else` 赋值操作。
    ```easyLanguage
    value1 = iff(condition, valueIfTrue, valueIfFalse);
    ```

**重要注意事项**：
*   `if` 后面的必须是能返回 `true` 或 `false` 的布尔表达式。
*   代码从左到右执行，一旦某个 `if` 或 `else if` 的条件满足，就会执行其对应的代码块，并且**跳过**后面所有的 `else if` 和 `else`。因此，条件的顺序至关重要。

---

## begin-end 代码块 📦

上一节我们介绍了单行代码的执行，但通常我们需要在条件成立时执行多行代码。这时就需要使用 `begin-end` 将多行语句包装成一个代码块。

`begin` 和 `end` 必须成对出现，标志着代码块的开始和结束。良好的缩进习惯能极大提高代码的可读性。

以下是使用 `begin-end` 的示例：
```easyLanguage
if (condition) then begin
    Statement1;
    Statement2;
    Statement3;
end
else begin
    StatementA;
    StatementB;
end;
```

---

## once 一次性执行 ⏱️

我们知道，策略或指标代码默认会在每根K线上都执行一次。但有些初始化操作（如检查周期、账户）只需要执行一次。这时就需要使用 `once` 关键字。

![](img/491e701777ae6ef227367c3c84530847_5.png)

`once` 后可以跟单行代码，也可以与 `begin-end` 联用形成一个代码块。

![](img/491e701777ae6ef227367c3c84530847_7.png)

```easyLanguage
once Print(“策略开始运行”); // 只在第一次运行时打印

![](img/491e701777ae6ef227367c3c84530847_9.png)

![](img/491e701777ae6ef227367c3c84530847_11.png)

once begin
    // 初始化变量或进行一次性检查
    if (BarType <> 2) then
        RaiseRunTimeError(“本策略仅适用于5分钟周期”);
end;
```

![](img/491e701777ae6ef227367c3c84530847_13.png)

**一个重要提示**：在指标中，如果设置项（如计算周期）选择“自动检测”，平台可能会多次尝试加载代码以确定最佳参数，这会导致 `once` 块被执行多次。若需严格只执行一次，可将相关参数设置为“用户指定”。

![](img/491e701777ae6ef227367c3c84530847_15.png)

![](img/491e701777ae6ef227367c3c84530847_17.png)

---

![](img/491e701777ae6ef227367c3c84530847_19.png)

![](img/491e701777ae6ef227367c3c84530847_21.png)

![](img/491e701777ae6ef227367c3c84530847_23.png)

## switch-case 多路选择 🔀

![](img/491e701777ae6ef227367c3c84530847_25.png)

`if-then` 适合基于布尔表达式的分支，而 `switch-case` 则更适合基于**一个特定表达式**（数值或字符串）结果的多路选择。它使代码结构更清晰。

![](img/491e701777ae6ef227367c3c84530847_27.png)

![](img/491e701777ae6ef227367c3c84530847_29.png)

![](img/491e701777ae6ef227367c3c84530847_31.png)

其基本结构是：计算 `switch` 后的表达式，然后根据 `case` 匹配到的值执行相应的代码块。`default` 用于处理所有 `case` 都不匹配的情况。

![](img/491e701777ae6ef227367c3c84530847_33.png)

我们可以将之前判断周期画不同均线的例子改写为 `switch-case`：
```easyLanguage
switch (BarTypeEx) begin
    case 4: // 日线
        Plot1(Average(Close, 5));
    case 2: // 分钟线
        Plot2(Average(Close, 20));
    case 3: // 小时线
        Plot3(Average(Close, 10));
    default: // 其他周期
        Plot4(Average(Close, 30));
end;
```

**扩展用法**：
*   多个值执行相同代码：`case 1, 2, 3:`
*   数值范围：`case 10 to 20:`
*   配合逻辑运算符：`case > 100:`

---

![](img/491e701777ae6ef227367c3c84530847_35.png)

![](img/491e701777ae6ef227367c3c84530847_37.png)

## for 循环 🔁

`for` 循环用于重复执行代码块特定的次数。我们在讲解数组时已经重点使用过它。

![](img/491e701777ae6ef227367c3c84530847_39.png)

![](img/491e701777ae6ef227367c3c84530847_41.png)

它有两种计数方向：
*   `for` 变量 = 起始值 `to` 结束值：递增循环。
*   `for` 变量 = 起始值 `downto` 结束值：递减循环。

![](img/491e701777ae6ef227367c3c84530847_43.png)

循环体通常用 `begin-end` 包裹。
```easyLanguage
for i = 0 to 9 begin
    Print(“循环次数: ”, i);
    Sum = Sum + MyArray[i];
end;
```

![](img/491e701777ae6ef227367c3c84530847_45.png)

![](img/491e701777ae6ef227367c3c84530847_47.png)

![](img/491e701777ae6ef227367c3c84530847_49.png)

---

![](img/491e701777ae6ef227367c3c84530847_51.png)

## while 循环 🔄

`while` 循环会在条件为 `true` 时持续执行代码块。它特别适合在循环次数不确定时使用。

**最关键的一点是：必须确保循环有出口，避免死循环**。通常在循环体内需要修改条件判断所依赖的变量。

```easyLanguage
value1 = 0;
while (value1 < 20) begin
    Print(“当前值: ”, value1);
    value1 = value1 + 2; // 修改条件变量，确保能退出循环
end;
```

**循环控制**：在循环体内，可以使用 `break` 语句立即终止整个循环。
```easyLanguage
while (condition) begin
    // ... 一些操作 ...
    if (specialCondition) then
        break; // 立即跳出while循环
    // ... break之后的代码不会被执行 ...
end;
```

---

## 总结

本节课我们一起学习了EasyLanguage中六种核心的流程控制结构：
1.  **`if-then(-else)`**：基于条件执行不同分支。
2.  **`begin-end`**：将多行语句组合成代码块。
3.  **`once`**：确保代码仅在首次运行时执行。
4.  **`switch-case`**：基于表达式的值进行多路选择。
5.  **`for` 循环**：执行已知次数的重复操作。
6.  **`while` 循环**：在条件满足时重复执行，需注意防止死循环。

![](img/491e701777ae6ef227367c3c84530847_53.png)

理解并熟练运用这些流程控制，是编写逻辑严谨、功能强大的交易策略与指标的基础。请务必通过练习来巩固这些概念。