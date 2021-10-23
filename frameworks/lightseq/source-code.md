其中 pytorch 层面的 encoder layer，会申请总体大小的 prameter，然后会绑定给 layer，跟他共享内存。这样是一个大片的内存里，包含了很多个小 layer 的 parameter

## 疑问
1. [pytorch x.div(y) ](https://pytorch.org/docs/stable/generated/torch.div.html#torch.div) : 并没有详细说明除法里采用何种近似策略？用的 rn，看 ls 里 dropout half 里的实现
2. half 的实现里，输入/出的 half, 却用 reinterpret_cast<float4*>(out) ,可以这样写？看论文意思是计算过程强制转为 float32 来计算
3. x.div(keep_prop)*mask 这步符合结合律吗？x\*mask/keep_prop	=> mask 是 int，所以不满足。因为 0/0.7 之后就不是0了
4. x*m[0] 和 \__hmul2(X, m2) 有啥差别？什么时候用 \*，什么时候用 mul ?
5. 对于精度转换，会自动做吗？比如 floatx * intm，如果自动做，那提供精度转换函数的意义在哪里？
6. 对于 ls\_dropout\_kernel 的实现，是每四个元素在一个kernel 里处理。其中只有 cur_rand4 是四个元素一批的。这种快还是每个 kernel 里处理一个元素快呢
7.  mixed precision : half 有 half2 当作一个batch 一起处理的，所以速度快（几乎一倍？）; 而且可以一次处理4、8个数据。比如 uint64* m8. m8[0] = mask8[i];