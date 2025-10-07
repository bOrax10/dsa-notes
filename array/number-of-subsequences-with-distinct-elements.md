# Number of Subsequences with Distinct Elements

### Problem Statement

Given an array of $$n$$ integers, the task is to count the total number of non-empty subsequences that are composed of distinct elements. Since the result can be very large, it must be computed modulo $$10^9+7$$.

***

### Approach

The key insight is that the original positions of the elements do not matter, only the frequencies of the unique values. A distinct-element subsequence can be formed by choosing at most one instance of each unique number available in the array. Let's walk through the example `[1, 2, 1, 3]` to see how it works.

1. Count Frequencies: The first step is to iterate through the entire array and count the occurrences of each unique integer. A hash map is perfect for this, storing each unique number and its frequency. For the example `[1, 2, 1, 3]`, the frequencies are: `{1: 2, 2: 1, 3: 1}`.
2. Calculate Choices for Each Unique Element: For any unique number `x` that appears `k` times in the array, we have `k + 1` choices when forming a subsequence:
   * We can choose any one of its `k` occurrences to include in our subsequence (giving `k` choices).
   * We can choose to not include `x` at all (giving `1` choice).
   * This gives a total of `k + 1` options for each unique element.
3. Combine Choices: By the fundamental principle of counting, the total number of ways to form a subsequence (including the empty one) is the product of the number of choices for each unique element.
   * For `[1, 2, 1, 3]`:
     * Choices for `1` (frequency 2): `2 + 1 = 3`
     * Choices for `2` (frequency 1): `1 + 1 = 2`
     * Choices for `3` (frequency 1): `1 + 1 = 2`
     * Total combinations = `3 * 2 * 2 = 12`
4. Exclude the Empty Subsequence: The calculation above includes one case where we choose to include _none_ of the elements, which results in the empty subsequence. Since the problem asks for the count of non-empty subsequences, we must subtract 1 from the total.
   * Final Answer = `12 - 1 = 11`

***

### C++ Implementation

```cpp
#include<bits/stdc++.h>
using namespace std;

const int mod = 1e9 + 7;

long long countDistinctSubsequences(vector<int> &arr) {
    int n=arr.size();
    map<int, int> freq;
    for(int i = 0; i < n; i++) {
        freq[arr[i]]++;
    }
    
    long long ans = 1;
    for (auto &p : freq)
    {
        ans = (ans * (p.second + 1)) % mod;
    }
    ans = (ans - 1) % mod;
    if (ans < 0)
        ans += mod;
    
    return ans;
}
```

***

### Complexity Analysis

* Time Complexity - $$O(NlogN)$$ - Using unordered\_map is not advised as it can be $$O(N^2)$$ in worst case.
* Space Complexity - $$O(N)$$

***

### Problems

[CSES Task 3421](https://cses.fi/problemset/task/3421)
