# Check Bipartite - DFS

### Problem Statement

Given an undirected graph, the task is to determine if it is bipartite. A graph is bipartite if its vertices can be divided into two disjoint and independent sets, U and V, such that every edge in the graph connects a vertex in U to one in V.

A more intuitive way to think about this is: can we color the graph with just two colors (e.g., 0 and 1) such that no two adjacent vertices have the same color? An important property is that a graph is bipartite if and only if it does not contain any odd-length cycles.

***

The DFS-based approach directly implements the two-coloring strategy. We traverse the graph, assigning one of two colors to each vertex we visit. A conflict arises if we find an edge connecting two vertices that have already been assigned the same color.

The algorithm works as follows:

1. Initialization: Create a `color` array of size `V` and initialize all entries to a sentinel value (e.g., -1) to indicate that no vertex has been colored yet.
2. Start DFS: Begin a DFS traversal from an arbitrary uncolored vertex. Assign it the first color (e.g., color 0).
3. Recursive Coloring: In the DFS function for a given `node` with color `col`:
   * Assign `color[node] = col`.
   * Iterate through all of its neighbors (`it`):
     * If the neighbor is uncolored (`color[it] == -1`): Recursively call the DFS function on it, assigning it the opposite color (`!col`). If this recursive call detects a conflict (returns `false`), propagate this result up.
     * If the neighbor is already colored: Check its color. If `color[it]` is the same as `col`, then we have found two adjacent vertices with the same color. This means the graph is not bipartite, so return `false`.
4. Handling Disconnected Graphs: The main function should loop through all vertices from 0 to V-1. If a vertex is uncolored, it means it belongs to a new, undiscovered component, so we start a new DFS traversal from it. If any component is not bipartite, the entire graph is not bipartite.

***

### C++ Implementation

```cpp
#include<bits/stdc++.h>
using namespace std;

bool dfs(int node, int col, int color[], vector<int> adj[]) {
    color[node] = col;

    // Traverse adjacent nodes
    for (auto it : adj[node]) {
        // If the neighbor is uncolored, color it with the opposite color
        if (color[it] == -1) {
            if (dfs(it, !col, color, adj) == false) {
                return false;
            }
        }
        // If the neighbor is already colored with the same color, it's a conflict.
        else if (color[it] == col) {
            return false;
        }
    }
    return true;
}

bool isBipartite(int V, vector<int> adj[]) {
    int color[V];
    for (int i = 0; i < V; i++) color[i] = -1;

    // Loop to handle disconnected components
    for (int i = 0; i < V; i++) {
        if (color[i] == -1) {
            if (dfs(i, 0, color, adj) == false) {
                return false;
            }
        }
    }
    return true;
}
```

***

### Complexity Analysis

* Time Complexity: $$O(V + E)$$
* Space Complexity: $$O(V)$$
*
