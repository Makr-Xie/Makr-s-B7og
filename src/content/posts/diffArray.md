---
  title: 差分数组
  pubDate: 2024-11-17
  categories: ["笔记"]
  description: ''
---

这周做题的时候，发现了个处理很多区间增加或减少的思路-----前缀和里的**差分数组**。


参考 Leetcode 390：

>给定一个长度为 `n` 的数组 `arr`，还有 `m` 个区间 `[li, ri]`。我们需要让每个区间内的元素 `a[i]` 都加 1。

传统做法是针对每个区间用一个 `for` 循环，逐个更新区间内的元素。这种方法的时间复杂度是 **O(mn)**，在数据量大的情况下效率稀烂。

但有一种更快的做法，可以降低到 **O(m + n)**，是这么操作的：

1. 新建一个长度为 `n+1` 的数组 `diff`，初始值为 0。
2. 对于每个区间 `[li, ri]`：
    - 将 `diff[li]` 加 1。
    - 将 `diff[ri + 1]` 减 1。
3. 处理完所有区间后， `diff` 数组的前缀和便是每个位置的最终增加量。

代码是这样的：

```cpp
vector<int> getModifiedArray(int length, vector<vector<int>>& updates) {
    vector<int> diff(length + 1, 0);
	
    for (int i = 0; i < updates.size(); i++) {
        diff[updates[i][0]] += updates[i][2];
        diff[updates[i][1] + 1] -= updates[i][2];
    }
  
    for (int i = 1; i < length; i++) {
        diff[i] += diff[i - 1];
    }

    return vector<int>(diff.begin(), diff.begin() + length);
}
```

屌吧？反正屌到我这个渣渣了。