# Cycle Detection - Undirected Graph - BFS

### Problem Statement

Given an undirected graph, the goal is to determine if it contains a cycle. A cycle is a path within the graph that starts and ends at the same vertex and includes at least one edge.

***

### Approach

The BFS-based approach works by exploring the graph level by level. We can detect a cycle if we encounter a vertex that has already been visited but is not the immediate parent of the current vertex. This indicates that there's an alternative path to an already discovered node, which closes a loop.

The algorithm uses a queue to manage the traversal and keeps track of both the current node and its parent.

1. Initialization: Start a BFS from an arbitrary unvisited vertex. To do this, create a queue and push a pair containing the starting vertex and a placeholder for its parent (e.g., -1). Mark the starting vertex as visited.
2. Level-by-Level Traversal: While the queue is not empty, dequeue a `(node, parent)` pair.
3. Neighbor Exploration: For the dequeued `node`, iterate through all of its `adjacentNode`s:
   * If `adjacentNode` is unvisited: Mark it as visited and enqueue it as a pair `{adjacentNode, node}`. This means we've discovered a new node, and the current `node` is its parent in this traversal.
   * If `adjacentNode` is already visited: We must check if it's the immediate `parent`. If `adjacentNode` is not the `parent`, it means we've found a path back to an already visited node that is not our direct predecessor. This confirms the existence of a cycle.
4. Handling Disconnected Graphs: If the graph might have multiple disconnected components, the main function should loop through all vertices and start a new BFS traversal for any vertex that hasn't been visited yet.

***

### C++ Implementation

```cpp
#include<bits/stdc++.h>
using namespace std;

bool detect(int src, vector<int> adj[], int vis[]) {
    vis[src] = 1;
    // The queue stores pairs of {node, parent_node}.
    queue<pair<int, int>> q;
    q.push({src, -1}); 

    while (!q.empty()) {
        int node = q.front().first;
        int parent = q.front().second;
        q.pop();

        // Go to all adjacent nodes
        for (auto adjacentNode : adj[node]) {
            // If adjacent node is not visited, mark and push to queue.
            if (!vis[adjacentNode]) {
                vis[adjacentNode] = 1;
                q.push({adjacentNode, node});
            }
            // If adjacent node is visited and is not its own parent, a cycle exists.
            else if (parent != adjacentNode) {
                return true;
            }
        }
    }
    // No cycle found in this component.
    return false;
}

bool isCycle(int V, vector<int> adj[]) {
    int vis[V] = {0};
    // This loop handles graphs with multiple disconnected components.
    for (int i = 0; i < V; i++) {
        if (!vis[i]) {
            if (detect(i, adj, vis)) return true;
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
