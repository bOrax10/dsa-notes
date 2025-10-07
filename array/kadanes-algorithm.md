# Kadane's Algorithm

### Problem Statement

To find the maximum subarray sum of an array.

***

### Algorithm

The key idea of the algorithm is that any subarray with a **negative sum** cannot contribute to the maximum subarray sum. If the running sum ever becomes negative, it will only reduce the total if we continue, so we discard it by resetting the sum to `0`.

We iterate through the array once, maintaining a running sum. At each step:

* Add the current element to the running sum.
* If the sum drops below `0`, reset it to `0` (ignore that subarray).
* Track the maximum sum encountered so far.

Note: In some variations of the problem, we are allowed to choose the **empty subarray** as well. In such cases, since the sum of the empty subarray is `0`, the final answer is `max(maxSum, 0)`.

**Example:**\
For the array `{-1, -4, -5}`, the maximum sum would be `0` (choosing the empty subarray), instead of `-1`.

***

### C++ Implementation

```cpp
#include <bits/stdc++.h>
using namespace std;

long long maxSubarraySum(int arr[], int n) {
    long long maxi = LLONG_MIN;
    long long sum = 0;

    for (int i = 0; i < n; i++) {

        sum += arr[i];

        if (sum > maxi) {
            maxi = sum;
        }

        if (sum < 0) {
            sum = 0;
        }
    }

    // To consider the sum of the empty subarray
    //if (maxi < 0) maxi = 0;

    return maxi;
}
```

***

### Complexity Analysis

* Time Complexity - $$O(N)$$
* Space Complexity - $$O(1)$$

***

### Problems

* [53. Maximum Subarray (Medium)](https://leetcode.com/problems/maximum-subarray)
* [363. Max Sum of Rectangle No Larger Than K (Hard)](https://leetcode.com/problems/max-sum-of-rectangle-no-larger-than-k/)
* [2272. Substring With Largest Variance (Hard)](https://leetcode.com/problems/substring-with-largest-variance)
* [CSES Task 1643](https://cses.fi/problemset/task/1643)
* [CF 75D - Big Maximum Sum (Hard)](https://codeforces.com/contest/75/problem/D)
