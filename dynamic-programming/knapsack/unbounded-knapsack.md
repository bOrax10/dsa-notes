# Unbounded Knapsack

In this pattern, we are given a set of items and we can use any item multiple times to fill a "knapsack" of a certain capacity.&#x20;

***

### Problem Statement

Given a target value `N` and a set of items `C = {c_1, c_2, ...}`, where each item has a "weight" or "value", our goal is to compute some aggregate result for reaching `N` by combining these items. We can use each item an unlimited number of times.

The goal can be:

* **Counting:** Find the total number of distinct ways to form the sum `N`.
* **Optimization:** Find the minimum or maximum number of items required to form the sum `N`.

This maps to knapsack terminology:

* **Knapsack Capacity (`W`):** The target value `N`.
* **Item Weights (`w_i`):** The value of each item `c_i` in our set `C`.

***

### Approach

#### 2D DP&#x20;

The most straightforward way to formalize the state is with two dimensions: `dp[i][j]`, representing the answer for a knapsack of capacity `j` using only the **first `i` items** from our set.

* **State:** `dp[i][j]` = The result for target `j` using items from index `0` to `i-1`.
* **Base Case:** `dp[0][0]` is the solution for a target of 0. For counting, it's `1`. For optimization, it's `0`.
* **Recurrence:** For each item `i` and capacity `j`, we have two choices:
  1. **Don't include item `i-1`:** The result is the same as solving for capacity `j` with the first `i-1` items. This is `dp[i-1][j]`.
  2. **Include item `i-1`:** We use the item `i-1` (with weight `c_{i-1}`). The result depends on the solution for the remaining capacity `j - c_{i-1}`. Since we can use item `i-1` again, we look up `dp[i][j - c_{i-1}]`.

This gives the general 2D recurrence: `dp[i][j] = AGGREGATE( dp[i-1][j], operation(dp[i][j - c_{i-1}]) )` \* `AGGREGATE` is the function we use to combine results (e.g., `+` for counting, `min` for optimization). \* `operation` is what we do with the subproblem result (e.g., just the value, or `value + 1`).

```cpp
// Time: O(N * C), Space: O(N * C)
// N = number of items, C = capacity
vector<vector<long long>> dp(N + 1, vector<long long>(C + 1, INITIAL_VALUE));

// Base case for dp[0][0]
dp[0][0] = BASE_CASE_VALUE;

for (int i = 1; i <= N; ++i) {
    int item_weight = items[i-1];
    for (int j = 0; j <= C; ++j) {
        // Option 1: Don't use the current item i-1
        dp[i][j] = dp[i-1][j];
        
        // Option 2: Use the current item i-1
        if (j >= item_weight) {
            // Apply the specific aggregate and operation for your problem
            // e.g., for counting: dp[i][j] = (dp[i-1][j] + dp[i][j - item_weight]) % MOD;
            // e.g., for min coins: dp[i][j] = min(dp[i-1][j], 1 + dp[i][j - item_weight]);
        }
    }
}
return dp[N][C];
```

#### 1D DP Optimization

Notice that in the 2D recurrence `dp[i][j] = AGGREGATE(dp[i-1][j], ...)` the calculation for row `i` only depends on values from row `i-1` and other values in the _current_ row `i` (specifically, `dp[i][j - item_weight]`). This dependency allows us to optimize away the first dimension of the DP table.

We can use a single array `dp[j]` where:

* `dp[j]` (before an update in the inner loop) stores the value from the previous row, `dp[i-1][j]`.
* When we calculate the new `dp[j]`, we use `dp[j - item_weight]`, which has _already been updated_ in this same pass for item `i`. This correctly represents `dp[i][j - item_weight]`.

This collapses the 2D recurrence into a simpler 1D formula, significantly reducing space complexity.

```cpp
// Time: O(N * C), Space: O(C)
// N = number of items, C = capacity
vector<long long> dp(C + 1, INITIAL_VALUE);
dp[0] = BASE_CASE_VALUE;

// This loop order is for COMBINATIONS (order doesn't matter)
for (int i = 0; i < N; ++i) {
    int item_weight = items[i];
    for (int j = item_weight; j <= C; ++j) {
        // The 1D update combines the two choices from the 2D version
        // e.g., for counting: dp[j] = (dp[j] + dp[j - item_weight]) % MOD;
        // e.g., for min coins: dp[j] = min(dp[j], 1 + dp[j - item_weight]);
    }
}
return dp[C];
```

***

#### **Order of Loop in 1D DP:**

*   **Order Does Not Matter (Combinations):** Outer loop iterates through **items**, inner loop iterates through **capacity**. This follows the 2D-to-1D optimization logic and ensures each set of items is counted only once.

    ```cpp
    for (int item : items) {
        for (int j = item; j <= N; ++j) { /* ... */ }
    }

    ```
*   **Order Matters (Permutations):** Outer loop iterates through **capacity**, inner loop iterates through **items**. This structure reconsiders every item at each capacity `j`, counting all possible orderings.

    ```cpp
    for (int j = 1; j <= N; ++j) {
        for (int item : items) { /* ... */ }
    }

    ```

***

### Complexity Analysis

* **Time Complexity:** `O(N * C)`
  * `N` is the target sum (capacity).
  * `C` is the number of items.
* **Space Complexity:**
  * 2D DP: `O(N * C)`
  * 1D DP: `O(N)` (where `N` is the capacity)

***

### Problems

[CSES Task 1633](https://cses.fi/problemset/task/1633)

[CSES Task 1634](https://cses.fi/problemset/task/1634)

[CSES Task 1635](https://cses.fi/problemset/task/1635)

[CSES Task 1636](https://cses.fi/problemset/task/1636)

[CSES Task 1637](https://cses.fi/problemset/task/1637)

[Frog I](https://atcoder.jp/contests/dp/tasks/dp_a)

[Frog II](https://atcoder.jp/contests/dp/tasks/dp_b)

[322. Coin Change (Medium)](https://leetcode.com/problems/coin-change/description/)

[518. Coin Change II (Medium)](https://leetcode.com/problems/coin-change-ii/description/)

