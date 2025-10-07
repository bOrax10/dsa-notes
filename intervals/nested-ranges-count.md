# Nested Ranges Count

### Problem Statement

You're given $$n$$ ranges. Your task is to count for each range how many other ranges it contains and how many other ranges contain it.

A range $$[a,b]$$ contains another range $$[c,d]$$ if $$a≤c$$ and $$d≤b$$.

***

### Approach

A naive solution would be to compare every pair of ranges, leading to an $$O(n^2)$$ complexity, which is too slow for the given constraints. There is a much more efficient $$O(nlogn)$$ solution using a clever combination of sorting, coordinate compression, and a Fenwick Tree (also known as a Binary Indexed Tree or BIT).

The core idea is to handle one dimension of the problem (`x` coordinates) with sorting, so we only need a data structure for the other dimension (`y` coordinates). The ranges are sorted using a special comparison function:

* Primarily, sort by the start point `x` in ascending order.
* If two ranges have the same start point `x`, sort them by their end point `y` in descending order.

This specific order is crucial. When we iterate through the sorted ranges, any range `j` that appears before a range `i` is guaranteed to have $$x_j​≤x_i​$$. This simplifies our problem: we now only need to count how many of these previous ranges also satisfy the `y` coordinate condition.

The range endpoints can be very large (up to $$109$$), but a Fenwick Tree requires a compact index range. Since there are at most $$2n$$ distinct endpoints, we can map these large coordinate values to a smaller, dense set of integers (e.g., from 1 to `k`, where `k` is the number of unique `y` coordinates). This process is called coordinate compression.

A Fenwick Tree is a data structure that excels at two operations, both in $$O(logk)$$ time:

* `update(idx, val)`: Adds `val` to the element at index `idx`.
* `query(idx)`: Calculates the sum of elements from index 1 to `idx`.

We'll use the BIT to keep track of the compressed `y` coordinates we have encountered during our iteration.

The problem is broken down into two parts, each solved with a separate pass over the sorted ranges.

* First Pass (Forward Iteration): Counting Ranges that are Contained
  * We iterate through the sorted ranges from `i = 0` to `n-1`.
  * For the current range `[x_i, y_i]`, we need to find how many ranges `j` contain it. Due to our sorting, any range `j` we've already seen has $$x_j​≤x_i​$$. So, we just need to count how many of those also have $$y_j​≥y_i​$$.
  * This is equivalent to querying our BIT for the number of `y` coordinates already added that fall in the range `[y_i, max_y]`. The code calculates this with `query(bit_size) - query(compressed_y - 1)`.
  * After the query, we add the current range's `y_i` to the BIT using `update(compressed_y, 1)`.
* Second Pass (Backward Iteration): Counting Ranges that Contain Others
  * We reset the BIT and iterate through the sorted ranges backward, from `i = n-1` down to `0`.
  * For the current range `[x_i, y_i]`, we need to find how many ranges `j` it contains. Due to sorting and backward iteration, any range `j` we've already seen in this pass has $$xj​≥xi​$$. So, we just need to count how many of those also have $$yj​≤yi​$$.
  * This is equivalent to querying our BIT for the number of `y` coordinates already added that fall in the range `[1, y_i]`. The code does this with `query(compressed_y)`.
  * After the query, we add the current range's `y_i` to the BIT.

By using the original `id` of each range, we can store the results and print them in the initial order.

***

### C++ Implementation

```cpp
#include<bits/stdc++.h>
using namespace std;

struct Range {
    int x, y, id;
};

void update(vector<int>& bit, int idx, int val) {
    for (; idx < bit.size(); idx += idx & -idx) {
        bit[idx] += val;
    }
}

int query(const vector<int>& bit, int idx) {
    int sum = 0;
    for (; idx > 0; idx -= idx & -idx) {
        sum += bit[idx];
    }
    return sum;
}

bool compareRanges(const Range &a, const Range &b) {
    if (a.x != b.x) {
        return a.x < b.x; // Ascending by start point
    }
    return a.y > b.y; // Descending by end point
}

pair<vector<int>, vector<int>> countNestedRanges(vector<Range> ranges) {
    int n = ranges.size();
    if (n == 0) {
        return {{}, {}};
    }

    // Coordinate Compression
    vector<int> y_coords;
    y_coords.reserve(n);
    for (const auto& r : ranges) {
        y_coords.push_back(r.y);
    }
    sort(y_coords.begin(), y_coords.end());
    y_coords.erase(unique(y_coords.begin(), y_coords.end()), y_coords.end());

    map<int, int> y_map;
    for (int i = 0; i < y_coords.size(); ++i) {
        y_map[y_coords[i]] = i + 1;
    }
    
    // Sort Ranges
    sort(ranges.begin(), ranges.end(), compareRanges);

    // Process with Fenwick Tree
    vector<int> contains_count(n);
    vector<int> contained_by_count(n);
    int bit_size = y_coords.size();

    // Pass 1 (Backward): How many ranges does range[i] contain?
    vector<int> bit(bit_size + 1, 0);
    for (int i = n - 1; i >= 0; --i) {
        int compressed_y = y_map[ranges[i].y];
        contains_count[ranges[i].id] = query(bit, compressed_y);
        update(bit, compressed_y, 1);
    }

    // Pass 2 (Forward): How many ranges contain range[i]?
    bit.assign(bit_size + 1, 0);
    for (int i = 0; i < n; ++i) {
        int compressed_y = y_map[ranges[i].y];
        contained_by_count[ranges[i].id] = query(bit, bit_size) - query(bit, compressed_y - 1);
        update(bit, compressed_y, 1);
    }

    return {contains_count, contained_by_count};
}
```

***

### Complexity Analysis

* Time Complexity - $$O(NlogN)$$
* Space Complexity - $$O(N)$$

***

### Problems

[CSES Task 2169](https://cses.fi/problemset/task/2169)
