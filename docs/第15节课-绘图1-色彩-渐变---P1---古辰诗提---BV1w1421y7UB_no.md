# 量化交易编程：第15课：绘图与色彩应用

在本节课中，我们将开始学习第六章“绘图”的内容。绘图的核心是在图表上绘制所需的线条、箭头等元素。本节课将重点讲解色彩设置、渐变色应用，并通过分析内置的“彩虹移动平均线”指标，学习如何编写自定义函数和指标。

![](img/692a4b959385c8bc21d400907866f41b_1.png)

![](img/692a4b959385c8bc21d400907866f41b_3.png)

## 色彩基础与设置

EasyLanguage可以为图表上的划线、趋势线、文字或箭头等物件设定颜色。系统预定义了17种基本颜色关键字，同时也支持通过RGB指令直接指定三原色来创建更丰富的色彩。

**预定义颜色关键字**包括：`Black`、`Blue`、`Cyan`、`Green`等。你可以直接在代码中使用这些关键字。

**RGB颜色**：所有颜色均由红（Red）、绿（Green）、蓝（Blue）三原色混合而成。在计算机中，每种原色有256个强度等级（0-255）。因此，RGB颜色总数为 `256 * 256 * 256 = 16,777,216` 种，其取值范围为 `0` 到 `16,777,215`。

在MC中，预定义的17种颜色被称为“传统色”（Legacy Color），其取值范围为 `0` 到 `16`。你可以使用数字编码或关键字来指定它们。

### 使用传统色码绘图

以下是一个简单的绘图示例，使用 `Plot` 函数绘制一条移动平均线。

![](img/692a4b959385c8bc21d400907866f41b_5.png)

```easylanguage
Inputs: Price(Close), Length(20);
Variables: MAValue(0);

![](img/692a4b959385c8bc21d400907866f41b_7.png)

MAValue = Average(Price, Length);

Plot1(MAValue, "MovingAverage1");
SetPlotColor(1, Blue); // 使用关键字指定颜色
```

**重要提示**：如果你想在 `SetPlotColor` 或 `Plot` 的 `Color` 参数中直接使用传统色的数字编码（如 `1` 代表蓝色），而非关键字，则必须在代码顶部启用传统色值。否则，数字编码可能不会被正确识别。

启用传统色值的方法如下：

```easylanguage
{ 在代码文件顶部添加以下属性声明 }
[LegacyColorValue = true]

Inputs: Price(Close), Length(20);
Variables: MAValue(0);

MAValue = Average(Price, Length);

Plot1(MAValue, "MovingAverage1");
SetPlotColor(1, 4); // 现在数字‘4’（代表Cyan青色）可以被正确识别
```

属性声明 `[LegacyColorValue = true]` 告诉编译器启用传统颜色数值。这与我们之前接触过的 `[IntraBarOrderGeneration = true]` 等属性声明用法类似。

### 使用RGB颜色绘图

除了传统色，你还可以直接使用RGB数值来指定颜色，这提供了极大的灵活性。

```easylanguage
Plot1(MAValue, "MovingAverage1");
SetPlotColor(1, RGB(10, 150, 200)); // 使用RGB函数直接指定颜色
```

![](img/692a4b959385c8bc21d400907866f41b_9.png)

![](img/692a4b959385c8bc21d400907866f41b_11.png)

`RGB` 函数接受三个参数，分别代表红、绿、蓝的强度（0-255）。

此外，系统还提供了 `GetRValue`、`GetGValue`、`GetBValue` 和 `GetRGBValues` 等函数，用于从颜色值中提取或获取RGB分量，但在基础绘图中较少使用。

## 渐变色的应用

渐变色功能可以根据一个数值在定义范围内的变化，动态地改变线条的颜色。这在显示超买超卖指标（如RSI）时非常有用，可以直观地通过颜色变化提示风险。

函数语法如下：
`GradientColor(Numeric Expression, MinValue, MaxValue, BeginColor, EndColor)`

*   **Numeric Expression**: 数值表达式，其值将决定颜色。
*   **MinValue, MaxValue**: 定义数值的变化范围。
*   **BeginColor, EndColor**: 定义颜色范围的起始色和结束色。当数值等于`MinValue`时，显示`BeginColor`；等于`MaxValue`时，显示`EndColor`；在两者之间时，显示过渡的渐变色。

### 渐变色示例：为RSI指标着色

让我们创建一个RSI指标，并使其数值在30以下时偏黄色，在70以上时偏红色，中间区域呈现渐变。

```easylanguage
Inputs: RsiLength(14);
Variables: RsiValue(0);

RsiValue = RSI(Close, RsiLength);

// 绘制RSI线，并使用渐变色
Plot1(RsiValue, "MyRSI");
SetPlotColor(1, GradientColor(RsiValue, 10, 80, Yellow, Red));
```

在这个例子中：
*   `RsiValue` 是动态变化的数值。
*   我们设定其颜色变化范围为 `10` 到 `80`。
*   当 `RsiValue` 接近10（下限）时，线条颜色接近黄色（`Yellow`）。
*   当 `RsiValue` 接近80（上限）时，线条颜色接近红色（`Red`）。
*   在10到80之间，颜色在黄到红之间平滑过渡。

这样，图表上的RSI线就不再是单一颜色，而是能直观反映超买超卖状态的彩色线条。

