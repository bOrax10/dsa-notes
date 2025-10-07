# Array Division

### Problem Statement

Given an array of $$n$$ positive integers and an integer $$k$$, the task is to partition the array into exactly $$k$$ contiguous subarrays. The goal is to find a partition that minimizes the maximum sum among all subarrays.

***

### Approach

The problem asks to find the minimum possible value for the maximum sum. This structure is a perfect fit for binary search on the answer. Instead of trying to find the best division, we can ask a simpler question: "Is it possible to divide the array into $$k$$ subarrays such that no subarray sum exceeds a certain value `max_sum`?"

1. The Monotonic Property: This question has a monotonic property. If we can divide the array with a maximum sum of `S`, we can certainly do it with any maximum sum greater than `S`. This allows us to binary search for the smallest possible `S` that works.
   * `possible(S)`: `[False, False, ..., False, True, True, ..., True]`
   * Our goal is to find the first `True`.
2. The `check` Function: To perform the binary search, we need a function, let's call it `is_possible(max_sum)`, that returns `true` if the array can be divided into at most $$k$$ subarrays where each sum is no more than `max_sum`. We can implement this greedily:
   * Iterate through the array, adding elements to the current subarray.
   * Keep track of the `current_sum` and the number of `subarrays_needed` (initialized to 1).
   * If adding the next element would make `current_sum` exceed `max_sum`, we must end the current subarray and start a new one. We increment `subarrays_needed` and reset `current_sum` to the current element.
   * After iterating through the whole array, if `subarrays_needed` is less than or equal to $$k$$, then it's possible.
3. The Binary Search:
   * Search Space: The smallest possible answer (lower bound) is the largest single element in the array. The largest possible answer (upper bound) is the sum of all elements.
   * Logic: We perform a binary search within this range. For each `mid` value (a potential maximum sum), we call our `is_possible(mid)` function.
     * If it's possible, `mid` is a potential answer. We store it and try for an even smaller maximum sum by searching in the lower half (`high = mid - 1`).
     * If it's not possible, we need to allow for a larger maximum sum, so we search in the upper half (`low = mid + 1`).

The final answer will be the smallest `mid` for which `is_possible(mid)` returned `true`.

***

### C++ Implementation

```cpp
#include<bits/stdc++.h>
using namespace std;

bool is_possible(long long max_allowed_sum, int k, const vector<int>& arr) {
    int subarrays_needed = 1;
    long long current_sum = 0;

    for (int val : arr) {
        if (val > max_allowed_sum) {
            return false;
        }
        
        if (current_sum + val > max_allowed_sum) {
            subarrays_needed++;
            current_sum = val;
        } else {
            current_sum += val;
        }
    }
    
    return subarrays_needed <= k;
}

long long minimizeMaxSubarraySum(const vector<int>& arr, int k) {
    long long low = 0;
    long long high = 0;

    for (int val : arr) {
        low = max(low, (long long)val);
        high += val;
    }
    
    long long ans = high;

    while (low <= high) {
        long long mid = low + (high - low) / 2;
        if (is_possible(mid, k, arr)) {
            ans = mid;
            high = mid - 1;
        } else {
            low = mid + 1;
        }
    }
    
    return ans;
}
```

***

### Complexity Analysis

{% include "../.gitbook/includes/time-complexity-space-com....md" %}

***

### Problems

[CSES Task 1085](https://cses.fi/problemset/task/1085)
