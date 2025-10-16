# Cycle Detection - Directed Graph - DFS

### Problem Statement

Given a directed graph, the task is to determine if it contains a cycle. A cycle in a directed graph is a path that starts and ends at the same vertex, following the direction of the edges.

***

### Approach

In a directed graph, simply finding a visited neighbor doesn't guarantee a cycle. We need to know if that visited neighbor is an ancestor in the current traversal path. The DFS-based solution elegantly handles this by using two separate "visited" arrays.

1. `vis[]` (Visited Array): This is a general-purpose array that marks a node as visited once its DFS traversal has been initiated. It prevents us from re-processing nodes in different components.
2. `pathVis[]` (Path Visited Array): This is the key to cycle detection. It marks which nodes are currently active in the recursion call stack for the current DFS path. A node is marked in `pathVis` when we enter its DFS function and unmarked when we exit.

The Algorithm:

1. Start a DFS from an arbitrary unvisited vertex.
2. When the DFS function is called for a `node`:
   * Mark `node` as visited in both `vis` and `pathVis`.
3. Iterate through all adjacent vertices (`it`) of the `node`:
   * If `it` is unvisited: Make a recursive DFS call to it. If this sub-call finds a cycle, propagate `true` up.
   * If `it` is already visited: Check the `pathVis` array. If `pathVis[it]` is also true, it means we've found an edge pointing back to a node that is currently in our recursion stack (an ancestor). This is a back-edge, which confirms a cycle exists. Return `true`.
4. Backtrack: If the loop finishes without finding a cycle, it means all paths from `node` are safe. Before returning, unmark `node` from the path visited array (`pathVis[node] = 0`). This is a crucial backtracking step.

***

### C++ Implementation

```cpp
#include<bits/stdc++.h>
using namespace std;

bool dfsCheck(int node, vector<int> adj[], int vis[], int pathVis[]) {
    vis[node] = 1;
    pathVis[node] = 1;

    // Traverse for adjacent nodes
    for (auto it : adj[node]) {
        // If the neighbor is not visited, recurse
        if (!vis[it]) {
            if (dfsCheck(it, adj, vis, pathVis) == true)
                return true;
        }
        // If the neighbor is visited AND is on the current path, a cycle is found.
        else if (pathVis[it]) {
            return true;
        }
    }

    // Backtrack: remove the node from the current path
    pathVis[node] = 0;
    return false;
}

bool isCyclic(int V, vector<int> adj[]) {
    int vis[V] = {0};
    int pathVis[V] = {0};

    // This loop handles graphs with disconnected components
    for (int i = 0; i < V; i++) {
        if (!vis[i]) {
            if (dfsCheck(i, adj, vis, pathVis) == true) return true;
        }
    }
    return false;
}
```

***

### Complexity Analysis

* Time Complexity: $$O(V + E)$$
* Space Complexity: $$O(V)$$
