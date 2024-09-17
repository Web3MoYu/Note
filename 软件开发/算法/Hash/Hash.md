# 布隆过滤器

> 布隆过滤器的原理是，当一个元素被加入集合时，通过$K$个[散列函数](https://zh.wikipedia.org/wiki/散列函数)将这个元素映射成一个位[数组](https://zh.wikipedia.org/wiki/数组)中的$K$个点，把它们置为$1$。检索时，我们只要看看这些点是不是都是$1$就（大约）知道集合中有没有它了：如果这些点有任何一个$0$，则被检元素一定不在；如果都是$1$，则被检元素很可能在。这就是布隆过滤器的基本思想。

**简单位运算**

```java
int[] arr = new int[10]; // bit数组 32bit * 10 -> 320bits

// 拿到i位的状态
int numIndex = i / 32;
int bitIndex = i % 32;
int s = ((arr[numIndex] >> bitIndex) & 1);

// 将i位的状态改位1
arr[numIndex] = arr[numIndex] | (1 << bitIndex);

// 改为0
arr[numIndex] = arr[numIndex] & (~(1<<bitIndex));
```

布隆过滤器的实现主要是跟$M$和$K$有关，$M$是bit位的长度，如何让其均匀分布在bit位上，$K$是散列的个数。而决定$M$和$K$的是$N$(样本量)、$P$(失误率)
$$
M=-{\frac{n\times \\lnP}{(\ln2)^2}},K=\ln2\times \frac{M}{N},P_真={(1-e^{-\frac{n\times K_真}{M_真}})}^{K_真}
$$
