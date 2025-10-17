# Toposort - DFS

### Problem Statement

Given a Directed Acyclic Graph (DAG), the task is to find a linear ordering of its vertices such that for every directed edge from vertex `u` to vertex `v`, vertex `u` comes before vertex `v` in the ordering. This ordering is not unique.

***

### Approach

The DFS-based approach leverages the nature of the recursion stack. A vertex is only considered "finished" (and added to our sort) after all of its descendants have been fully explored. This naturally gives us the reverse of the order we want. A stack is the perfect data structure to reverse this order for the final result.

The algorithm works as follows:

1. Initialization: We need a `visited` array to keep track of visited nodes and a `stack` to store the topologically sorted vertices.
2. Start DFS: Iterate through all vertices of the graph. If a vertex has not been visited, start a DFS traversal from it. This handles disconnected components.
3. Recursive Exploration: In the DFS function for a given `node`:
   * Mark the `node` as visited.
   * Recursively call DFS for all its unvisited neighbors.
   * Crucial Step: After the recursive calls for all neighbors have returned (meaning all descendants of `node` have been fully explored and pushed onto the stack), push the current `node` onto the stack.
4. Final Result: Once the DFS is complete for all vertices, the stack contains the vertices in reverse topological order. Popping elements from the stack one by one will give a valid topological sort.

***

### C++ Implementation

```cpp
#include<bits/stdc++.h>
using namespace std;

void dfs(int node, int vis[], stack<int> &st, vector<int> adj[]) {
    vis[node] = 1;
    // First, visit all unvisited neighbors
    for (auto it : adj[node]) {
        if (!vis[it]) {
            dfs(it, vis, st, adj);
        }
    }
    // After all neighbors are visited, push the current node to the stack
    st.push(node);
}

vector<int> topoSort(int V, vector<int> adj[]) {
    int vis[V] = {0};
    stack<int> st;

    // Handle disconnected components by iterating through all vertices
    for (int i = 0; i < V; i++) {
        if (!vis[i]) {
            dfs(i, vis, st, adj);
        }
    }

    // Extract the sorted order from the stack
    vector<int> ans;
    while (!st.empty()) {
        ans.push_back(st.top());
        st.pop();
    }
    return ans;
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
