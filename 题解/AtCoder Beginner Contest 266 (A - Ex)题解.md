# AtCoder Beginner Contest 266 (A - Ex)题解

# A - Middle Letter	

读入后直接输出即可。

# B - Modulo Number	

答案是 $(n \% 998244353 + 998244353) \% 998244353$

# C - Convex Quadrilateral	

对于逆时针相邻的三个点 $a, b, c$，叉乘判断点 $c$ 是否在向量 $\overrightarrow{\mathrm{ab}}$ 的左侧。

```cpp

int mul(array<int, 2> a, array<int, 2> b) {
  return a[0] * b[1] - a[1] * b[0];
}

signed main() {
  ios::sync_with_stdio(false); 
  cin.tie(0);

  vector<array<int, 2>> a(4);
  for (int i = 0; i < 4; i++) {
    cin >> a[i][0] >> a[i][1];
  }
  int flag = true;
  for (int i = 0; i < 4; i++) {
    int j = (i + 1) % 4, k = (i + 2) % 4;
    array<int, 2> u = {a[j][0] - a[i][0], a[j][1] - a[i][1]};
    array<int, 2> v = {a[k][0] - a[i][0], a[k][1] - a[i][1]};
    if (mul(u, v) <= 0) flag = false;
  }
  if (flag) cout << "Yes";
  else cout << "No";

  return 0;
}
```

# D - Snuke Panic (1D)	

按照时间轴动态规划一下即可。

```cpp
signed main() {
  ios::sync_with_stdio(false); 
  cin.tie(0);
   
  int n; cin >> n;
  vector<vector<int>> a(1e5 + 1, vector<int> (5, 0));
  vector<vector<int>> dp(1e5 + 1, vector<int> (5, -0x3f3f3f3f3f3f3f3f));
  for (int i = 1; i <= n; i++) {
    int t, x, val;
    cin >> t >> x >> val;
    a[t][x] += val;
  }
  int ans = 0;
  dp[0][0] = 0;
  for (int i = 1; i <= (int) 1e5; i++) {
    for (int j = 1; j <= 3; j++) {
      dp[i][j] = max(dp[i - 1][j + 1], dp[i - 1][j - 1]);
      dp[i][j] += a[i][j];
    }
    dp[i][0] = a[i][0] + dp[i - 1][1];
    dp[i][4] = a[i][4] + dp[i - 1][3];
    for (int j = 0; j <= 4; j++) {
      dp[i][j] = max(dp[i][j], dp[i - 1][j] + a[i][j]);
      ans = max(ans, dp[i][j]);
    }
  }

  cout << ans << endl;

  return 0;
}
```

# E - Throwing the Die	

考虑最优策略是什么，假设当前在第 $i$ 轮游戏, 还有 $N - i$ 轮游戏，第 $i$ 轮游戏骰子摇出来是 $x$, 那么考虑要不要在这一轮终止游戏。

设 $f(n)$ 表示进行 $n$ 轮游戏的期望最优值。那么本题的答案就是 $f(N)$。

显然，如果 $x$ 大于 $f(N - i)$，说明这一轮摇出来的东西比后面继续游戏摇出来的期望要好，所以应该立刻终止游戏，选择 $x$ 作为结果。如果 $x$ 小于 $f(N - i)$, 应该继续游戏。

所以每一轮枚举 $6$ 种骰子进行转移即可。

```cpp
signed main() {
  ios::sync_with_stdio(false);
  cin.tie(0);
   
  int n; cin >> n;

  vector<double> f(n + 1);
  f[1] = 3.5;
  for (int i = 2; i <= n; i++) {
    f[i] = 0.0;
    for (int j = 1; j <= 6; j++) {
      if (j > f[i - 1]) {
        f[i] += j / 6.0;
      } else {
        f[i] += f[i - 1] / 6.0;
      }
    }
  }

  cout << fixed << setprecision(10) << f[n];

  return 0;
}
```

# F - Well-defined Path Queries on a Namori	

题意：给一个无向连通图，问两点之间是否只有唯一的一条路径。

显然在一个点数大于 $1$ 的边双连通分量里面，任意两点之间都有至少两条不同的路径。答案一定是 `No`。

考虑什么时候是 `Yes`。

当且仅当询问的两个点之间的路径全部是桥的时候才能是 `Yes`。

所以找出所有的桥，把相邻的桥用并查集并起来即可。

```cpp
signed main() {
  ios::sync_with_stdio(false); 
  cin.tie(0);
   
  int n; cin >> n;
  vector<vector<int>> g(n + 1);
  vector<array<int, 2>> edges;

  auto add = [&](int u , int v) {
    edges.push_back({u, v});
    g[u].push_back((int) edges.size() - 1);
  };

  for (int i = 1; i <= n; i++) {
    int u, v; cin >> u >> v;
    add(u, v), add(v, u);
  }

  Tarjan T(1, n, g, edges);
  DSU dsu(n);
  for (int i = 0; i < (int) edges.size(); i++) {
    if (T.isbridge[i]) dsu.merge(edges[i][0], edges[i][1]);
  }

  for (auto &it : T.bcc) {
    if ((int) it.size() != 1) continue;
    int u = it[0];
    for (auto &jt : g[u]) {
      dsu.merge(edges[jt][0], edges[jt][1]);
    }
  }
  int q; cin >> q;
  for (int i = 1; i <= q; i++) {
    int u, v; cin >> u >> v;
    if (dsu.find(u) == dsu.find(v)) cout << "Yes" << endl;
    else cout << "No" << endl;
  }

  return 0;
}
```

# G - Yet Another RGB Sequence	

题意：考虑一个字符串，其中有 $R$ 个 `R`, $G$ 个 `G`, $B$ 个 `B`, $K$ 个 `RG`。问这样的字符串有多少个。

先把 $k$ 个 `RG` 单独提出来，`R` 剩 $R - k$ 个，`G` 剩 $G - k$ 个。考虑用这些 `R`, `RG`, `B` 组成的字符串。这样的字符串有 $\frac{r + b}{(r - k)!k!b!}$ 个。 再把 `G` 插入到这些字符串中，但是不能插入到 `R` 后面，方案有 $\left(\begin{array}{c} b + g \\ g-k\end{array}\right)$ 个。所以最终答案是 $\frac{r + b}{(r - k)!k!b!} \left(\begin{array}{c} b + g \\ g-k\end{array}\right)$ 。

```cpp
signed main() {
  ios::sync_with_stdio(false); 
  cin.tie(0);
   
  int r, g, b, k; cin >> r >> g >> b >> k;

  Combination T(2e6);
  int res = T.fac[r + b] * T.inv(T.fac[r - k] * T.fac[k] % MOD * T.fac[b] % MOD) % MOD;
  res = res * T.C(b + g, g - k) % MOD;
  cout << res << endl;

  return 0;
}
```

# EX - Snuke Panic (2D)	

正在写...