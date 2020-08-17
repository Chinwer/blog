+++

author = "邱圆辉"

title = "力扣总结：650. 只有两个键的键盘"

date = "2020-08-10"

description = "第650题的思路总结"

tags = [
    "leetcode", "solution"
]

images = ["http://47.94.16.255/images/images/2020/08/09/pexels-photo-531321.jpg"]

+++

### 题目描述

最初在一个记事本上只有一个字符 'A'。你每次可以对这个记事本进行两种操作：

- `Copy All` (复制全部) : 你可以复制这个记事本中的所有字符(部分的复制是不允许的)。
- `Paste` (粘贴) : 你可以粘贴你上一次复制的字符。
  给定一个数字 `n` 。你需要使用最少的操作次数，在记事本中打印出 **恰好**  `n` 个 'A'。输出能够打印出 `n` 个 'A' 的最少操作次数。

### 示例

```
输入: 3
输出: 3
解释:
最初, 我们只有一个字符 'A'。
第 1 步, 我们使用 Copy All 操作。
第 2 步, 我们使用 Paste 操作来获得 'AA'。
第 3 步, 我们使用 Paste 操作来获得 'AAA'。
```

### 思路一

具体思路就懒得写了，反正是写给自己看的，详情可参考题解：[动态规划](https://leetcode-cn.com/problems/2-keys-keyboard/solution/dong-tai-gui-hua-xiang-xi-fen-xi-jie-shi-wei-shi-y/)。

- dp 数组定义

    ```
    dp[i][j]: 要使得当前记事本上 'A' 的个数为 i，且缓冲区 'A' 的个数为 j 的最小步数
    ```

- 状态转移方程为：

  ```
  dp[i][j] = min(dp[i - j][j] + 1, dp[i][j]), j < i
  ```

- c++代码：

  ```c++
     class Solution {
        public:
            int minSteps(int n) {
                if (!n) {
                    return 0;
                }
                // dp[i][j]: there are i 'A's on screen, and j 'A's in cache
                vector<vector<int>> dp(n + 1, vector(n + 1, n));
                dp[1][0] = 0;
                dp[1][1] = 1;
                for (int i = 1; i <= n; i++) {
                    int min_ = dp[i][0];
                    for (int j = 1; j < i; j++) {
                        dp[i][j] = min(dp[i][j], dp[i - j][j] + 1);
                        min_ = min(min_, dp[i][j]);
                    }
                    dp[i][i] = min_ + 1;
                }
                return dp[n][n] - 1;
            }
    };
  ```

### 思路二

题解链接：[题解](https://leetcode-cn.com/problems/2-keys-keyboard/solution/zhi-jie-dpji-ke-by-ding-tao/)。

- dp 数组定义

  ```
  dp[i]: 让屏幕上出现 i 个 'A' 的最小步数
  ```

- 状态转移方程

  ```
  dp[i] = min(dp[i], dp[j] + i / j), i % j == 0
  ```

- c++代码

  ```c++
  class Solution {
    	public:
      	int minSteps(int n) {
              vector<int> dp(n + 1, n);
              dp[0] = dp[1] = 0;
              for (int i = 2; i <= n; i++) {
                  for (int j = 1; j < i; j++) {
                      if (i % j == 0) {
  						dp[i] = min(dp[i], dp[j] + i / j);
                      }
                  }
              }
              return dp[n];
          }
  };
  ```

### 思路三

题解链接：[官方题解](https://leetcode-cn.com/problems/2-keys-keyboard/solution/zhi-you-liang-ge-jian-de-jian-pan-by-leetcode/)。

思路就是这个题的答案其实是将 `n` 进行质因数分解，然后得到的所有质因子的和。

- c++代码

  ```c++
  class Solution {
  	public:
      	int minSteps(int n) {
              int res = 0, d = 2;
              while (n > 1) {
  				if (n % d == 0) {
                      res += d;
                      n /= d;
                  }
                  d++;
              }
              return res;
          }
  };
  ```

  