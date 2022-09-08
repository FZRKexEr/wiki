# 关于 Splay 和 平衡树

## 核心思想
把每次操作的点旋转为二叉树的根节点，并把路径上的点都通过双旋压缩一下，可以使得树的高度在 $\log n$ 级别。

## Splay 的两个功能
1. 作为二叉搜索树。用 Splay 来使得二叉搜索树的层数在 $\log n$ 级别。
2. 维护区间信息。把一个区间通过 splay 操作放进一棵子树中，使用 lazy 标记修改整个区间的信息。

## Splay 例题

1. 文艺平衡树 (基础的区间翻转)
2. [序列终结者](https://www.luogu.com.cn/problem/P4146) (Splay 区间操作)
3. [Play with Chain](https://vjudge.net/problem/HDU-3487) (Splay 区间移动+翻转)

4. [\[HNOI2012\]永无乡](https://www.luogu.com.cn/problem/P3224) (合并平衡树)


