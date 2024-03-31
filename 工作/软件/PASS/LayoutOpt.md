# LayoutAlignOpt

## 背景

`LayoutAlignToNPU` Pass会统一进行channelnorm的插入，但是其算法比较通用，针对于某些特殊情况可能并不需要插入channelNorm，需要消掉这部分多余的channelnorm

## 原则

> 只能在不影响前后算子的对齐模式下进行优化

`LayoutAlignToNPU`阶段严格遵循算子的对齐要求插入channelnorm，若修改了前后算子的对齐模式将会引起连锁反应（对齐模式链式传播），可能会导致其它算子的对齐模式改变而不符合预期要求

## 模式

### Const + ChannelNorm

对于const，在`LayoutAlignToNPU`阶段认为它总是非对齐方式存储的，但是实际上，const的数据即使是紧凑排布的也可以直接设置为对齐模式， 对于`Const（NALIGN） + ChannelNorm（true） + NextOp（ALIGN）` 的这种模式， 可以直接修改为`Const(ALIGN) + NextOp(ALIGN)`

该模式不会影响`NextOp`的对齐模式，并且`Const`前面没有输入，所以不会导致其它算子的对齐模式发生改变

### ChannelNorm + ChannelNorm

两个连续的channelnorm有以下两种情况：

- preop(Tensor) + channelnorm(true) + chanenlnorm(false) + nextOp(Tensor)
- preop(Cx) + channelnorm(false) + chanenlnorm(true) + nextOp(Cx)

第一种情况的效果是从Tensor-->Tensor, 第二种情况是Cx-->Cx,两个channelnorm的效果抵消，所以这两个channelnorm

均可消除

### OneDimension ChannelNorm

> 当前对齐模式为C(K)维对齐，会沿着C(K)维对数据进行切分然后顺序摆放

基于现在的对齐方式，一维数据在对齐排布和紧凑排布两种情况下数据的位置应该是相同的，区别在于对齐模式下数据末尾会添加脏数据对齐到64的整数倍，即一维的数据既可以被认为是对齐排布也可以被认为是非对齐排布，对一维数据的channelnorm可以直接消除

注：

1. 这里可以认为数据的batch是1，所以不会发生batch的2048bit对齐
2. 消掉这种channelnorm之后不能遍历funcOp进行inferlayout操作，channelnorm能保证对齐模式的连贯性，消掉之后对齐模式实际上相当于从内部截断

### MultiDimension ChannelNorm

该模式是对`OneDimension ChannelNorm`的拓展， 若一个channelNorm的输入数据是多维，但是齐除了C（K）维之外的其它维度都为1，可以被认为是`OneDimension ChannelNorm`类型

