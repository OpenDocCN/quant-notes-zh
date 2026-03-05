# 从零开始量化：第20课：绘图6 - 文字2 📝

在本节课中，我们将继续深入学习MC平台中文本（Text）对象的高级应用。我们将重点介绍如何获取和设置文本的各种属性，并学习一个关键函数 `GetAppInfo`，它允许我们获取图表界面的信息，从而实现将文本动态、准确地放置在图表上的任意位置。

![](img/a9aa4ba75239969cdcfd796690ef6b6a_1.png)

上一节我们介绍了文本的基本创建和删除，本节中我们来看看如何获取图表信息并利用它来精确定位文本。

![](img/a9aa4ba75239969cdcfd796690ef6b6a_3.png)

![](img/a9aa4ba75239969cdcfd796690ef6b6a_5.png)

---

## 文本属性设置与获取

以下是文本对象相关的一系列函数，用于设置和获取其样式、位置等属性。

### 设置字体
`TextSetPoint` 用于设置指定编号文本的字体。返回0表示设置成功，返回2表示无效。
```easy
TextSetPoint(TextID, FontName);
```

### 设置字体大小
`TextSetSize` 用于设置指定编号文本的字体大小。返回0表示设置成功，返回2表示无效。字体大小不宜设置过大。
```easy
TextSetSize(TextID, FontSize);
```

### 设置对齐方式
`TextSetStyle` 用于设置文本与K线的对齐方式，分为水平（H）和垂直（V）两个方向。
*   **水平对齐（H）**：0-左侧对齐，1-右侧对齐，2-中央对齐。
*   **垂直对齐（V）**：0-底部对齐，1-顶部对齐，2-中央对齐。

### 设置边框
`TextSetBorder` 用于为文本增加或取消边框。`Value2` 参数为 `True` 时显示边框，为 `False` 时取消边框。
```easy
TextSetBorder(TextID, True); // 显示边框
```

### 设置文本属性
`TextSetAttribute` 用于设置文本的附加样式，如加粗、斜体、外框、删除线、下划线等。`Attribute` 参数对应不同的样式。
```easy
TextSetAttribute(TextID, Attribute, True); // 启用某项属性
```

### 获取活动文本ID
`TextGetActive` 用于获取最新创建的文本ID号，其逻辑与趋势线等绘图对象相同。

### 获取文本的日期与时间
`TextGetDate` 和 `TextGetTime` 用于获取文本创建时的日期和时间。`TextGetTimeDT` 则返回掳掠日格式的日期时间（整数部分为日期，小数部分为时间）。

### 获取文本对应的价格
`TextGetValue` 用于获取指定文本编号所对应文字的垂直价格。这个价格取决于创建文本时 `Price` 参数所设置的值（如 `High`, `Close`, `Low`）。
```easy
Value2 = TextGetValue(TextID);
```

### 获取文本内容与颜色
`TextGetString` 用于获取文本的内容。`TextGetColor` 用于获取文本的颜色，如需获取传统色码，需将 `LegacyColorValue` 参数设为 `True`。

### 获取背景色与字体信息
`TextGetBGColor` 用于获取文本的背景色。`TextGetFontName` 和 `TextGetSize` 分别用于获取字体名称和字形大小。

### 获取对齐方式与边框状态
`TextGetHStyle` 和 `TextGetVStyle` 用于获取文本的水平与垂直对齐方式。`TextGetBorder` 返回一个布尔值，表示文本是否有边框。

### 获取文本属性状态
`TextGetAttribute` 用于获取文本的某项属性（如加粗、斜体等）是否被启用，返回一个布尔值。

---

## 动态定位文本：GetAppInfo 函数

要在图表上自由地放置文本，我们需要知道图表当前的坐标范围。`GetAppInfo` 函数可以返回应用程序（图表）的特定属性信息。

该函数的核心用途是获取图表价格轴和时间轴的刻度范围。
```easy
Value1 = GetAppInfo(aiHighestDispValue); // 获取价格刻度最大值
Value2 = GetAppInfo(aiLowestDispValue);  // 获取价格刻度最小值
Value3 = GetAppInfo(aiLeftDispDateTime); // 获取时间刻度最左侧的掳掠日
Value4 = GetAppInfo(aiRightDispDateTime); // 获取时间刻度最右侧的掳掠日
```
有了这四个值，我们就可以计算出图表上任意一点的坐标。

