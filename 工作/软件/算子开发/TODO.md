# 算子及语义

## TODO

| name   | status     | desc                   |
| ------ | ---------- | ---------------------- |
| count  | 📗📗📗📗📗📗📗📗📗📗 | NTENSOR<br />1. tensor |
| argmax | 📗📗📗📗📗📗📗📗📗📗 | NTENSOR                |
| argmin | 📗📗📗📗📗📗📗📕📕📕 | NTENSOR                |
|        | 📕📕📕📕📕📕📕📕📕📕 |                        |
|        | 📕📕📕📕📕📕📕📕📕📕 |                        |
|        | 📕📕📕📕📕📕📕📕📕📕 |                        |
|        | 📕📕📕📕📕📕📕📕📕📕 |                        |
|        | 📕📕📕📕📕📕📕📕📕📕 |                        |
|        | 📕📕📕📕📕📕📕📕📕📕 |                        |

- 数据比对
- 转换越界
- 重写前几个pass， 泛用
- QuantTest 1709888145



## CGRA

### arith

| name           | introduce                                                    | inst_adapter | use  |
| -------------- | ------------------------------------------------------------ | ------------ | ---- |
| V_V_abs        | 向量求绝对值                                                 | ✔            | ✔    |
| V_V_recip      | 倒数                                                         | ✔            | ✔    |
| V_V_square     | 平方                                                         | ✔            | ✔    |
| V_V_sqrt       | 平方根                                                       | ✔            | ✔    |
| V_V_rsqrt      | 平方根的导数                                                 | ✔            | ✔    |
| V_V_neg        | 相反数                                                       | ✔            | ✔    |
| V_VV_max       | src0和src1相同位置位置元素最大值                             | ✔            | ✔    |
| V_VS_max       | src元素分别和常熟相比,将最大值写入目标向量对应位置           | ✔            | ✔    |
| V_VuV_max      | unit为单元向量<br />src分成N个元素个数和unit相同的片段向量<br />N个片段向量分别和unit对应元素相比取最大值<br />dst和src元素个数相同 | ✔            | ✔    |
| V_VuV_max_loop | TODO                                                         | ✔            | ✔    |
| V_VV_min       |                                                              | ✔            | ✔    |
| V_VuV_min      |                                                              | ✔            | ✔    |
| V_VuV_min_loop |                                                              | ✔            | ✔    |
| V_VV_add       |                                                              | ✔            | ✔    |
| V_VS_add       |                                                              | ✔            | ✔    |
| V_VuV_add      |                                                              | ✔            | ✔    |
| V_VuV_add_loop |                                                              | ✔            | ✔    |
| V_VV_sub       |                                                              | ✔            | ✔    |
| V_VS_sub       |                                                              | ✔            | ✔    |
| V_VuV_sub      |                                                              | ✔            | ✔    |
| V_VuV_sub_loop |                                                              | ✔            | ✔    |
| V_VV_mul       |                                                              | ✔            | ✔    |
| V_VS_mul       |                                                              | ✔            | ✔    |
| V_VuV_mul      |                                                              | ✔            | ✔    |
| V_VuV_mul_loop |                                                              | ✔            | ✔    |
| V_VV_div       |                                                              | ✔            | ✔    |
| V_VS_div       |                                                              | ✔            | ✔    |
| V_VuV_div      |                                                              | ✔            | ✔    |
| V_VuV_div_loop |                                                              | ✔            | ✔    |

### Relation

