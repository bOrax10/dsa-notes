# Longest Subarray with Distinct Elements

### Problem Statement

Given an array of $$n$$ integers, $$A=[k1​,k2​,…,kn​]$$, the goal is to find the length of the longest subarray that contains only unique elements.

For the example input `1 2 1 3 2 7 4 2`, the longest such sequence is `1 3 2 7 4`, which has a length of 5.

***

### Approach

The core idea is to maintain a "window" that contains only unique songs and to expand or shrink this window dynamically to find the longest one possible.

This implementation uses a frequency map to keep track of the songs currently inside the window.

1. Initialization:
   * Two pointers, `i` (the left edge of the window) and `j` (the right edge), are initialized to the start of the playlist at index `0`.
   * A map, `freq`, is used to store the count (frequency) of each element within the current window `[i, j)`.
   * An integer `ans` is initialized to `0` to store the maximum length found so far.
2. The Main Loop: The `while (j < n)` loop works by trying to expand the window to the right using the `j` pointer.
3. Window Expansion :
   * Inside the loop, the `if (freq[arr[j]] == 0)` condition checks if the element at the right edge (arr`[j]`) is new to the current window. If its count in the map is `0`, it's unique.
   * If the element is unique, its count is incremented in the map (`freq[arr[j]]++`), and the window is successfully expanded by moving the right pointer forward (`j++`).
4. Window Contraction:
   * If the condition is false (the `else` block), it means arr`[j]` is already present in the window `[i, j)`, so we have a duplicate.
   * Update Max Length: Before fixing the window, the length of the _last valid unique window_ (`j - i`) is calculated and compared with `ans` to see if we've found a new maximum.
   * Shrink the Window: To remove the duplicate and make the window valid again, the window must shrink from the left. The element at the `left` pointer (arr`[i]`) is removed from the window by decrementing its count (`freq[arr[i]]--`), and the left pointer is moved forward (`i++`). The loop then continues, re-evaluating the same arr`[j]` against the new, smaller window.
5. Final Check:
   * After the loop finishes, there's one final `ans = max(ans, j - i)` check. This is crucial because the loop might end with the longest unique sequence being at the very end of the playlist. This final calculation ensures that the length of the last valid window is also considered.

***

### C++ Implementation

```cpp
#bits<stdc++.h>
using namespace std;

int findLongestUniqueSequence(vector<int> &arr) {
    int n = arr.size();
    int i = 0, j = 0;
    int ans = 0;
    map<int, int> freq;
    while (j < n)
    {
        debug(i, j);
        if (freq[arr[j]] == 0)
        {
            freq[arr[j]]++;
            j++;
        }
        else
        {
            ans = max(ans, j - i);
            freq[arr[i]]--;
            i++;
        }
    }
    ans = max(ans, j - i);
    
    return ans;
}
```

***

### Complexity Analysis

* Time Complexity - $$O(NlogN)$$ - Using unordered\_map is not advised as it can be $$O(N^2)$$ in worst case.
* Space Complexity - $$O(N)$$

***

### Problems

[CSES Task 1141](https://cses.fi/problemset/task/1141)
