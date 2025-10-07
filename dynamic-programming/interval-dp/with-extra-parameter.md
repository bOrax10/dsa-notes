# With Extra Parameter

### Problem Statement

In this pattern, you are given a sequence (an array or a string). The goal is to perform a series of operations on contiguous subsegments (intervals) of this sequence to achieve an optimal value (e.g., maximum points, minimum cost).

Unlike standard Interval DP, the optimal solution for an interval `[i, j]` cannot be determined solely by the solutions of its sub-intervals. The decision you make for a sub-interval is influenced by or influences elements _outside_ of it. This dependency requires an extra parameter in the DP state to carry the necessary context.

***

### Approach

The core idea is to augment the standard Interval DP state `dp[i][j]` with one or more parameters that capture the external context required to make an optimal decision.

**DP State**

The generalized state is often a 3D array: `dp[i][j][p]`

* `i`: The starting index of the interval.
* `j`: The ending index of the interval.
* `p`: The extra parameter that holds the crucial contextual information linking the interval `[i, j]` to the rest of the sequence.

This parameter `p` is the key to solving the problem. It allows a subproblem to "know" about the larger context it is a part of, enabling it to make decisions that are globally optimal, not just locally optimal.

**State Transition**

The solution for `dp[i][j][p]` is typically found by iterating through all possible split points `k` within the interval (`i <= k < j`) and combining the results of the subproblems in a recursive manner. The recurrence structure often looks like this:

`dp[i][j][p] = max/min over k (solve(i, k, p1) + solve(k + 1, j, p2) + cost_of_split/merge)`

Here, `p1` and `p2` are the new state parameters for the resulting subproblems, which depend on the original state `p` and the split point `k`. The "cost" represents the value gained or lost by performing the operation at the split point.

Here are some standard abstract roles for the parameter `p`:

* 1\. An External Tally or Count
  * Description: `p` can represent a count of elements outside the interval (typically immediately to the left or right) that share a key property with one of the interval's endpoints (e.g., `element[i]` or `element[j]`).
  * Use Case: This is common in problems involving "merge" or "grouping" operations. The solution for the interval `[i, j]` needs to know about this external group to correctly calculate the value of a merge that crosses the interval's boundary. When a recursive call is made on a sub-interval, this tally `p` may be increased or reset to zero depending on the operation.
* 2\. A Target State or Configuration
  * Description: `p` can define the desired final state for the interval `[i, j]`. Instead of simply asking for "the best score for `[i, j]`," the state asks for "the best score to transform `[i, j]` into state `p`."
  * Use Case: This is used in problems where an interval can be resolved in multiple ways, and the chosen way affects later operations. For example, `p` could represent the number of final items the interval `[i, j]` must be consolidated into.
* 3\. Boundary Conditions or Adjacent Values
  * Description: `p` (or multiple parameters) can hold the values or properties of the elements immediately adjacent to the interval, at `i-1` and `j+1`.
  * Use Case: This is crucial when the value of an operation performed _inside_ the interval `[i, j]` depends directly on its immediate neighbors. This pattern often involves solving the problem in reverse, where you decide which element in `[i, j]` is removed last, making `i-1` and `j+1` its effective neighbors.

***

### C++ Implementation

```cpp
#include<bits/stdc++.h>
using namespace std;

int solve(int i, int j, int p) {
    // Base case for an empty or invalid interval
    if (i > j) {
        return 0; 
    }
    // Base case for a single-element interval
    if (i == j) {
        // Return the value based on the element and parameter p
        return calculate_base_case(i, p);
    }
    // Return cached result if already computed
    if (memo[i][j][p] != -1) {
        return memo[i][j][p];
    }

    // Initialize the result for the current state.
    // This might be an initial split or a default operation on the whole interval.
    int result = initialize_result(i, j, p);

    // Iterate through all possible split points 'k'
    for (int k = i; k < j; ++k) {
        // Calculate the result of combining subproblems at split 'k'
        // The logic to derive new parameters (p1, p2) and the cost is problem-specific.
        int subproblem_result = combine(solve(i, k, p1), solve(k + 1, j, p2), cost_of_split);
        
        // Update the overall result (e.g., take the min or max)
        result = optimize(result, subproblem_result);
    }

    // Cache and return the final computed result for this state
    return memo[i][j][p] = result;
}
```

***

### Complexity Analysis

* Time Complexity: Generally $$O(N^3⋅P)$$ or higher
  * The state space is defined by `i`, `j`, and `p`. This gives $$N×N×P$$ states.
  * For each state, the transition involves iterating through a split point `k`, which takes $$O(N)$$ time.
  * This results in a typical time complexity of $$O(N⋅N⋅P⋅N)=O(N3⋅P)$$, where `N` is the length of the sequence and `P` is the range of the extra parameter.
* Space Complexity: $$O(N^2⋅P)$$
  * The space is determined by the size of the memoization table, which needs to store the result for each possible state `(i, j, p)`.

***

### Problems

[546. Remove Boxes (Hard)](https://leetcode.com/problems/remove-boxes/description/)
