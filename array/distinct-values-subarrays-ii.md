# Distinct Values Subarrays II

### Problem Statement

Given an array of $$n$$ integers and an integer $$k$$, the task is to count the total number of contiguous subarrays that contain at most $$k$$ distinct elements.

***

### Approach

The core idea is to maintain a "window," defined by a `left` and a `right` pointer, that is always valid (i.e., it contains at most $$k$$ distinct elements).

1. The Counting Insight: If the subarray from `left` to `right` is valid, then every subarray that ends at `right` and starts at an index between `left` and `right` is also valid. The number of such subarrays is `right - left + 1`. This allows us to count all valid subarrays ending at the current `right` position in one step.
2. The Algorithm:
   * We use a hash map (`std::map`) to keep track of the frequencies of elements within the current window `[left, right]`.
   * We start with an empty window (`left=0`) and expand it by moving the `right` pointer from `0` to `n-1`.
   * At each step `right`, we add the new element `arr[right]` to our window and update its frequency in the map.
   * Shrink the Window: If, after adding `arr[right]`, the number of distinct elements in our map exceeds `k`, the window is invalid. We must shrink it by moving the `left` pointer to the right. As we move `left`, we decrease the frequency of `arr[left]` in the map. If an element's frequency drops to zero, we remove it from the map entirely. We repeat this until the number of distinct elements is back to at most `k`.
   * Add to Count: Once the window `[left, right]` is valid again, we add its length (`right - left + 1`) to our total count.

By the end of the iteration, we will have counted all valid subarrays exactly once.

***

### C++ Implementation

```cpp
#include<bits/stdc++.h>
using namespace std;

long long countSubarraysWithAtMostKDistinct(const vector<int> &arr, int k)
{
    int n = arr.size();
    long long count = 0;

    map<int, int> window_counts;
    int left = 0;

    for (int right = 0; right < n; ++right)
    {
        window_counts[arr[right]]++;

        while (window_counts.size() > k)
        {
            window_counts[arr[left]]--;
            if (window_counts[arr[left]] == 0)
            {
                window_counts.erase(arr[left]);
            }
            left++;
        }

        count += (right - left + 1);
    }

    return count;
}
```

***

### Complexity Analysis

* Time Complexity - $$O(NlogN)$$
* Space Complexity - $$O(N)$$

***

### Problems

[CSES Task 2428](https://cses.fi/problemset/task/2428/)
