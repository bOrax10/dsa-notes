# Floyd Warshall Algorithm

The Floyd-Warshall algorithm is a dynamic programming algorithm used to find the shortest paths between all pairs of vertices in a weighted graph. The algorithm can handle negative edge weights and can also be used to detect the presence of negative cycles.

***

### Algorithm

Floyd-Warshall works by considering intermediate vertices. It iteratively improves the distance estimate between any two vertices `i` and `j` by checking if going through an intermediate vertex `k` provides a shorter path.

1. Initialization:
   * Start with a distance matrix `dist[i][j]` initialized based on the input graph's adjacency matrix.
   * The distance from a vertex `i` to itself (`dist[i][i]`) is 0.
   * If there is a direct edge from `i` to `j` with weight `w`, `dist[i][j] = w`.
   * If there is no direct edge from `i` to `j`, `dist[i][j] = infinity`. (Your code uses `1e9` for infinity and handles `-1` input).
2. Iteration: The core of the algorithm involves three nested loops:
   * The outermost loop iterates through all possible intermediate vertices `k` (from 0 to V-1).
   * The two inner loops iterate through all possible source vertices `i` (from 0 to V-1) and destination vertices `j` (from 0 to V-1).
   * Inside the loops, the algorithm checks if the path from `i` to `j` _through_ `k` (`dist[i][k] + dist[k][j]`) is shorter than the currently known shortest path from `i` to `j` (`dist[i][j]`).
   * If it is shorter, update `dist[i][j]`: `dist[i][j] = min(dist[i][j], dist[i][k] + dist[k][j])`
3. Completion: After the loops complete (after considering all possible intermediate vertices `k`), the `dist[i][j]` matrix will contain the shortest path distances between all pairs of vertices.
4. Negative Cycle Detection: A negative cycle exists if, after the algorithm completes, any diagonal element `dist[i][i]` is negative. This indicates that starting at `i`, going through some path (presumably involving a negative cycle), and returning to `i` results in a total negative distance.

***

### C++ Implementation

```cpp
#include<bits/stdc++.h>
using namespace std;

void shortest_distance(vector<vector<int>>& matrix) {
    int n = matrix.size();
    const int INF = 1e9;

    //  Initialization: Replace -1 with INF and set diagonal to 0
    for (int i = 0; i < n; i++) {
        for (int j = 0; j < n; j++) {
            if (matrix[i][j] == -1) {
                matrix[i][j] = INF;
            }
            if (i == j) matrix[i][j] = 0;
        }
    }

    // Iteration: Consider each vertex 'k' as an intermediate vertex
    for (int k = 0; k < n; k++) {
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < n; j++) {
                // Check if path through 'k' is shorter
                // Ensure that intermediate paths are not infinity to avoid overflow
                if (matrix[i][k] != INF && matrix[k][j] != INF) {
                     matrix[i][j] = min(matrix[i][j], matrix[i][k] + matrix[k][j]);
                }
            }
        }
    }

    // Optional: Detect Negative Cycles (check if dist[i][i] < 0)
    // for (int i = 0; i < n; i++) {
    //     if (matrix[i][i] < 0) {
    //         // Negative Cycle Detected
    //     }
    // }

    // Post-processing: Convert INF back to -1 if needed
    for (int i = 0; i < n; i++) {
        for (int j = 0; j < n; j++) {
            if (matrix[i][j] == INF) {
                matrix[i][j] = -1;
            }
        }
    }
}
```

***

### Complexity Analysis

* Time Complexity: $$O(V^3)$$
* Space Complexity: $$O(V^2)$$

***

### Problems

[Shortest Routes II](https://cses.fi/problemset/task/1672)

[1334. Find the City With the Smallest Number of Neighbors at a Threshold Distance (Medium)](https://leetcode.com/problems/find-the-city-with-the-smallest-number-of-neighbors-at-a-threshold-distance/description/)
