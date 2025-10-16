# Check Bipartite - BFS

### Problem Statement

Given an undirected graph, the task is to determine if it is bipartite. A graph is bipartite if its vertices can be divided into two disjoint and independent sets, U and V, such that every edge in the graph connects a vertex in U to one in V.

A more intuitive way to think about this is: can we color the graph with just two colors (e.g., 0 and 1) such that no two adjacent vertices have the same color? An important property is that a graph is bipartite if and only if it does not contain any odd-length cycles.

***

### Approach

The BFS approach naturally explores the graph level by level, making it perfect for the two-coloring strategy. We assign colors to vertices as we discover them, ensuring that each new level of vertices gets the opposite color from the previous one.

The algorithm works by attempting to color the graph and looking for a contradiction:

1. Initialization: We use a `color` array, initially marking all vertices as uncolored (e.g., with -1). We also use a queue for the BFS traversal.
2. Start BFS: Begin a traversal from an arbitrary uncolored vertex. Assign it the first color (e.g., 0) and push it onto the queue.
3. Level-by-Level Coloring: While the queue is not empty:
   * Dequeue a `node`.
   * For each `neighbor` of the `node`:
     * If the neighbor is uncolored: This is the first time we're seeing it. We assign it the opposite color of the current `node` (`!color[node]`) and add it to the queue.
     * If the neighbor is already colored: We must check for a conflict. If the `neighbor` has the same color as the current `node` (`color[neighbor] == color[node]`), it means we've found an edge connecting two nodes of the same color. This violates the bipartite condition, and we can immediately conclude the graph is not bipartite.
4. Handling Disconnected Graphs: To ensure all parts of the graph are checked, the main function should iterate through all vertices. If a vertex is found to be uncolored, it belongs to a new component, and we start a new BFS traversal from it. If any component fails the check, the entire graph is not bipartite.

***

### C++ Implementation

```cpp
#include<bits/stdc++.h>
using namespace std;

bool check(int start, int V, vector<int> adj[], int color[]) {
    queue<int> q;
    q.push(start);
    color[start] = 0; // Assign the first color

    while (!q.empty()) {
        int node = q.front();
        q.pop();

        for (auto it : adj[node]) {
            // If the adjacent node is not yet colored,
            // assign the opposite color and add it to the queue.
            if (color[it] == -1) {
                color[it] = !color[node];
                q.push(it);
            }
            // If the adjacent node has the same color, a conflict is found.
            else if (color[it] == color[node]) {
                return false;
            }
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
            if (check(i, V, adj, color) == false) {
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

***

### Problems

[Building Teams](https://cses.fi/problemset/task/1668)

[3710. Maximum Partition Factor (Hard)](https://leetcode.com/problems/maximum-partition-factor/)

[886. Possible Bipartition (Medium)](https://leetcode.com/problems/possible-bipartition/description/)

[785. Is Graph Bipartite? (Medium)](https://leetcode.com/problems/is-graph-bipartite/description/)
