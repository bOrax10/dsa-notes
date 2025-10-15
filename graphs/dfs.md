# DFS

Depth First Search (DFS) is a fundamental graph traversal algorithm that explores as far as possible along each branch before backtracking.

***

### Algorithm

DFS works like solving a maze by following one path to its very end. If you hit a dead end, you backtrack to the last junction and try a different path. This "depth-first" strategy naturally uses a stack, which is handled implicitly by recursion in the provided code.

The algorithm follows these steps:

1. Start: Pick a starting vertex.
2. Visit and Mark: Mark the current vertex as visited to avoid cycles and redundant work. Add it to your traversal list.
3. Explore Deeper: For the current vertex, pick one of its unvisited neighbors and recursively call the DFS function on that neighbor.
4. Backtrack: Once all paths from a vertex have been fully explored (i.e., the recursive calls for all its neighbors have returned), the function for the current vertex returns, effectively "backtracking" to the previous vertex.
5. Repeat: This process continues until all vertices reachable from the start have been visited.

***

### C++ Implementation

```cpp
#include<bits/stdc++.h>
using namespace std;

void dfs(int node, vector<int> adj[], int vis[], vector<int> &ls) {
    // Mark the current node as visited
    vis[node] = 1;
    // Add the node to the result list
    ls.push_back(node);
    
    // Recursively visit all unvisited neighbors
    for (auto it : adj[node]) {
        if (!vis[it]) {
            dfs(it, adj, vis, ls);
        }
    }
}

vector<int> dfsOfGraph(int V, vector<int> adj[]) {
    // 'vis' array to keep track of visited vertices, initialized to 0 (false).
    int vis[V] = {0};
    int start = 0;
    vector<int> ls; // List to store the DFS traversal

    // Start the recursive traversal from the 'start' node.
    dfs(start, adj, vis, ls);
    return ls;
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

[ABC Path](https://www.spoj.com/problems/ABCPATH/)

[CF 734E - Anton and Tree](https://codeforces.com/contest/734/problem/E)

[Sheep](https://www.spoj.com/problems/KOZE/)

[VALIDATE THE MAZE](https://www.spoj.com/problems/MAKEMAZE/)

[200. Number of Islands&#x20;(Medium)](https://leetcode.com/problems/number-of-islands/)

[CF 767C - Garland](https://codeforces.com/contest/767/problem/C)

[CF 813C - The Tag Game](https://codeforces.com/contest/813/problem/C)

[CF 24A - Ring road](https://codeforces.com/contest/24/problem/A)

[CF 29C - Mail Stamps](https://codeforces.com/contest/29/problem/C)

[Counting Rooms](https://cses.fi/problemset/task/1192)

[Labyrinth](https://cses.fi/problemset/task/1193)

[Building Roads](https://cses.fi/problemset/task/1666)
