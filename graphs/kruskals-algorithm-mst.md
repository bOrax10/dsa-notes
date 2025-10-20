# Kruskal's Algorithm - MST

Kruskal's algorithm is a greedy algorithm used to find a Minimum Spanning Tree (MST) for a weighted, undirected, and connected graph.

MST - A subgraph that connects all vertices together without any cycles such that the sum of the weights of all the edges in the tree is minimized.

***

### Algorithm

Kruskal's algorithm builds the MST by iteratively adding the cheapest available edge that does not form a cycle with the edges already chosen.

The algorithm relies on the following steps:

1. Sort Edges: Create a list of all edges in the graph and sort them in non-decreasing order based on their weights.
2. Initialize: Start with an empty set of edges for the MST. Each vertex is initially considered its own separate component or tree.
3. Iterate and Add: Process the sorted edges one by one. For each edge `(u, v)` with weight `w`:
   * Check if adding this edge would create a cycle. This means checking if vertices `u` and `v` are already in the same connected component. We can use DSU for this
   * If `u` and `v` are in different components: Add the edge `(u, v)` to the MST result set and merge the components containing `u` and `v`. This edge is safe because it connects two previously disconnected parts.
   * If `u` and `v` are already in the same component: Adding this edge would create a cycle, so we discard it.
4. Termination: Continue this process until `V-1` edges have been added to the MST (where `V` is the number of vertices). The resulting set of edges forms the MST.

***

### C++ Implementation

```cpp
#include<bits/stdc++.h>
using namespace std;

// Disjoint Set Union (DSU) with Path Compression and Union by Size/Rank
class DisjointSet {
    vector<int> parent, rank, size; // Can use either rank or size
public:
    DisjointSet(int n) {
        rank.resize(n, 0);
        size.resize(n, 1);
        parent.resize(n);
        iota(parent.begin(), parent.end(), 0); // parent[i] = i
    }

    // Find representative with path compression
    int findUPar(int node) {
        if (node == parent[node])
            return node;
        return parent[node] = findUPar(parent[node]);
    }

    // Union by Rank
    void unionByRank(int u, int v) {
        int ulp_u = findUPar(u);
        int ulp_v = findUPar(v);
        if (ulp_u == ulp_v) return;
        if (rank[ulp_u] < rank[ulp_v]) {
            parent[ulp_u] = ulp_v;
        } else if (rank[ulp_v] < rank[ulp_u]) {
            parent[ulp_v] = ulp_u;
        } else {
            parent[ulp_v] = ulp_u;
            rank[ulp_u]++;
        }
    }

    // Union by Size
    void unionBySize(int u, int v) {
        int ulp_u = findUPar(u);
        int ulp_v = findUPar(v);
        if (ulp_u == ulp_v) return;
        if (size[ulp_u] < size[ulp_v]) {
            parent[ulp_u] = ulp_v;
            size[ulp_v] += size[ulp_u];
        } else {
            parent[ulp_v] = ulp_u;
            size[ulp_u] += size[ulp_v];
        }
    }
};

int spanningTree(int V, vector<vector<int>> adj[]) {
    // Extract edges from adjacency list
    vector<pair<int, pair<int, int>>> edges;
    for (int i = 0; i < V; i++) {
        for (auto it : adj[i]) {
            int adjNode = it[0];
            int wt = it[1];
            int node = i;
            // Add edge only once for undirected graph (e.g., if node < adjNode)
            if (node < adjNode) {
                edges.push_back({wt, {node, adjNode}});
            }
        }
    }

    // Sort edges by weight
    sort(edges.begin(), edges.end());

    // Initialize DSU and MST weight
    DisjointSet ds(V);
    int mstWt = 0;
    int edgesInMST = 0;

    // Iterate through sorted edges
    for (auto it : edges) {
        int wt = it.first;
        int u = it.second.first;
        int v = it.second.second;

        // Check if adding the edge creates a cycle
        if (ds.findUPar(u) != ds.findUPar(v)) {
            // Add edge to MST and unite components
            mstWt += wt;
            ds.unionBySize(u, v); // Or unionByRank
            edgesInMST++;
            // Optimization: stop if MST is complete
            if (edgesInMST == V - 1) break;
        }
    }

    // Optional: check if edgesInMST == V-1 if graph might be disconnected
    return mstWt;
}
```

***

### Complexity Analysis

* Time Complexity: $$O(ElogV)$$
* Space Complexity: $$O(V+E)$$

***

### Problems

[Cost](https://www.spoj.com/problems/KOICOST/)

[BMW](https://www.spoj.com/problems/MARYBMW/)

[Edges in MST](https://codeforces.com/contest/160/problem/D)

[Flea](https://codeforces.com/problemset/problem/32/C)

[Igor In the Museum](https://codeforces.com/problemset/problem/598/D)

[Hongcow Builds A Nation](https://codeforces.com/problemset/problem/744/A)

[Bytelandian Blingors Network](https://www.spoj.com/problems/BLINNET/)

[Help the old King](https://www.spoj.com/problems/IITKWPCG/)

[Hierarchy](https://codeforces.com/contest/17/problem/B)

[Modems](https://www.spoj.com/problems/EC_MODE/)

