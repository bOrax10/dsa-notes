# BFS

Breadth-First Search (BFS) is a fundamental graph traversal algorithm that explores vertices layer by layer.

***

### Algorithm

BFS explores the graph in "breadth," meaning it visits all of a node's immediate neighbors before moving on to the next level of neighbors. This level-by-level exploration is like the ripples spreading out from a stone dropped in water. This strategy naturally uses a queue data structure.

The algorithm follows these steps:

1. Start: Pick a starting vertex, mark it as visited, and add it to a queue.
2. Process Queue: While the queue is not empty:
   * Dequeue a vertex. This is the current vertex. Add it to your traversal list.
   * For the current vertex, find all of its unvisited neighbors.
   * Mark each unvisited neighbor as visited and enqueue them.
3. Repeat: This process continues until the queue is empty, which means all reachable vertices have been visited.

***

### C++ Implementation

```cpp
#include<bits/std++.h>
using namespace std;

vector<int> bfsOfGraph(int V, vector<int> adj[]) {
    // 'vis' array to keep track of visited vertices, initialized to 0 (false).
    int vis[V] = {0};
    vis[0] = 1; // Mark the starting node as visited

    queue<int> q;
    q.push(0); // Push the starting node into the queue

    vector<int> bfs; // List to store the BFS traversal

    // Iterate until the queue is empty
    while(!q.empty()) {
        // Get the front element of the queue and process it
        int node = q.front();
        q.pop();
        bfs.push_back(node);

        // Traverse all its neighbors
        for(auto it : adj[node]) {
            // If the neighbor has not been visited,
            // mark it as visited and add it to the queue.
            if(!vis[it]) {
                vis[it] = 1;
                q.push(it);
            }
        }
    }
    return bfs;
}
```

***

### Complexity Analysis

{% include "../.gitbook/includes/time-complexity-space-com....md" %}

***

### Problems

[CF 580C - Kefa and Park](https://codeforces.com/problemset/problem/580/C)

[Eagle and Dogs](https://www.spoj.com/problems/EAGLE1/)

[Ada and Island](https://www.spoj.com/problems/ADASEA/)

[CF 734E - Anton and Tree](https://codeforces.com/contest/734/problem/E)

[ABC Path](https://www.spoj.com/problems/ABCPATH/)

[Sheep](https://www.spoj.com/problems/KOZE/)

[VALIDATE THE MAZE](https://www.spoj.com/problems/MAKEMAZE/)

[200. Number of Islands&#x20;(Medium)](https://leetcode.com/problems/number-of-islands/)

[CF 813C - The Tag Game](https://codeforces.com/contest/813/problem/C)

[CF 24A - Ring road](https://codeforces.com/contest/24/problem/A)

[CF 29C - Mail Stamps](https://codeforces.com/contest/29/problem/C)

[Counting Rooms](https://cses.fi/problemset/task/1192)

[Building Roads](https://cses.fi/problemset/task/1666)

[Labyrinth](https://cses.fi/problemset/task/1193)

[Message Route](https://cses.fi/problemset/task/1667)
