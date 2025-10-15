---
title: Time Complexity - Space Com...
---

* Time Complexity - $$O(V + 2E)$$ for undirected graphs & $$O(V + E)$$ for directed graphs - Where V is the number of vertices and E is the number of edges.
* Space Complexity - $$O(V)$$ -  for visited array. $$O(W)$$ for the queue, where W is the maximum width of the graph. In the worst case (a complete graph or a star graph), the queue might hold nearly all vertices, making the space complexity $$O(V)$$.
