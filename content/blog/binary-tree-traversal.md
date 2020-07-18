+++

author = "邱圆辉"

title = "二叉树的几种遍历方式总结"

date = "2020-07-18"

description = "二叉树的先序、中序、后序及层次遍历"

tags = [

   "binary tree"

]

images = ["http://47.94.16.255/images/images/2020/07/18/elf.jpg"]

+++



记录一下二叉树的几种遍历方式的代码模板，包括前序、中序、后序及层次遍历，均用c++实现。

### 先序遍历

- 递归版本

  ```c++
  void preorder_traversal(TreeNode *root) {
  	if (!root) {
  		return;
  	}
  	/* Visit the current node */
  	preorder_traversal(root->left);
  	preorder_traversal(root->right);
  }
  ```

- 迭代版本一

  ```c++
  void preorder_traversal(TreeNode *root) {
  	stack<TreeNode *> s;
      s.push(root);
      while (!s.empty()) {
          TreeNode *node = s.top();
          s.pop();
          if (node) {
  			/* Visit the current node */
              s.push(root->right);
              s.push(root->left);
          }
      }
  }
  ```

- 迭代版本二

  ```c++
  void preorder_traversal(TreeNode *root) {
      stack<TreeNode *> s;
      TreeNode *node = root;
      while (node || !s.empty()) {
          while (node) {
              s.push(node);
              /* Visit the current node */
              node = node->left;
          }
          node = s.top();
          s.pop();
          node = node->right;
      }
  }
  ```

- 迭代优化版本

  优化思路为只对右子节点进行压栈。

  ```c++
  void preorder_traversal(TreeNode *root) {
  	stack<TreeNode *> s;
      TreeNode *node = root;
      while (true) {
  		if (node) {
              /* Visit the current node */
              if (node->right) {
  				s.push(node->right);
              }
          } else if (s.empty()) {
  			return;
          } else {
              node = s.top();
              s.pop();
          }
      }
  }
  ```

### 中序遍历

- 递归版本

  ```c++
  void inorder_traversal(TreeNode *root) {
      if (!root) {
  		return;
      }
      inorder_traversal(root->left);
      /* Visit the current node */
      inorder_traversal(root->right);
  }
  ```

- 迭代版本

  ```c++
  void inorder_traversal(TreeNode *root) {
  	stack<TreeNode *> s;
      TreeNode *node = root;
      while (node || !s.empty()) {
  		while (node) {
              s.push(node);
              node = node->left;
          }
          node = s.top();
          s.pop();
          /* Visit the current node */
          node = node->right;
      }
  }
  ```

### 后序遍历

- 递归版本

  ```c++
  void postorder_traversal(TreeNode *root) {
     	postorder_traversal(root->left);
     	postorder_traversal(root->right);
      /* Visit the current node */
  }
  ```

- 迭代版本

  ```c++
  void postorder_traversal(TreeNode *root) {
      stack<TreeNode *> s;
      TreeNode *node = root;
      TreeNode *pre = nullptr;
      while (node || !s.empty()) {
          while (node) {
              s.push(node);
              node = node->left;
          }
          node = s.top();
          if (!(node->right) || node->right == pre) {
              /* Visit the current node */
              s.pop();
              pre = node;
              node = nullptr;
          } else {
              node = node->right;
          }
      }
  }
  ```

### 层次遍历

- 递归版本(DFS)

  ```c++
  struct TreeNode {
      int val;
      TreeNode *left;
      TreeNode *right;
      TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
  };
  
  vector<vector<int>> levelorder_traversal(TreeNode *root) {
      vector<vector<int>> res;
      dfs(res, root, 0);
      return res;
  }
  
  void dfs(vector<vector<int>> &res, TreeNode *root, int level) {
      if (!root) {
          return;
      }
      if (level >= res.size()) {
          res.push_back(vector<int>());
      }
      res[level].push_back(root->val);
  	dfs(root->left, level + 1);
      dfs(root->right, level + 1);
  }
  ```

  

- 迭代版本(BFS)

  ```c++
  void levelorder_traversal(TreeNode *root) {
      if (!root) {
          return;
      }
      queue<TreeNode *> q;
      TreeNode *cur = root;
      q.push(cur);
      while (!q.empty()) {
          cur = q.pop();
          /* Visit the current node */
          if (root->lchild) {
              q.push(root->lchild);
          }
          if (root->rchild) {
              q.push(root->rchild);
          }
      }
  }
  ```

### 先中后序遍历统一

最后附上一种将这三种遍历方式的形式统一起来的写法，原文见：[秒杀全场吧，后浪](https://leetcode-cn.com/problems/binary-tree-preorder-traversal/solution/miao-sha-quan-chang-ba-hou-lang-by-sonp/)

- 先序

  ```c++
  void preorder_traversal(TreeNode *root) {
      stack<TreeNode *> s;
      if (root) {
          s.push(root);
      }
      while (!s.empty()) {
          TreeNode *cur = s.top();
          s.pop();
          if (cur) {
              if (cur->right) {
                  s.push(cur->right);
              }
              if (cur->left) {
                  s.push(cur->left);
              }
              // re-push the current node, for follow-up processing
              s.push(cur);
              // insert an empty node before the current one
              // meaning that the current node has already been accessed
              s.push(nullptr);
          } else {
              /* Visit the current node */
              // pop the current node out thoroughly
              s.pop();
          }
      }
  }
  ```

- 中序

  ```c++
  void preorder_traversal(TreeNode *root) {
      stack<TreeNode *> s;
      if (root) {
          s.push(root);
      }
      while (!s.empty()) {
          TreeNode *cur = s.top();
          s.pop();
          if (cur) {
              if (cur->right) {
                  s.push(cur->right);
              }
              s.push(cur);
              s.push(nullptr);
              if (cur->left) {
                  s.push(cur->left);
              }
          } else {
              /* Visit the current node */
              // pop the current node out thoroughly
              s.pop();
          }
      }
  }
  ```

- 后序

  ```c++
  void preorder_traversal(TreeNode *root) {
      stack<TreeNode *> s;
      if (root) {
          s.push(root);
      }
      while (!s.empty()) {
          TreeNode *cur = s.top();
          s.pop();
          if (cur) {
              s.push(cur);
              s.push(nullptr);
              if (cur->right) {
                  s.push(cur->right);
              }
              if (cur->left) {
                  s.push(cur->left);
              }
          } else {
              /* Visit the current node */
              // pop the current node out thoroughly
              s.pop();
          }
      }
  }	
  ```
