+++

author = "邱圆辉"

title = "BFS的两个解题模板"

date = "2020-07-18"

description = "BFS两个模板"

tags = [ "leetcode", "BFS", "template" ]

images = ["http://47.94.16.255/images/images/2020/07/19/v2-73b8307b2db44c617f4e8515ce67dd39_1440w.jpg"]

+++

### BFS模板

BFS使用队列，将每个目前未搜索的结点依次入队，然后弹出队列首部元素进行遍历，一般有两个模板：

- 不需要确定当前遍历到了哪一层

  ```c++
  while (!queue.empty()) {
      cur = queue.front();
      queue.pop();
      for (node : adjacents_of(cur)) {
          if (node is valid && has not been accessed) {
              queue.push(node)
          }
      }
  }
  ```

  力扣中用到此模板的常见题目有：

  - [二叉树的层序遍历](https://leetcode-cn.com/problems/binary-tree-level-order-traversal/)
  - [二叉树的最大深度](https://leetcode-cn.com/problems/maximum-depth-of-binary-tree/)

- 需要确定当前遍历到了哪一层

  ```c++
  int level = 0;
  while (!queue.empty()) {
      int size = queue.size();
      for (int i = 0; i < size; i++) {
          cur = queue.front();
          queue.pop();
          for (node : adjacents_of(cur)) {
              if (node is valid && has not been accessed) {
                  queue.push(node);
              }
          }
      }
      level++;
  }
  ```

  力扣中用到此模板的常见题目有：

  - [对称二叉树](https://leetcode-cn.com/problems/symmetric-tree/)
  - [路径总和](https://leetcode-cn.com/submissions/detail/89155933/)