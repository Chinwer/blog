+++

author = "邱圆辉"

title = "力扣总结：105. 从前序与中序遍历序列构造二叉树"

date = "2020-07-19"

description = "第105题的几种思路"

tags = ["leetcode", "solution"]

images = ["http://47.94.16.255/images/images/2020/07/19/smallccd6fce7874f4f9351ddf67c71ed45361594566515.jpg"]

+++

### 题目链接

[105. 从前序遍历与中序遍历序列构造二叉树](https://leetcode-cn.com/problems/construct-binary-tree-from-preorder-and-inorder-traversal/)

### 题目要求

根据一棵树的前序遍历与中序遍历构造二叉树，你可以假设树中没有重复元素。

### 思路

- 递归版本

  - 核心思路

    - $preorder$ 前序遍历序列的第一项肯定是根节点：[根 | 左 | 右]；

    - 根据根节点在 $inorder$ 中序遍历序列中的位置，将其分割为 [左 | 根 | 右] 三个部分；
    - 通过 $inorder$ 中确定的左右子树的结点个数，在 $preorder$ 中确定左右子树的序列；
    - 对左右子树，递归重复上述操作。

  - c++代码

    ```c++
    struct TreeNode {
        int val;
        TreeNode *left;
        TreeNode *right;
        TreeNode(int x) : val(x), left(NULL), right(NULL) {}
    };
    
    class Solution {
        public:
            TreeNode* buildTree(vector<int>& preorder, vector<int>& inorder) {
                map<int, int> pos;
                int presize = preorder.size(), insize = inorder.size();
                for (int i = 0; i < insize; i++) {
                    pos[inorder[i]] = i;
                }
                return build(preorder, 0, presize - 1, inorder, 0, insize - 1, pos);
            }
    
            TreeNode *build(vector<int> &pre, int prestart, int preend,
                vector<int> &in, int instart, int inend, map<int, int> &pos) {
                if (instart > inend || prestart > preend) {
                    return nullptr;
                }
                int root_val = pre[prestart];
                int root_idx = pos[root_val];
                int nums_left = root_idx - instart;
                TreeNode *root = new TreeNode(root_val);
                root->left = build(pre, prestart + 1, prestart + nums_left,
                    in, instart, root_idx - 1, pos);
                root->right = build(pre, prestart + nums_left + 1, preend,
                    in, root_idx + 1, inend, pos);
                return root;
            }
    };
    ```

    

  - 几个注意点

    - 注意递归调用的边界条件；
    - 注意递归时 $inorder, preorder$ 中左右子树的边界；

    - 为了避免每次都必须遍历整个 $inorder$ 序列来寻找根节点，采用一个从数组元素到索引的映射表 $pos$ 来降低时间复杂度。

  - 复杂度分析

    - 时间复杂度：$O(n)$ ，其中 $n$ 是树中节点个数。
    - 空间复杂度：$O(n)$ ，除了返回值所需要的 $O(n)$ 空间以外，还需要 $O(n)$ 来存储哈希表，及 $O(h)$ 的递归栈空间，其中 $h$ 是树的高度。因此整体空间复杂度为 $O(n)$。

- 递归优化版本

  此算法来自国外版讨论区：[Simple O(n) without map](https://leetcode.com/problems/construct-binary-tree-from-preorder-and-inorder-traversal/discuss/34543/Simple-O(n)-without-map)

  - 核心思路

    - 变量 $pre$ 代表当前要构造的树的根节点；
    - 变量 $in$ 代表在 $inorder$ 中，当前要构造的树的第一个节点；
    - 变量 $stop$ 代表在 $inorder$ 中，当前要构造的树的最后一个节点（不包括 $stop$ ）；
    - 每次递归调用都可以通过 $stop$ 变量得知当前树的构造终点，同时告诉左子树它的终点即为当前构造的树的根节点，告诉右子树它的终点为当前构造的树的终点。

  - c++代码

    ```c++
    class Solution {
    	private:
        	int in = 0;
        	int pre = 0;
        
        public:
        	TreeNode buildTree(vector<int> &preorder, vector<int> &inorder) {
                return build(preorder, inorder, INT_MIN);
            }
        
        	TreeNode build(vector<int> &preorder, vector<int> &inorder, int stop) {
                if (pre == preorder.size()) {
                    return nullptr;
                }
                if (inorder[in] == stop) {
    				in++;
                    return nullptr;
                }
                TreeNode root = new TreeNode(preorder[pre++]);
                root->left = build(preorder, inorder, root->val);
                root->right = build(preorder, inorder,  stop);
                return root;
            }
    };
    ```

  - 复杂度分析

    - 时间复杂度：$O(n)$。
    - 空间复杂度：$O(n)$，相比上述算法少了额外的用来存储哈希表的 $O(n)$ 的空间。

- 迭代版本

  此算法来自官方题解：[从前序与中序遍历序列构造二叉树](https://leetcode-cn.com/problems/construct-binary-tree-from-preorder-and-inorder-traversal/solution/cong-qian-xu-yu-zhong-xu-bian-li-xu-lie-gou-zao-9/)

  - 核心思路

    - 用一个栈和一个指向 $inorder$ 的指针 $in$ 辅助二叉树的构造。其中栈中存放的是所有尚未考虑过右儿子的节点，栈顶就是当前遍历到的 $preorder$ 中的节点。
    - 每次判断当前遍历到的 $preorder$ 中的节点是否与 $inorder[in]$ 相等：
      - 若相等，则说明当前**已经沿着某个节点走到了它左子树的最左端了**，此时不断弹出栈顶节点并右移 $in$ ，最后将当前节点作为最后一个弹出的节点的右孩子；
      - 否则，将当前节点作为栈顶结点的左孩子；
      - 无论哪种情况，最后都将当前节点入栈。

  - c++代码

    ```c++
    class Solution {
        public:
        	TreeNode *buildTree(vector<int> &preorder, vector<int> &inorder) {
                if (preorder.empty()) {
                    return nullptr;
                }
                
                stack<TreeNode *> s;
                TreeNode *root = new TreeNode(preorder[0]);
                s.push(root);
                int in = 0, size = preorder.size();
                for (int i = 1; i < size; i++) {
                    int preorder_val = preorder[i];
                    TreeNode *node = s.top();
                    if (node->val != inorder[in]) {
                        node->left = new TreeNode(preorder_val);
                        s.push(node->left);
                    } else {
                        while (!s.empty() && s.top()->val == inorder[in]) {
                            node = s.top();
                            s.pop();
                            in++;
                        }
                        node->right = new TreeNode(preorder_val);
                        s.push(node->right);
                    }
                }
                return root;
            }
    }
    ```

  - 复杂度分析

    - 时间复杂度：$O(n)$
    - 空间复杂度：$O(n)$ 