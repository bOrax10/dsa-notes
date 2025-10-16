# Cycle Detection - Directed Graph - BFS

### Problem Statement

Given a directed graph, the task is to determine if it contains a cycle. A cycle in a directed graph is a path that starts and ends at the same vertex, following the direction of the edges.

***

### Approach

The algorithm's core idea is based on a property of Directed Acyclic Graphs (DAGs): a DAG must have at least one vertex with an in-degree of 0 (a vertex with no incoming edges). Kahn's algorithm attempts to generate a topological sort of the graph. If it succeeds in sorting all the vertices, the graph is acyclic. If it gets "stuck," a cycle must exist.

The process works as follows:

1. Compute In-degrees: First, calculate the in-degree for every vertex in the graph by iterating through all the edges.
2. Initialize the Queue: Create a queue and add all vertices that currently have an in-degree of 0. These are the starting points of our topological sort.
3. Process Vertices (BFS):
   * Start a BFS-like process. Dequeue a vertex from the queue and increment a counter for the number of vertices successfully processed.
   * Think of this dequeued vertex as being "removed" from the graph.
   * For each neighbor of the removed vertex, decrease its in-degree by 1. This simulates removing the edges that originated from the processed vertex.
   * If a neighbor's in-degree becomes 0, it means all of its prerequisite vertices have been processed. Add this neighbor to the queue.
4. Check for Cycles: After the queue is empty, compare the counter of processed vertices with the total number of vertices (`V`).
   * If `counter == V`, it means we were able to process every single vertex. This is only possible in a DAG. Therefore, the graph has no cycle.
   * If `counter < V`, it means some vertices were never added to the queue because their in-degree never dropped to 0. This can only happen if they are part of a cycle, which creates a circular dependency. In this case, the graph has a cycle.

***

### C++ Implementation

```cpp
#include<bits/stdc++.h>
using namespace std;

bool isCyclic(int V, vector<int> adj[]) {
    // Compute in-degrees for all vertices
    vector<int> indegree(V, 0);
    for (int i = 0; i < V; i++) {
        for (auto it : adj[i]) {
            indegree[it]++;
        }
    }

    // Initialize the queue with all nodes having an in-degree of 0
    queue<int> q;
    for (int i = 0; i < V; i++) {
        if (indegree[i] == 0) {
            q.push(i);
        }
    }

    int count = 0;
    // Process vertices in a BFS-like manner
    while (!q.empty()) {
        int node = q.front();
        q.pop();
        count++; // Increment count of topologically sorted nodes

        // Reduce the in-degree of all neighbors
        for (auto it : adj[node]) {
            indegree[it]--;
            // If a neighbor's in-degree becomes 0, add it to the queue
            if (indegree[it] == 0) {
                q.push(it);
            }
        }
    }

    // Check for a cycle
    // If the count of processed nodes is not equal to the total number of vertices,
    // then the graph has a cycle.
    return count != V;
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