---

## 实战：在图表中央动态显示指标值

让我们结合以上知识，编写一个在图表中央动态显示移动平均线（MA）值的指标。

**步骤与逻辑：**
1.  **获取图表坐标**：使用 `GetAppInfo` 获取价格和时间的最大、最小值。
2.  **计算中心点**：计算时间和价格的中心坐标。
3.  **转换时间格式**：将掳掠日时间转换为EL可用的日期和时间。
4.  **创建文本**：在计算出的中心点位置创建文本，显示当前MA值。
5.  **美化文本**：设置文本的字体大小和颜色，使其更醒目。
6.  **管理文本对象**：使用 `TextDelete` 确保每次更新前删除旧文本，避免重叠。

以下是完整的示例代码：
```easy
Inputs: MALength(30);

Vars: MyTextID(0), UpPrice(0), LeftTime(0), RightTime(0), MidJulian(0), MidDate(0), MidTime(0), MAValue(0), DisplayText("");

// 1. 计算指标值
MAValue = Average(Close, MALength);

// 2. 获取图表界面信息
UpPrice = GetAppInfo(aiHighestDispValue); // 价格轴顶部
LeftTime = GetAppInfo(aiLeftDispDateTime); // 时间轴左侧
RightTime = GetAppInfo(aiRightDispDateTime); // 时间轴右侧

// 3. 计算图表中央的坐标（价格和时间）
MidJulian = (LeftTime + RightTime) / 2; // 时间中心（掳掠日格式）
MidDate = JulianToDate(MidJulian); // 转换为EL日期
MidTime = DateTimeToTime(MidJulian); // 转换为EL时间

![](img/a9aa4ba75239969cdcfd796690ef6b6a_7.png)

![](img/a9aa4ba75239969cdcfd796690ef6b6a_9.png)

// 4. 构建要显示的文本内容
DisplayText = "MA(" + NumToStr(MALength, 0) + ") = " + NumToStr(MAValue, 2);

// 5. 删除旧的文本（避免重叠），然后创建新文本
If LastBarOnChart then Begin
    TextDelete(MyTextID); // 关键步骤：先删除
    MyTextID = TextNew(MidDate, MidTime, UpPrice - 10, DisplayText);
    
    // 6. 设置文本样式
    TextSetSize(MyTextID, 20);
    TextSetColor(MyTextID, Cyan);
End;
```

**代码解析与注意事项：**
*   **动态更新**：由于代码运行在每根K线上（`LastBarOnChart` 条件），文本会随着图表的滚动或缩放而自动重新计算位置，始终保持在视觉中央。
*   **删除的重要性**：`TextDelete(MyTextID)` 是至关重要的步骤。如果不删除旧文本，每次更新都会创建一个新文本，导致大量文本重叠堆积，影响图表清晰度。
*   **灵活性**：你可以修改 `DisplayText` 的构建方式，加入任何你想监控的指标或变量，使其成为一个强大的信息面板。

---

![](img/a9aa4ba75239969cdcfd796690ef6b6a_11.png)

## 总结 🎯

![](img/a9aa4ba75239969cdcfd796690ef6b6a_13.png)

![](img/a9aa4ba75239969cdcfd796690ef6b6a_15.png)

![](img/a9aa4ba75239969cdcfd796690ef6b6a_17.png)

本节课中我们一起学习了文本对象更深入的操作方法，并掌握了一个强大的工具 `GetAppInfo` 函数。

1.  **文本属性**：我们系统学习了如何设置和获取文本的字体、大小、颜色、边框、对齐方式等各种属性。
2.  **动态定位**：通过 `GetAppInfo` 函数，我们能够获取图表的价格和时间刻度范围，从而计算出任意位置的坐标，实现了文本的精准、动态放置。
3.  **实战应用**：我们完成了一个实战案例，在图表中央动态显示移动平均线的值。这个案例的核心逻辑是：**获取坐标 -> 计算位置 -> 创建文本 -> 美化样式 -> 管理对象（删除旧文本）**。

![](img/a9aa4ba75239969cdcfd796690ef6b6a_19.png)

将文本与图表信息获取结合，你可以在图表上创建灵活的信息提示板，实时显示关键指标、价格差、信号状态等，极大地提升了策略监控和图表分析的效率。请务必动手实践，并牢记管理绘图对象（先删后建）的原则。