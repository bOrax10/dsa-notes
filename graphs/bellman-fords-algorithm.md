# Bellman Ford's Algorithm

Bellman-Ford is an algorithm designed to find the shortest paths from a single source vertex to all other vertices in a weighted directed graph, even when some edge weights are negative. It can also detect the presence of negative cycles reachable from the source.

***

### Algorithm

Bellman-Ford works by systematically relaxing all edges in the graph. Relaxation is the process of checking if the current known shortest path to a vertex `v` can be improved by going through an intermediate vertex `u`.

The core idea is based on the following property: In a graph with `V` vertices, the shortest path from a source to any other vertex (if no negative cycles exist) can have at most `V-1` edges.

1. Initialization: Create a `dist` array of size `V`. Initialize `dist[S] = 0` and all other `dist[v] = infinity`.
2. Relaxation Loops: Repeat the following `V-1` times:
   * For every edge `(u, v)` with weight `w` in the graph:
     * If `dist[u] + w < dist[v]`, update `dist[v] = dist[u] + w`.
   * After `V-1` iterations, if there are no negative cycles reachable from `S`, the `dist` array will hold the correct shortest path distances.
3. Negative Cycle Detection: Perform one _additional_ iteration (the `V`-th iteration) over all edges:
   * If, during this iteration, any edge `(u, v)` with weight `w` can still be relaxed (i.e., `dist[u] + w < dist[v]`), it means a shorter path exists even after `V-1` iterations. This is only possible if there is a negative cycle reachable from the source `S` that involves edge `(u, v)`. In this case, we report the presence of a negative cycle.

***

### C++ Implementation

```cpp
#include<bits/stdc++.h>
using namespace std;

vector<int> bellman_ford(int V, vector<vector<int>>& edges, int S) {
    // Use a large number to represent infinity, careful with overflow
    const int INF = 1e8; 
    vector<int> dist(V, INF);
    
    // 1. Initialization
    dist[S] = 0;

    // 2. Relax all edges V-1 times
    for (int i = 0; i < V - 1; i++) {
        for (const auto& edge : edges) {
            int u = edge[0];
            int v = edge[1];
            int wt = edge[2];
            // Check for potential overflow before adding weight
            if (dist[u] != INF && dist[u] + wt < dist[v]) {
                dist[v] = dist[u] + wt;
            }
        }
    }

    // 3. Nth relaxation to check for negative cycles
    for (const auto& edge : edges) {
        int u = edge[0];
        int v = edge[1];
        int wt = edge[2];
        // If a distance can still be improved, a negative cycle exists
        if (dist[u] != INF && dist[u] + wt < dist[v]) {
            // Negative cycle detected
            return {-1}; 
        }
    }

    // No negative cycle found, return the shortest distances
    return dist;
}
```

***

### Complexity Analysis

* Time Complexity: $$O(V *E)$$
* Space Complexity: $$O(V)$$

***

### Problems

[Cycle Finding](https://cses.fi/problemset/task/1197)

[High Score](https://cses.fi/problemset/task/1673)

