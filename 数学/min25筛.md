# min25 筛

## 前置

1. 我们想求前缀和 $\sum_{i=1}^n f(i)$
2. $f(i)$ 是积性函数。且 $f(p^{c})$ 能快速求值。
2. 定义完全积性函数 $F(x)$, 并且 $F(p) = f(p)$， 即在 $x$ 等于质数的时候 $F(x)$ 和 $f(x)$ 相等。

## DP 

定义两个 $dp$ 数组。

### 第一个
$$g(n, j)=\sum_{i=1}^n F(i)[i \in \mathrm{p} \| i \text { 的最小质因子大于第 } \mathrm{j} \text { 个素数 }]$$

注意第零个质数默认为 $1$, $g(n, 0)$ 不包含 $F(1)$

转移

$$g(n, j)= \begin{cases}g(n, j-1) & p_j^2>n \\ g(n, j-1)-F\left(p_j\right) \cdot\left(g\left(\left\lfloor\frac{n}{p_j}\right\rfloor, j-1\right)-\sum_{i=1}^{j-1} F\left(p_j\right)\right) & p_j^2 \leq n\end{cases}$$

求 $g(n, j)$ 的目的是得到 $1 - n$ 内所有质数 $x$ 的 $f(x)$ 之和。因为这时候 $F(x)$ 和 $f(x)$ 相同。所以我们最终要求的是 $g(n, c)$, 其中 $c$ 是第一个满足
 $p_{c}^{2} > n$ 的数。
### 第二个

$$S(n, j)=\sum_{i=0}^n f(i)[i \text { 的最小质因子大于第 } \mathrm{j} \text { 个素数 }]$$

$$S(n, j)=g(n,c)-\sum_{i=1}^{j} f\left(p_i\right)+\sum_{k=j+1}^{p_k^2 \leq n} \sum_{e=1}^{p_k^e \leq n} f\left(p_k^e\right)\left(S\left(\left\lfloor\frac{n}{p_k^e}\right\rfloor, k\right)+[e>1]\right)$$

其中 $g(n,c)-\sum_{i=1}^{j} f\left(p_i\right)$， 计算得到了在范围内的所有质数， $\sum_{k=j+1}^{p_k^2 \leq n} \sum_{e=1}^{p_k^e \leq n}$ 是枚举合数的最小质因子和最小质因子的次数，后面利用积性函数的性质得到所有合数的 $f(x)$ 和。其中 $[e > 1]$ 是为了保证不算入质数(质数都用 $g$ 算)。

## $g(n, 0)$ 计算

考虑到我们最后对于 $g$ 数组只需要求 $g(n, c)$, ‌它的意义是所有质数位置的 $f$    之和，如果 $f(p)$ 是低次多项式（假设次数是 $x$ )，那么我们就可以求 $x$ 个 $g$ 函数, 每个 $g$ 函数对应的 $f'$ 是 $f'(p) = p ^ {k}$, $k$ 是某个次数。因为只有把 $f(p)$ 设成这样简单的形式才好算 $g(n, 0)$。

那么当 $F(p) = f(p) = p ^ {k}$ 的时候怎么算 $\sum_{1 \leq i \leq n} F(i)$ (就是 $g(n, 0)$) 呢。这里有一个关键性质:

如果 $F(p) = p ^ {k}$ 那么可以证明 $F(i) = i ^ {k}$，也就是 $F(i)$ 是个完全积性函数。这时候 $\sum_{1 \leq i \leq n} F(i)$ 可以直接套公式计算(如果 $k$ 很小的话)。