# 量化编程：16：彩虹色解析与指标画线 🎨📈

在本节课中，我们将深入学习如何解析和绘制彩虹色指标，并详细探讨EasyLanguage中`Plot`语句的各种用法。我们将从上一节的渐变色概念出发，逐步构建多条彩虹移动平均线，并学习如何绘制不同类型的图表元素。

## 彩虹色指标解析与绘制

上一节我们介绍了渐变色，并通过内置的彩虹指数移动平均线演示了其内核逻辑。其核心计算逻辑看似复杂，但通过逐层分析可以简化理解。

关键数值是`array2`，而`ARI3`用于存放颜色类型。因此，最关键的数值是`RY2`。`RY2`通过一个核心公式计算得出，其中涉及`ARI1`，而`ARI1`的计算又依赖于`ARI0`。这样一层层剥开分析，逻辑并不复杂。

![](img/f019cb22ab0bd7c7d32afeeb3c4fcbda_1.png)

![](img/f019cb22ab0bd7c7d32afeeb3c4fcbda_3.png)

使用MC这类三方平台，可以将主要精力集中于逻辑分析和研究，无需过多纠缠于语法细节。但如果进行开源开发，数据获取和处理将是需要反复研究的重要部分。

![](img/f019cb22ab0bd7c7d32afeeb3c4fcbda_5.png)

![](img/f019cb22ab0bd7c7d32afeeb3c4fcbda_7.png)

### 绘制多条彩虹线

绘制多条彩虹线最简单且最实用的方法是逐一罗列。你可以从`ram ma1`一直写到`ram ma8`。这种方式最简单有效。

当然，你也可以尝试使用数组来管理这些线，但这种方式需要注意一些问题。下面我们通过一个示例来演示。

首先，新建一个指标`demo16`，输入以下代码框架：
```easylanguage
Input: priceValue(Close);
Variables: rambleValue(0);
Arrays: rambleArray[8](0);
```

![](img/f019cb22ab0bd7c7d32afeeb3c4fcbda_9.png)

这里定义了一个一维数组`rambleArray`来存储8条线的值。注意，数组索引从0开始，但我们通常使用1到8。

![](img/f019cb22ab0bd7c7d32afeeb3c4fcbda_11.png)

接着，我们使用循环来计算每条线的值：
```easylanguage
For value1 = 1 to 8 Begin
    rambleArray[value1] = MyRainbow(priceValue, value1 * 10);
End;
```

其中，`MyRainbow`是上节课我们自定义的函数，用于计算单条彩虹线的值。这里传入价格`priceValue`和长度参数`value1 * 10`（即10, 20, ..., 80）。

![](img/f019cb22ab0bd7c7d32afeeb3c4fcbda_13.png)

![](img/f019cb22ab0bd7c7d32afeeb3c4fcbda_15.png)

![](img/f019cb22ab0bd7c7d32afeeb3c4fcbda_17.png)

然后，我们需要将数组中的值绘制出来：
```easylanguage
Plot1(rambleArray[1]);
Plot2(rambleArray[2]);
... // 以此类推，直到Plot8
```

**注意**：在循环中直接调用`MyRainbow`函数可能会遇到问题。因为每次循环调用时，函数内部可能错误地引用了前一次循环的状态，而不是数组中的前一个值。这会导致逻辑错误和绘图不准确。

![](img/f019cb22ab0bd7c7d32afeeb3c4fcbda_19.png)

![](img/f019cb22ab0bd7c7d32afeeb3c4fcbda_21.png)

正确的做法是将`MyRainbow`的核心逻辑直接嵌入循环中，并显式地引用数组的前一个元素进行计算。修改后的核心循环逻辑如下：
```easylanguage
If CurrentBar = 1 Then Begin
    rambleArray[1] = priceValue;
End;

![](img/f019cb22ab0bd7c7d32afeeb3c4fcbda_23.png)

If CurrentBar > 1 Then Begin
    For value1 = 2 to 8 Begin
        rambleArray[value1] = rambleArray[value1 - 1] + (priceValue - rambleArray[value1 - 1]) / (value1 * 10 + 1);
    End;
End;
```

![](img/f019cb22ab0bd7c7d32afeeb3c4fcbda_25.png)

![](img/f019cb22ab0bd7c7d32afeeb3c4fcbda_27.png)

这样就能确保每条线都基于正确的上一个值进行计算。

![](img/f019cb22ab0bd7c7d32afeeb3c4fcbda_29.png)

### 添加渐变色效果

