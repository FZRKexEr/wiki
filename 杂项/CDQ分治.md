# CDQ 分治

## 概述
类似于普通的分治算法。CDQ 分治对第一位偏序信息做普通的分治，第二维偏序信息排序后利用单调性做双指针维护，第三维信息用树状数组求前缀来维护。复杂度为 $O(n \log ^{2} n)$

1D/1D 动态规划很多都能用 CDQ 分治把复杂度从 $O(n^{2})$ 降到 $O(n \log^{2} n)$。把数组下标作为第一偏序，分治数组。利用双指针 check 所有能转移的位置，用树状数组得到的 sum 或者 min/max 来更新 dp 值。

## 例题
1. 洛谷 P3810 【模板】三维偏序（陌上花开）
2. 二维最长上升子序列。

## 需要注意的细节

1. CDQ 分治题目有时会卡常，数据会很大（也许是因为想卡掉树套树）。尽量使用树状数组来维护第三维信息。
2. 线段树/树状数组不要在递归时现场重建，在递归函数外建好树后，每次用完就复原，虽然复杂度不受影响，但是会加速很多。
3. 尽量不要用动态开点线段树，离散化会加速很多。
4. 因为 CDQ 分治不可避免的需要多次排序，按不同关键词排序，所以在输入数据的时候就记录一下 id，用 id 来更新信息，可以避免很多因为排序导致的混乱。

## 例子
二维最长上升子序列

```cpp
    vector<int> dp(n + 1, 0);
    BIT T(dis.size());
    function<void(int, int)> CDQ = [&] (int l, int r) {
      if (l == r) {
        dp[l]++;
        return;
      }
      int mid = (l + r) >> 1;
      CDQ(l, mid); 

      sort(a.begin() + l, a.begin() + mid + 1, [&](auto i, auto j) { return i[1] < j[1]; });
      sort(a.begin() + mid + 1, a.begin() + r + 1, [&](auto i, auto j) { return i[1] < j[1]; });

      int p = l;
      for (int i = mid + 1; i <= r; i++) {
        while (p <= mid && a[p][1] < a[i][1]) {
          T.modify(a[p][2], dp[a[p][0]]);
          p++;
        }
        if (a[i][2] != 1) {
          dp[a[i][0]] = max(dp[a[i][0]], T.query(a[i][2] - 1));
        }
      }

      for (int i = l; i < p; i++) {
        T.init(a[i][2]);
      }

      sort(a.begin() + l, a.begin() + mid + 1);
      sort(a.begin() + mid + 1, a.begin() + r + 1);
      CDQ(mid + 1, r);
    };
``` 