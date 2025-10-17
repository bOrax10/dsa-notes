# Kahn's Algorithm - Toposort - BFS

### Problem Statement

Given a Directed Acyclic Graph (DAG), the task is to find a linear ordering of its vertices such that for every directed edge from vertex `u` to vertex `v`, vertex `u` comes before vertex `v` in the ordering. This ordering is not unique.

***

### Algorithm

Kahn's algorithm works by systematically processing vertices that have no remaining dependencies. It uses a Breadth-First Search (BFS) approach based on the in-degree of each vertex (the number of incoming edges).

The core idea is that a vertex with an in-degree of 0 has no prerequisites and can be placed first in the sorted order. The algorithm proceeds as follows:

1. Compute In-degrees: Calculate the in-degree for every vertex in the graph by iterating through all the edges.
2. Initialize Queue: Create a queue and add all vertices that currently have an in-degree of 0. These are the starting points of our sort.
3. Process Vertices: While the queue is not empty:
   * Dequeue a vertex and add it to our final topological sort list.
   * For each neighbor of the dequeued vertex, decrease its in-degree by 1. This simulates "fulfilling" a dependency.
   * If any neighbor's in-degree becomes 0, it means all of its prerequisites have been met, so we enqueue it.

This process continues until the queue is empty. If the final sorted list contains all the vertices, a valid topological order has been found. If not, the graph contains a cycle.

***

### C++ Implementation

```cpp
#include<bits/stdc++.h

vector<int> topoSort(int V, vector<int> adj[]) {
    // 1. Compute in-degrees for all vertices
    vector<int> indegree(V, 0);
    for (int i = 0; i < V; i++) {
        for (auto it : adj[i]) {
            indegree[it]++;
        }
    }

    // 2. Initialize the queue with all nodes having an in-degree of 0
    queue<int> q;
    for (int i = 0; i < V; i++) {
        if (indegree[i] == 0) {
            q.push(i);
        }
    }
    
    vector<int> topo;
    // 3. Process vertices in a BFS-like manner
    while (!q.empty()) {
        int node = q.front();
        q.pop();
        topo.push_back(node);

        // "Remove" the node by decrementing its neighbors' in-degrees
        for (auto it : adj[node]) {
            indegree[it]--;
            // If a neighbor's in-degree becomes 0, add it to the queue
            if (indegree[it] == 0) {
                q.push(it);
            }
        }
    }
    return topo;
}
```

***

### Complexity Analysis

* Time Complexity: $$O(V + E)$$
* Space Complexity: $$O(V)$$

***

### Problems

[Course Schedule](https://cses.fi/problemset/task/1679)

[207. Course Schedule (Medium)](https://leetcode.com/problems/course-schedule/description/)

[210. Course Schedule II (Medium)](https://leetcode.com/problems/course-schedule-ii/description/)

[CF 2143C - Max Tree](https://codeforces.com/contest/2143/problem/C)