要为这些线添加渐变色，我们需要使用`RainbowColor`函数。首先，定义一个数组来存储颜色值：
```easylanguage
Arrays: colorArray[8](0);
```

![](img/f019cb22ab0bd7c7d32afeeb3c4fcbda_31.png)

然后，在循环中为每个索引分配颜色值：
```easylanguage
For value1 = 1 to 8 Begin
    colorArray[value1] = RainbowColor(value1, 1, 8, Green, Blue);
End;
```

![](img/f019cb22ab0bd7c7d32afeeb3c4fcbda_33.png)

![](img/f019cb22ab0bd7c7d32afeeb3c4fcbda_35.png)

![](img/f019cb22ab0bd7c7d32afeeb3c4fcbda_37.png)

`RainbowColor`函数根据数值（`value1`，范围1-8）在绿色到蓝色的光谱中分配一个颜色。最后，在`Plot`语句中引用对应的颜色数组值即可。

![](img/f019cb22ab0bd7c7d32afeeb3c4fcbda_39.png)

编译并加载该指标，你就能看到一组带有从绿到蓝渐变色的移动平均线。

## 指标画线详解

![](img/f019cb22ab0bd7c7d32afeeb3c4fcbda_41.png)

![](img/f019cb22ab0bd7c7d32afeeb3c4fcbda_43.png)

EasyLanguage中，`Plot`语句主要用于在图表上绘制图形，共有五种类型：线条、柱状图、点、十字以及K线。一个脚本中最多可以绘制999条独立的线。

### Plot 语句参数详解

![](img/f019cb22ab0bd7c7d32afeeb3c4fcbda_45.png)

![](img/f019cb22ab0bd7c7d32afeeb3c4fcbda_47.png)

![](img/f019cb22ab0bd7c7d32afeeb3c4fcbda_49.png)

通过查看帮助文档（F1），可以了解`Plot`的完整参数列表：
`Plot[N] (Price, “Name”, Color, BGColor, Width, “String”);`

![](img/f019cb22ab0bd7c7d32afeeb3c4fcbda_51.png)

以下是每个参数的说明：

![](img/f019cb22ab0bd7c7d32afeeb3c4fcbda_53.png)

![](img/f019cb22ab0bd7c7d32afeeb3c4fcbda_55.png)

1.  **N**: 绘图编号，从1到999。不能使用循环变量，必须显式写出每个`Plot`语句。
2.  **Price**: 需要绘制的数值。可以使用偏移量，例如`Plot2(Close[2])`。正值表示向左（过去）偏移，负值表示向右（未来）偏移。
3.  **Name**: 线条的名称，将显示在图表图例中。这是一个**必需参数**，如果你想设置后面的颜色等参数，则必须提供名称。
4.  **Color**: 线条的颜色。
5.  **BGColor**: 背景颜色。此参数**仅在市场扫描器窗口中生效**，在普通图表窗口中不起作用。通常使用`Default`关键字。
6.  **Width**: 线宽，取值范围为1到14。
7.  **String**: 要显示的文本字符串。当使用此参数时，绘制的将不是线条，而是文本。这在市场扫描器中非常有用。

![](img/f019cb22ab0bd7c7d32afeeb3c4fcbda_57.png)

**重要提示**：参数传递是基于位置的。如果你想跳过某个可选参数（例如，想设置颜色但不想设置名称），这是不允许的。你必须为前面的所有参数提供值（或使用`Default`占位）。

![](img/f019cb22ab0bd7c7d32afeeb3c4fcbda_59.png)

![](img/f019cb22ab0bd7c7d32afeeb3c4fcbda_61.png)

### 在市场扫描器中的应用

![](img/f019cb22ab0bd7c7d32afeeb3c4fcbda_63.png)

![](img/f019cb22ab0bd7c7d32afeeb3c4fcbda_65.png)

![](img/f019cb22ab0bd7c7d32afeeb3c4fcbda_67.png)

市场扫描器是一个实时监控多个合约数据的工具。你可以在“文件”->“新建”->“市场扫描器窗口”中打开它。

![](img/f019cb22ab0bd7c7d32afeeb3c4fcbda_69.png)

![](img/f019cb22ab0bd7c7d32afeeb3c4fcbda_71.png)

![](img/f019cb22ab0bd7c7d32afeeb3c4fcbda_73.png)

当你在指标中使用`Plot`输出字符串时，该字符串会显示在市场扫描器对应的列中。例如：
```easylanguage
Plot1(Close, “ClosePrice”, Default, Cyan, 1, “Hello”);
```
这会在市场扫描器中创建一个名为“ClosePrice”的列，并在其中显示文本“Hello”，同时该单元格的背景色为青色。

