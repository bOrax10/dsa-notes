# Two Sum

### Problem Statement

Given an array of $$n$$ integers and a target value $$x$$, the task is to find two distinct indices in the array whose corresponding values sum up to $$x$$. If a solution exists, return the indices; otherwise, indicate that no such pair exists.

***

### Approach

While a brute-force $$O(n^2)$$ approach of checking every pair is too slow for the given constraints, there are two common efficient methods.

**1. Hash Map (or `std::map`) Approach**

This is a very fast and intuitive method. We can iterate through the array once, and for each element `a`, we calculate the `complement` needed to reach the target sum (`complement = x - a`).

We then use a hash map to check if this `complement` exists among the elements we've already seen.

* If the complement is found in the map, we have our pair. We can immediately return the current element's index and the complement's index (which we stored in the map).
* If the complement is not found, we add the current element `a` and its index to the map and move to the next element.

This approach works because by the time we find a pair, we've processed both numbers, ensuring they are at distinct positions.

**2. Two-Pointer Approach**

This method requires sorting the array first but uses less space.

1. Store and Sort: First, we need to preserve the original indices. We can store the array values along with their original positions (e.g., in a vector of pairs) and then sort this structure based on the values.
2. Two Pointers: We initialize two pointers: a `left` pointer at the beginning of the sorted array and a `right` pointer at the end.
3. Iterate and Adjust: We calculate the sum of the values at the `left` and `right` pointers.
   * If the sum equals the target $$x$$, we have found our pair and can return their original indices.
   * If the sum is less than $$x$$, we need a larger sum. We move the `left` pointer one step to the right to include a larger value.
   * If the sum is greater than $$x$$, we need a smaller sum. We move the `right` pointer one step to the left to include a smaller value.
4. We repeat this process until the pointers cross. If they cross without finding a solution, no such pair exists.

***

### C++ Implementation

```cpp
#include<bits/stdc++.h>
using namespace std;

pair<int, int> findTwoSum_TwoPointers(const vector<int>& arr, int x) {
    int n = arr.size();
    if (n < 2) {
        return {-1, -1};
    }

    vector<pair<int, int>> indexed_arr(n);
    for (int i = 0; i < n; ++i) {
        indexed_arr[i] = {arr[i], i + 1}; // {value, 1-based_index}
    }
    
    sort(indexed_arr.begin(), indexed_arr.end());
    
    int left = 0;
    int right = n - 1;
    
    while (left < right) {
        long long current_sum = (long long)indexed_arr[left].first + indexed_arr[right].first;
        
        if (current_sum == x) {
            return {indexed_arr[left].second, indexed_arr[right].second};
        } else if (current_sum < x) {
            left++;
        } else {
            right--;
        }
    }
    
    return {-1, -1};
}
```

***

### Complexity Analysis

* Time Complexity - $$O(N)$$
* Space Complexity - $$O(1)$$

***

### Problems

[CSES Task 1640](https://cses.fi/problemset/task/1640)