| name           | introduce     | inst_adapter | use  |
| -------------- | ------------- | ------------ | ---- |
| V_VV_eq        |               | ✔            | ✔    |
| bV_VV_eq       | b表示结果取反 | ✔            | ✔    |
| V_VS_eq        |               | ✔            | ✔    |
| bV_VS_eq       |               | ✔            | ✔    |
| V_VuV_eq       |               | ✔            | ✔    |
| bV_VuV_eq      |               | ✔            | ✔    |
| V_VuV_eq_loop  |               | ✔            | ✔    |
| bV_VuV_eq_loop |               | ✔            | 📕    |
| V_VV_ne        |               | ✔            | ✔    |
| bV_VV_ne       |               | ✔            | ✔    |
| V_VS_ne        |               | ✔            | ✔    |
| bV_VS_ne       |               | ✔            | ✔    |
| V_VuV_ne       |               | ✔            | ✔    |
| bV_VuV_ne      |               | ✔            | ✔    |
| V_VuV_ne_loop  |               | ✔            | ✔    |
| bV_VuV_ne_loop |               | ✔            | 📕    |
| V_VV_ge        | 大于等于      | ✔            | ✔    |
| bV_VV_ge       |               | ✔            | ✔    |
| V_VS_ge        |               | ✔            | ✔    |
| bV_VS_ge       |               | ✔            | ✔    |
| V_VuV_ge       |               | ✔            | ✔    |
| bV_VuV_ge      |               | ✔            | ✔    |
| V_VuV_ge_loop  |               | ✔            | ✔    |
| bV_VuV_ge_loop |               | ✔            | 📕    |
| V_VV_gt        | 大于          | ✔            | ✔    |
| bV_VV_gt       |               | ✔            | ✔    |
| V_VS_gt        |               | ✔            | ✔    |
| bV_VS_gt       |               | ✔            | ✔    |
| V_VuV_gt       |               | ✔            | ✔    |
| bV_VuV_gt      |               | ✔            | ✔    |
| V_VuV_gt_loop  |               | ✔            | ✔    |
| bV_VuV_gt_loop |               | ✔            | 📕    |
| V_VV_le        | 小于等于      | ✔            | ✔    |
| bV_VV_le       |               | ✔            | ✔    |
| V_VS_le        |               | ✔            | ✔    |
| bV_VS_le       |               | ✔            | ✔    |
| V_VuV_le       |               | ✔            | ✔    |
| bV_VuV_le      |               | ✔            | ✔    |
| V_VuV_le_loop  |               | ✔            | ✔    |
| bV_VuV_le_loop |               | ✔            | 📕    |
| V_VV_lt        | 小于          | ✔            | ✔    |
| bV_VV_lt       |               | ✔            | ✔    |
| V_VS_lt        |               | ✔            | ✔    |
| bV_VS_lt       |               | ✔            | ✔    |
| V_VuV_lt       |               | ✔            | ✔    |
| bV_VuV_lt      |               | ✔            | ✔    |
| V_VuV_lt_loop  |               | ✔            | ✔    |
| bV_VuV_lt_loop |               | ✔            | 📕    |
|                |               |              |      |

### Logical

| name           | introduce | inst_adapter | use  |
| -------------- | --------- | ------------ | ---- |
| V_V_not        |           | ✔            | ✔    |
| V_VV_and       |           | ✔            | ✔    |
| V_VuV_and      |           | ✔            | ✔    |
| V_VuV_and_loop |           | ✔            | ✔    |
| V_VV_or        |           | ✔            | ✔    |
| V_VuV_or       |           | ✔            | ✔    |
| V_VuV_or_loop  |           | ✔            | ✔    |
| V_VV_xor       |           | ✔            | ✔    |
| V_VuV_xor      |           | ✔            | ✔    |
| V_VuV_xor_loop |           | ✔            | ✔    |
|                |           | ✔            |      |

### BitWise

| name              | introduce                                         | inst_adapter | use  |
| ----------------- | ------------------------------------------------- | ------------ | ---- |
| bV_bV_not         | 向量元素按比特位求非<br />src_elem_num是8的整数倍 | ✔            |      |
| bV_bVbV_and       |                                                   | ✔            |      |
| bV_bVubV_and      |                                                   | ✔            |      |
| bV_bVubV_and_loop |                                                   | 📕            | 📕    |
| bV_bVbV_or        |                                                   | ✔            |      |
| bV_bVubV_or       |                                                   | ✔            |      |
| bV_bVubV_or_loop  |                                                   | 📕            | 📕    |
| bV_bVbV_xor       |                                                   | ✔            |      |
| bV_bVubV_xor      |                                                   | ✔            |      |

### Transcendental

| name       | introduce         | inst_adapter |      |
| ---------- | ----------------- | ------------ | ---- |
| V_V_log2   | 求对数, 底数为2   | ✔            | ✔    |
| V_V_ln     | 求对数, 底数为e   | ✔            | ✔    |
| V_V_pow2   | 求2的x次幂        | ✔            | ✔    |
| V_V_exp    | 求e的x次幂,高精度 | ✔            | ✔    |
| V_V_exp_lp | 求e的x次幂,高带宽 | ✔            | ✔    |
| V_V_sin    |                   | ✔            | ✔    |
| V_V_cos    |                   | ✔            | ✔    |

