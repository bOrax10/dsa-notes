# Segment Tree

Segment Trees are powerful data structures used for efficiently handling range queries and point updates on an array.

***

### Problem Statement

Given an array `A` of size `N`, the task is to efficiently perform two types of operations:

1. Query(L, R): Calculate an aggregate value (like sum, minimum, maximum, GCD, etc.) over a subarray `A[L...R]`.
2. Update(index, value): Change the value of an element `A[index]` to `value`.

***

### Approach

A Segment Tree is a binary tree where each node represents an interval (or segment) of the original array.

1. Structure:
   * The root node represents the entire array `A[0...N-1]`.
   * Each internal node represents an interval `[L, R]`. Its left child represents the interval `[L, mid]` and its right child represents `[mid+1, R]`, where `mid = floor((L+R)/2)`.
   * Each leaf node represents a single element `A[i]` (interval `[i, i]`).
   * The tree typically has a height of $$O(\log N)$$ and around $$2N - 1$$ nodes (often implemented using an array of size $$4N$$ for simplicity).
   * Each node stores the aggregate value for its corresponding interval, computed from the values of its children.
2. Build Operation: The tree is built recursively, starting from the leaves (which take the array values) and moving up. The value in each internal node is computed by combining the values of its left and right children using the specific aggregation operation (e.g., sum, min, max).
3. Query Operation `Query(L, R)`: To find the aggregate value for a query range `[L, R]`, we traverse the tree starting from the root. At each node representing an interval `[nodeL, nodeR]`:
   * If the node's interval is completely outside the query range, return an identity value (e.g., 0 for sum, infinity for min).
   * If the node's interval is completely inside the query range, return the pre-computed value stored in the node.
   * If the node's interval partially overlaps the query range, recursively query its left and right children and combine their results.
4. Update Operation `Update(index, value)`: To update `A[index]` to `value`, we traverse the tree from the root down to the leaf node corresponding to `index`.
   * Update the value at the leaf node.
   * As we return up the recursion path, update the aggregate values in all ancestor nodes by recombining the (potentially changed) values of their children.

Both query and update operations involve traversing a path from the root to a leaf (or branches covering the query range), resulting in logarithmic time complexity.

***

### C++ Implementation

```cpp
#include<bits/stdc++.h>
using namespace std;

/* Note:
For 0-based indexing, child nodes will have indices 2 * node + 1 and 2 * node + 2
*/
class SegmentTree {
private:
    vector<long long> tree;
    vector<long long> arr;
    int n;

    // Recursive build function
    void build(int node, int start, int end) {
        if (start == end) {
            tree[node] = arr[start];
        } else {
            int mid = start + (end - start) / 2;
            build(2 * node, start, mid);
            build(2 * node + 1, mid + 1, end);
            // For Sum:
            tree[node] = tree[2 * node] + tree[2 * node + 1];
            // For Min: tree[node] = min(tree[2 * node], tree[2 * node + 1]);
            // For Max: tree[node] = max(tree[2 * node], tree[2 * node + 1]);
            // For GCD: tree[node] = gcd(tree[2 * node], tree[2 * node + 1]);
        }
    }

    // Recursive update function
    void update(int node, int start, int end, int idx, long long val) {
        if (start == end) {
            arr[idx] = val;
            tree[node] = val;
        } else {
            int mid = start + (end - start) / 2;
            if (start <= idx && idx <= mid) {
                update(2 * node, start, mid, idx, val);
            } else {
                update(2 * node + 1, mid + 1, end, idx, val);
            }
            // For Sum:
            tree[node] = tree[2 * node] + tree[2 * node + 1];
            // For Min: tree[node] = min(tree[2 * node], tree[2 * node + 1]);
            // For Max: tree[node] = max(tree[2 * node], tree[2 * node + 1]);
            // For GCD: tree[node] = gcd(tree[2 * node], tree[2 * node + 1]);
        }
    }

    // Recursive query function
    long long query(int node, int start, int end, int l, int r) {
        // Node interval is completely outside the query range
        if (r < start || end < l) {
            // For Sum:
            return 0LL;
            // For Min: return LLONG_MAX;
            // For Max: return LLONG_MIN;
            // For GCD: return 0; // GCD(x, 0) = xm
        }
        // Node interval is completely inside the query range
        if (l <= start && end <= r) {
            return tree[node];
        }
        // Node interval partially overlaps query range
        int mid = start + (end - start) / 2;
        long long p1 = query(2 * node, start, mid, l, r);
        long long p2 = query(2 * node + 1, mid + 1, end, l, r);
        
        // For Sum:
        return p1 + p2;
        // For Min: return min(p1, p2);
        // For Max: return max(p1, p2);
        // For GCD: return gcd(p1, p2);
    }

public:
    SegmentTree(const vector<long long>& input_arr) {
        n = input_arr.size();
        arr = input_arr;
        tree.resize(4 * n);
        build(1, 0, n - 1);
    }

    // Public query function
    long long query(int l, int r) {
        return query(1, 0, n - 1, l, r);
    }

    // Public update function
    void update(int idx, long long val) {
        update(1, 0, n - 1, idx, val);
    }
};
```

***

### Complexity Analysis

* Time Complexity:
  * Build: $$O(N)$$
  * Query: $$O(\log N)$$
  * Update: $$O(\log N)$$
* Space Complexity: $$O(N)$$

***

### Problems

[Knight Tournament](https://codeforces.com/contest/356/problem/A)

<details>

<summary>Hint:</summary>

Store the minimum index of the _active_ knight in each node, and use point updates to "eliminate" losers by setting their value to infinity.

</details>

[Xenia and Bit Operations](https://codeforces.com/problemset/problem/339/D)

<details>

<summary>Hint:</summary>

Store the result of the operation in each node, alternating the operation (OR/XOR) at each level of the tree during builds and updates.

</details>

[Distinct Characters Queries](https://codeforces.com/problemset/problem/1234/D)

<details>

<summary>Hint:</summary>

Store a bitmask (representing the set of characters) in each node, and merge nodes by taking the bitwise OR of their masks.

</details>

[Ant colony](https://codeforces.com/contest/474/problem/F)

[Drazil and Park](https://codeforces.com/contest/515/problem/E)

[Hotel Queries](https://cses.fi/problemset/task/1143/)

