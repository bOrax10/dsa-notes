# Disjoint Set Union

The Disjoint Set Union (DSU) data structure, also known as Union-Find, is a data structure that efficiently tracks a partition of a set of elements into a number of disjoint (non-overlapping) subsets.

***

### Approach

The DSU is typically implemented using an array, often called `parent`, where `parent[i]` stores the parent of element `i`.

1. Representation: Each subset forms a tree, where the root of the tree is the representative of the subset. Initially, each element is its own parent (`parent[i] = i`).
2. `find(i)` Operation: To find the representative of the subset containing element `i`, we traverse up the tree by following the `parent` pointers until we reach an element whose parent is itself (the root).
3. `union(u, v)` Operation: To merge the subsets containing elements `u` and `v`:
   * First, find the representatives of their respective sets using the `find` operation: `rootU = find(u)` and `rootV = find(v)`.
   * If `rootU` and `rootV` are already the same, `u` and `v` are already in the same subset, so do nothing.
   * Otherwise, make one root the parent of the other. For example, set `parent[rootU] = rootV`.

Optimizations: To make these operations very fast, two key optimizations are used:

* Path Compression: During the `find(i)` operation, after finding the root, we make all nodes along the path point directly to the root. This flattens the tree for future `find` operations involving these nodes. Your code implements this in the line `return parent[node] = findUPar(parent[node]);`.
* Union by Rank/Size: When merging two trees during the `union` operation, instead of arbitrarily making one root the parent of the other, we use a heuristic:
  * Union by Rank: Attach the tree with the smaller "rank" (an upper bound on the tree's height) to the root of the tree with the larger rank. If ranks are equal, pick one root as the parent and increment its rank. This helps keep the trees relatively shallow.
  * Union by Size: Attach the tree with fewer nodes (smaller size) to the root of the tree with more nodes. This also helps keep the trees balanced. Your code provides both `unionByRank` and `unionBySize`.

***

### C++ Implementation

```cpp
#include<bits/stdc++.h>
using namespace std;

class DisjointSet {
    vector<int> rank, parent, size;
public:
    DisjointSet(int n) {
        rank.resize(n + 1, 0); // Initialize rank for Union by Rank
        parent.resize(n + 1);
        size.resize(n + 1);    // Initialize size for Union by Size
        for (int i = 0; i <= n; i++) {
            parent[i] = i;     // Each node is its own parent initially
            size[i] = 1;       // Each set initially has size 1
        }
    }

    // Find operation with Path Compression
    int findUPar(int node) {
        if (node == parent[node]) // If node is the root
            return node;
        // Recurse to find root and apply path compression
        return parent[node] = findUPar(parent[node]);
    }

    // Union by Rank: Merges sets based on tree height approximation
    void unionByRank(int u, int v) {
        int ulp_u = findUPar(u); // Find root of u
        int ulp_v = findUPar(v); // Find root of v
        if (ulp_u == ulp_v) return; // Already in the same set
        if (rank[ulp_u] < rank[ulp_v]) {
            parent[ulp_u] = ulp_v; // Attach smaller rank tree to larger
        } else if (rank[ulp_v] < rank[ulp_u]) {
            parent[ulp_v] = ulp_u;
        } else { // If ranks are equal
            parent[ulp_v] = ulp_u;
            rank[ulp_u]++; // Increment rank of the new root
        }
    }

    // Union by Size: Merges sets based on number of nodes
    void unionBySize(int u, int v) {
        int ulp_u = findUPar(u); // Find root of u
        int ulp_v = findUPar(v); // Find root of v
        if (ulp_u == ulp_v) return; // Already in the same set
        if (size[ulp_u] < size[ulp_v]) {
            parent[ulp_u] = ulp_v; // Attach smaller size tree to larger
            size[ulp_v] += size[ulp_u]; // Update size of the new root
        } else {
            parent[ulp_v] = ulp_u;
            size[ulp_u] += size[ulp_v];
        }
    }
};
```

***

### Complexity Analysis

* Time Complexity: The amortized time complexity for both `find` and `union` operations is nearly constant, specifically $$O(α(N))$$, where $$α(N)$$ is the inverse Ackermann function. This function grows extremely slowly ($$α(N)$$ < 5 for any practical value of N), making the operations effectively constant time on average.
* Space Complexity: $$O(N)$$

***

### Problems

[Roads not only in Berland](https://codeforces.com/contest/25/problem/D)

[Strange Food Chain](https://www.spoj.com/problems/CHAIN/)

[COLORFUL ARRAY](https://www.spoj.com/problems/CLFLARR/)

[Consecutive Letters](https://www.spoj.com/problems/CONSEC/)