### 激活函数

| name          | introduce | inst_adapter |      |
| ------------- | --------- | ------------ | ---- |
| V_V_tanh      |           | ✔            | ✔    |
| V_V_sigmod    |           | ✔            | ✔    |
| V_V_relu      |           | ✔            | ✔    |
| V_V_satrelu   |           | ✔            | 📕    |
| V_V_leakyrelu |           | ✔            | 📕    |
| V_V_softplus  |           | ✔            | ✔    |

### 规约操作

| name    | introduce                                                    | inst_adapter |      |
| ------- | ------------------------------------------------------------ | ------------ | ---- |
| T_T_sum | 张量做SumReduce操作, 支持以下维度:<br />C方向规约,结果为HW(C=1), dim = 0 | ✔            | ✔    |
| T_T_avg |                                                              | ✔            | ✔    |
| T_T_max |                                                              | ✔            | ✔    |
| T_T_min |                                                              | ✔            | ✔    |



### 池化操作

| name         | introduce       | inst_adapter |      |
| ------------ | --------------- | ------------ | ---- |
| T_T_avg      |                 | ✔            | ✔    |
| T_T_sum      |                 | ✔            | ✔    |
| T_T_max      |                 | ✔            | ✔    |
| T_T_indexmax |                 | ✔            | ✔    |
| T_T_min      |                 | ✔            | ✔    |
| T_T_indexmin | 返回数据和index | ✔            | ✔    |

### 数据搬移

| name              | introduce                                                    | inst_adapter | use  |
| ----------------- | ------------------------------------------------------------ | ------------ | ---- |
| T_T_unpool        | unpool操作<br />每个窗口写回操作时,src数据写道dst数据的滑窗内的位置都是由imm提供的index表示 | ✔            | ✔    |
| T_T_unpool_avg    | avg pool 反向传播操作                                        | ✔            | ✔    |
| T_T_maskunpool    | 带index的pool 反向传播操作                                   | 📕            | 📕    |
| T_T_mirror        | 矩阵水平镜像                                                 | ✔            | ✔    |
| T_T_transpose     | 矩阵转置                                                     | ✔            | ✔    |
| T_T_rotate90      | 矩阵顺时针旋转90度                                           | ✔            | ✔    |
| T_T_rotate180     |                                                              | ✔            | ✔    |
| T_T_rotate270     |                                                              | ✔            | ✔    |
| T_T_nchw2nhwc     |                                                              | ✔            | ✔    |
| T_T_nhwc2nchw     |                                                              | ✔            | ✔    |
| T_T_concat        |                                                              | ✔            | ✔    |
| T_T_pad           |                                                              | ✔            | ✔    |
| T_T_tensorNorm    | 将以ch连续存储的输入张量在ch方向补齐到标准形式然后按照标准形式存储 | ✔            | ✔    |
| T_T_maskmove      | mask为1将数据写入dst, 为0dst数据不变                         | ✔            | ✔    |
| T_T_gatherscatter | 数据搬移, size表示byte个数<br />stride在各维度的stride值都是in byte的<br /> | ✔            | 📕    |
| V_V_maskgather    | 数据收集                                                     | ✔            | 📕    |
| V_bV_maskgather   |                                                              | ✔            | 📕    |
| T_T_img2col       | 特征图tensor转换成矩阵形式                                   | ✔            | 📕    |
|                   |                                                              |              |      |



### Convert

| name           | introduce | status |
| -------------- | --------- | ------ |
| V_V_int8_fp16  |           |        |
| V_V_int8_bf16  |           |        |
| V_V_int8_fp32  |           |        |
| V_V_int8_tf16  |           |        |
| V_V_int16_fp16 |           |        |
| V_V_int16_bf16 |           |        |
| V_V_int16_fp32 |           |        |
| V_V_int16_tf32 |           |        |
| V_V_int32_fp16 |           |        |
| V_V_int32_bf16 |           |        |
| V_V_int32_fp32 |           |        |
| V_V_int32_tf32 |           |        |
| V_V_bf16_int16 |           |        |
| V_V_bf16_int32 |           |        |
| V_V_bf16_fp16  |           |        |
| V_V_bf16_fp32  |           |        |
| V_V_bf16_tf32  |           |        |
|                |           |        |
|                |           |        |
|                |           |        |
|                |           |        |
|                |           |        |
|                |           |        |
|                |           |        |
|                |           |        |
|                |           |        |
|                |           |        |
|                |           |        |
|                |           |        |

