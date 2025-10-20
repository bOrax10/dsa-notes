# Prim's Algorithm

Prim's algorithm is a greedy algorithm used to find a Minimum Spanning Tree (MST) for a weighted, undirected, and connected graph.

MST - A subgraph that connects all vertices together without any cycles such that the sum of the weights of all the edges in the tree is minimized.

***

### Algorithm

Prim's algorithm builds the MST incrementally, starting from an arbitrary vertex. It maintains a set of vertices already included in the MST. At each step, it finds the minimum-weight edge that connects a vertex inside the MST set to a vertex outside the set, and adds this edge and the corresponding outside vertex to the MST set.

This process continues until all vertices are included in the MST. The greedy choice of always picking the cheapest edge connecting the current tree to a new vertex guarantees that the final tree will have the minimum possible total weight.

A priority queue is typically used to efficiently find the minimum-weight edge connecting the growing MST to the remaining vertices.

1. Initialization:
   * Choose a starting vertex (e.g., vertex 0).
   * Create a `visited` array (or set) to track vertices included in the MST.
   * Initialize a min-priority queue. Add the starting vertex to the PQ with a weight of 0 ( `{0, start_node}` ).
   * Initialize the MST weight sum to 0.
2. Main Loop: While the priority queue is not empty:
   * Extract Minimum: Extract the vertex `node` with the minimum weight `wt` from the priority queue.
   * Check Visited: If `node` has already been visited, skip it (this edge is internal to the MST or leads to an already included vertex via a more expensive path found earlier).
   * Add to MST: Mark `node` as visited and add its connecting edge weight `wt` to the total MST `sum`.
   * Explore Neighbors: For each neighbor `adjNode` of `node` with edge weight `edW`:
     * If `adjNode` has not been visited, add it to the priority queue as `{edW, adjNode}`. The priority queue will automatically handle finding the minimum edge connecting to any unvisited neighbor discovered so far.

***

### C++ Implementation

```cpp
#include<bits/stdc++.h>
using namespace std;
   
int spanningTree(int V, vector<vector<int>> adj[]) {
    // Min-priority queue stores pairs {weight, node}.
    priority_queue<pair<int, int>,
                   vector<pair<int, int>>, greater<pair<int, int>>> pq;

    // Visited array to keep track of vertices included in the MST.
    vector<int> vis(V, 0);

    // Start from node 0 with weight 0.
    pq.push({0, 0});
    int sum = 0; // Stores the sum of edge weights in the MST

    // Continue until the priority queue is empty (or V edges are added for MST)
    while (!pq.empty()) {
        // Extract the edge with the minimum weight connecting to an unvisited node
        auto it = pq.top();
        pq.pop();
        int wt = it.first;
        int node = it.second;

        // If the node is already visited, skip it.
        if (vis[node] == 1) continue;

        // Include the node in MST: mark visited and add edge weight to sum.
        vis[node] = 1;
        sum += wt;

        // Explore neighbors of the newly added node.
        for (auto edge : adj[node]) {
            int adjNode = edge[0];
            int edW = edge[1];
            // If the neighbor is not yet in the MST, add the connecting edge to the PQ.
            if (!vis[adjNode]) {
                pq.push({edW, adjNode});
            }
        }
    }
    // Return the total weight of the MST.
    return sum;
} 
```

***

### Complexity Analysis

* Time Complexity: $$O(ElogV)$$
* Space Complexity: $$O(V+E)$$

***

### Problems

[Road Reparation](https://cses.fi/problemset/task/1675)

[1584. Min Cost to Connect All Points (Medium)](https://leetcode.com/problems/min-cost-to-connect-all-points/description/)

