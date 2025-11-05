# Segment Tree with Lazy Propagation

Lazy Propagation is an optimization technique for Segment Trees that allows for efficient handling of range updates in addition to range queries.

***

### Problem Statement

You are given an array `A` o\f size `N`. The task is to efficiently perform two types of operations:

1. Range Update `update(L, R, value)`: Apply a modification (e.g., add `value`, set to `value`) to all elements in a subarray `A[L...R]`.
2. Range Query `query(L, R)`: Calculate an aggregate value (e.g., sum, minimum, maximum) over a subarray `A[L...R]`.

***

### Approach

The core idea is to postpone updates on a node and apply them only when necessary. We introduce an auxiliary `lazy` array (the same size as the `tree` array) to store these pending updates.

1. Lazy Array: `lazy[node]` stores a pending update that needs to be applied to the segment represented by `node` and all of its descendants. It's initialized with an identity value (e.g., 0 for addition).
2. Pushing Down Updates: Before we access the children of a node (in either an `update` or `query` operation), we must first apply any pending updates stored at that node. This is done by a `push` or `propagate` function:
   * Apply the `lazy[node]` value to the current `tree[node]`.
   * If the node is not a leaf, pass the lazy update down to its children by updating `lazy[left_child]` and `lazy[right_child]`.
   * Clear the pending update at the current node by resetting `lazy[node]` to the identity value.
3. Range Update `update(L, R, value)`: The update operation traverses the tree to find the nodes that cover the range `[L, R]`.
   * If a node's interval is completely inside the update range, we don't go deeper. We update `lazy[node]` with the given `value` and update `tree[node]` based on this new lazy value. We stop here, leaving the "lazy" tag to be propagated down later.
   * If a node's interval partially overlaps, we first `push` down any pending updates from the current node to its children. Then, we recursively call `update` on the children that overlap with the range. After the recursive calls return, we update the current `tree[node]` based on its children's new values.
4. Range Query `query(L, R)`: The query operation works similarly to a standard Segment Tree, but with one crucial addition.
   * Before processing any node, we must first `push` down any pending updates from that node to its children. This ensures that the values we use for calculation are up-to-date.
   * After pushing, the rest of the query logic (checking for full, partial, or no overlap) remains the same.

***

### C++ Implementation

```cpp
#include<bits/stdc++.h>
using namespace std;

class LazySegmentTree {
private:
    vector<long long> tree;
    vector<long long> lazy;
    vector<int>& arr;
    int n;

    void build(int node, int start, int end) {
        if (start == end) {
            tree[node] = arr[start];
            return;
        }
        int mid = start + (end - start) / 2;
        build(2 * node, start, mid);
        build(2 * node + 1, mid + 1, end);
        tree[node] = tree[2 * node] + tree[2 * node + 1];
    }

    // Push pending updates down to children
    void push(int node, int start, int end) {
        if (lazy[node] != 0) {
            // Apply lazy value to current node
            // The update depends on the length of the segment
            tree[node] += (long long)(end - start + 1) * lazy[node];

            // If not a leaf, pass lazy value to children
            if (start != end) {
                lazy[2 * node] += lazy[node];
                lazy[2 * node + 1] += lazy[node];
            }
            // Clear lazy value for current node
            lazy[node] = 0;
        }
    }

    // Range Update
    void update(int node, int start, int end, int l, int r, int val) {
        push(node, start, end); // Apply pending updates first

        // No overlap
        if (start > r || end < l) {
            return;
        }

        // Complete overlap: update lazy and return
        if (l <= start && end <= r) {
            lazy[node] += val;
            push(node, start, end); // Apply the new lazy value immediately
            return;
        }

        // Partial overlap: recurse on children
        int mid = start + (end - start) / 2;
        update(2 * node, start, mid, l, r, val);
        update(2 * node + 1, mid + 1, end, l, r, val);

        // Update current node from children
        tree[node] = tree[2 * node] + tree[2 * node + 1];
    }

    // Range Query
    long long query(int node, int start, int end, int l, int r) {
        // No overlap
        if (start > r || end < l) {
            return 0; // Identity for sum
        }

        push(node, start, end); // Apply pending updates first

        // Complete overlap
        if (l <= start && end <= r) {
            return tree[node];
        }

        // Partial overlap: query children and combine
        int mid = start + (end - start) / 2;
        long long p1 = query(2 * node, start, mid, l, r);
        long long p2 = query(2 * node + 1, mid + 1, end, l, r);
        return p1 + p2;
    }

public:
    LazySegmentTree(vector<int>& input_arr) : arr(input_arr) {
        n = arr.size();
        tree.resize(4 * n);
        lazy.resize(4 * n, 0);
        build(1, 0, n - 1);
    }

    void update(int l, int r, int val) {
        update(1, 0, n - 1, l, r, val);
    }

    long long query(int l, int r) {
        return query(1, 0, n - 1, l, r);
    }
};
```

***

### Complexity Analysis

* Time Complexity:
  * Build: $$O(N)$$
  * Range Query: $$O(log N)$$
  * Range Update: $$O(log N)$$
* Space Complexity: $$O(N)$$ for the tree and lazy arrays.

***

### Problems

[Polynomial Queries](https://cses.fi/problemset/task/1736)

[Range Updates and Sums](https://cses.fi/problemset/task/1735)

[Circular RMQ](https://codeforces.com/problemset/problem/52/C)

[The Untended Antiquity](https://codeforces.com/problemset/problem/869/E)

[Please, another Queries on Array?](https://codeforces.com/problemset/problem/1114/F)

[XOR on Segment](https://codeforces.com/problemset/problem/242/E)

[SUM and REPLACE](https://codeforces.com/problemset/problem/920/F)

[A Simple Task](https://codeforces.com/problemset/problem/558/E)

[Kefa and Watch](https://codeforces.com/problemset/problem/580/E)

[Eyes Closed](https://codeforces.com/problemset/problem/895/E)

[Alphabet Permutations](https://codeforces.com/problemset/problem/610/E)

[DZY Loves Fibonacci Numbers](https://codeforces.com/contest/446/problem/C)

[The Child and Sequence](https://codeforces.com/contest/438/problem/D)

[Lucky Array](https://codeforces.com/contest/121/problem/E)
