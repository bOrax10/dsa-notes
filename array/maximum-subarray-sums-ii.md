# Maximum Subarray Sums II

### Problem Statement

Given an array of $$n$$ integers (which can be positive, negative, or zero), the task is to find the maximum sum of any contiguous subarray whose length is within a specified range `[a, b]`.

***

### Approach

This problem can be solved efficiently by combining prefix sums with a sliding window deque to find the optimal subarray ending at each possible position.

1. Prefix Sums: First, we transform the original array into a prefix sum array. A prefix sum `P[i]` is the sum of all elements from the start of the array up to index `i-1`. The sum of any subarray from index `j` to `i-1` can then be calculated in constant time as `P[i] - P[j]`.
2.  The Core Insight: Our goal is to maximize `P[i] - P[j]`. For a fixed ending position `i`, this is equivalent to _minimizing_ `P[j]`. The length constraint `a <= (i-1) - j + 1 <= b` on the subarray `[j, i-1]` translates to a constraint on the index `j` of the prefix sum we subtract: `i-b <= j <= i-a`.

    So, for each ending position `i`, we need to find the minimum `P[j]` where `j` is in the range `[i-b, i-a]`.
3. Sliding Window Deque: Finding this minimum `P[j]` efficiently for every `i` is a classic "Sliding Window Minimum" problem. A deque (double-ended queue) is the perfect data structure for this.
   * The deque will store the indices `j` of the prefix sums.
   * It will always be maintained in increasing order of their corresponding prefix sum values (`P[deque.front()]` will be the minimum).
   * As we iterate with `i` from `a` to `n`:
     * Remove old indices: We first remove any indices from the front of the deque that are too old (i.e., less than `i-b`).
     * Maintain min property: Before adding the new index `i-a` to the back, we remove any indices from the back whose prefix sums are greater than or equal to `P[i-a]`. This ensures the deque stays sorted by prefix sum values.
     * Add new index: We add the index `i-a` to the back of the deque.
     * Calculate Max Sum: The best subarray ending at `i-1` is `P[i] - P[deque.front()]`. We update our global maximum sum with this value.

This process finds the maximum subarray sum in a single pass after the initial prefix sum calculation.

***

### C++ Implementation

```cpp
#include<bits/stdc++.h>
using namespace std;

long long maxSubarraySumWithLengthConstraint(const vector<int>& arr, int a, int b) {
    int n = arr.size();

    vector<long long> prefix_sums(n + 1, 0);
    for (int i = 0; i < n; ++i) {
        prefix_sums[i + 1] = prefix_sums[i] + arr[i];
    }
    
    long long max_sum = -3e18;
    
    // Use a deque to maintain a sliding window of minimum prefix sums
    // The deque stores indices of the prefix_sums array.
    deque<int> dq;
    
    for (int i = a; i <= n; ++i) {
        // The window for the start of the subarray (j) is [i-b, i-a]
        // First, add the new potential start P[i-a] to the window
        while (!dq.empty() && prefix_sums[dq.back()] >= prefix_sums[i - a]) {
            dq.pop_back();
        }
        dq.push_back(i - a);
        
        // Remove indices from the front that are now out of the window [i-b, i-a]
        if (dq.front() < i - b) {
            dq.pop_front();
        }
        
        // The minimum P[j] in the valid window is at the front of the deque.
        // Calculate the sum for the subarray ending at i-1.
        max_sum = max(max_sum, prefix_sums[i] - prefix_sums[dq.front()]);
    }
    
    return max_sum;
}
```

***

### Complexity Analysis

{% include "../.gitbook/includes/time-complexity-space-com....md" %}

***

### Problems

[CSES Task 1644](https://cses.fi/problemset/task/1644)