![](img/f019cb22ab0bd7c7d32afeeb3c4fcbda_75.png)

![](img/f019cb22ab0bd7c7d32afeeb3c4fcbda_77.png)

![](img/f019cb22ab0bd7c7d32afeeb3c4fcbda_79.png)

你还可以在代码中动态设置这些属性：
```easylanguage
SetPlotBGColor(1, Cyan); // 设置第一个Plot的背景色
SetPlotColor(2, White); // 设置第二个Plot的文本颜色
```
这为实时监控和警报提供了很大的灵活性。

![](img/f019cb22ab0bd7c7d32afeeb3c4fcbda_81.png)

### 使用 Default 关键字

![](img/f019cb22ab0bd7c7d32afeeb3c4fcbda_83.png)

![](img/f019cb22ab0bd7c7d32afeeb3c4fcbda_85.png)

`Default`关键字表示使用用户在图表属性中设置的默认样式。如果你在`Plot`语句中明确指定了颜色、线宽等，那么用户在图表上就无法再修改这些属性。反之，如果你使用`Default`，或者不填写该参数，则用户可以在加载指标后自由调整样式。

![](img/f019cb22ab0bd7c7d32afeeb3c4fcbda_87.png)

![](img/f019cb22ab0bd7c7d32afeeb3c4fcbda_89.png)

![](img/f019cb22ab0bd7c7d32afeeb3c4fcbda_91.png)

例如：
- `Plot1(Close, “MA”, Red);` -> 线条永远是红色，用户无法更改。
- `Plot1(Close, “MA”, Default);` -> 线条颜色默认为用户设置，且用户可以随时修改。

![](img/f019cb22ab0bd7c7d32afeeb3c4fcbda_93.png)

![](img/f019cb22ab0bd7c7d32afeeb3c4fcbda_95.png)

![](img/f019cb22ab0bd7c7d32afeeb3c4fcbda_97.png)

### 绘制K线图

![](img/f019cb22ab0bd7c7d32afeeb3c4fcbda_99.png)

![](img/f019cb22ab0bd7c7d32afeeb3c4fcbda_101.png)

使用`PlotPaintBar`或`PlotPB`语句可以绘制自定义的K线图。其基本语法需要提供四个价格序列：
`PlotPaintBar(High, Low, Open, Close, “Name”);`

![](img/f019cb22ab0bd7c7d32afeeb3c4fcbda_103.png)

![](img/f019cb22ab0bd7c7d32afeeb3c4fcbda_105.png)

![](img/f019cb22ab0bd7c7d32afeeb3c4fcbda_107.png)

你必须在图表的“样式”设置中，将对应的绘图类型选择为“Bar High/Low (LeftTick/RightTick)”，才能正确显示K线。

![](img/f019cb22ab0bd7c7d32afeeb3c4fcbda_109.png)

![](img/f019cb22ab0bd7c7d32afeeb3c4fcbda_111.png)

![](img/f019cb22ab0bd7c7d32afeeb3c4fcbda_113.png)

下节课，我们将利用这个功能，学习如何绘制抛开时间因素的“砖形图”。

![](img/f019cb22ab0bd7c7d32afeeb3c4fcbda_115.png)

![](img/f019cb22ab0bd7c7d32afeeb3c4fcbda_117.png)

## 总结

![](img/f019cb22ab0bd7c7d32afeeb3c4fcbda_119.png)

本节课我们一起深入学习了两个核心内容：
1.  **彩虹色指标**：我们解析了其内部逻辑，学习了如何通过数组和循环绘制多条带有渐变色效果的移动平均线，并注意了在循环中处理前后值依赖的关键点。
2.  **指标画线**：我们详细探讨了`Plot`语句的各类参数、五种绘图类型，以及如何在线条、文本模式间切换。特别强调了参数的位置传递规则、`Default`关键字的作用，以及`Plot`在市场扫描器中的特殊应用（如显示文本和背景色）。最后，我们介绍了绘制自定义K线图的基础方法。

![](img/f019cb22ab0bd7c7d32afeeb3c4fcbda_121.png)

![](img/f019cb22ab0bd7c7d32afeeb3c4fcbda_123.png)

![](img/f019cb22ab0bd7c7d32afeeb3c4fcbda_125.png)

掌握这些绘图技巧，是构建直观、丰富量化分析图表的基础。