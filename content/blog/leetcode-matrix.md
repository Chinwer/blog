+++

author = "邱圆辉"

title = "几个矩阵有关的题"

date = "2020-08-09"

description = "总结一下力扣里面几个跟矩阵有关的题"

tags = [
    "leetcode", "solution"
]

images = ["http://47.94.16.255/images/images/2020/08/09/amazing-animal-beautiful-beautifullcb727d2872867c53.jpg"]

+++

### 第1139题

#### 题目链接

[1139. 最大的以1为边界的正方形](https://leetcode-cn.com/problems/largest-1-bordered-square/)

#### 题目描述

给你一个由若干 `0` 和 `1` 组成的二维网格 `grid`，请你找出边界全部由 `1` 组成的最大 **正方形** 子网格，并返回该子网格中的元素数量。如果不存在，则返回 `0`。

#### 示例

```
输入：grid = [[1,1,1],[1,0,1],[1,1,1]]
输出：9

输入：grid = [[1, 1, 0, 0]]
输出：1
```

#### 思路

DP数组的定义

```
dp[i][j][0]: i,j左边连续的1的个数（包括自身）
dp[i][j][1]: i,j上边连续的1的个数（包括自身）
```

思路来自于题解 [Java动态规划](https://leetcode-cn.com/problems/largest-1-bordered-square/solution/java-dong-tai-gui-hua-by-resolmi/)。

#### c++代码

```c++
class Solution {
    public:
        int largest1BorderedSquare(vector<vector<int>>& grid) {
            if (grid.empty()) {
                return 0;
            }
            int res = 0;
            int m = grid.size();
            int n = grid[0].size();
            vector<vector<vector<int>>> dp(m + 1, vector(n + 1, vector(2, 0)));
            for (int i = 1; i <= m; i++) {
                for (int j = 1; j <= n; j++) {
                    if (grid[i - 1][j - 1]) {
                        dp[i][j][0] = dp[i][j - 1][0] + 1;
                        dp[i][j][1] = dp[i - 1][j][1] + 1;
                        int len = min(dp[i][j][0], dp[i][j][1]);
                        while (len > res) {
                            if (dp[i - len + 1][j][0] >= len && dp[i][j - len + 1][1] >= len) {
                                break;
                            }
                            len--;
                        }
                        res = max(res, len);
                    }
                }
            }
            return res * res;
        }
};
```

#### 几个注意点

写一下做了几个与这个类似的题之后，我的一些个人总结。

- 这个题考察的是一个以1为边界的方阵，而与方阵边界内的元素无关。

- 需要定义一个三维数组来记录每个点向左和向上的最大连续1的个数。

- 数组的前两维的维度要比 `grid` 数组的两个维度大一，这样不用考虑边界情况。

- 每次找到当前点左边和上边最大连续1的个数后，要不断缩小这个方阵的边长，直到这个方阵的边界都为1。

  具体判断条件见 `while` 循环里面的 `if` 判断条件。

### 第1740题

#### 题目链接

[面试题17.23. 最大黑方阵](https://leetcode-cn.com/problems/max-black-square-lcci/)

#### 题目描述

给定一个方阵，其中每个单元(像素)非黑即白。设计一个算法，找出 4 条边皆为黑色像素的最大子方阵。

返回一个数组 `[r, c, size]` ，其中 `r, c` 分别代表子方阵左上角的行号和列号，`size` 是子方阵的边长。若有多个满足条件的子方阵，返回 `r` 最小的，若 `r` 相同，返回 `c` 最小的子方阵。若无满足条件的子方阵，返回空数组。

#### 示例

```
示例一
输入:
[
   [1,0,1],
   [0,0,1],
   [0,0,1]
]
输出: [1,0,2]
解释: 输入中 0 代表黑色，1 代表白色，标粗的元素即为满足条件的最大子方阵

示例二
输入:
[
   [0,1,1],
   [1,0,1],
   [1,1,0]
]
输出: [0,0,1]
```

#### 思路

DP数组的定义

```
dp[i][j][0]: i,j右边连续的1的个数（包括自身）
dp[i][j][1]: i,j下边连续的1的个数（包括自身）
```

思路来自于题解 [c++动态规划](https://leetcode-cn.com/problems/max-black-square-lcci/solution/c-dong-tai-gui-hua-by-tmoonli/)。

#### c++代码

```c++
class Solution {
    public:
        vector<int> findSquare(vector<vector<int>>& matrix) {
            int n = matrix.size();
            if (!n) {
                return {};
            }
            int r = -1, c = -1, max_len = 0;
            // dp[i][j][0]: right
            // dp[i][j][1]: down
            vector<vector<vector<int>>> dp(n + 1, vector(n + 1, vector(2, 0)));
            for (int i = n - 1; i >= 0; i--) {
                for (int j = n - 1; j >= 0; j--) {
                    if (matrix[i][j]) {
                        continue;
                    }
                    dp[i][j][0] = dp[i][j + 1][0] + 1;
                    dp[i][j][1] = dp[i + 1][j][1] + 1;
                    int len = min(dp[i][j][0], dp[i][j][1]);
                    while (len >= max_len) {
                        if (dp[i + len - 1][j][0] >= len &&
                            dp[i][j + len - 1][1] >= len) {
                            r = i;
                            c = j;
                            max_len = len;
                            break;
                        }
                        len--;
                    }
                }
            }
            if (r == -1) {
                return {};
            }
            return { r, c, max_len };
        }
};
```

#### 几个注意点

- 这个题跟上面那个十分类似，不同点在于需要从下往上，从右往左遍历原矩阵。而上面那个题则是从上往下，从左往右遍历。
- 同样 `dp` 数组的维度要比原矩阵的大1，便于边界处理。
- 注意如果矩阵中没有黑像素点，即0，要返回一个空数组。

### 第1277题

#### 题目链接

[1277. 统计全为1的正方形子矩阵](https://leetcode-cn.com/problems/count-square-submatrices-with-all-ones/)

#### 题目描述

给你一个 `m * n` 的矩阵，矩阵中的元素不是 `0` 就是 `1`，请你统计并返回其中完全由 `1` 组成的 **正方形** 子矩阵的个数。

#### 示例

```
输入：matrix =
[
  [0,1,1,1],
  [1,1,1,1],
  [0,1,1,1]
]
输出：15
解释： 
边长为 1 的正方形有 10 个。
边长为 2 的正方形有 4 个。
边长为 3 的正方形有 1 个。
正方形的总数 = 10 + 4 + 1 = 15.
```

#### 思路

DP数组的定义：

```
dp[i][j]: 以matrix[i][j]为右下角的正方形子矩阵的最大边长
```

状态转移方程：

```
dp[i][j] = min(dp[i][j - 1], dp[i - 1][j], dp[i - 1][j - 1])
```

思路来自于官方题解：[统计全为1的正方形子矩阵](https://leetcode-cn.com/problems/count-square-submatrices-with-all-ones/solution/tong-ji-quan-wei-1-de-zheng-fang-xing-zi-ju-zhen-2/)。

#### c++代码

```c++
class Solution {
    public:
        int countSquares(vector<vector<int>>& matrix) {
            int res = 0;
            int m = matrix.size();
            int n = matrix[0].size();
            // dp[i][j]: maximum length of square matrix of all ones
            //   with matrix[i][j] as its lower right corner
            vector<vector<int>> dp(m, vector(n, 0));
            // base case: the first row
            for (int i = 0; i < n; i++) {
                if (matrix[0][i]) {
                    res++;
                    dp[0][i] = 1;
                }
            }
            // base case: the first column
            for (int i = 1; i < m; i++) {
                if (matrix[i][0]) {
                    res++;
                    dp[i][0] = 1;
                }
            }
            for (int i = 1; i < m; i++) {
                for (int j = 1; j < n; j++) {
                    if (!matrix[i][j]) {
                        continue;
                    }
                    int len = _min(
                        dp[i][j - 1],
                        dp[i - 1][j],
                        dp[i - 1][j - 1]
                    );
                    dp[i][j] = len + 1;
                    res += dp[i][j];
                }
            }
            return res;
        }

        int _min(int a, int b, int c) {
            return min(a, min(b, c));
        }
};
```

#### 几个注意点

- 这个题跟上面两个不同，考察的是全为1的子矩阵。
- 递推时不再需要同时考虑两个方向上最大连续1的个数，只需要考虑两者的最小值，也就是子矩阵的边长。
- 这里 `dp` 的维度与原矩阵相同就可以了，这样边界处理同样很方便。

### 第1504题

#### 题目链接

[1504. 统计全1子矩形](https://leetcode-cn.com/problems/count-submatrices-with-all-ones/)

#### 题目描述

给你一个只包含 0 和 1 的 `rows * columns` 矩阵 `mat` ，请你返回有多少个 **子矩形** 的元素全部都是 1 。

#### 示例

```
输入：mat = [[1,0,1],
            [1,1,0],
            [1,1,0]]
输出：13
解释：
有 6 个 1x1 的矩形。
有 2 个 1x2 的矩形。
有 3 个 2x1 的矩形。
有 1 个 2x2 的矩形。
有 1 个 3x1 的矩形。
矩形数目总共 = 6 + 2 + 3 + 1 + 1 = 13 。
```

#### 思路

这题乍一看跟上面那个一模一样，但其实这里要数的是全1的矩形个数，而不像上一题一样非得是正方形。因此会比上面那个稍微麻烦一丢丢，但总体思路还是很相似的。

DP数组定义：

```
dp[i][j]: (i, j)左边最大连续1的个数（包括自身）
```

我们进一步统计以 `(i, j)` 作为全1矩阵右下角的所有矩阵的个数。

统计方法是枚举矩阵的高，其可能取值为 `[1, i + 1]`。

具体解释参见官方题解：[统计全1子矩形](https://leetcode-cn.com/problems/count-submatrices-with-all-ones/solution/tong-ji-quan-1-zi-ju-xing-by-leetcode-solution/)

#### c++代码

```c++
class Solution {
    public:
        int numSubmat(vector<vector<int>>& mat) {
            int m = mat.size();
            int n = mat[0].size();
            vector<vector<int>> dp(m, vector(n, 0));
            for (int i = 0; i < m; i++) {
                for (int j = 0; j < n; j++) {
                    if (!mat[i][j]) {
                        continue;
                    }
                    if (!j) {
                        dp[i][j] = 1;
                    } else {
                        dp[i][j] = dp[i][j - 1] + 1;
                    }
                }
            }
            int res = 0;
            for (int i = 0; i < m; i++) {
                for (int j = 0; j < n; j++) {
                    int min_ = 150;
                    for (int k = 0; k <= i; k++) {
                        min_ = min(min_, dp[i - k][j]);
                        res += min_;
                    }
                }
            }
            return res;
        }
};
```

#### 几个注意点

- 这个题跟上一题一样，关心矩阵内所有的元素，而不仅仅是边界元素。

- 同样，`dp` 数组的维度跟原矩阵保持一样就可以了。

- 注意统计以 `(i, j)` 作为矩形右下角的所有全1矩形个数的方法。（代码的20到28行）

- 我第一次AC的代码中同时考虑了 `(i, j)` 左边和上边最大连续1的个数，看了题解后才发现其实只需记录一个方向上的就够了，所以如果修改 `dp` 的定义为：

  ```
  dp[i][j]: (i, j)上边最大连续1的个数
  ```

  也是可以的。这样的话，每次统计矩形个数时就是枚举矩形的宽而不是高了，思路还是没变。

### 总结

总之这几个与矩阵有关的题难度都不大，并且思路都有一个共同点：

`dp[i][j]` 表示的都是 **(i, j) 左/上/右/下 边最大连续 1/0 的个数** ，区别主要有以下几点：

- 遍历矩阵的顺序：从上往下还是从下往上，从左往右还是从右往左

- `dp` 数组的维度：是否比原矩阵的维度大1，关键在于怎么定义 `dp` 数组的大小更有利于边界情况的处理，这里可以简单记忆如下：

  - 如果只考虑边界元素，就定义成比原矩阵维度大1。
  - 如果还要考虑中间元素，就定义成与原矩阵一样大。

- 如果只考虑边界元素，在得到 `dp[i][j]` 的值后，需要不断缩小可能的矩形边长直到满足题目要求；

  如果还要考虑中间元素，在得到 `dp[i][j]` 后，便可直接根据它来计算结果了。