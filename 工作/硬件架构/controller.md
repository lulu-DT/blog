# NeuralCore controller

![image-20240202142340369](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20240202142340369.png)

> NeuralCore Controller为NeuralCore的控制单元，接收kernel cluster发送的指令，并做依赖检测，然后讲指令发送到执行单元

## 特性、规格

![image-20240202153439876](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20240202153439876.png)

- C908MP有4个C908 core，每个core抽象为1个worker，每个worker有一个虚拟的NCC，对应的有一个RF和cmd queue
- 每个虚拟的NCC根据基址来区分是否是自己的请求访问
- Woker通过LD/ST方式配置对于指令的数据和控制寄存器后，该指令加上对应的cmd-type（CT、NE、RDMA、WDMA、TDMA、SCALAR）即被发射到cmd queue中
- 每个wofker内有6个cmd queue（CT、NE、RDMA、WDMA、TDMA、SCALAR），每个queue的深度为8，也可以单独设置
- 单个cmd_queue内为顺序发射，每个worker内地 6个cmd queue间调度方式为round robin，只有head且依赖接触的指令参与调度
- worker之间的仲裁方式为优先级调度
- worker之间默认没有依赖关系
- NCC内部有scalar unit，支持recip、sqrt、sin、cos、log2、pow2运算，指出FP16、BF16、TF32、FP32，并将结果写回RF
- 支持CT结果写回NCC RF，最大支持一条指令写回两条数据
- SPM依赖检测方式划分逻辑bank，目前划分了64个逻辑bank
- DMA依赖检测方式为判断地址的overlap，目前存放了8条历史信息

#### NCC可以分成RF、IB、ARB、COMPRESS四类模块

![image-20240202153646133](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20240202153646133.png)

![image-20240202153653309](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20240202153653309.png)

![image-20240202153707152](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20240202153707152.png)





