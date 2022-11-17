# C++ 语法

## vector 的效率

一般情况, vector 在开好空间后很难被卡。但是写矩阵的时候一定不要用 vector, 小矩阵用 vector 和 array 的速度差距在 5 倍以上。我也不知道为什么。

## 行末空格
把 `" \n"` 想像成字符串数组。

```
for (int i = 1; i <= n; i++) {
	for (int j = 1; j <= n; j++) {
		cout << a[i][j] << " \n"[j == n];
	}
}
```

## move()

移动 STL

```
vector<int> a, b = {1, 2, 3};
a = move(b)
```