# High Level Description

## 名词解释

### 硬件

| 名词                   | 含义                             |
| ---------------------- | -------------------------------- |
| Spatial Computing      | 空间计算                         |
| Spatial Computing Tile | 空间计算节点                     |
| Tile ConfREG           | Tile内的配置寄存器存放空间       |
| Spatial Core           | 负责Spatial Computing的控制核    |
| Kernel Core            | 一个支持Vector的RiscV核          |
| NeuralCore             | 支持神经网络加速的核             |
| DTE                    | Date Transmission Engine         |
| L0TensorReg            | NeuralCore中的Tensor数据寄存器组 |
| L1SPM                  | L1 Scratch-pad-memory            |
| CGRATensor             | 除矩阵与卷积以外的功能单元       |
| NeuralEngine           | 矩阵与卷据功能单元               |
| LSU                    | NeuralCore的访存单元             |
| Scalar                 | 浮点、定点标量                   |
| Vector                 | 浮点、定点向量                   |
| Unit Vector（uV）      | 浮点、定点单元向量               |
| BoolVector（bV）       | bool向量                         |

### 编程

| 名词 | 含义                                       |
| ---- | ------------------------------------------ |
| CSR  | Current statue register当前状态寄存器      |
| TFR  | Tensor format register存储格式寄存器       |
| PDR  | Padding register Padding操作寄存器         |
| SWR  | Sliding Window register 划窗操作寄存器     |
| SGPR | Scalar General purpose register 标量寄存器 |
| TenR | Tensor register 数据存储寄存器             |

## OverView

### 空间计算

#### SIMT-GPGPU

> **单指令多线程通用图形处理单元**

- **定义：** GPGPU是通用图形处理单元的缩写，它最初是为图形渲染而设计的，但在过去几年中，由于其高度并行的架构，已被广泛用于科学计算、深度学习和其他需要大规模并行处理的应用程序。
- **SIMT架构：** NVIDIA的CUDA架构是SIMT的一个例子，它是一种基于SIMT（Single Instruction, Multiple Thread）的并行计算模型。在SIMT中，每个线程组（thread block）中的线程执行相同的指令，但可以处理不同的数据。这种模型适用于数据并行任务，如矩阵运算和深度学习。
- **特点：** GPGPU通过高度并行的处理单元和大规模的线程数，能够加速大规模数据并行任务。CUDA、OpenCL等是用于在GPU上进行通用计算的编程框架。

#### **SMP-NPU**

> **对称多处理神经处理单元**

- **定义：** NPU是神经处理单元的缩写，是专门设计用于神经网络加速的硬件。SMP则是对称多处理的缩写，指的是多个处理单元对称地共享相同的内存空间。
- **SMP架构：** SMP-NPU的架构通常设计为多个处理核心对称共享内存，这使得它们能够更好地协同工作，提高整体的计算性能。这种架构有助于处理神经网络工作负载，其中神经网络的层次结构通常可以并行处理。
- **特点：** NPU专注于深度学习和神经网络相关的计算任务，通过定制的硬件加速神经网络的训练和推断，提高了效率和性能。这些处理单元通常优化了矩阵运算等与神经网络相关的计算。

#### Spatial Computing Architecture

> 控制核心（CPU Core）、计算核心（AI Core、DSP）、高速接口（PCIE、Ethernet、Link）、专用加速设备（Video codec）通过嵌入式网络（Noc）进行数据通信和同步

![image-20240202144728476](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20240202144728476.png)



### overview

![image-20240202144829754](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20240202144829754.png)

- `T`表示一个SpatialComputingTile,任意两个Tile之间可以实现Tile和Tile之间的通信，不依赖DDR
- `Noc`：实现了计算核心、高速IO、存储设备之间的互联互通
- `LPDDR`：为片外存储器、提供了大容量存储的需求 
- `High-speed IO`: 实现芯片之间的高速互联，提供芯片之间Tile的通信能力



### Tile Arctitecture

![image-20240202145505472](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20240202145505472.png)

> 一个Tile包含SMP Kernel Cluster、Spatial Engine、NeuralCore、L1SPM、Bus Matrix0、Noc等
>
> 

#### SMP Kernel Cluster

 cluster为一个SMP架构的多核RISCV子系统，每个RISCV包含私有的L1指令Cache、L1数据Cache、`四个`RISCV共享L2

#### SpatialEngine

 SpatialEngine子系统原来卸载Tile内的网络传输负载

#### L1SPM

为Tile之间以及Tile内部多个执行单元之间（NeuralEngine、CGRATensor、LSU）的数据交换的存储空间，由多个Logic Bank组成，Logic Bank之间采用LSB（last significant bit）interleaving方式访问

- logic bank：为一组1024bit宽的logic memory bank，每个logic memory bank由多个physical SRAM组成
- L1Xbar： 实现SMP-Kernel-Cluster、SpatialEngine、NeuralCore、Externel Tile与L1 logic bank的互通互联，以及LSB interleaving
- L1SPM的master包括：`Spatial Core`、`Spatital DTE`、`Kernel Core`、`NeuralCore`、`External Tile`

#### Noc

- `Noc`: 实现了master之间的互联互通

#### BusMatrix0

实现了master（SMP-Kernel-Cluster、SpatialEngine-RV）与Slave（Noc、L1SPM、peripheralDomain）之间的互联

L1SPM会映射到BusMatrix0的三段地址空间上，分别表示了不同的访问属性

#### NeuralCore

为面向AI定制的加速单元，包含以下几部分：

##### NeuralCoreController

为NeuralCore的控制单元，接收SMP-Kernel-Cluster发送的命令，对命令执行译码，依赖检查。然后将其发送到对应的执行单元

<img src="C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20240202151200327.png" alt="image-20240202151200327" style="zoom:50%;" />

##### CGRA.TENSOR

为一组针对AI定制的CGRA功能单元，功能表述格式为CGRATensor_function_format_name.type

![image-20240202151517673](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20240202151517673.png)

##### NeuralEngine

执行卷积、矩阵操作，功能描述格式为NeuralEngine_function_subfunction.type

![image-20240202151709681](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20240202151709681.png)

##### LSU

实现L1SPM-DDR、L1SPM-L1SPM、DDR-DDR的数据交互









