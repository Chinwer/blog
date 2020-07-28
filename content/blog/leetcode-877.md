+++

author = "邱圆辉"

title = "力扣总结877：石子游戏"

date = "2020-07-28"

description = "第877题的几种思路"

tags = [
    "leetcode", "solution"
]

images = ["http://47.94.16.255/images/images/2020/07/19/ed93cb4a546971f4dfaaf31666dfb8b1_1440w.jpg"]

+++



### 题目链接

[石子游戏](https://leetcode-cn.com/problems/stone-game/)

### 题目描述

亚历克斯和李用几堆石子在做游戏。偶数堆石子排成一行，每堆都有正整数颗石子 $piles[i]$ 。

游戏以谁手中的石子最多来决出胜负。石子的总数是奇数，所以没有平局。

亚历克斯和李轮流进行，亚历克斯先开始。 每回合，玩家从行的开始或结束处取走整堆石头。 这种情况一直持续到没有更多的石子堆为止，此时手中石子最多的玩家获胜。

假设亚历克斯和李都发挥出最佳水平，当亚历克斯赢得比赛时返回 $true$，当李赢得比赛时返回 $false$。

### 示例

> 输入：[5, 3, 4, 5]
>
> 输出：true
>
> 解释：
>
> 亚历克斯先开始，只能拿前 5 颗或后 5 颗石子 。
> 假设他取了前 5 颗，这一行就变成了 [3,4,5] 。
> 如果李拿走前 3 颗，那么剩下的是 [4,5]，亚历克斯拿走后 5 颗赢得 10 分。
> 如果李拿走后 5 颗，那么剩下的是 [3,4]，亚历克斯拿走后 4 颗赢得 9 分。
> 这表明，取前 5 颗石子对亚历克斯来说是一个胜利的举动，所以我们返回 true 。

### 思路一

- 动态规划状态的定义

  此思路来自[这里](https://labuladong.gitbook.io/algo/dong-tai-gui-hua-xi-lie/dong-tai-gui-hua-zhi-bo-yi-wen-ti)，简单来说，此思路核心为一个二维的  $n\times n$ 的 $dp$ 数组，数组中每个元素均为一个 $pair$ 对，其中：

  - $dp[i][j].first$ 表示对于 $piles[i...j]$ 这部分石头堆，先手能获得的最高分数。
  - $dp[i][j].second$ 表示对于 $piles[i...j]$ 这部分石头堆，后手能获得的最高分数。

  因此，最终的答案为先手和后手的最终分数之差，即：

  ```c++
  dp[0][n - 1].first - dp[0][n - 1].second
  ```

  

- 状态方程

  根据上述 $dp$ 数组的定义，可以得到状态方程如下：
  ```c++
  dp[i][j].first = max(piles[i] + dp[i + 1][j].second, piles[j] + dp[i][j - 1].second)
  ```
  解释：我作为先手，面对 $piles[i...j]$ 时，有两种选择：

  - 选择最左边的那堆石头，然后面对 $piles[i + 1...j]$ 
  - 选择最右边的那堆石头，然后面对 $piles[i...j - 1]$

  不管哪种选择，选完后都轮到对方，即我变成了后手。

  

- base case

  当只有一堆石头时，先手的得分即为那堆石头的个数，后手的得分为0：

  ```c++
  dp[i][i].first = piles[i];
  dp[i][i].second = 0;
  ```

  其中 $0 \leq i = j < n$ 。

  

- c++代码

  根据上述分析可写出如下代码：

  ```c++
  class Solution {
      public:
          bool stoneGame(vector<int>& piles) {
              int n = piles.size();
              vector<vector<pair<int, int>>> dp(n, vector(n, pair(0, 0)));
              // base case
              for (int i = 0; i < n; i++) {
                  dp[i][i].first = piles[i];
                  dp[i][i].second = 0;
              }
              for (int i = n - 2; i >= 0; i--) {
                  for (int j = i + 1; j < n; j++) {
                      int left = piles[i] + dp[i + 1][j].second;
                      int right = piles[j] + dp[i][j - 1].second;
                      if (left > right) {
                          dp[i][j].first = left;
                          dp[i][j].second = dp[i + 1][j].first;
                      } else {
                          dp[i][j].first = right;
                          dp[i][j].second = dp[i][j - 1].first;
                      }
                  }
              }
              return dp[0][n - 1].first - dp[0][n - 1].second;
          }
  };
  ```

  

### 思路二

- 动态规划状态的定义

  此思路来自[这里](https://cloud.tencent.com/developer/article/1446700)，youtube上也有这种思路的讲解视频：[视频](https://www.youtube.com/watch?v=xJ1Rc30Pyes)

  此思路核心同样为一个 $n \times n$ 的二维 $dp$ 数组，其中 $dp[i][j]$ 定义为：**在面对石子堆 $piles[i...j]$ 时，先手与后手获得的分数差的最大值**。

  

- 状态方程

  根据上述 $dp$ 状态的定义，可得状态方程如下：

  ```c++
  dp[i][j] = max(piles[i] - dp[i + 1][j], piles[j] - dp[i][j - 1])
  ```



- base case

  当只有一堆石头时，两者得分差的最大值即为这对石头的石子数：

  ```c++
  dp[i][i] = piles[i]
  ```



- c++代码

  根据上述分析，可写出如下代码：

  ```c++
  class Solution {
    	public:
      	bool stoneGame(vector<int> &piles) {
              int n = piles.size();
              // dp[i][j] means the maximum difference between
              // the score of the first hand and second hand
              vector<vector<int>> dp(n, vector(n, 0));
              for (int i = 0; i < n; i++) {
                  dp[i][i] = piles[i];
              }
              for (int i = n - 2; i >= 0; i--) {
                  for (int j = i + 1; j < n; j++) {
                      dp[i][j] = max(piles[i] - dp[i + 1][j], piles[j] - dp[i][j - 1]);
                  }
              }
              return dp[0][n - 1] > 0;
          }
  };
  ```

  