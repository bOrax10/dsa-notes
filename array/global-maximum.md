# Global Maximum

### Problem Statement

You are given an array of N integers and an integer K. Your task is to find a subsequence of size K from the array such that the minimum absolute difference between any two elements in the subsequence is maximized.

***

### Approach

The problem's goal to "maximize a minimum" is a strong signal to use binary search on the answer. Instead of inefficiently checking every possible subsequence, we ask a simpler yes/no question: "Can we find a subsequence of size `k` where the minimum difference between any two elements is at least `D`?"

This question has a crucial monotonic property: if a minimum difference `D` is achievable, any smaller difference is also certainly achievable. This allows us to efficiently search for the largest possible `D` that works. The binary search operates within a defined search space, with a lower bound of 1 and an upper bound of `max_element - min_element`.

To perform this binary search, we need a `check` function that verifies if a given `minDifference` is possible. This is done using a greedy strategy on the sorted array. The process is as follows:

* Sort the array: This is a mandatory first step.
* Greedy Selection: Pick the first element of the sorted array (`arr[0]`) to be in our subsequence.
* Iterate and Select: Go through the rest of the sorted array, always picking the next earliest element that is at least `minDifference` greater than the last element selected.
* Count: If you can pick `k` or more elements this way, the `minDifference` is achievable.

This greedy choice is optimal because by always picking the earliest possible valid element, we maximize the remaining space for all subsequent elements. The binary search then uses this `check` function. If `check(mid)` is true, we store `mid` as a potential answer and search the upper half for an even better solution. Otherwise, we search the lower half.

***

### C++ Implementation

```cpp
#include <bits/stdc++.h
using namespace std;

bool canSelect(int min_diff, int k, const vector<int>& arr) {
    int count = 1;
    int last_selected = arr[0];

    for (size_t i = 1; i < arr.size(); ++i) {
        if (arr[i] - last_selected >= min_diff) {
            count++;
            last_selected = arr[i];
        }
    }
    return count >= k;
}

int solveMaximizeMinimum(vector<int>& arr, int k) {
    if (k > arr.size()) {
        return -1;
    }

    sort(arr.begin(), arr.end());

    int low = 1;
    int high = arr.back() - arr.front();
    int ans = 0;

    while (low <= high) {
        int mid = low + (high - low) / 2;
        if (canSelect(mid, k, arr)) {
            ans = mid;
            low = mid + 1;
        } else {
            high = mid - 1;
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

[1552. Magnetic Force Between Two Balls (Medium)](https://leetcode.com/problems/magnetic-force-between-two-balls/)

