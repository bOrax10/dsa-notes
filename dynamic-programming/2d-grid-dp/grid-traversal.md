# Grid Traversal

### Problem Statement

You are given a 2D grid and are asked to find a path from a starting cell (e.g., top-left `(0,0)`) to an ending cell (e.g., bottom-right `(R-1, C-1)`). The movement is typically restricted, most commonly to only right and down steps.

The task is to calculate a property of this path, such as:

* The total number of unique paths.
* The path with the minimum or maximum sum/cost, where each cell has a value.

***

### Approach

The solution is built by iterating through the grid and calculating the answer for each cell based on the cells from which we could have arrived.

* DP State: `dp[i][j]` will store the optimal value (e.g., number of paths, minimum cost) to reach the cell at row `i` and column `j`.
* State Transition: The value for `dp[i][j]` is derived from the cells that can legally move to `(i, j)`. For standard right/down movement, these are the cells directly above (`i-1, j`) and to the left (`i, j-1`).
  * For counting paths: The number of ways to reach `(i, j)` is the sum of the ways to reach its neighbors. `dp[i][j] = dp[i-1][j] + dp[i][j-1]`
  * For finding a minimum cost path: The minimum cost to reach `(i, j)` is the cost of the cell itself plus the minimum of the costs from its neighbors. `dp[i][j] = cost[i][j] + min(dp[i-1][j], dp[i][j-1])`
* Base Case: The starting cell, `dp[0][0]`, is usually initialized to a starting value (e.g., `1` for path counting, or `cost[0][0]` for cost optimization).

***

### C++ Implementation

```cpp
#include <bits/stdc++.h>
using namespace std;

vector<vector<long long>> GridTraversal(int rows, int cols, vector<vector<bool>>& is_invalid,
    long long initial_value,
    function<long long(long long, long long)> aggregator
) {
    vector<vector<long long>> dp(rows, vector<long long>(cols, 0));

    // Set the base case
    if (!is_invalid(0, 0)) {
        dp[0][0] = initial_value;
    }

    for (int i = 0; i < rows; ++i) {
        for (int j = 0; j < cols; ++j) {
            if (i == 0 && j == 0) continue; // Skip the base case
            if (is_invalid(i, j)) continue; // Skip invalid cells

            long long from_top = (i > 0 && !is_invalid(i - 1, j)) ? dp[i - 1][j] : INF;
            long long from_left = (j > 0 && !is_invalid(i, j - 1)) ? dp[i][j - 1] : INF;
            // long long from diagonal = (i>0 && j>0 && !is_invalid(i-1,j-1)) ? dp[i-1][j-1] : INF;
            
            // aggregator = sum, min or max
            dp[i][j] = aggregator(from_top, from_left);
        }
    }
    return dp;
}
```

***

### Complexity Analysis

* Time Complexity: $$O(Rows×Cols)$$ We must visit each cell of the DP table once, and the calculation for each cell is typically constant time, $$O(1)$$.
* Space Complexity: $$O(Rows×Cols)$$ This is for storing the entire 2D DP table. This can be optimized to $$O(Cols)$$ because the calculation for each cell only depends on the previous row and the current row.

***

### Problems

[CSES Task 1638](https://cses.fi/problemset/task/1638)

[62. Unique Paths (Medium)](https://leetcode.com/problems/unique-paths/)

[63. Unique Paths II (Medium)](https://leetcode.com/problems/unique-paths-ii/description/)

[64. Minimum Path Sum (Medium)](https://leetcode.com/problems/minimum-path-sum/description/)