// TODO

### 辅助计算类

| name               | introduce                                                    | inst_adapter | use  |
| ------------------ | ------------------------------------------------------------ | ------------ | ---- |
| S_V_count          | 计算向量类的非零元素个数                                     | ✔            | ✔    |
| V_V_argmax         | 返回最大值和index(maxvalue, index)                           | ✔            | ✔    |
| V_V_argmin         | 返回最小值和index(maxvalue, index)                           | ✔            | ✔    |
| T_memset           | 写入指定值                                                   | ✔            | ✔    |
| V_V_fp32_factorize | 将一个FP32分解成三个BF16格式数据                             | 📕            | 📕    |
| V_V_bit2fp         | 根据一个bitwise向量, 生成一个fp格式的向量<br />如果bit是0, 对应vector元素为1<br />否则为0 | ✔            | ✔    |
| T_T_bilinear       | 双线性插值                                                   | ✔            | ✔    |
| V_V_lut16          | 16位查找表, LUT表项数据为16bit<br />dst[i] = \*(fp16\*)((char\*)lut + src[i])<br />lut为查找表的起始地址<br />src[i]为第i个待查目标相对于LUT的基地址的便宜地址 | ✔            | ✔    |
| V_V_lut32          | 32位查找表, LUT表项数据为32bit<br />dst[i] = \*(fp32\*)((char\*)lut + src[i])<br />lut为查找表的起始地址<br />src[i]为第i个待查目标相对于LUT的基地址的便宜地址 | ✔            | ✔    |
| V_rand_gen         | 生成随机数                                                   | ✔            | ✔    |
| V_V_elem_mask      | 将一组32bit输入数据, 按照概率变成0或者保持原值               | 📕            | 📕    |
|                    |                                                              |              |      |
|                    |                                                              |              |      |
|                    |                                                              |              |      |
|                    |                                                              |              |      |
|                    |                                                              |              |      |
|                    |                                                              |              |      |
|                    |                                                              |              |      |
|                    |                                                              |              |      |



## 算子开发

### Count

> 计算向量内非零元素个数

```c++
// 参数
void count(int32* dst, bf16* src, int src_elem_num);
void count(int32* dst, fp16* src, int src_elem_num);
void count(int32* dst, tf32* src, int src_elem_num);
void count(int32* dst, fp32* src, int src_elem_num);
```

### argmax

dst_addr, dst_addr1, src0_addr, elem_num, input format

### bilinear

tmp_w是在src中的x， float类型  tmp_w = h / scale_h

### elem_mask

![image-20240228162100520](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20240228162100520.png)



![image-20240228162138686](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20240228162138686.png)



![image-20240228162336328](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20240228162336328.png)

![image-20240228162414932](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20240228162414932.png)



### rand_gen

#### 描述

![image-20240228164553088](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20240228164553088.png)

#### sim实现

![image-20240228163252624](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20240228163252624.png)

#### 参数对应

![image-20240228163332235](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20240228163332235.png)

#### adapter



![image-20240228165122819](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20240228165122819.png)

### Rotate

#### rotate 90

![image-20240229162237543](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20240229162237543.png)

![image-20240229161903534](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20240229161903534.png)





#### rotate 180

![image-20240229162247720](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20240229162247720.png)

![image-20240229161922018](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20240229161922018.png)

#### rotate 270

![image-20240229162257858](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20240229162257858.png)

![image-20240229161937033](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20240229161937033.png)



### activation

#### V_V_satrelu

![image-20240229184445692](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20240229184445692.png)

![image-20240229184307726](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20240229184307726.png)

#### V_V_leakyrelu

![image-20240229184453572](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20240229184453572.png)

![image-20240229184153682](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20240229184153682.png)





input - kernel + 1 = out

input +- kernel + 1 + padl + padr = 







### Quant

- 随机shape（3M以内）
- (N)Tensor
- 随机dtype(输入fp32，fp16， 输出int8)
- 随机参数（scale, zp）
