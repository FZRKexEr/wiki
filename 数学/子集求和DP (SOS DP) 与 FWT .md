# 子集求和DP (SOS DP) 与 FWT 

> dkr 博客：http://blog.leanote.com/post/rockdu/TX20

## 子集求和DP / 高维前缀和
## 解决的问题
求 $f(x), 1 \leq x \leq N$ ，其中 $f(x)$ 为 $x$ 的所有子集的权值和, $F[mask]=\sum_{i \subseteq \text { mask }} A[i]$ 。

## 暴力做法
无损枚举子集 $O(3 ^ N)$：

```cpp
// iterate over all the masks
for (int mask = 0; mask < (1<<n); mask++){
	F[mask] = A[0];
    // iterate over all the subsets of the mask
    for(int i = mask; i > 0; i = (i-1) & mask){
    	F[mask] += A[i];
    }
}
```
## SOS DP
复杂度 $O(N\log N)$

设状态 $f(S, i)$ 表示 $mask = S$ 时，只有前 $i$ 位存在不同(完全相同也可以)的子集的权值和。  

> 这里和上面的状态的区别是：`F[mask]` 直接就包含了所有的子集的权值和，这里是分开来表示的。

状态转移方程： 
如果 $S$ 的第 $i$ 位等于 $0$ : `f(S, i) = f(S, i - 1)`
如果 $S$ 的第 $i$ 位等于 $1$ : `f(S, i) = f(S, i - 1) + f(S ^ (1 << i), i - 1)`

```cpp
for (int i = 1; i <= 1e6; i++) {
    s[i][0] = a[i];
    if (i & 1) s[i][0] += a[i - 1];
    for (int j = 1; j <= 21; j++) { // 注意必须跑满
        s[i][j] = 0;
        s[i][j] += s[i][j - 1];
        if (i & (1 << j)) {
            s[i][j] += s[i ^ (1 << j)][j - 1];
        }
    }
}
```

```cpp
//iterative version
for(int mask = 0; mask < (1<<N); ++mask){
	dp[mask][-1] = A[mask];	//handle base case separately (leaf states)
	// 注意：不要用-1， 从0开始
	for(int i = 0;i < N; ++i){
		if(mask & (1<<i))
			dp[mask][i] = dp[mask][i-1] + dp[mask^(1<<i)][i-1];
		else
			dp[mask][i] = dp[mask][i-1];
	}
	F[mask] = dp[mask][N-1];
}
//memory optimized, super easy to code.
for(int i = 0; i<(1<<N); ++i)
	F[i] = A[i];
for(int i = 0;i < N; ++i) for(int mask = 0; mask < (1<<N); ++mask){
// 考考你，这里无论mask正着刷表还是倒着刷表，答案都一样，为什么呢？
	if(mask & (1<<i))
		F[mask] += F[mask^(1<<i)];
}
```

## FWT
> https://blog.csdn.net/a_forever_dream/article/details/105110089

> 求解 `&` 和 `|` 的 卷积实际上是 FZT(fast zeta transform) 和 FMT(fast mobius transform) 但是常常被归为FWT(fast Walsh–Hadamard transform)

### 或
一段非常精彩的解释
> 感性理解其实很简单, $F W T(A)[i]$ 记录的是 $i$ 的子集之和, $F W T(B)[i]$ 也是，那么他们相乘，其实就是双方子集中 的每一个元素两两相乘，取出来的两个元素的下标或起来一定还是 $i$ 的子集中的一个，由于 $C[i]$ 记录的是下标或起来 为 $i$ 的乘积之和，那么全部乘完之后，我们不仅得到了 $C[i]$, 还得到了 $i$ 的所有子集的 $C$, 也就是 $F W T(C)[i]$ 。

FWT 或的正变换其实就是 SOS DP。`dp[mask][0]` 就是原数组， `dp[mask][limit]` 就是 FWT。


### 与
$C_{k} = \sum_{i \& j = k}A_{i}B_{j}$
显然 $i$ 和 $j$ 一定是 $k$ 的父集，所以把 `SOSDP` 倒过来就行了。

### 异或

异或无法通过 `SOSDP` 得到，考虑使用分治的策略，列出一些从 $0$ 开始的下标，通过观察可以发现一些规律（就是这样），然后就能得到了。


### 做题经验 
FWT 最常见的是异或。FWT的题基本上不会直接能看出是FWT，需要根据一些性质做一些转换，弄成FWT问题，这就涉及到异或（与，或）的一些性质。基本思路是用位运算（主要是异或）来描述题目的一些操作。然后用FWT快速完成。
例如：
1. `E - United in Stormwind` 中，需要把两个二进制数的不同用异或来描述。并且统计异或和的对数。
2. https://leetcode-cn.com/problems/count-pairs-with-xor-in-a-range/ 统计异或和在一定范围内的对数。
3. FWT坑点说明：
    1. n 必须是2的倍数
    2. fwt只能求出 0 ~ n-1 的fwt，言外之意是如果要求 2^m 的fwt 需要把n设置成 2^(m+1)
    3. 不能擅自不取模，即使题目不要求取模，也要取模。如果非要不取模，需要用栈来存计算顺序，ufwt时，倒着做一遍。（取模会更慢）
    4. inv2 不要每次都算，这个非常耗时。
    5. FWT数组可能非常大（结果可能很小），所以不能以题目数据范围为准，一定要开long long