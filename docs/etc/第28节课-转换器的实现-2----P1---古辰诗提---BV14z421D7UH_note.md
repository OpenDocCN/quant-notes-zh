# 量化交易基础：第28课：转换器的实现(2)

在本节课中，我们将继续学习转换器的实现，重点讲解如何更新委托单、处理成交信息，以及如何将策略发出的委托请求转换为符合交易所规则的委托单。我们将一步步构建这些核心功能。

上一节我们介绍了仓位更新的逻辑，本节中我们来看看如何处理委托单和成交信息。

## 更新委托单状态

![](img/ed239ab0db6671cb73535e056f2adf31_1.png)

![](img/ed239ab0db6671cb73535e056f2adf31_2.png)

![](img/ed239ab0db6671cb73535e056f2adf31_4.png)

当委托单发送到服务器后，我们需要更新其状态并计算冻结仓位。以下是实现步骤：

1.  **维护活跃委托单字典**：我们使用一个字典 `active_orders` 来存储所有处于活跃状态的委托单。活跃状态包括：提交中、未成交、部分成交。
    ```python
    self.active_orders = {}  # key: order_id, value: order_data
    ```

2.  **更新委托单**：当收到新的委托单信息时，首先更新 `active_orders` 字典，并清理其中已不活跃的委托单。
    ```python
    def update_order(self, order):
        # 如果委托单仍活跃，则更新到字典中
        if order.is_active:
            self.active_orders[order.order_id] = order
        
        # 清理已不活跃的委托单
        for order in list(self.active_orders.values()):
            if not order.is_active:
                self.active_orders.pop(order.order_id)
        
        # 重新计算冻结仓位
        self.calculate_frozen()
    ```

3.  **计算冻结仓位**：遍历所有活跃委托单，对于平仓委托，计算其尚未成交的部分作为冻结数量。
    ```python
    def calculate_frozen(self):
        # 首先将所有冻结仓位归零
        self.long_yd_frozen = 0
        self.long_td_frozen = 0
        self.short_yd_frozen = 0
        self.short_td_frozen = 0
        
        for order in self.active_orders.values():
            # 开仓委托不冻结仓位
            if order.offset == Offset.OPEN:
                continue
            
            # 计算该委托的冻结数量 = 委托总数 - 已成交数
            frozen = order.volume - order.traded
            
            # 根据委托方向和类型，更新对应的冻结仓位
            if order.direction == Direction.LONG:
                # 买平仓 => 冻结空单
                if order.offset == Offset.CLOSE_YESTERDAY:
                    self.short_yd_frozen += frozen
                elif order.offset == Offset.CLOSE_TODAY:
                    self.short_td_frozen += frozen
                elif order.offset == Offset.CLOSE:
                    # 优先冻结昨仓
                    self.short_yd_frozen += frozen
                    # 如果冻结昨仓数量超过了实际昨仓持仓
                    if self.short_yd_frozen > self.short_yd:
                        # 超出部分冻结今仓
                        self.short_td_frozen += (self.short_yd_frozen - self.short_yd)
                        # 昨仓冻结数修正为实际持仓数
                        self.short_yd_frozen = self.short_yd
            else:  # order.direction == Direction.SHORT
                # 卖平仓 => 冻结多单
                if order.offset == Offset.CLOSE_YESTERDAY:
                    self.long_yd_frozen += frozen
                elif order.offset == Offset.CLOSE_TODAY:
                    self.long_td_frozen += frozen
                elif order.offset == Offset.CLOSE:
                    # 优先冻结昨仓
                    self.long_yd_frozen += frozen
                    if self.long_yd_frozen > self.long_yd:
                        self.long_td_frozen += (self.long_yd_frozen - self.long_yd)
                        self.long_yd_frozen = self.long_yd
        
        # 更新总冻结仓位
        self.long_frozen = self.long_yd_frozen + self.long_td_frozen
        self.short_frozen = self.short_yd_frozen + self.short_td_frozen
    ```

## 更新成交信息

成交信息到达后，我们需要更新实际的持仓数量。其逻辑相对直接，但需注意区分开仓和平仓，以及平昨仓、平今仓和普通平仓。

以下是更新成交的核心逻辑：

