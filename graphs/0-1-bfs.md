# 0-1 BFS

0-1 Breadth-First Search (BFS) is a specialized graph algorithm for finding the shortest path in a weighted graph where edge weights are restricted to either 0 or 1.

***

### Algorithm

Standard BFS works for unweighted graphs because it explores vertices in increasing order of their distance from the source. Dijkstra's algorithm handles general non-negative weights using a priority queue. 0-1 BFS is a clever optimization that combines the efficiency of BFS with the ability to handle these specific weights.

The core idea is to use a deque (double-ended queue) instead of a regular queue. The deque will maintain a crucial property: the distances of the vertices inside it will always be sorted (or nearly sorted).

When we process a vertex `u` and consider its neighbor `v`:

1. If the edge `(u, v)` has weight 0: We push `v` to the front of the deque. A 0-weight edge doesn't increase the path length, so we want to explore this new vertex `v` immediately, as if it were part of the same "layer" as `u`.
2. If the edge `(u, v)` has weight 1: We push `v` to the back of the deque. A 1-weight edge increases the path length, so `v` belongs to the next "layer," just like in a standard BFS.

By always processing 0-weight edges first, we ensure that when we extract a vertex from the front of the deque, we are always processing the vertex with the minimum current distance, just like Dijkstra's algorithm, but without the logarithmic overhead of a priority queue.

***

### C++ Implementation

```cpp
#include<bits/stdc++.h>
using namespace std;

const int INF = 1e9;

vector<int> bfs_01(const vector<vector<pair<int, int>>>& adj, int V, int src) {
    vector<int> dist(V, INF);
    dist[src] = 0;

    deque<int> dq;
    dq.push_front(src);

    while (!dq.empty()) {
        int u = dq.front();
        dq.pop_front();

        for (auto& edge : adj[u]) {
            int v = edge.first;
            int weight = edge.second;

            // Relaxation step
            if (dist[u] + weight < dist[v]) {
                dist[v] = dist[u] + weight;
                
                // Add to deque based on edge weight
                if (weight == 0) {
                    dq.push_front(v);
                } else {
                    dq.push_back(v);
                }
            }
        }
    }
    return dist;
}
```

***

### Complexity Analysis

* Time Complexity: $$O(V + E)$$
* Space Complexity: $$O(V)$$

***

### Problems

[KATHTHI](https://www.spoj.com/problems/KATHTHI/)

[CF 877D - Olya and Energy Drinks](https://codeforces.com/problemset/problem/877/D)

[CF 590C - Three States](https://codeforces.com/problemset/problem/590/C)

[CF 173B - Chamber of Secrets](https://codeforces.com/problemset/problem/173/B)

[CF 173C - Spiral Maximum](https://codeforces.com/problemset/problem/173/C)

[1368. Minimum Cost to Make at Least One Valid Path in a Grid&#x20;(Hard)](https://leetcode.com/problems/minimum-cost-to-make-at-least-one-valid-path-in-a-grid/description/)

