+++

author="邱圆辉"

title = "斜向遍历二维数组 "

date = "2020-07-25"

description = "斜向遍历二维数组的代码框架"

tags = ["array", "traversal"]

images = ["http://47.94.16.255/images/images/2020/07/19/v2-1d69c904759f7e5085873840922dba35_1440w.jpg"]

+++



在动态规划中有时会需要斜着遍历二维数组，在这里用c++简单记录一下遍历的代码：

```c++
vector<vector<int>> dp(n, vector(n, 0));
for (int l = 2; l <= n; l++) {
    for (int i = 0; i < n; i++) {
        int j = l + i - 1;
        /* Visit dp[i][j] */
    }
}
```

