# 量化交易课程：29：转换器的实现(3) - 进仓与锁仓模式

![](img/1da7437ee3e4982cc5fcd1a12fd7d143_1.png)

在本节课中，我们将继续学习转换器的实现，重点讲解进仓模式和锁仓模式的逻辑与代码编写。上一节我们介绍了如何为上期所和上期能源的订单进行转换，本节中我们来看看如何处理更复杂的交易模式。

![](img/1da7437ee3e4982cc5fcd1a12fd7d143_3.png)

![](img/1da7437ee3e4982cc5fcd1a12fd7d143_5.png)

![](img/1da7437ee3e4982cc5fcd1a12fd7d143_7.png)

## 概述

![](img/1da7437ee3e4982cc5fcd1a12fd7d143_9.png)

本节课我们将学习两种特殊的交易模式：进仓模式和锁仓模式。进仓模式忽略开平仓指令，只关注买卖方向；锁仓模式则旨在避免平今仓的高额手续费。我们将分别实现这两种模式的转换逻辑。

## 进仓模式转换逻辑

进仓模式的核心原则是“先平后开”。它不区分开仓或平仓指令，只根据买卖方向来调整净持仓，确保不会同时持有多单和空单。

以下是进仓模式的基本处理流程：

1.  **判断方向**：根据订单的买卖方向，确定需要先平掉的反向持仓。
2.  **优先平仓**：计算对应方向的可平持仓量，并优先进行平仓操作。
3.  **剩余开仓**：如果平仓后订单数量仍有剩余，则将剩余部分转换为开仓。

对于上期所和上期能源的品种，平仓操作需要进一步拆分为“平昨”和“平今”。

### 进仓模式代码实现

以下是进仓模式转换函数的核心代码框架：

```python
def convert_to_net(self, req: OrderRequest):
    cf = self.converter_functions
    req_list = []

    # 计算可用持仓（非上期所/上期能源）
    if req.direction == Direction.LONG:
        pos_available = cf.short_pos - cf.short_pos_frozen  # 可平空仓
    else:  # Direction.SHORT
        pos_available = cf.long_pos - cf.long_pos_frozen    # 可平多仓

    volume_left = req.volume

    # 第一步：优先平仓
    if pos_available > 0:
        close_req = copy(req)
        close_req.offset = Offset.CLOSE
        close_volume = min(pos_available, volume_left)
        close_req.volume = close_volume
        req_list.append(close_req)
        volume_left -= close_volume

    # 第二步：剩余部分开仓
    if volume_left > 0:
        open_req = copy(req)
        open_req.offset = Offset.OPEN
        open_req.volume = volume_left
        req_list.append(open_req)

    return req_list
```

对于上期所和上期能源，需要分别计算昨仓和今仓的可平量，并依次进行平昨、平今操作。

![](img/1da7437ee3e4982cc5fcd1a12fd7d143_11.png)

## 锁仓模式转换逻辑

![](img/1da7437ee3e4982cc5fcd1a12fd7d143_13.png)

锁仓模式的目的是避免平今仓惩罚。其核心规则是“永不平今”，所有平仓操作都转换为“平昨”，如果昨仓不足，则用“开今仓”来替代。

以下是锁仓模式的处理步骤：

1.  **判断方向**：确定订单方向对应的反向持仓（例如，买方向对应空头持仓）。
2.  **优先平昨**：计算反向持仓的昨仓可用量，并优先进行平昨操作。
3.  **不足开今**：如果平昨后数量仍不足，则将不足的部分转换为开仓（今仓）。

### 锁仓模式代码实现

以下是锁仓模式转换函数的核心代码框架：

```python
def convert_to_lock(self, req: OrderRequest):
    cf = self.converter_functions
    req_list = []

    # 计算可用昨仓
    if req.direction == Direction.LONG:
        yd_available = cf.short_yd_pos - cf.short_yd_frozen  # 可平空头昨仓
    else:  # Direction.SHORT
        yd_available = cf.long_yd_pos - cf.long_yd_frozen    # 可平多头昨仓

    volume_left = req.volume

    # 第一步：优先平昨
    if yd_available > 0:
        close_req = copy(req)
        # 根据交易所决定平仓类型
        if req.exchange in [Exchange.SHFE, Exchange.INE]:
            close_req.offset = Offset.CLOSE_YESTERDAY
        else:
            close_req.offset = Offset.CLOSE
        close_volume = min(yd_available, volume_left)
        close_req.volume = close_volume
        req_list.append(close_req)
        volume_left -= close_volume

    # 第二步：剩余部分开仓（今仓）
    if volume_left > 0:
        open_req = copy(req)
        open_req.offset = Offset.OPEN
        open_req.volume = volume_left
        req_list.append(open_req)

    return req_list
```

## 模式优先级与整体流程

![](img/1da7437ee3e4982cc5fcd1a12fd7d143_15.png)

![](img/1da7437ee3e4982cc5fcd1a12fd7d143_17.png)

在完整的转换器中，需要合理安排不同模式的判断顺序。通常的优先级是：
1.  锁仓模式
2.  进仓模式
3.  针对上期所/上期能源的普通转换
4.  默认直接返回原订单

![](img/1da7437ee3e4982cc5fcd1a12fd7d143_19.png)

![](img/1da7437ee3e4982cc5fcd1a12fd7d143_21.png)

这确保了特殊模式的规则能被优先执行。

![](img/1da7437ee3e4982cc5fcd1a12fd7d143_23.png)

![](img/1da7437ee3e4982cc5fcd1a12fd7d143_25.png)

![](img/1da7437ee3e4982cc5fcd1a12fd7d143_27.png)

## 总结

![](img/1da7437ee3e4982cc5fcd1a12fd7d143_29.png)

本节课中我们一起学习了进仓模式和锁仓模式的转换器实现。进仓模式通过“先平后开”来管理净持仓，而锁仓模式通过“平昨开今”来规避平今仓惩罚。理解这些业务逻辑是编写正确转换代码的关键。虽然代码细节较多，但只要理清核心流程，逐步实现，就能构建出功能完善的转换器。下一节课，我们将查看VnPy平台自带转换器的源码，进行对比学习。