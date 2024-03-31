# Stream

## 名词解释

| 名词         | 含义                                                |
| ------------ | --------------------------------------------------- |
| NPU CARD     | Kuiper NPU办卡                                      |
| NPU core     | 执行AI计算的核心单元，也叫tile，共16个              |
| NPU          | Tile的泛称                                          |
| AP           | Application Processor                               |
| NE           | Neural Core，执行AI运算的CPU core及其他外设         |
| Kernel Core  | 调度Neural core执行AI运算的CPU core及其他外设       |
| Spatial core | 连接kernel core与AP进行数据收发的CPU core及其他外设 |

## 通信方式

### one-to-all(scatter)

> 分发

![image-20240202184544024](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20240202184544024.png)

### all-to-one（Gather）

![image-20240202184615022](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20240202184615022.png)

### all-to-all

![image-20240202184635355](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20240202184635355.png)

### broadcast

![image-20240202184647240](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20240202184647240.png)

### allgather

![image-20240202184700817](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20240202184700817.png)

### reduce scatter

![image-20240202184711142](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20240202184711142.png)

### all reduce

![image-20240202184721404](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20240202184721404.png)

### reduce

![image-20240202184737868](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20240202184737868.png)



## Stream定义

> stream表示数据传输通道

### 通信模式

#### Unicast

数据点对点

#### Scatter

数据分发

#### shuffle

数据先重新排布，然后进行类似scatter/unicast的处理

#### Broadcast

广播

#### Gather

数据聚合

### inStream

> 数据进入网络时，是输入input，成为instream

inStream有Unicast、scatter、shuffle、broadcast操作

### outStream

> 数据从网络进入stream，是输出stream，成为outstram

outstream有Unicast、gather、shuffle操作



### MailBox

用于Noc连接的RISC-V core间通信的硬件模块，支持tile内部的spatial core和kernel core之间，同时也可以用于远端的RISC-V的通信



























