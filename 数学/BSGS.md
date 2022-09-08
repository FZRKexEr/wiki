# BSGS

## 朴素 BSGS

复杂度: $O(\sqrt n)$ 

$$a^{x} \equiv b \quad(\bmod p)$$

令 $$x=A\lceil\sqrt{p}\rceil-B (0 \leq A, B \leq\lceil\sqrt{p}\rceil)$$

 $$a^{A\lceil\sqrt{p}\rceil-B} \equiv b(\bmod p)$$
 
 $$a^{A\lceil\sqrt{p}\rceil} \equiv b a^{B}(\bmod p)$$
 
 预处理 $b a^{B}$ 的所有取值，然后枚举 $A$。用 hash 或者 map 判断是否存在相等的 $b a^{B}$
## 求 $x^{a} \equiv b \quad(\bmod p)$

用原根表示 $x = g ^ {c}$, 求离散对数即可。