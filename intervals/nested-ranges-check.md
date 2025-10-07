# Nested Ranges Check

### Problem Statement

Given $$n$$ ranges, your task is to determine for each range if it contains some other range and if some other range contains it.

Range $$[a,b]$$ contains range $$[c,d]$$ if $$a≤c$$ and $$d≤b$$.

For example, given the ranges <kbd>\[1,6] \[2,4] \[4, 8] \[3, 6]</kbd> the output should be:

<kbd>1 0 0 0</kbd>\
<kbd>0 1 0 1</kbd>

***

### Approach

A naive brute-force solution would compare every pair of ranges, resulting in an $$O(n^2)$$ time complexity, which is too slow for $$n=2⋅10^5$$. A more efficient approach is needed, and the key is sorting.

We'll sort the ranges based on a specific custom rule:

* Primarily, sort by the start point (`x`) in ascending order.
* If the start points are the same, sort by the end point (`y`) in descending order.

This sorting order places wider ranges before narrower ones when they share the same starting point. This is crucial for our logic. To produce the output in the original order, we must store the original index of each range before sorting.

After sorting, we can find the answers in two linear passes over the sorted data.

**1. Finding Ranges "Contained By" Another (Forward Pass)**

To check if a range `[x, y]` is contained by another, we need some range `[a, b]` that came _before_ it in the sorted list such that $$a≤x$$ and $$y≤b$$.

* The sorting already guarantees that $$a≤x$$.
* So, we only need to check if there is any previously seen range whose end point `b` is greater than or equal to our current end point `y`.

We can do this by iterating through the sorted ranges from beginning to end while keeping track of the maximum end point (`max_end`) encountered so far. If the current range's end point `y` is less than or equal to `max_end`, it is contained.

**2. Finding Ranges That "Contain" Another (Backward Pass)**

To check if a range `[x, y]` contains another, we need some range `[c, d]` that comes _after_ it in the sorted list such that $$x≤c$$ and $$d≤y$$.

* The sorting guarantees that $$x≤c$$.
* So, we only need to check if there is any range we will see later whose end point `d` is less than or equal to our current end point `y`.

We can do this by iterating through the sorted ranges from end to beginning while keeping track of the minimum end point (`min_end`) encountered so far in our reverse pass. If the current range's end point `y` is greater than or equal to `min_end`, it must contain another range.

***

### C++ Implementation

```cpp
#include<bits/stdc++.h>
using namespace std;

struct Range {
    int x, y, id;
};

bool compareRanges(const Range& a, const Range& b) {
    if (a.x != b.x) {
        return a.x < b.x;
    }
    return a.y > b.y;
}

pair<vector<int>, vector<int>> checkNestedRanges(const vector<pair<int, int>>& input_ranges) {
    int n = input_ranges.size();
    if (n == 0) {
        return {{}, {}};
    }
    
    vector<Range> ranges(n);
    for (int i = 0; i < n; ++i) {
        ranges[i] = {input_ranges[i].first, input_ranges[i].second, i};
    }

    sort(ranges.begin(), ranges.end(), compareRanges);

    vector<int> contains(n, 0);
    vector<int> contained_by(n, 0);

    // 3. Forward pass to check for "contained by"
    int max_end = 0;
    for (const auto& range : ranges) {
        if (range.y <= max_end) {
            contained_by[range.id] = 1;
        }
        max_end = max(max_end, range.y);
    }

    // 4. Backward pass to check for "contains"
    int min_end = 1e9 + 7;
    for (int i = n - 1; i >= 0; --i) {
        if (ranges[i].y >= min_end) {
            contains[ranges[i].id] = 1;
        }
        min_end = min(min_end, ranges[i].y);
    }

    return {contains, contained_by};
}
```

***

### Complexity Analysis

* Time Complexity - $$O(NlogN)$$
* Space Complexity - $$O(N)$$

***

### Problems

[CSES Task 2168](https://cses.fi/problemset/task/2168)
