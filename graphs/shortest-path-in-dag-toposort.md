# Shortest Path in DAG - Toposort

### Problem Statement

Given a weighted Directed Acyclic Graph (DAG) and a source vertex `s`, the task is to find the shortest path distance from `s` to all other vertices in the graph. Unlike algorithms like Dijkstra, this method works correctly even if the graph contains negative edge weights.

***

### Approach

DAGs have a special property: they contain no cycles. This allows us to process the vertices in a topologically sorted order.

The key insight is:

* If we process the vertices in a topologically sorted order, whenever we are considering a vertex `u`, we are guaranteed to have already processed all vertices that have a path leading _to_ `u`.
* This means we have already found the shortest paths to all predecessors of `u`.

The algorithm works as follows:

1. Topological Sort: First, compute a topological sort of the DAG. The DFS-based method (as shown in your code) is suitable. This gives us a linear ordering of vertices where for every edge `(u, v)`, `u` appears before `v` in the ordering.
2. Initialize Distances: Create a `distance` array `dist` of size `V`. Initialize `dist[source]` to 0 and all other `dist[i]` to infinity.
3. Relax Edges: Iterate through the vertices in the order given by the topological sort. For each vertex `u`:
   * If `dist[u]` is not infinity (meaning `u` is reachable from the source):
     * For each neighbor `v` of `u` with edge weight `w`:
       * Perform the relaxation step: If `dist[u] + w < dist[v]`, update `dist[v] = dist[u] + w`.

Since we process vertices in topological order, when we relax the edges outgoing from `u`, we know that `dist[u]` already holds the shortest path distance to `u`. Therefore, this single pass correctly computes the shortest paths to all other vertices.

***

### C++ Implementation

```cpp
#include<bits/stdc++.h>
using namespace std;

void topoSort(int node, vector<pair<int, int>> adj[], int vis[], stack<int>& st) {
    vis[node] = 1;
    for (auto it : adj[node]) {
        int v = it.first;
        if (!vis[v]) {
            topoSort(v, adj, vis, st);
        }
    }
    st.push(node);s
}

vector<int> shortestPath(int N, int M, vector<vector<int>>& edges) {
    // Create adjacency list representation of the graph
    vector<pair<int, int>> adj[N];
    for (int i = 0; i < M; i++) {
        int u = edges[i][0];
        int v = edges[i][1];
        int wt = edges[i][2];
        adj[u].push_back({v, wt});
    }

    // Perform Topological Sort
    int vis[N] = {0};
    stack<int> st;
    for (int i = 0; i < N; i++) {
        if (!vis[i]) {
            topoSort(i, adj, vis, st);
        }
    }

    // Initialize distances
    vector<int> dist(N);
    for (int i = 0; i < N; i++) {
        dist[i] = 1e9;
    }
    dist[0] = 0; // Assuming source is vertex 0

    // Relax edges according to topological order
    while (!st.empty()) {
        int node = st.top();
        st.pop();

        // Only relax edges if the node is reachable
        if (dist[node] != 1e9) {
            for (auto it : adj[node]) {
                int v = it.first;
                int wt = it.second;

                // Relaxation step
                if (dist[node] + wt < dist[v]) {
                    dist[v] = dist[node] + wt;
                }
            }
        }
    }

    // Mark unreachable nodes as -1
    for (int i = 0; i < N; i++) {
        if (dist[i] == 1e9) dist[i] = -1;
    }
    return dist;
}
```

***

### Complexity Analysis

* Time Complexity: $$O(V + E)$$
* Space Complexity: $$O(V+E)$$

***

### Problems

[Longest Flight Route](https://cses.fi/problemset/task/1680)
