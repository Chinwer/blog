+++

author = "邱圆辉"

title = "力扣总结：5. 最长回文子串"

date = "2020-07-13"

description = "第五题的两种思路"

tags = [
    "leetcode", "solution"
]

images = ["http://47.94.16.255/images/images/2020/07/13/star1.jpg"]

+++

### 题目要求

给定一个字符串 $s$ ，找到 $s$ 中最长的回文子串。你可以假设 $s$ 的最大长度为1000

### 思路

#### 一、动态规划法

- 核心思路

  - 如果一个字符串的头尾两个字符不一样，则它一定不是回文的；
  - 否则：
    - 如果里面的子串是回文，则整体为回文；
    - 否则不是回文。

- 状态转移方程

  记 $dp[i][j]$ 表示子串 $s[i..j]$ 是否为回文子串，则有：

  ```c
  dp[i][j] = (s[i] == s[j]) && dp[i + 1][j - 1]
  ```

- 边界条件

  当 $s[i] == s[j]$ 且 $j - i < 3$ 时直接有： $dp[i][j] = true$ 

- 原题解

  [动态规划法](https://leetcode-cn.com/problems/longest-palindromic-substring/solution/zhong-xin-kuo-san-dong-tai-gui-hua-by-liweiwei1419/)

#### 二、中心扩散法

- 核心思路

  遍历每一个索引，以其为中心向两边进行扩散。枚举中心位置的复杂度为 $O(n)$ ，从中心位置得到回文子串的复杂度为 $O(n)$，因此整体复杂度为 $O(N^2)$ 。

- 原题解

  [中心扩散法](https://leetcode-cn.com/problems/longest-palindromic-substring/solution/zhong-xin-kuo-san-dong-tai-gui-hua-by-liweiwei1419/)