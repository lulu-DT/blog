# Neural engine

## 内部架构

### 结构图

![image-20240220140045526](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20240220140045526.png)

- tensor ctrl: 主控制模块
- act ctrl: 激活读取控制模块
  - 激活存储local buffer
  - 数据读取控制器
- wt ctrl: 权重读取控制模块
  - 权重存储local buffer
  - 权重读取控制器
- PE阵列

### TU

> Tensor unit, 4096个FP14(BF16/TF32)MAC单元, 8192个INT8 MAC单元

#### 1. 输入

- 输入激活
- 输入权重
- 输入psum矩阵

#### 2. 输出

- PE阵列psum结果

#### 3. 运算类型

- 卷积
- depthwise卷积
- 矩阵乘
- 卷积反向权重梯度计算

#### 4. PE阵列

##### 结构

- PE阵列: 包含4个PE Array
- PE Array: 包含4个PE Group
- PE Group: 包含16个PE unit
- PE: 包含16个MAC单元(FP16)

##### 模式

- 典型模式: 4个PE array之间复用相同的激活, PE array之间使用不同的权重
- depthwise模式: 4个PE array输入的激活数据上均是无复用的, 是四组不同激活.PE array之间使用不同的权重

##### 细节

- 水平方向的PE group共享权重wgt输入(行方向的PE使用相同的权重, 不同的激活)
- 垂直方向的PE group共享激活atc输入(列方向的PE使用相同的激活, 不同的权重)

![image-20240220143639459](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20240220143639459.png)

##### psum local buffer

> PE阵列内部会包含一组ping pang的psum buffer, 用于缓存矩阵乘得到的部分和数据

从逻辑上看, PE阵列整体包含32组的psum buffer, ping中16组, pang中16组

PE阵列一次计算得到P16\*F16的输出矩阵,会缓存在一组psum buffer中, 16组psum buffer总共可以缓存P64\*F64的输出矩阵

在输出buffer级别, 可以完成[P64,F64]\*[C64,F64]的矩阵乘运算.psum buffer的pingpang切换是以64\*64\*64的矩阵乘为粒度进行切换的

![image-20240220145400337](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20240220145400337.png)

![image-20240220145516845](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20240220145516845.png)

PE阵列psum矩阵实际存储对应关系:

如上图,psum buffer分布在各个PE中

**// TODO(没看懂)**

##### 附录(结构图)

---

PE阵列:

![image-20240220141950360](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20240220141950360.png)

PE Array:

![image-20240220142048621](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20240220142048621.png)

PE Group:

![image-20240220142204110](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20240220142204110.png)

PE:

![image-20240220142224107](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20240220142224107.png)



#### PPU

> post process unit

1. 组成

输入:

- bias/scale

输出:

- 结果