```python
def update_trade(self, trade):
    # 处理开仓
    if trade.offset == Offset.OPEN:
        if trade.direction == Direction.LONG:
            self.long_td += trade.volume  # 多开，今增多仓
        else:  # trade.direction == Direction.SHORT
            self.short_td += trade.volume # 空开，今增空仓
    
    # 处理平昨仓
    elif trade.offset == Offset.CLOSE_YESTERDAY:
        if trade.direction == Direction.LONG:
            self.short_yd -= trade.volume # 买平昨 => 空单昨仓减少
        else:  # trade.direction == Direction.SHORT
            self.long_yd -= trade.volume  # 卖平昨 => 多单昨仓减少
    
    # 处理平今仓
    elif trade.offset == Offset.CLOSE_TODAY:
        if trade.direction == Direction.LONG:
            self.short_td -= trade.volume # 买平今 => 空单今仓减少
        else:  # trade.direction == Direction.SHORT
            self.long_td -= trade.volume  # 卖平今 => 多单今仓减少
    
    # 处理普通平仓（未指定平昨/平今）
    elif trade.offset == Offset.CLOSE:
        if trade.direction == Direction.LONG:
            # 优先平昨仓（空单）
            self.short_yd -= trade.volume
            # 如果昨仓不够平，剩余部分平今仓
            if self.short_yd < 0:
                self.short_td += self.short_yd  # short_yd为负数，相当于减
                self.short_yd = 0
        else:  # trade.direction == Direction.SHORT
            # 优先平昨仓（多单）
            self.long_yd -= trade.volume
            if self.long_yd < 0:
                self.long_td += self.long_yd
                self.long_yd = 0
    
    # 最后更新总持仓
    self.long_pos = self.long_yd + self.long_td
    self.short_pos = self.short_yd + self.short_td
```

## 转换委托请求

这是转换器的核心功能，负责将策略发出的一个委托请求（可能只是一个“平仓”指令），根据交易所规则（如上期所优先平昨）和当前冻结仓位，转换为一个或多个具体的委托单（如“平昨仓X手”和“平今仓Y手”）。

以下是针对上期所（及上期能源）的转换逻辑：

```python
def convert_req_shfe(self, req):
    # 如果是开仓请求，无需转换，直接返回
    if req.offset == Offset.OPEN:
        return [req]
    
    # 根据平仓方向，确定是平多单还是平空单，并计算可用仓位
    if req.direction == Direction.LONG:
        # 买平仓 => 平空单
        yd_available = self.short_yd - self.short_yd_frozen
        td_available = self.short_td - self.short_td_frozen
    else:  # req.direction == Direction.SHORT
        # 卖平仓 => 平多单
        yd_available = self.long_yd - self.long_yd_frozen
        td_available = self.long_td - self.long_td_frozen
    
    pos_available = yd_available + td_available
    
    # 检查：请求平仓量是否超过可用仓位
    if req.volume > pos_available:
        # 通常应报错或原样返回，由上层处理
        return [req]
    
    req_list = []
    volume_left = req.volume
    
    # 第一步：优先平昨仓（如果昨仓有可用仓位）
    if yd_available > 0:
        close_yd = copy(req)
        close_yd.offset = Offset.CLOSE_YESTERDAY
        # 平昨数量 = min(剩余需平量， 昨仓可用量)
        volume = min(volume_left, yd_available)
        close_yd.volume = volume
        req_list.append(close_yd)
        volume_left -= volume
    
    # 第二步：如果还有剩余需平量，则平今仓
    if volume_left > 0:
        close_td = copy(req)
        close_td.offset = Offset.CLOSE_TODAY
        close_td.volume = volume_left
        req_list.append(close_td)
    
    return req_list
```

**注意**：对于非上期所/上期能源的交易所，其转换逻辑可能不同（例如，默认只发`Offset.CLOSE`，由交易所或柜台决定平昨/平今）。转换器需要根据合约所属交易所来调用不同的转换函数。

## 总结

![](img/ed239ab0db6671cb73535e056f2adf31_6.png)

本节课中我们一起学习了转换器后半部分的实现。我们掌握了如何通过`update_order`来维护委托单状态并计算冻结仓位，通过`update_trade`来根据成交信息更新实际持仓。最后，我们深入探讨了`convert_req`的核心，即如何将策略的委托请求，结合交易所规则和当前仓位状态，拆解为具体的可执行的委托单列表。理解并正确实现这些逻辑，是构建稳定、准确的量化交易系统的关键一步。