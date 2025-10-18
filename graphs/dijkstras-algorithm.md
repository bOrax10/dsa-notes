# Dijkstra's Algorithm

Dijkstra's algorithm is a fundamental algorithm for finding the shortest paths from a single source vertex to all other vertices in a weighted graph with non-negative edge weights.

***

### Algorithm

Dijkstra's algorithm uses a greedy strategy. It maintains a set of vertices whose shortest distance from the source is finalized. It repeatedly selects the vertex `u` with the minimum current distance among those not yet finalized, marks it as finalized, and then relaxes all edges going out from `u`. Relaxation means checking if the path through `u` offers a shorter route to its neighbors.

1\. Initialization

* Create a `dist` array to store the shortest distance found so far from the source `S` to every other vertex. Initialize `dist[S] = 0` and all other `dist[v] = infinity`.
* Initialize the chosen data structure (priority queue or set) by adding the source vertex with its distance: `{0, S}`.

2\. Main Loop - Processing Vertices The core difference lies in how we efficiently find the vertex with the minimum distance and handle updates.

a. Using a Priority Queue (Min-Heap)

* Extract Minimum: While the priority queue is not empty, extract the pair `{distance, vertex}` with the smallest `distance`. Let this be `{dis, node}`.
* Stale Check: A key step with the priority queue is checking if the extracted distance `dis` is greater than the already known shortest distance `dist[node]`. If it is, this pair represents an older, longer path that we've already improved upon, so we simply ignore it (`continue`).
* Relaxation: If the extracted distance is current (`dis <= dist[node]`), iterate through all neighbors `v` of `node` with edge weight `w`. If `dist[node] + w < dist[v]`, we've found a shorter path to `v`. Update `dist[v] = dist[node] + w` and push the new pair `{dist[v], v}` onto the priority queue. We _don't_ remove the old, longer path entry for `v` if it exists; we just add the improved one.

b. Using a Set

* Extract Minimum: While the set is not empty, find the element with the smallest distance. Since `std::set` keeps elements sorted, this is simply the first element (`st.begin()`). Extract this pair `{dis, node}` and remove it from the set.
* Relaxation: Iterate through all neighbors `v` of `node` with edge weight `w`. If `dist[node] + w < dist[v]`, we've found a shorter path.
  * Remove Old Entry: If `v` was already in the set with its old, longer distance (`dist[v] != infinity`), we must remove the pair `{dist[v], v}` from the set.
  * Insert New Entry: Update `dist[v] = dist[node] + w` and insert the new pair `{dist[v], v}` into the set.

3\. Termination The algorithm terminates when the priority queue or set is empty. The `dist` array then contains the shortest path distances from the source `S`.

Crucial Constraint: Dijkstra's algorithm's greedy approach works only if all edge weights are non-negative.

***

### C++ Implementation

1. Priority Queue

```cpp
#include<bits/stdc++.h>
using namespace std;

vector<int> dijkstra(int V, vector<vector<int>> adj[], int S) {
    // Min-priority queue stores {distance, node}
    priority_queue<pair<int, int>, vector<pair<int, int>>, greater<pair<int, int>>> pq;

    vector<int> distTo(V, INT_MAX);

    distTo[S] = 0;
    pq.push({0, S}); // {distance, source_node}

    while (!pq.empty()) {
        int dis = pq.top().first;
        int node = pq.top().second;
        pq.pop();

        // Skip if we found a shorter path already
        if (dis > distTo[node]) continue;

        // Relax edges for neighbors
        for (auto it : adj[node]) {
            int v = it[0];
            int w = it[1];
            if (distTo[node] + w < distTo[v]) {
                distTo[v] = distTo[node] + w;
                pq.push({distTo[v], v}); // Push the updated distance
            }
        }
    }
    return distTo;
}
```

2. Set

```cpp
#include<bits/stdc++.h>
using namespace std;

vector <int> dijkstra(int V, vector<vector<int>> adj[], int S) {
    // Set stores {distance, node} ordered by distance
    set<pair<int,int>> st;

    vector<int> dist(V, INT_MAX);

    dist[S] = 0;
    st.insert({0, S}); // Insert source

    while(!st.empty()) {
        // Extract the node with the minimum distance
        auto it = *(st.begin());
        int dis = it.first;
        int node = it.second;
        st.erase(it);

        // Relax edges for neighbors
        for(auto it : adj[node]) {
            int adjNode = it[0];
            int edgW = it[1];

            if(dis + edgW < dist[adjNode]) {
                // Erase the old entry if it exists
                if(dist[adjNode] != INT_MAX)
                    st.erase({dist[adjNode], adjNode});

                // Update the distance and insert the new entry
                dist[adjNode] = dis + edgW;
                st.insert({dist[adjNode], adjNode});
             }
        }
    }
    return dist;
}
```

***

### Complexity Analysis

* Time Complexity: $$O(ElogV)$$
* Space Complexity: $$O(V)$$

***

### Problems

[Shortest Routes I](https://cses.fi/problemset/task/1671/)

[Flight Discount](https://cses.fi/problemset/task/1195)

[Flight Routes](https://cses.fi/problemset/task/1196)

[Delivery Route](https://www.spoj.com/problems/DELIVER/)

[COSTLY CHESS](https://www.spoj.com/problems/CCHESS/)

[Easy Dijkstra Problem](https://www.spoj.com/problems/EZDIJKST/en/)

[Ada and Trip](https://www.spoj.com/problems/ADATRIP/)

[The Shortest Path](https://www.spoj.com/problems/SHPATH/)

[743. Network Delay Time (Medium)](https://leetcode.com/problems/network-delay-time/description/)

[1514. Path with Maximum Probability (Medium)](https://leetcode.com/problems/path-with-maximum-probability/)

[CF 20C - Dijkstra?](https://codeforces.com/problemset/problem/20/C)

[CF 59E - Shortest Path](https://codeforces.com/problemset/problem/59/E)

[CF 449B - Jzzhu and Cities](https://codeforces.com/problemset/problem/449/B)

[CF 464E - The Classic Problem](https://codeforces.com/problemset/problem/464/E)

[CF 567E - President and Roads](https://codeforces.com/problemset/problem/567/E)

[CF 715B - Complete The Graph](https://codeforces.com/problemset/problem/715/B)

[CF 843D - Dynamic Shortest Path](https://codeforces.com/problemset/problem/843/D)

[CF 757F - Team Rocket Rises Again](https://codeforces.com/contest/757/problem/F)

[CF 827F - Dirty Arkady's Kitchen](https://codeforces.com/contest/827/problem/F)
