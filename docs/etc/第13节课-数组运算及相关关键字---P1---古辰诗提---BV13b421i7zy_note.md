# 量化编程基础：13：数组运算及相关关键字 - P1 🧮

在本节课中，我们将深入学习数组的运算逻辑，并介绍EasyLanguage中与数组操作相关的核心关键字和函数。理解数组元素的移动、赋值和比较是编写高效量化策略的基础。

## 数组元素的移动逻辑 🔄

上一节我们介绍了数组的基本概念和初始化。本节中，我们来看看如何正确地移动数组中的元素。

数组元素的移动方向至关重要。如果逻辑错误，会导致整个数组的值变得相同。例如，考虑一个存储收盘价的数组 `ClosePrice`，其最大索引为 `MaxValue`。

**错误示例（从小到大移动）：**
```easyLanguage
For Value1 = 1 To MaxValue Begin
    ClosePrice[Value1] = ClosePrice[Value1 - 1];
End;
ClosePrice[MaxValue] = Close; // 存入新值
```
这种写法会导致数组所有元素最终都变成 `ClosePrice[0]` 的初始值。因为当 `Value1=1` 时，`ClosePrice[1]` 被赋值为 `ClosePrice[0]`。接着当 `Value1=2` 时，`ClosePrice[2]` 被赋值为已经变成 `ClosePrice[0]` 的 `ClosePrice[1]`，如此传递，整个数组的值都会被覆盖为 `ClosePrice[0]`。

**正确逻辑（从大到小移动）：**
正确的做法是先移动旧数据，再存入新值，避免覆盖问题。
```easyLanguage
// 方法一：使用索引0
For Value1 = MaxValue DownTo 1 Begin
    ClosePrice[Value1] = ClosePrice[Value1 - 1];
End;
ClosePrice[0] = Close; // 最新值存放在索引0

// 方法二：不使用索引0，从索引1开始存储有效数据
For Value1 = MaxValue DownTo 2 Begin
    ClosePrice[Value1] = ClosePrice[Value1 - 1];
End;
ClosePrice[1] = Close; // 最新值存放在索引1
```
核心原则是：**先迁移，后覆盖**。务必在编码前理清数据移动的路径，可以画图辅助理解。

## 多维数组的理解 📊

理解了一维数组后，多维数组就相对容易了。关键是要明白，多维数组的容量是各维度大小的乘积，而不是相加。

定义一个二维数组：
```easyLanguage
Array: OpenPrice[10, 10](0);
```
这个数组不是存储20个值（10+10），而是存储100个值（10 × 10）。它形成了一个10行10列的表格。访问元素时，需要指定两个索引：`OpenPrice[行索引, 列索引]`。

为多维数组赋值通常需要使用嵌套循环：
```easyLanguage
For Value1 = 10 DownTo 1 Begin
    For Value2 = 10 DownTo 1 Begin
        // 在一个维度上进行数据迁移
        OpenPrice[0, Value1] = OpenPrice[0, Value1 - 1];
    End;
    // 存入新值
    OpenPrice[0, 0] = Open;
End;
```
**注意**：在EasyLanguage中，多维数组的索引必须在声明时固定为常数（如 `10`），不能使用变量（如 `MaxValue`），这限制了其灵活性。因此，建议优先使用多个一维动态数组，而不是高维数组。

## 数组相关关键字与函数 🛠️

![](img/c074c4c012fbdc80b504dcf5720f48fe_1.png)

![](img/c074c4c012fbdc80b504dcf5720f48fe_3.png)

以下是EasyLanguage中处理数组的一些内置关键字和函数。

### ArraySet 与相关函数

*   **`ArraySet`**
    将整个数组的所有元素设置为同一个指定值。
    ```easyLanguage
    ArraySet(MyArray, 0); // 将MyArray所有元素设为0
    ```

*   **`ArrayGetType`**
    获取数组存储的数据类型。返回值为：2（布尔型）、3（字符串型）、7（数值型）。
    ```easyLanguage
    Value1 = ArrayGetType(ClosePrice); // 返回7，表示数值型数组
    ```

*   **`ArraySum`**
    计算一维数组中指定索引范围内所有元素的总和。
    ```easyLanguage
    // 假设ClosePrice存储了值 [0,1,2,3,4,5,6,7,8,9,10]
    Value2 = ArraySum(ClosePrice, 2, 6); // 计算索引2到6的元素和：2+3+4+5+6 = 20
    ```
    **重要提示**：数组索引从0开始。

*   **`ArraySort`**
    对数组中指定范围内的元素进行排序。数值按大小，布尔值（True>False），字符串按ASCII码值。

*   **`ArraySetValueRange`**
    将数组中指定索引范围内的所有元素设置为同一个值。
    ```easyLanguage
    ArraySetValueRange(ClosePrice, 2, 6, 10); // 将索引2到6的元素都设为10
    ```

*   **`ArrayGetMaxIndex` / `ArraySetMaxIndex`**
    `ArrayGetMaxIndex` 获取数组当前的最大索引值。
    `ArraySetMaxIndex` 设置数组的最大索引，返回True表示成功，False表示失败。

### 数组比较函数

*   **`ArrayCompare`**
    比较两个一维数组中指定起始位置、指定数量的元素是否相同。
    ```easyLanguage
    Value3 = ArrayCompare(SourceArray, 2, TargetArray, 2, 4);
    ```
    **返回值说明**：
    *   `0`: 所比较的每组元素完全相同。
    *   `1`: 源数组（`SourceArray`）中第一个不同的元素 **大于** 目标数组对应元素。
    *   `-1`: 源数组中第一个不同的元素 **小于** 目标数组对应元素。
    对于布尔数组，True (1) 大于 False (0)。

## 总结 📝

本节课我们一起深入学习了数组的核心运算逻辑和内置函数。
1.  **数组移动**：关键在于理解数据迁移的方向，遵循“先迁移，后覆盖”的原则，避免数据被错误覆盖。
2.  **多维数组**：其容量是维度乘积，且索引通常需为常数，灵活性较低，建议多用一维数组组合。
3.  **内置函数**：`ArraySet`, `ArraySum`, `ArrayCompare` 等函数能极大简化数组的赋值、计算和比较操作。

![](img/c074c4c012fbdc80b504dcf5720f48fe_5.png)

掌握数组的存取与运算，是构建复杂数据结构和量化模型的重要一步。后续的函数、指标等内容，更多是知识面的扩展。请务必动手练习，画图理清逻辑，直至完全理解。