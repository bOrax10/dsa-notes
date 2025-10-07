# Next Permutation

### Problem Statement

Given an array of integers `nums`, find the **next permutation** of `nums`. The next permutation is defined as the next lexicographically greater permutation of the integers.

* If all permutations of the array were sorted lexicographically, the next permutation is the one immediately following the given array.
* If the array is the last permutation (sorted in descending order), return the first permutation (sorted in ascending order).

#### Example:

```
Input:  [2, 1, 5, 4, 3, 0, 0]
Output: [2, 3, 0, 0, 1, 4, 5]
```

***

### Algorithm

The steps to find the next permutation are as follows:

1. **Find the break-point `i`:**
   * Traverse the array from the end to the start.
   * Find the first index `i` such that `arr[i] < arr[i+1]`.
   * Example: In `[2,1,5,4,3,0,0]`, the break-point is at index 1 because `arr[1]=1` and `arr[2]=5`.
2. **Check if the break-point exists:**
   * If no break-point exists (array is sorted in descending order), reverse the entire array and return it as the next permutation.
3. **If a break-point exists:**
   1. Find the smallest number **greater than `arr[i]`** in the right half of the array (from `i+1` to `n-1`) and swap it with `arr[i]`.
   2. Reverse the subarray to the right of the break-point (`i+1` to `n-1`).
   3. Return the modified array.

**Alternative:** Use the built-in `next_permutation` method in C++.

***

### C++ Implementation

```cpp
#include <bits/stdc++.h>
using namespace std;

vector<int> nextGreaterPermutation(vector<int> &nums)
{
    int n = nums.size();

    // Step 1: Find the break point:
    int ind = -1; // break point
    for (int i = n - 2; i >= 0; i--)
    {
        if (nums[i] < nums[i + 1])
        {
            ind = i;
            break;
        }
    }

    // If break point does not exist:
    if (ind == -1)
    {
        reverse(nums.begin(), nums.end());
        return nums;
    }

    // Step 2: Find the next greater element and swap it with arr[ind]:
    for (int i = n - 1; i > ind; i--)
    {
        if (nums[i] > nums[ind])
        {
            swap(nums[i], nums[ind]);
            break;
        }
    }

    // Step 3: reverse the right half:
    reverse(nums.begin() + ind + 1, nums.end());

    return nums;
}


```

***

### Complexity Analysis

* Time Complexity - $$O(N)$$
* Space Complexity - $$O(1)$$
