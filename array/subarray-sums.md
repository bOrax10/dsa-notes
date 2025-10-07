# Subarray Sums

### Problem Statement

Given an array of $$n$$ **positive** integers and a target value $$x$$, the task is to count the total number of contiguous subarrays whose elements sum up to exactly $$x$$.

For example, the array <kbd>\[2,4,1,2,7]</kbd>  there are 3 subarrays with the sum = 7: `[2, 4, 1]`, `[4, 1, 2]`, and `[7].`

***

### Approach

Since the array contains only positive integers, we can use an efficient two-pointer method. A more general approach that also works for arrays with negative numbers involves using a prefix sum map.

**1. Two-Pointer (Sliding Window) Approach - Works only with positive numbers**

This is the optimal approach for this specific problem because all integers are positive. We maintain a "window" over the array with a `left` and a `right` pointer. The sum of the elements inside this window is our `current_sum`.

* Expand the Window: We start with an empty window (`left=0`, `right=0`). We move the `right` pointer one step at a time, adding the new element to `current_sum`.
* Check the Sum:
  * If `current_sum` equals the target `x`, we've found a valid subarray. We increment our count.
  * If `current_sum` exceeds the target `x`, the window is too large. We must shrink it by moving the `left` pointer to the right and subtracting the leftmost element from `current_sum`. We repeat this until the sum is no longer greater than `x`.
* We continue this process until the `right` pointer has traversed the entire array.

**2. Prefix Sum with Hash Map Approach - Works with negative numbers also**

This is a more powerful technique that also works if the array contains zeros or negative numbers.

* Prefix Sums: A prefix sum `P[i]` is the sum of all elements from the start of the array up to index `i`. The sum of any subarray from index `j` to `i` can be calculated as `P[i] - P[j-1]`.
* The Logic: We are looking for subarrays ending at `i` whose sum is `x`. This means we are looking for a starting index `j` such that `P[i] - P[j-1] = x`. Rearranging this, we get `P[j-1] = P[i] - x`.
* Hash Map: We can iterate through the array, calculating the `current_prefix_sum` at each position. For each `current_prefix_sum`, we check if `current_prefix_sum - x` exists in a hash map.
  * The hash map stores the frequencies of all prefix sums we have encountered so far.
  * If `current_prefix_sum - x` is in the map, it means there are one or more previous subarrays whose sum can be "subtracted" to leave our target `x`. We add its frequency to our total count.
  * After checking, we add the `current_prefix_sum` to the map.

***

### C++ Implementation

1. Two pointer Approach

```cpp
#include<bits/stdc++.h>
using namespace std;

long long countSubarrays(const vector<int>& arr, int x) {
    int n = arr.size();
    long long count = 0;
    long long current_sum = 0;
    int left = 0;

    for (int right = 0; right < n; ++right) {
        current_sum += arr[right];

        while (current_sum > x && left <= right) {
            current_sum -= arr[left];
            left++;
        }

        if (current_sum == x) {
            count++;
        }
    }
    return count;
}
```

2. Prefix Sum Approach

```cpp
#include<bits/stdc++.h>
using namespace std;

long long countSubarrays(const vector<int>& arr, int x) {
    int n = arr.size();
    map<long long, int> prefix_sum_counts;
    prefix_sum_counts[0] = 1; // Base case for subarrays starting at index 0

    long long count = 0;
    long long current_prefix_sum = 0;

    for (int val : arr) {
        current_prefix_sum += val;
        long long complement = current_prefix_sum - x;

        if (prefix_sum_counts.count(complement)) {
            count += prefix_sum_counts[complement];
        }

        prefix_sum_counts[current_prefix_sum]++;
    }
    return count;
}
```

***

### Complexity Analysis

| Approach       | Time Complexity | Space Complexity | Notes                                                                                                                                                                   |
| -------------- | --------------- | ---------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Sliding Window | $$O(n)$$        | $$O(1)$$         | Highly efficient. Both `left` and `right` pointers traverse the array only once. Only works for positive integers.                                                      |
| Prefix Sum Map | $$O(nlogn)$$    | $$O(n)$$         | The most general solution. Works for any integers (positive, negative, or zero). Becomes $$O(n)$$ average time if `unordered_map` is used with custom random seed hash. |

***

### Problems

[CSES Task 1660](https://cses.fi/problemset/task/1660)