## 实战：解析与重建“彩虹移动平均线”

MC内置了一个名为“彩虹移动平均线”（Rainbow Moving Average）的指标，它由一组颜色渐变的均线组成，完美运用了渐变色技术。通过分析并重建这个指标，我们可以深入学习复杂指标的逻辑和自定义函数的编写。

### 1. 分析内置指标逻辑

首先，我们查看内置彩虹指标的代码。其核心是一个循环计算，为每条均线赋予不同的平滑参数和颜色。关键点在于计算均线值的数组和对应的颜色数组。

我们发现其核心计算公式为一种特殊的指数平滑：
`当前均线值 = 前一期均线值 + (平滑系数) * (价格 - 前一期均线值)`
其中，每条线的平滑系数不同，由 `2 / (基准长度 + 索引值)` 决定。

### 2. 提取核心逻辑编写函数

我们不需要完全复制复杂的代码，而是提取其最核心的计算逻辑，封装成一个可重用的函数。

首先，我们创建一个计算单条彩虹均线的函数 `MyRainbowMA`：

```easylanguage
{ 函数：计算彩虹移动平均线值 }
Inputs: PriceValue(NumericSeries), BaseLength(NumericSimple);
Variables: MyRainbow(0);

If CurrentBar = 1 Then
    MyRainbow = PriceValue
Else
    MyRainbow = MyRainbow[1] + (2 / (BaseLength + 1)) * (PriceValue - MyRainbow[1]);

MyRainbowMA = MyRainbow;
```

**代码解释**：
*   `Inputs` 定义了输入参数：`PriceValue`（价格序列，如Close）和 `BaseLength`（基准长度，决定平滑度）。
*   当处理第一根Bar时，直接将均线值初始化为当前价格。
*   从第二根Bar开始，应用核心的递归计算公式进行平滑。
*   函数返回计算得到的 `MyRainbow` 值。

### 3. 使用函数构建指标

有了核心函数后，创建彩虹指标就变得非常简单。我们新建一个指标，调用这个函数来计算并绘制均线。

```easylanguage
{ 指标：我的彩虹移动平均线 }
Inputs: BaseLength(10);

Variables: RainbowMAValue(0);

![](img/692a4b959385c8bc21d400907866f41b_13.png)

// 调用自定义函数计算彩虹均线值
RainbowMAValue = MyRainbowMA(Close, BaseLength);

![](img/692a4b959385c8bc21d400907866f41b_15.png)

// 绘制均线
Plot1(RainbowMAValue, "MyRainbow");
SetPlotColor(1, White);
```

**指标逻辑**：
*   接收一个参数 `BaseLength`。
*   调用之前编写的 `MyRainbowMA` 函数，传入收盘价 `Close` 和基准长度 `BaseLength`，得到计算后的均线值。
*   使用 `Plot1` 将这个值绘制在图表上。

通过修改 `BaseLength` 参数，我们可以绘制出平滑度不同的单条彩虹均线。如果需要多条，只需创建多个变量，调用函数时传入不同的 `BaseLength` 值（例如 10, 20, 30...），并分别绘制即可。

### 4. 函数中的参数传递进阶

在上面的函数中，我们通过函数名 `MyRainbowMA` 直接返回一个值。这是最常用的方式。如果需要从函数中输出多个值，可以使用 `NumericRef` 类型的参数（按引用传递）。

例如，修改函数，使其同时返回计算值和另一个自定义值：

```easylanguage
Inputs: PriceValue(NumericSeries),
        BaseLength(NumericSimple),
        OutExtraValue(NumericRef); // 声明一个引用参数用于输出额外值

Variables: MyRainbow(0);

If CurrentBar = 1 Then
    MyRainbow = PriceValue
Else
    MyRainbow = MyRainbow[1] + (2 / (BaseLength + 1)) * (PriceValue - MyRainbow[1]);

MyRainbowMA = MyRainbow;
OutExtraValue = BaseLength * 2; // 通过引用参数传出一个计算值
```

在指标中调用时：

```easylanguage
Inputs: BaseLength(10);
Variables: RainbowMAValue(0), ExtraVal(0);

// 调用函数，ExtraVal将接收函数内部赋给OutExtraValue的值
RainbowMAValue = MyRainbowMA(Close, BaseLength, ExtraVal);

Plot1(RainbowMAValue, "MyRainbow");
Print("Extra Value: ", ExtraVal); // 可以打印或使用这个额外值
```

这种方式突破了函数只能返回一个主值的限制，使得函数功能更加灵活。

## 总结

![](img/692a4b959385c8bc21d400907866f41b_17.png)

在本节课中，我们一起学习了MC中绘图的基础——色彩系统。
*   我们了解了如何使用预定义的传统色和通过RGB值自定义颜色，并掌握了启用传统色数字编码的关键步骤。
*   我们探索了**渐变色**的原理和应用，学会了如何让指标线根据数值动态变化颜色，以增强图表的可读性。
*   最后，我们通过**实战演练**，深入剖析了内置的“彩虹移动平均线”指标，并成功将其核心逻辑提取出来，编写成自定义函数和指标。这个过程巩固了我们对函数封装、参数传递以及指标构建的理解，是迈向编写复杂量化策略的重要一步。