# Number of Subarrays with Distinct Elements

### Problem Statement

Given an array of $$n$$ integers,  count the total number of contiguous subarrays that contain only distinct (unique) elements.

For the example array `[1, 2, 1, 3]`, the 8 subarrays with distinct elements are: `[1]`, `[2]`, `[1]`, `[3]`, `[1, 2]`, `[2, 1]`, `[1, 3]`, `[2, 1, 3].`

***

### Approach

This problem can be solved efficiently using a sliding window approach. We maintain a window, defined by a `left` pointer (`i`) and a `right` pointer (`j`), that always contains unique elements. The core idea is to expand this window to the right and, for each successful expansion, calculate how many new valid subarrays have been formed.

1. Data Structure: A frequency map (`freq`) is used to keep track of the count of each element within the current window `[i, j)`.
2. Pointers: A `left` pointer `i` and a `right` pointer `j` are initialized to `0`. A variable `ans` will accumulate the total count.
3. Algorithm Logic:
   * Window Expansion: The `while` loop tries to expand the window by moving the `j` pointer to the right.
     * If the element `arr[j]` is new to the window (its count in the map is `0`), we expand the window.
   * The Counting Insight: When we successfully add a new unique element at index `j-1`, any subarray ending at `j-1` that starts at or after `i` will also be unique. If the current valid window is `[i, j-1]`, its length is `j - i`. This means there are exactly `j - i` such new subarrays. For example, if the window is `[2, 1, 3]`, the new subarrays are `[3]`, `[1, 3]`, and `[2, 1, 3]`. The code `ans += (j - i);` correctly adds this number to the total count.
   * Window Contraction:
     * If the element `arr[j]` is a duplicate (its count in the map is not `0`), the window is no longer valid.
     * To fix this, the window must shrink from the left. The `left` pointer `i` is moved one step to the right, and the corresponding element `arr[i]` is removed from the map (by decrementing its count). This process continues until the duplicate is no longer present in the window.

By iterating through the array once with the `j` pointer, we can count all valid subarrays in a single pass.

***

### C++ Implementation

```cpp
#include<bits/stdc++.h>
using namespace std;

long long countDistinctSubarrays(vector<int>& arr) {
    int n = arr.size();
    map<int, int> freq;
    
    int i = 0, j = 0;
    ll ans = 0;

    while (j < n)
    {
        if (freq[arr[j]] == 0)
        {
            freq[arr[j]]++;
            j++;
            ans += (j - i);
        }
        else
        {
            freq[arr[i]]--;
            i++;
        }
    }
    
    return ans;
}
```

***

### Complexity Analysis

* Time Complexity - $$O(NlogN)$$ - Using unordered\_map is not advised as it can be $$O(N^2)$$ in worst case.
* Space Complexity - $$O(N)$$

***

### Problems

[CSES Task 3420](https://cses.fi/problemset/task/3420)
