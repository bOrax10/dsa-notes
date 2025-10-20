# Prim's Algorithm - MST

Prim's algorithm is a greedy algorithm used to find a Minimum Spanning Tree (MST) for a weighted, undirected, and connected graph.

MST - A subgraph that connects all vertices together without any cycles such that the sum of the weights of all the edges in the tree is minimized.

***

### Algorithm

#### Sparse Graphs

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

#### Dense Graphs

The algorithm works by iteratively growing the Minimum Spanning Tree (MST) one vertex at a time. It maintains a set of vertices already included in the MST. At each step, it finds the cheapest edge connecting a vertex _inside_ the MST to a vertex _outside_ the MST and adds that edge and vertex to the tree.

1. Data Structures:
   * `min_edge[v]`: Stores the minimum weight of an edge connecting vertex `v` (which is outside the MST) to _any_ vertex currently inside the MST. Initialized to infinity for all vertices except the start vertex (which is 0).
   * `visited[v]`: A boolean flag indicating whether vertex `v` has already been added to the MST. Initialized to false.
2. Algorithm Steps:
   * Repeat the following V times (once for each vertex to be added to the MST):
     * Find Minimum Edge: Scan through _all_ vertices. Find the unvisited vertex `u` that has the smallest value in the `min_edge` array. This `u` is the next vertex to add to the MST, connected by the cheapest possible edge.&#x20;
     * Add to MST: Mark vertex `u` as visited. Add the value `min_edge[u]` to the total weight of the MST.
     * Update Neighbors: Iterate through all neighbors `v` of the newly added vertex `u`. If the edge weight `(u, v)` is smaller than the current `min_edge[v]`, update `min_edge[v]` to this smaller weight. This reflects that we've found a potentially cheaper way to connect `v` to the growing MST (via `u`).

This process continues until all V vertices are included. Because finding the minimum edge in each of the V iterations requires scanning up to V vertices, the total time complexity is O(V²). For dense graphs where the number of edges E is close to V², this can be as efficient as or even faster than the O(E log V) heap-based versions due to lower constant factors and simpler data structures.

***

### C++ Implementation

1. Sparse Graphs - Priority Queue

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

2. Dense Graphs - Naive&#x20;

```cpp
#include<bits/stdc++.h>
using namespace std;

const int INF = INT_MAX;

int spanningTree_Naive(int V, const vector<vector<pair<int, int>>>& adj) {
    if (V == 0) return 0;

    // min_edge[i]: minimum weight edge connecting vertex i to the MST
    vector<int> min_edge(V, INF);
    // visited[i]: true if vertex i is in the MST
    vector<bool> visited(V, false);
    int mst_weight_sum = 0;

    // Start with vertex 0
    min_edge[0] = 0;

    for (int count = 0; count < V; ++count) {
        // Find the unvisited vertex 'u' with the smallest min_edge
        int u = -1;
        int min_val = INF;
        for (int v_idx = 0; v_idx < V; ++v_idx) {
            if (!visited[v_idx] && min_edge[v_idx] < min_val) {
                min_val = min_edge[v_idx];
                u = v_idx;
            }
        }

        // If no vertex can be reached (disconnected graph), break or handle error
        if (u == -1) break; // Or return an error value if graph must be connected

        // Add vertex 'u' to the MST
        visited[u] = true;
        mst_weight_sum += min_edge[u];

        // Update min_edge values for neighbors of 'u'
        for (const auto& edge : adj[u]) {
            int v = edge.first;
            int weight = edge.second;
            if (!visited[v] && weight < min_edge[v]) {
                min_edge[v] = weight;
            }
        }
    }

    return mst_weight_sum;
}
```

***

### Complexity Analysis

| **Implementation**      | **Time Complexity** | **Space Complexity** | **Best For**  |
| ----------------------- | ------------------- | -------------------- | ------------- |
| 1. Priority Queue / Set | O(E log V)          | O(V + E)             | Sparse Graphs |
| 2. Naive                | O(V²)               | O(V + E)             | Dense Graphs  |

***

### Problems

[Road Reparation](https://cses.fi/problemset/task/1675)

[1584. Min Cost to Connect All Points (Medium)](https://leetcode.com/problems/min-cost-to-connect-all-points/description/)

