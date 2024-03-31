# LayoutAlignToNPU

## 背景

1. 不同的Op数据类型排布有要求， 有的需要对齐， 有的不需要对齐

1. 同一种Op在不同情况下的对齐方式或有区别

## 原则

- 尽可能少的插入ChannelNorm，性能更高
- 尽可能使用非对其的模式： 非对其模式占用内存更少

## 方案(贪心算法)

### 模型抽象

将ir看作一个有向图， 节点表示op，节点颜色表示节点要求的对齐模式，将channelNorm看作是融合节点

- 若两个节点的颜色冲突，需要在节点中间插入融合节点化解冲突
- BOTH类型节点不与任何类型节点冲突

节点示意图如下：

用黄色节点表示需要对齐的op， 用灰色节点表示不需要对齐的op， 绿色表示都可以的op， 融合节点分两类（方向不同）

![image-20240314161935067](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20240314161935067.png)

### 问题分析

该问题的难点在于某些节点对颜色有特殊的要求，例如某些节点颜色只能为黄色，有些节点只能为灰色，但是更多的节点其实两种颜色都可以使用。

首先我们简化下问题，若节点的颜色都可以任意选择我们会怎么做？

很自然的，我们会想到从图的入方向进行顺序的推导，`像水流过管道一样`，从入口到出口，只需要一次遍历即可将所有的节点都进行染色

当我们限制了图中间的某些节点的颜色的时候问题就来了，当我们还像上述这样顺序进行遍历的时候，若当前推导的颜色与该节点要求的颜色不一致我们便无法向前推进，幸运的是，我们可以插入额外的节点来进行调和，使得我们的`水流`能够顺利通过，这里，我们仍然可以在一次遍历的过程中将所有的节点染色

接下来涉及到另一个问题，对于没有颜色要求的节点（BOTH），其颜色应该在什么时候确定？

该问题的解决方案无外乎以下几点：

- 直接初始化为其中一种（NALIGN）或者（ALIGN）
- 就近原则，选择前方或者后方最近的确定颜色的节点进行适配

对于第一种方案来说，我们考虑极端情况，假设将所有BOTH类型节点初始化为NALIGN，在假设我们的图中存在很多的Conv节点（Conv必须要求是对齐的），在这种情况下我们每次遇到BOTH类型与Conv的交界处的时候我们都需要插入融合节点，但是实际上并不需要，反之亦然

对于第二种方案来说，实际上这也是一种按需插入的方案， 仅在需要的时候插入，因为它会follow前后节点的颜色，若前后节点颜色冲突那自不必说，无论是在该节点前方插入还是在该节点后方插入，我们肯定是会去插入一个节点解决冲突， 但是当前后节点颜色不冲突的时候，我们便不会去插入，所以这是效率最高的一种方式

### 算法概述

- 遍历有向图中的每个节点，检查其后续节点的颜色。

- 如果邻居节点的颜色与当前节点颜色冲突，则插入一个新的融合节点，并调整边，使得冲突解决。

- 继续遍历直到所有节点属性冲突都得到解决

#### 数据结构

- 有向图：funcOp代表整个有向图

- 节点属性（AlignMode）

​	AlignMode代表了一个Op的对齐模式，总共有三种,`NALIGN`、`ALIGN`、`BOTH`

```c++
enum AlignMode {
  ALIGN = 0,
  NALIGN,
  BOTH,
  UNKNOWN
}
```

- 边：op间的define(use)关系代表边

#### 算法步骤

- 初始化每个节点的颜色（Align信息）
- 遍历有向图，查询冲突位置并记录
- 在冲突位置插入融合节点

<img src="C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20240326192033823.png" alt="image-20240326192033823" style="zoom: 50%;" />

#### 复杂度分析

一次遍历即可完成对融合节点的插入， 时间复杂度O(n)

#### 算法演示

假设有以下IR

<img src="C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20240310164021156.png" alt="image-20240310164021156" style="zoom:50%;" />

遍历ir图， 在橘色和灰色交界处插入过渡（channelNorm）

<img src="C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20240310164416292.png" alt="image-20240310164416292" style="zoom: 67%;" />

---

## 实现细节

### BOTH类型Op处理

> BOTH类型的Op能够适应任何一种对齐要求， 因此我们这里需要依据原则（尽可能少的插入channelNorm）进行处理， 插入channnelNorm的前提是Op和其User的对齐要求冲突，因此，对于BOTH类型的Op，只需要跟随其Parent节点的对齐要求即可

