---
description: >-
  This is a more abstract but powerful pattern where the DP state doesn't
  represent a spatial grid but rather a relationship between a position in a
  sequence and a state at that position.
---

# DP on Profiles/States

### Problem Statement

You are given a sequence of length N. Your task is to construct or evaluate this sequence, where for each position `i`, its value or "state" is constrained by the state of the preceding element at `i-1`. The goal is to count the total number of valid sequences or find an optimal sequence according to some criteria.

***

### Approach

The solution involves building a 2D DP table where one dimension tracks the position in the sequence and the other tracks the possible states at that position.

* DP State: `dp[i][j]` = The value (e.g., number of ways, min/max cost) for a valid prefix of the sequence of length `i` which ends in state `j`.
  * `i`: Represents the index or position in the sequence (from `1` to `N`).
  * `j`: Represents a possible state or value at that position (from `1` to `M`, where `M` is the number of states).
* State Transition: The value for `dp[i][j]` is calculated by aggregating the values from all states `k` at the previous position `i-1` that can legally transition to state `j` at position `i`.
  * For counting problems: `dp[i][j] = sum(dp[i-1][k])` for all states `k` that can transition to `j`.
  * For optimization problems: `dp[i][j] = value[i][j] + min/max(dp[i-1][k])` for all states `k` that can transition to `j`.
* Base Case: The base case is usually the first row (`dp[1]` or `dp[0]`), which is initialized based on the problem's starting conditions.
* Final Answer: The final answer is typically the sum (for counting) or the min/max (for optimization) of all values in the last row of the DP table (`dp[N]`), as the sequence can end in any valid state.

***

### C++ Implementation

```cpp
#include<bits/stdc++.h>
using namespace std;

// A "fixed value" (e.g., constraints[0]=k) means the state MUST be k.
// A "wildcard" (constraints[0]=0) means any state j is allowed.
long long solveProfileDP_2D(int n, int m, const vector<int>& constraints) {
    // For counting: 0. For min-cost: INF. For max-score: -INF.
    long long initial_value = 0; 
    vector<vector<long long>> dp(n, vector<long long>(m + 1, initial_value));

    for (int j = 1; j <= m; ++j) {
        if (is_state_valid(0, j, constraints)) {
            dp[0][j] = 1; // Or a starting cost
        }
    }

    for (int i = 1; i < n; ++i) {
        for (int j = 1; j <= m; ++j) {
            
            // Check if state 'j' is allowed at the current position 'i'
            if (is_valid_state(i, j, constraints)) {
            
                // Iterate through ALL possible previous states 'k'
                for (int k = 1; k <= m; ++k) {                   
                
                    // Check if a transition from previous state 'k' to current state 'j' is valid
                    // or explicitly consider only valid transitions
                    if (is_valid_transition(k, j)) {
                
                        // Aggregate the value from dp[i-1][k]
                        dp[i][j] = aggregate(dp[i][j], dp[i-1][k]);
                    }
                }
                
                // Optional: Finalize the value, e.g., by adding a cost for the current state
                // dp[i][j] += cost[i][j];
            }
        }
    }

    long long final_answer = initial_value;
    for (int j = 1; j <= m; ++j) {
        final_answer = aggregate(final_answer, dp[n - 1][j]);
    }
    
    return final_answer;
}
```

***

### Complexity Analysis

* &#x20;Time Complexity: $$O(n⋅m^2)$$

Note on Optimization: For many problems, the transition from a state `j` is only possible from a small, constant number of previous states (e.g., `j-1`, `j`, `j+1`). In such cases, the innermost loop becomes constant time, $$O(1)$$, and the overall time complexity reduces to a more efficient $$O(n⋅m)$$.

* Space Complexity: $$O(n⋅m)$$

Note on Optimization: As discussed previously, since the calculation for any row `i` only depends on the values in the preceding row `i-1`, the space can be optimized to $$O(m)$$ by storing only the previous and current rows.

***

### Problems

[CSES Task 1746](https://cses.fi/problemset/task/1746)

[931. Minimum Falling Path Sum (Medium)](https://leetcode.com/problems/minimum-falling-path-sum/description/)

[1219. Path with Maximum Gold (Medium)](https://leetcode.com/problems/path-with-maximum-gold/description/)
