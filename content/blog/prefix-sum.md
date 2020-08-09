+++

author = "邱圆辉"

title = "前缀和模板"

date = "2020-08-08"

description = "一维前缀和以及二维前缀和的代码模板"

tags = [
    "leetcode", "solution"
]

images = ["http://47.94.16.255/images/images/2020/08/09/ocean-3605547_1280.jpg"]

+++

### 一维前缀和

```c++
vector<int> one_dimension_prefix(vector<int> nums) {
    int n = nums.size();
	vector<int> prefix(n + 1);
    for (int i = 1; i <= n; i++) {
        prefix[i] = prefix[i - 1] + nums[i - 1];
    }
    return prefix;
}
```

### 二维前缀和

```c++
vector<vector<int>> two_dimension_prefix(vector<vector<int>> nums) {
    int m = nums.size();
    int n = nums[0].size();
    vector<vector<int>> prefix(m + 1, vector(n + 1, 0));
    for (int i = 1; i <= m; i++) {
        for (int j = 1; j <= n; j++) {
            prefix[i][j] = nums[i][j] + prefix[i - 1][j]
                + prefix[i][j - 1] - prefix[i - 1][j - 1];
        }
    }
    return prefix;
}
```