#### 单输入Op

但输入Op跟随其输入的defineOp的对齐要求

#### 多输入Op

若多个输入的defineOp的对齐要求相同，则初始化为对应的对齐要求

若多个输入的defineOp的对齐要求不同，则根据输入Op的defineOp的拓扑顺序进行决定（优先位置靠前的mode）

#### 代码实现

```c++
AlignMode opAlignMode = getOpAlignMode(op);
for (auto user : op->getUsers()) {
	AlignMode userAlignMode = getOpAlignMode(user);
  if (opAlignMode != BOTH && userAlignMode == BOTH) {
    // follow parent align mode
  	userAlignMode = opAlignMode;
  }
}
```

### 冲突判断

冲突可以分为两种情况：

- 当前节点`ALIGN`, user节点`NALIGN`
- 当前节点`NALIGN`, user节点`ALIGN`

这两种情况都需要插入channelNorm解决冲突，只是channelNorm的方向不同， 第一种为`False`, 第二种为`True`

```c++
if (userOpAlignMode != opAlignMode && userAlignMode != BOTH) {
	bool direction = opAlignMode == NALIGN ? false : true;
}
```

### 冲突位置

一个op可能有多个输入， 所以当前op可能与多个前置节点冲突， 需要根据Operand的index确定冲突位置， 每一个index表示一个前置节点， 故有以下数据结构：

`std::map<Operation*, std::vector<int>> insertPoint_`

key表示冲突节点(user)， value表示该节点与第几个operand的defineOp冲突，可能有多个

```c++
for (output : op->getOpResults()) {
  for (int i = 0; i < user->getNumOperands(); i ++) {
    if (output != user->getOperand(i)) continue;
    insertPoint_[user].push_back(i);
    break;
  }
}
```

### AlignMode判断

#### op含有dev_info

这种情况只会发生在特定case里面， 说明使用者想固定某个op的对齐模式，所以这里应该根据其dev_info里面指定的对齐模式进行初始化

#### ALIGN Only的Op

以下op的输出只能是对齐的，原因是oplib对应的算子只支持该模式，可以参考算子开发文档

`Gemm`，`Conv类`，`Pool类`，`Reduce`，`Transpose`， `Concatenate`， `Scatter`， `Pad`

#### NALIGN Only的Op

类似于input和output类型的算子，这种算子无法根据上下文判断其对齐模式，也无法根据其属性进行判断，这种算子的对齐取决于使用者对case的设计，所以这种算子归类为只能是`NALIGN`模式

#### SliceOp

sliceOp的对齐模式需要根据其属性进行判断，如果它的start的最后一维（C维）大于0，并且end的最后一维（C维）是64的整数倍，这种是不能看作对齐的，其余情况均看作`ALIGN`

#### Odd-Dimension Output

如果输出的维度是奇数（1，3），这种Op只能看作是`NALIGN`,因为`Cx`模式需要数据的维度是2或者4

#### Need BroadCast Ops

这一类Op表示有多个输入， 且其中的某一个输入需要broadcast，由于被broadcast的输入维度通常是一维，而一维的数据可以同时被认为满足两种对齐模式， 这种对一维数据的channelNorm可以在后面的pass中进行消除，所以对于这种op我们将其初始化为其Complete input的defineOp的对齐模式

eg: `V_VS_add, V_VS_mul, V_VS_div, V_VS_max...`

#### ChannelNormOp

对于channelNormOp， 需要根据其direction属性进行判断， 但是一般在该Pass之前不会存在ChannelNorm的插入



## Pass流程

### 主流程

```c++
void runOnOperation() {  
  // 存储Op的ALIGN信息和channelNorm的插入点
 	collectInfo()
  
  // 根据收集到的插入点信息插入channelNorm
  insertChannelNorm();
  
  // upToDown更新IR，调用inferShape和inferLayout
  updataIR();
}
```



## 效果展示

### 普通情况

> 沿用Parent Op的对齐方式

<img src="C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20240314145535310.png" alt="image-20240314145535310" style="zoom:50%;" />

<img src="C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20240312171516224.png" alt="image-20240312171516224" style="zoom:50%;" />

<img src="C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20240314145317846.png" alt="image-20240314145317846" style="zoom:50%;" />

### 特殊情况

<img src="C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20240312172034766.png" alt="image-20240312172034766" style="zoom:50%;" />

<img src="C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20240314144417797.png" alt="image-20240314144417797" style="zoom:50%;" />
