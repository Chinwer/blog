+++

author = "邱圆辉"

title = "力扣总结：956. 最高的广告牌"

date = "2020-08-17"

description = "第956题的思路总结"

tags = [
    "leetcode", "solution"
]

images = ["http://47.94.16.255/images/images/2020/08/09/pexels-photo-531321.jpg"]

+++

### 题目链接

[956. 最高的广告牌](https://leetcode-cn.com/problems/tallest-billboard/)

### 题目描述

你正在安装一个广告牌，并希望它高度最大。这块广告牌将有两个钢制支架，两边各一个。每个钢支架的高度必须相等。

你有一堆可以焊接在一起的钢筋 rods。举个例子，如果钢筋的长度为 1、2 和 3，则可以将它们焊接在一起形成长度为 6 的支架。

返回广告牌的最大可能安装高度。如果没法安装广告牌，请返回 0。

### 示例

```
输入：[1,2,3,6]
输出：6
解释：我们有两个不相交的子集 {1,2,3} 和 {6}，它们具有相同的和 sum = 6。
```

### 思路一

- 思路来源

  [来源一](https://leetcode-cn.com/problems/tallest-billboard/solution/leetcode-956-dong-tai-gui-hua-qiu-jie-by-ck0825/)，[来源二](https://leetcode-cn.com/problems/tallest-billboard/solution/dong-tai-gui-hua-jian-dan-yi-dong-de-1wei-dp-by-ch/)

- 状态定义

  ```
  dp[i][j]：将前 i 个钢管分成两个集合，集合值为 j 时，子集和的最大值
  ```

- 状态转移

  - 不用第 $i$ 个钢管，此时有：
    $$
    dp[i][j] = dp[i - 1][j]
    $$
    

  - 把第 $i$ 个钢管放在子集和较大的一边，此时有：
    $$
    dp[i][k] = max(dp[i][k], dp[i - 1][j] + rods[i]), k = j + rods[i]
    $$

  - 把第 $i$ 个钢管放在子集和较小的一边，此时有：
    $$
    dp[i][k] = max(dp[i][k], dp[i - 1][k] + rods[i]), k = abs(j - rods[i])
    $$

- 细节

  - 两层循环，先遍历每根钢管，再遍历所有可能的差值；
  - 当两个集合的差值为 $j$ 时，要求最大和 $dp[i - 1][j]$ 要大于 $j$ 才有意义。
  - 最后返回 $dp[n][0] / 2$。

- c++代码

  二维dp版本：

  ```c++
  class Solution {
      public:
          int tallestBillboard(vector<int>& rods) {
              int n = rods.size();
              int sum = accumulate(rods.begin(), rods.end(), 0);
              vector<vector<int>> dp(n + 1, vector(sum + 1, 0));
  
              for (int i = 1; i <= n; i++) {
                  for (int j = 0; j <= sum; j++) {
                      if (dp[i - 1][j] < j) {
                          continue;
                      }
                      dp[i][j] = max(dp[i][j], dp[i - 1][j]);
  
                      int k = j + rods[i - 1];
                      dp[i][k] = max(dp[i][k], dp[i - 1][j] + rods[i - 1]);
                      k = abs(j - rods[i - 1]);
                      dp[i][k] = max(dp[i][k], dp[i - 1][j] + rods[i - 1]);
                  }
              }
              return dp[n][0] / 2;
          }
  };
  ```

  优化为一维版本：

  ```c++
  class Solution {
      public:
          int tallestBillboard(vector<int>& rods) {
              int n = rods.size();
              int sum = accumulate(rods.begin(), rods.end(), 0);
              vector<int> dp(sum + 1, 0);
  
              for (int i = 0; i < n; i++) {
                  vector<int> tmp = dp;
                  for (int j = 0; j <= sum; j++) {
                      if (tmp[j] < j) {
                          continue;
                      }
                      int k = j + rods[i];
                      dp[k] = max(dp[k], tmp[j] + rods[i]);
                      k = abs(j - rods[i]);
                      dp[k] = max(dp[k], tmp[j] + rods[i]);
                  }
              }
              return dp[0] / 2;
          }
  };
  ```

### 思路二

- 思路来源

  具体思路我就懒得重复了，直接看原作者写的吧：[来源](https://leetcode-cn.com/problems/tallest-billboard/solution/yi-quan-ji-ben-mei-shuo-ming-bai-de-zhe-pian-kan-l/)

- c++代码

  ```c++
  class Solution {
    	public:
      	int tallestBillboard(vector<int> &rods) {
              int n = rods.size();
              unordered_map<int, int> dp;
              dp[0] = 0;
              for (int rod : rods) {
                  auto tmp = dp;
                  for (auto it = tmp.begin(); it != tmp.end(); it++) {
                      int key = it->first;
                      int val = it->second;
                      dp[key - rod] = max(dp[key - rod], tmp[key]);
                      dp[key + rod] = max(dp[key + rod], tmp[key] + rod);
                  }
              }
              return dp[0];
          }
  };
  ```

  

  