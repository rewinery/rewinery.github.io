---
title: 'Thinking of Leetcode weekly contest 420'
date: 2024-10-20
permalink: /posts/2024/10/Thinking-of-LeetCode-Weekly-Contest-420/
tags:
  - Leetcode
  - Weekly Contest
---

This contest is so ridiculous...

I finshed problem A, B, C out of A, B, C, D. But the ranking is nearly 5000.

Problem C is very poor.

Problem set:

> You are given an integer array nums.
>
> Any positive divisor of a natural number x that is strictly less than x is called a proper divisor of x. For example, 2 is a proper divisor of 4, while 6 is not a proper divisor of 6.
>
> You are allowed to perform an operation any number of times on nums, where in each operation you select any one element from nums and divide it by its greatest proper divisor.
>
> Return the minimum number of operations required to make the array non-decreasing.
>
> If it is not possible to make the array non-decreasing using any number of operations, return -1.
>
> Constraints:
> - `1 <= nums.length <= 10^5`
> - `1 <= num <= 10^6`

Firstly read the problem: use sieve of Eratosthenes to preprocess in `O(num log log num)`, and then only need to judge whether `nums[i] > nums[i+1]` is valid. If valid, then set `nums[i]` as the minimum prime factor of `nums[i]`. If it can't be replaced, or it is still larger than 'nums[i+1]' after replacement, then `return -1`. Total time consumption is `O(n + num log log num)`.

The train of thought is fine. But after looking around the discussion board, I realized that Leetcode calculates the time and space cost of all the test points together. So my code was 'MLE'.

In the competition, I tried the violent method, and directly sought the minimum prime factor for each number from small to large, and it passed, which I can't accept...

> This is the MLE code

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

> And fianlly this code passed

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