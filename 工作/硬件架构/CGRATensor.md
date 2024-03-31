# CGRATensor

## 模块框图

- CTRL_UNIT: 控制模块
- RAM_ACC_UNIT: 读写控制单元
- PIPE_UNIT: 数据计算单元

![image-20240220153133700](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20240220153133700.png)

## 工作流程

- 从Neural Controller接收指令
- 解析指令并生成控制信号
- 控制信号发送到RAM_ACC_UNIT和PIPE_UNIT
- RAM_ACC_UNIT从SPM0读取数据发送到PIPE_UNIT模块
- PIPE_UNIT模块进行计算
- 计算结果写回RAM_ACC_UNIT
- RAM_ACC_UNIT写回SPM0



## PIPE_UNIT

### 结构

- Arith_unit: 向量数学运算
- Logic_unit: 向量逻辑运算
- Convert_unit: 数据格式转换
- Int_unit: INT8类型数学运算
- Misc_unit: 向量数据收集,统计
- DMA_unit: 数据搬运,矩阵和张量存储方式转换

### 指令生成规则

#### 常规指令

- int_elem_count: 输入数据长度, 以字节为单位
- out_elem_count: 输出数据长度, 以字节为单位

![image-20240220155928081](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20240220155928081.png)

![image-20240220155943073](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20240220155943073.png)

#### 单位指令

- int_elem_count: 输入数据长度, 以字节为单位
- out_elem_count: 输出数据长度, 以字节为单位
- unit_elem_count: 单位向量长度, 以字节为单位

```c++
for(int i = 0; i < in_elem_count/PI; i++) {
  SPM0_Raddr0 = src0 + i * PI;
  if (k * PI >= unit_elem_count) {
    k = 0;
  } else {
    k ++;
  }
  SPM0_Raddr1 = src1 + K * PI;
  if (i == 0) {
    data_sof = 1;
  } else {
    data_sof = 0;
    if (I == elem_count/PI - 1) {
      data_eof = 1;
    } else {
      data_eof = 0;
    }
  }
}
```

![image-20240220160348951](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20240220160348951.png)

#### 规约指令

- Arg0: dim
- Arg1: n
- Arg2: h
- Arg3: w
- Arg4: c
- int_elem_count: 输入数据长度, 以字节为单位
- out_elem_count: 输出数据长度, 以字节为单位

## CTRL_UNIT

> CTRL_UNIT负责接收NeuralCore Controller发送的指令信息,解析生成其他模块计算所需的参数和控制信号
>
> 发送到RAM_ACC_UNIT的信号主要有指令读操作数及写操作数在SPM0中的地址,指令中涉及到的张量的各维度的长度或者向量的元素等信息,用于RAM_ACC_UNIT生成读写数据地址以及控制读写数据的迭代次数
>
> 发送到PIPE_UNIT的主要是控制信号, 用于控制流水线上的数据通路

![image-20240220154421140](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20240220154421140.png)

## RAM_ACC_UNIT

- 与外部SPM0存在两个读数据通道和一个写数据通道

![image-20240220154550089](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20240220154550089.png)

### ram_acc_ctrl

- 根据指令信息生成每条指令所需的读写SPM0缓存地址
- 处理访存地址间的bank冲突

### ram_acc_phy

根据ram_acc_ctrl模块发来的读写信息对SPM0缓存进行读写并根据控制信息对数据进行对其以及移位操作

### DMA

完成一些数据搬移以及维度转换指令

















