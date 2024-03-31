# Command Check

## 相关约束

### common

- 地址检查
- 

### Arith

#### Abs

> AbsVV(fmt\* src0_addr, fmt\* dst_addr, uint32_t elem_count, Data_fmt fmt)

- src0_addr

​	描述：src tensor在spm里面的偏移地址

​	约束：0 <= src0_addr < 3M

- dst_addr

  ​    描述：dst tensor在spm里面的偏移地址

  ​    约束：0 <= dst_addr< 3M

- elem_count

​	描述：数量

​	约束：无

- fmt

​	描述：输入输出数据类型

​	约束: 仅支持`bf16` 、`fp16`、 `tf32`、 `fp32`

### DataMove

#### T_T_unpool

> T_T_unpool(Type\*dst, Type\*src, int n, int h, int w, int c, int kernel_h, int kernel_w, int stride_w, int stride_h, int index)

- dst
- src
- kernel_h
- kernel_w
- stride_w
- stride_h

## 实现

