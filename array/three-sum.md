# Three Sum

### Problem Statement

Given an array of $$n$$ integers and a target value $$x$$, the task is to find three distinct indices in the array whose corresponding values sum up to $$x$$. If a solution exists, return the indices; otherwise, indicate that no solution exists.

***

### Approach

A brute-force solution of checking every possible triplet of numbers would have a time complexity of $$O(n^3)$$, which is too slow. A more efficient approach reduces the problem to a "Two Sum" problem within a loop.

1. Sort the Array: First, we need to preserve the original indices of the elements. We can store the array values along with their original positions (e.g., in a vector of pairs) and then sort this structure based on the values. Sorting is crucial for the next step.
2. Fix One Element and Use Two Pointers: We iterate through the sorted array with a loop, fixing one element at a time. Let's say we fix the element at index `i`. Our goal is now to find two other elements in the rest of the array that sum up to `x - arr[i]`.
3. Two-Pointer Scan: For each fixed element `arr[i]`, we can efficiently find the other two elements using the two-pointer technique on the remainder of the array.
   * We initialize a `left` pointer to `i + 1` and a `right` pointer to the end of the array (`n - 1`).
   * We calculate the sum of the three elements: `arr[i] + arr[left] + arr[right]`.
     * If the sum equals the target $$x$$, we have found our triplet and can return their original indices.
     * If the sum is less than $$x$$, we need a larger sum, so we move the `left` pointer one step to the right.
     * If the sum is greater than $$x$$, we need a smaller sum, so we move the `right` pointer one step to the left.
   * We repeat this process until the `left` and `right` pointers cross.

If the outer loop finishes without finding a valid triplet, no solution exists.

***

### C++ Implementation

```cpp
#include<bits/stdc++.h>
using namespace std;

tuple<int, int, int> findThreeSum(const vector<int>& arr, int x) {
    int n = arr.size();
    if (n < 3) {
        return {0, 0, 0};
    }

    vector<pair<int, int>> indexed_arr(n);
    for (int i = 0; i < n; ++i) {
        indexed_arr[i] = {arr[i], i + 1}; // {value, 1-based_index}
    }
    
    sort(indexed_arr.begin(), indexed_arr.end());
    
    // Fix the first element and use two pointers for the other two
    for (int i = 0; i < n - 2; ++i) {
        int left = i + 1;
        int right = n - 1;
        
        while (left < right) {
            long long current_sum = (long long)indexed_arr[i].first + indexed_arr[left].first + indexed_arr[right].first;
            
            if (current_sum == x) {
                return {indexed_arr[i].second, indexed_arr[left].second, indexed_arr[right].second};
            } else if (current_sum < x) {
                left++;
            } else {
                right--;
            }
        }
    }
    
    return {-1, -1, -1}; // No solution found
}
```

***

### Complexity Analysis

* Time Complexity - $$O(N^2)$$
* Space Complexity - $$O(N)$$

***

### Problems

[CSES Task 1641](https://cses.fi/problemset/task/1641)
