# 从零开始量化：27：交易信号及委托（五）📊

在本节课中，我们将继续学习MultiCharts中的内建出场函数。上一节我们介绍了内建出场函数的基本概念和特点，本节我们将通过具体代码演示其用法，并讲解如何为交易信号命名以及使用其他策略部位函数。

![](img/be882af3f0d99b5062add423f9732e83_1.png)

内建出场函数以 `Set` 开头，其最大特点是代码简洁，并且所有操作都是基于 `Tick` 级别执行的，无论代码中是否启用了 `Intrabar Order Generation` 选项。

![](img/be882af3f0d99b5062add423f9732e83_3.png)

---

## 内建出场函数演示

![](img/be882af3f0d99b5062add423f9732e83_5.png)

上一节课的演示尚未结束，现在我们将代码粘贴过来并进行编译。

![](img/be882af3f0d99b5062add423f9732e83_1.png)

![](img/be882af3f0d99b5062add423f9732e83_7.png)

插入信号“27”后，编译成功。

![](img/be882af3f0d99b5062add423f9732e83_9.png)

![](img/be882af3f0d99b5062add423f9732e83_3.png)

接下来，我们添加出场函数。`Set` 开头的函数用于出场，而 `Exit On Close` 函数则不需要在前面添加 `Set`。

以下是 `Set Stop Position` 和 `Set Stop Contract` 的示例。

![](img/be882af3f0d99b5062add423f9732e83_5.png)

![](img/be882af3f0d99b5062add423f9732e83_11.png)

直接单独使用 `Exit On Close` 即可。编译后，可以看到图表上标注了 “End of Day” 平仓点。

![](img/be882af3f0d99b5062add423f9732e83_13.png)

![](img/be882af3f0d99b5062add423f9732e83_7.png)

![](img/be882af3f0d99b5062add423f9732e83_15.png)

在策略绩效报告中，可以看到 “End of Day Exit” 被单独标注出来。

![](img/be882af3f0d99b5062add423f9732e83_17.png)

![](img/be882af3f0d99b5062add423f9732e83_9.png)

![](img/be882af3f0d99b5062add423f9732e83_19.png)

MultiCharts的绩效报告非常全面，可以查看最大回撤、最大亏损交易、最后一笔交易等详细信息，并支持日、月、年等多种周期分析。

![](img/be882af3f0d99b5062add423f9732e83_21.png)

---

![](img/be882af3f0d99b5062add423f9732e83_23.png)

## 止损与止盈函数

![](img/be882af3f0d99b5062add423f9732e83_25.png)

![](img/be882af3f0d99b5062add423f9732e83_27.png)

现在，我们来看看如何使用 `Set` 函数设置止损和止盈。

![](img/be882af3f0d99b5062add423f9732e83_29.png)

### 设置止损

![](img/be882af3f0d99b5062add423f9732e83_31.png)

如果你想设置固定金额止损，例如亏损100元（10个点）时平仓，可以使用 `Set Stop Loss` 函数。

![](img/be882af3f0d99b5062add423f9732e83_33.png)

即使不写 `Set Stop Position` 的前置声明，编译也不会出错。

![](img/be882af3f0d99b5062add423f9732e83_35.png)

![](img/be882af3f0d99b5062add423f9732e83_11.png)

编译没有问题。

![](img/be882af3f0d99b5062add423f9732e83_37.png)

![](img/be882af3f0d99b5062add423f9732e83_13.png)

![](img/be882af3f0d99b5062add423f9732e83_39.png)

![](img/be882af3f0d99b5062add423f9732e83_41.png)

图表上会标注 “Stop Loss”。

![](img/be882af3f0d99b5062add423f9732e83_15.png)

![](img/be882af3f0d99b5062add423f9732e83_43.png)

绩效报告中也会有 “Stop Loss” 的记录。

![](img/be882af3f0d99b5062add423f9732e83_45.png)

![](img/be882af3f0d99b5062add423f9732e83_17.png)

虽然图表显示有十个点的波动，但止损函数会单独标注。

![](img/be882af3f0d99b5062add423f9732e83_47.png)

![](img/be882af3f0d99b5062add423f0d99b5062add423f9732e83_19.png)

