+++

author = "邱圆辉"

title = "力扣总结：1248. 统计优美子数组"

date = "2020-07-13"

description = "力扣第1248题的两种思路"

tags = [
    "leetcode", "solution", "algorithm", "two-pointers"
]

images = ["http://47.94.16.255/images/content/images/users/Vk3/bkg_1594585280.jpg"]

+++

### 方法一：数学

- 思路和算法

  - 用一个 $odd$ 数组记录第 $i$ 个奇数的下标。因此子数组 $[odd[i], odd[i+k-1]]$ 恰好包含 $k$ 个奇数 ；

  - 同时，子数组 $(odd[i-1], odd[i])$ 及 $(odd[i+k-1], odd[i+k])$ 均为偶数。因此所有起始点为第 $i$ 个奇数的“优美”数组的个数即为：
    $$
    (odd[i] - odd[i-1]) * (odd[i+k] - odd[i+k-1])
    $$

- c++代码

  ```c++
  class Solution {
      public:
          int numberOfSubarrays(vector<int>& nums, int k) {
              vector<int> odds = { -1 };
              int size = nums.size();
              for (int i = 0; i < size; i++) {
                  if (nums[i] & 1) {
                      // nums[i] is an odd number
                      odds.push_back(i);
                  }
              }
              odds.push_back(size);
  
              int res = 0;
              size = odds.size() - 1;
              for (int i = 1; i + k <= size; i++) {
                  res += (odds[i] - odds[i - 1]) * (odds[i + k] - odds[i + k - 1]);
              }
              return res;
          }
  };
  ```

### 方法二：前缀和 + 差分

- 思路和算法

  - 考虑以 $i$ 结尾的 $\lceil 优美子数组 \rfloor$ 个数，我们需要统计对应的起始点 $j$ 的个数，其中 $0 \leq j \leq i $ 且子数组 $[j..i]$ 中恰好有 $k$ 个奇数。

  - 定义 $pre[i]$ 为子数组 $[0..i]$ 中奇数的个数，则递推公式为：
    $$
    pre[i] = pre[i-1] + (nums\&1)
    $$
    因此在考虑以 $i$ 结尾的 $\lceil 优美子数组 \rfloor$ 的个数时，只需考虑 $pre[j] = pre[i] - k$ 的 $j$ 的个数即可。

  - 建立频次数组 $cnt$ 记录 $pre[i]$ 出现的次数，从左往右扫描并更新，此时以 $i$ 结尾的个数即为 $cnt[pre[i] - k]$ ，最后答案为遍历所有下标的答案之和。

  - c++代码

    ```c++
    class Solution {
        public:
            int numberOfSubarrays(vector<int>& nums, int k) {
                vector<int> cnt;
                cnt.push_back(1);
                int res = 0, odd = 0;
                for (int num : nums) {
                    odd += num & 1;
                    res += odd >= k ? cnt[odd - k] : 0;
                    cnt[odd]++;
                }
                return res;
            }
    };
    
    ```

  ### 原题解

  [优美子数组个数](https://leetcode-cn.com/problems/count-number-of-nice-subarrays/solution/tong-ji-you-mei-zi-shu-zu-by-leetcode-solution/)