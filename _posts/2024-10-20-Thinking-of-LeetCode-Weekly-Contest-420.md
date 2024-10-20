---
title: 'leetcode 第 420 场周赛有感'
date: 2024-10-20
permalink: /posts/2024/10/Thinking-of-LeetCode-Weekly-Contest-420/
tags:
  - cool posts
  - Leetcode
  - Weekly Contest
---

这场比赛槽点太多，但是也学到了一点，那就是大数组预处理要直接全局处理

A 了 T1、T2、T3，但是排名直逼 6000......

这把掉大分，太坐牢了，想破头都没想到 T3 这个线性筛爆内存是要直接全局处理......

最后是用暴力做法过的，想不通为什么暴力做法能过......卡我也就算了，暴力能过是我不能接受的

题干：

> 给你一个整数数组 nums 。
>
> 一个正整数 x 的任何一个 严格小于 x 的 正 因子都被称为 x 的 真因数 。比方说 2 是 4 的 真因数，但 6 不是 6 的 真因数。
>
> 你可以对 nums 的任何数字做任意次 操作 ，一次 操作 中，你可以选择 nums 中的任意一个元素，将它除以它的 最大真因数 。
>
> 你的目标是将数组变为 非递减 的，请你返回达成这一目标需要的 最少操作 次数。
>
> 如果 无法 将数组变成非递减的，请你返回 -1 。
> 数据范围：
> - $1 \leq \text{nums.length} \leq 10^5$
> - $1 \leq \text{num} \leq 10^6$

读题：一眼埃拉托斯特尼筛法，用 $O(\text{num}\log\log \text{num})$ 的时间预处理，之后没一轮只需判断 `nums[i] > nums[i+1]` 是否成立，若成立，则将 `nums[i]` 替换为 `nums[i]` 的最小质因数即可。若无法替换或替换之后依旧比 `nums[i+1]` 大，那么 `return -1`。总时间开销为 $O(\text{n} + \text{num}\log\log \text{num})$。

思路没问题。但是事后逛了讨论区才知道：力蔻是把所有测试点一起计算时间和空间开销的。于是我的代码就这样被 `MLE` 了。

比赛中试了试暴力做法，直接对每个数从小到大求最小质因数，竟然过了......


> 这是被爆内存的代码

```c++
class Solution {
public:
    int minOperations(vector<int>& nums) {
        int n = nums.size();
        int a = 0;
        for (int i = 0; i < n; ++i) {
            a = max(a, nums[i]);
        }
        int *min_prime = new int[a+1];

        for (int i = 1; i <= a; ++i) {
            min_prime[i] = i;
        }
        int limit = (int)sqrt(a);
        for (int i = 2; i <= limit; ++i) {
            if (min_prime[i] == i) {
                for (int j = i * i; j <= a; j += i) {
                    min_prime[j] = min(min_prime[j], i);
                }
            }
        }

        int ans = 0;
        for (int i = n - 2; i >= 0; --i) {
            if (nums[i] > nums[i+1]) {
                ans ++;
                nums[i] = min_prime[nums[i]];
            }
            if (nums[i] > nums[i+1]) {
                return -1;
            }
        }
        return ans;
    }
};
```

> 最后改成如下代码才过。

```c++
int min_prime[1000001];
int inited = false;

void init() {
    if (inited) return ;
    inited = true;
    for (int i = 1; i <= 1000000; ++i) {
        min_prime[i] = i;
    }
    int limit = (int)sqrt(1000000);
    for (int i = 2; i <= limit; ++i) {
        if (min_prime[i] == i) {
            for (int j = i * i; j <= 1000000; j += i) {
                min_prime[j] = min(min_prime[j], i);
            }
        }
    }
}

class Solution {
public:
    int minOperations(vector<int>& nums) {
        init();
        int n = nums.size();
        int a = 0;
        for (int i = 0; i < n; ++i) {
            a = max(a, nums[i]);
        }

        int ans = 0;
        for (int i = n - 2; i >= 0; --i) {
            if (nums[i] > nums[i+1]) {
                ans ++;
                nums[i] = min_prime[nums[i]];
            }
            if (nums[i] > nums[i+1]) {
                return -1;
            }
        }
        return ans;
    }
};
```