![](img/be882af3f0d99b5062add423f9732e83_49.png)

尽管如此，**建议还是写上 `Set Stop Position` 声明**，以确保代码的清晰和一致性。

![](img/be882af3f0d99b5062add423f9732e83_51.png)

![](img/be882af3f0d99b5062add423f9732e83_25.png)

在绩效报告中对比，两者结果没有区别。

![](img/be882af3f0d99b5062add423f9732e83_53.png)

![](img/be882af3f0d99b5062add423f9732e83_27.png)

![](img/be882af3f0d99b5062add423f9732e83_55.png)

![](img/be882af3f0d99b5062add423f9732e83_57.png)

### 设置止盈

接下来是设置止盈，例如盈利1000元时平仓，使用 `Set Profit Target` 函数。

![](img/be882af3f0d99b5062add423f9732e83_37.png)

查看交易列表，可以看到 “Profit Target” 的记录。

![](img/be882af3f0d99b5062add423f9732e83_41.png)

![](img/be882af3f0d99b5062add423f9732e83_59.png)

当盈利达到1000元时，系统会自动平仓。

![](img/be882af3f0d99b5062add423f9732e83_61.png)

![](img/be882af3f0d99b5062add423f9732e83_43.png)

这相当于一个强制止盈和强制止损的功能。

![](img/be882af3f0d99b5062add423f9732e83_63.png)

---

## 其他内建出场函数

除了止损止盈，还有其他有用的内建函数。

![](img/be882af3f0d99b5062add423f9732e83_65.png)

### 盈亏平衡点出场

![](img/be882af3f0d99b5062add423f9732e83_67.png)

`Set Break Even` 函数用于在达到一定盈利后，价格回撤到盈亏平衡点时平仓。例如，设置盈利100元后启动保本。

![](img/be882af3f0d99b5062add423f9732e83_69.png)

![](img/be882af3f0d99b5062add423f9732e83_47.png)

编译后，图表上会出现 “Break Even Stop” 的标注。

![](img/be882af3f0d99b5062add423f9732e83_49.png)

### 回撤百分比出场

`Set Profit Target` 和 `Set Percent Trail` 可以组合使用，实现按比例回撤出场。例如，指定最大获利500元，当利润回撤25%时平仓。

![](img/be882af3f0d99b5062add423f9732e83_53.png)

在绩效报告中，可以看到平仓盈利大约是380元（500 * 75%）。

![](img/be882af3f0d99b5062add423f9732e83_57.png)

---

## 使用注意事项

内建 `Set` 函数非常方便，但使用时需要注意两点：

1.  **基于Tick更新**：所有 `Set` 函数都是基于 `Tick` 级别进行更新和计算的。
2.  **避免委托冲突**：当行情出现极端K线时，如果同时存在跟踪止盈和条件出场逻辑，可能会造成重复发送平仓委托。例如，价格在Tick级别触及止损先平仓，但K线收盘后条件出场逻辑可能再次触发。

为了避免这种情况，**在编写条件出场逻辑时，务必检查当前持仓状态**。可以使用 `MarketPosition` 函数来判断。

当平仓完成后，`MarketPosition` 的值会变为0，从而阻止条件出场逻辑再次执行。

![](img/be882af3f0d99b5062add423f9732e83_71.png)

需要注意的是，`MarketPosition` 在持多仓时为正数，持空仓时为负数。因此，在判断持仓数量时，需要使用绝对值函数 `AbsValue`。

![](img/be882af3f0d99b5062add423f9732e83_73.png)

**代码示例：检查持仓状态**
```easylanguage
// 使用绝对值确保持仓数量为正
If AbsValue(MarketPosition) > 0 Then Begin
    // 执行你的条件出场逻辑
End;
```

![](img/be882af3f0d99b5062add423f9732e83_75.png)

![](img/be882af3f0d99b5062add423f9732e83_77.png)

---

![](img/be882af3f0d99b5062add423f9732e83_79.png)

## 为交易信号命名

在实际策略中，为不同的进场信号命名有助于管理和追踪。以下是如何动态生成信号名称的示例。

![](img/be882af3f0d99b5062add423f9732e83_81.png)

