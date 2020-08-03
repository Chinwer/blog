+++

author = "邱圆辉"

title = "力扣总结：1372. 二叉树的最长交错路径"

date = "2020-08-02"

description = "第1372题的思路总结"

tags = [
    "leetcode", "solution"
]

images = ["http://47.94.16.255/images/images/2020/08/03/zen-peace-1728x1080.jpg"]

+++

### 题目链接

[二叉树的最长交错路径](https://leetcode-cn.com/problems/longest-zigzag-path-in-a-binary-tree/)

### 题目描述

给你一棵以 root 为根的二叉树，二叉树中的交错路径定义如下：

- 选择二叉树中 **任意** 节点和一个方向（左或者右）。
- 如果前进方向为右，那么移动到当前节点的的右子节点，否则移动到它的左子节点。
  改变前进方向：左变右或者右变左。
- 重复第二步和第三步，直到你在树中无法继续移动。
- 交错路径的长度定义为：**访问过的节点数目** - 1（单个节点的路径长度为 0 ）。

请你返回给定树中最长 **交错路径** 的长度。

### 思路

以下两种思路均来源于 [官方题解](https://leetcode-cn.com/problems/longest-zigzag-path-in-a-binary-tree/solution/er-cha-shu-zhong-de-zui-chang-jiao-cuo-lu-jing-b-2/)。

#### DFS

- 核心思路

  采用深度优先搜索的策略，从上到下遍历整棵树，遍历的同时维护变量 $is\\_left$ 和 $len$ ，其含义如下：

  - $is\\_left$ ：当前节点是否应该往左走。
  - $len$ ：当目前为止，已经满足要求的交错路径的长度。

  对于当前节点，如果应该往左走，也就是 $is\\_left$ 为 $true$，并且它有左孩子，就往左走，并且 $len$ 加一，否则如果有右孩子的话就往右走，同时将 $len$ 重置为1。$is\\_left$ 为 $false$ 时对称处理。

- c++代码

  ```c++
  class Solution {
      private:
          int res = 0;
  
      public:
          int longestZigZag(TreeNode* root) {
              if (!root) {
                  return 0;
              }
              dfs(root, true, 0);
              dfs(root, false, 0);
              return res;
          }
  
          void dfs(TreeNode *root, bool is_left, int len) {
              res = max(res, len);
              if (is_left) {
                  if (root->left) {
                      dfs(root->left, false, len + 1);
                  }
                  if (root->right) {
                      dfs(root->right, true, 1);
                  }
              } else {
                  if (root->right) {
                      dfs(root->right, true, len + 1);
                  }
                  if (root->left) {
                      dfs(root->left, false, 1);
                  }
              }
          }
  };
  ```

- 几个注意的地方

  - 为什么初始调用 `dfs` 时传入的 `len` 是 0 不是 1 ？

    因为初始调用时 `root` 没有父节点，当前走过的路径长度为 0。

  - 为什么重置 `len` 时是重置为 1 而不是 0 ？

    因为此时已经从当前节点走到了它的孩子节点，已经走过了一条长度为 1 的边。

#### BFS动态规划

- 核心思路

  记 ：

  - $f(u)$ 为从根到节点 $u$ 的路径上以 $u$ 结尾并且 $u$ 是它父节点的左孩子的最长交错路径的长度。
  - $g(u)$ 为从根到节点 $u$ 的路径上以 $u$ 结尾并且 $u$ 是它父节点的右孩子的最长交错路径的长度。

  记 $u$ 的父节点为 $father(u)$ ，状态转移方程为：
  $$
  f[u] = g[father(u)] + 1 \\\\
  g[u] = f[father(u)] + 1
  $$
   遍历时用二元组 `(child, parent)` 作为状态，其中 `child` 表示 `f` 和 `g` 中待计算的节点，`parent` 为它的父节点。

- c++代码

  ```c++
  class Solution {
      private:
          // f(u) is the length of longest zigzag path from root
          // to u with u being the left child of its parent node.
          // g(u) is the length of longest zigzag path from root
          // to u with u being the right child of its parent node.
          unordered_map<TreeNode *, int> f, g;
  
      public:
          int longestZigZag(TreeNode* root) {
              bfs_dp(root);
              int res = 0;
              for (const auto &u : f) {
                  res = max(res, max(u.second, g[u.first]));
              }
              return res;
          }
  
          void bfs_dp(TreeNode *root) {
              queue<pair<TreeNode *, TreeNode *>> q;
              f[root] = g[root] = 0;
              q.push({ root, nullptr });
              while (!q.empty()) {
                  auto fr = q.front();
                  q.pop();
                  auto child = fr.first;
                  auto parent = fr.second;
                  f[child] = g[child] = 0;
                  // state transfer equation:
                  // f[child] = g[father(child)] + 1; (u is the left child)
                  // g[child] = f[father(child)] + 1; (u is the right child)
                  if (parent) {
                      if (parent->left == child) {
                          f[child] = g[parent] + 1;
                      }
                      if (parent->right == child) {
                          g[child] = f[parent] + 1;
                      }
                  }
                  if (child->left) {
                      q.push({ child->left, child });
                  }
                  if (child->right) {
                      q.push({ child->right, child });
                  }
              }
          }
  };
  ```

下面的思路来自于[题解区](https://leetcode-cn.com/problems/longest-zigzag-path-in-a-binary-tree/solution/javazi-di-xiang-shang-shu-xing-dp-by-jackie-tien/)

#### 自底向上DFS树形DP

- 核心思路

  用 `res[0]` 表示当前节点下一步往左走的最大长度，`res[1]` 表示当前节点下一步往右走的最大程度。

- c++代码

  ```c++
  class Solution {
      private:
          int res = 0;
  
      public:
          int longestZigZag(TreeNode* root) {
              dfs_dp(root);
              return res;
          }
  
          vector<int> dfs_dp(TreeNode *root) {
              if (!root) {
                  return { -1, -1 };
              }
              vector<int> ret(2);
              vector<int> l = dfs_dp(root->left);
              vector<int> r = dfs_dp(root->right);
              ret[0] = l[1] + 1;
              ret[1] = r[0] + 1;
              res = max(res, max(ret[0], ret[1]));
              return ret;
          }
  };
  ```

  