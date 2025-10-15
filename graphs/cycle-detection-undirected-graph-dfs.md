# Cycle Detection - Undirected Graph - DFS

### Problem Statement

Given an undirected graph, the task is to determine if it contains a cycle. A cycle is a path in the graph that starts and ends at the same vertex, and which contains at least one edge.

***

### Approach

The core idea is to perform a DFS traversal while keeping track of the parent of each vertex in the traversal path. A cycle is detected if, during the traversal from a current vertex, we encounter an adjacent vertex that has already been visited and is not the immediate parent of the current vertex.

This indicates that we've found a "back-edge" – an alternative path back to a vertex that is already in our current recursion stack, which confirms the presence of a cycle.

The algorithm follows these steps:

1. Start a DFS from an arbitrary unvisited vertex.
2. In the DFS function, keep track of the current `node` and its `parent` in the traversal tree.
3. Mark the current `node` as visited.
4. Iterate through all adjacent vertices of the `node`. For each `adjacentNode`:
   * If `adjacentNode` has not been visited: Make a recursive DFS call to it, with the current `node` as its new parent. If this recursive call finds a cycle, propagate `true` up.
   * If `adjacentNode` is already visited: Check if it's the `parent` of the current `node`. If it is not the parent, we have found a back-edge, and thus a cycle exists. Return `true`.
5. If the graph has disconnected components, repeat the process for all unvisited vertices.

***

### C++ Implementation

```cpp
#include<bits/stdc++.h>
using namespace std;

bool dfs(int node, int parent, int vis[], vector<int> adj[]) {
    vis[node] = 1;

    // Visit all adjacent nodes
    for (auto adjacentNode : adj[node]) {
        // If the adjacent node is not visited, recurse
        if (!vis[adjacentNode]) {
            if (dfs(adjacentNode, node, vis, adj) == true) {
                return true;
            }
        }
        // If the adjacent node is visited and is not the immediate parent,
        // we have found a cycle.
        else if (adjacentNode != parent) {
            return true;
        }
    }
    return false;
}

bool isCycle(int V, vector<int> adj[]) {
    int vis[V] = {0};
    // This loop handles graphs with disconnected components
    for (int i = 0; i < V; i++) {
        if (!vis[i]) {
            if (dfs(i, -1, vis, adj) == true) return true;
        }
    }
    return false;
}
```

***

### Complexity Analysis

* Time Complexity: $$O(V + E)$$
* Space Complexity: $$O(V)$$

***

### Problems

[Round Trip](https://cses.fi/problemset/task/1669)