假设我们想实现一个分批建仓的策略，名称格式为 “Buy-1-1”, “Buy-1-2”, “Sell-2-1” 等。

![](img/be882af3f0d99b5062add423f9732e83_83.png)

![](img/be882af3f0d99b5062add423f9732e83_85.png)

**实现思路：**
*   使用 `TradeAccount` 记录交易次数。
*   使用 `MarketPosition` 的绝对值表示当前是第几次加仓。
*   使用 `NumToStr` 函数将数字转换为字符串并拼接。

**代码示例：动态生成进场名称**
```easylanguage
Variables:
    Name("");

![](img/be882af3f0d99b5062add423f9732e83_87.png)

If LastBarOnChart Then Begin
    // 多头进场命名
    If MarketPosition > 0 Then Begin
        Name = "Buy-" + NumToStr(TradeAccount + 1, 0) + "-" + NumToStr(AbsValue(MarketPosition), 0);
        Buy Name Next Bar at Market;
    End;

    // 空头进场命名
    If MarketPosition < 0 Then Begin
        Name = "Sell-" + NumToStr(TradeAccount + 1, 0) + "-" + NumToStr(AbsValue(MarketPosition), 0);
        SellShort Name Next Bar at Market;
    End;
End;
```

编译运行后，可以在绩效报告中看到按规则命名的交易信号。

![](img/be882af3f0d99b5062add423f9732e83_89.png)

![](img/be882af3f0d99b5062add423f9732e83_85.png)

![](img/be882af3f0d99b5062add423f9732e83_91.png)

---

## 策略部位函数

最后，我们介绍几个常用的策略部位信息函数。

### 获取进场信号名称

![](img/be882af3f0d99b5062add423f9732e83_93.png)

使用 `EntryName` 函数可以获取指定持仓部位的进场信号名称。参数为0表示获取最近一笔平仓交易的进场名称。

![](img/be882af3f0d99b5062add423f9732e83_95.png)

```easylanguage
Print(EntryName(0)); // 打印最近平仓交易的进场名称
Print(EntryName(1)); // 打印当前持仓中，最早一笔进场的名称
```

![](img/be882af3f0d99b5062add423f9732e83_97.png)

如果指定索引的持仓不存在，`EntryName` 不会报错，但会返回空值。如果需要检查索引是否有效，可以使用 `EntryNameCheck` 函数，无效索引会引发错误。

![](img/be882af3f0d99b5062add423f9732e83_99.png)

### 获取持仓以来的Bar数

`BarsSinceEntry` 函数返回自指定持仓部位进场以来经过的Bar数量。

```easylanguage
Print(BarsSinceEntry(1)); // 打印当前持仓中，最早一笔进场以来的Bar数
Print(BarsSinceEntry(2)); // 打印当前持仓中，第二笔进场以来的Bar数
```

### 获取最大持仓数量

`MaxContracts` 函数返回指定交易（由 `TradeAccount` 索引）的最大持仓合约数。

![](img/be882af3f0d99b5062add423f9732e83_101.png)

```easylanguage
Print(MaxContracts(1)); // 打印第一次交易的最大持仓数
Print(MaxContracts(2)); // 打印第二次交易的最大持仓数
```

![](img/be882af3f0d99b5062add423f9732e83_103.png)

---

## 总结

本节课我们一起学习了以下内容：

1.  **内建出场函数的应用**：演示了 `Exit On Close`、`Set Stop Loss`、`Set Profit Target`、`Set Break Even` 和 `Set Percent Trail` 等函数的用法。
2.  **使用注意事项**：强调了内建函数基于 `Tick` 更新，并讲解了如何通过检查 `MarketPosition` 来避免与条件出场逻辑冲突。
3.  **动态信号命名**：学习了如何通过拼接字符串为分批进场的交易信号生成有意义的名称，便于策略分析和追踪。
4.  **策略部位函数**：介绍了 `EntryName`、`BarsSinceEntry`、`MaxContracts` 等用于获取持仓信息的函数。

![](img/be882af3f0d99b5062add423f9732e83_105.png)

MultiCharts内置了大量的函数和策略范例，关键在于了解其存在和用途，在实际需要时能够查阅和应用，而无需死记硬背。