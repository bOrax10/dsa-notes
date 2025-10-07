# 0/1 Knapsack

### Problem Statement

Given a set of N items, each with a specific weight and value, and a knapsack with a maximum weight capacity, the goal is to determine the combination of items to include in the knapsack that maximizes the total value without exceeding the capacity.

The "0/1" property means that for each item, you have a binary choice: you can either take the item (1) or leave it behind (0). You cannot take a fraction of an item or take the same item multiple times.

***

### Approach

This problem is solved using dynamic programming. The core idea is to build a solution by making a decision for each item one by one. For every item, we decide whether to include it in our optimal solution or not.

#### **DP State**

We can define our state as `dp[i][w]`, representing the maximum value we can achieve using a subset of the first `i` items with a total weight capacity of `w`.

**State Transition**

When we consider the `i`-th item, we have two choices:

1. Don't Take the Item: If we don't take the `i`-th item, the maximum value is simply the best we could achieve with the first `i-1` items and the same capacity `w`. The value would be `dp[i-1][w]`.
2. Take the Item: We can only do this if the item's weight (`weight[i]`) is less than or equal to the current capacity `w`. If we take it, we get its value (`value[i]`), and the remaining capacity for the first `i-1` items is now `w - weight[i]`. The value from this choice would be `value[i] + dp[i-1][w - weight[i]]`.

The optimal solution `dp[i][w]` is the maximum of these two choices. This gives us the recurrence relation:

`dp[i][w] = max(dp[i-1][w], value[i] + dp[i-1][w - weight[i]])`

#### **Space Optimization**

We can observe that the calculation for the current row `i` only depends on the values from the _previous_ row `i-1`. This means we don't need to store the entire 2D table. We can optimize the space down to a 1D array of size `C+1`.

Let `dp[w]` be the maximum value for a capacity `w`. When we process a new item, we update this `dp` array. A crucial detail is that the inner loop for the capacity `w` must run backward, from `C` down to the item's weight. This ensures that when we calculate `dp[w]`, the value of `dp[w - weight[i]]` is from the _previous_ iteration (before the current item was considered), correctly simulating the 0/1 choice.

***

### C++ Implementation

1. 2D DP

```cpp
#include<bits/stdc++.h>
using namespace std;

long long knapsack_2d(int capacity, const vector<int>& weights, const vector<int>& values) {
    int n = weights.size();
    
    // dp[i][w]: max value using first 'i' items with capacity 'w'
    vector<vector<long long>> dp(n + 1, vector<long long>(capacity + 1, 0));

    for (int i = 1; i <= n; ++i) {
        int current_weight = weights[i - 1];
        int current_value = values[i - 1];
        
        for (int w = 0; w <= capacity; ++w) {
            // Option 1: Don't take the i-th item
            dp[i][w] = dp[i - 1][w];
            
            // Option 2: Take the i-th item (if it fits)
            if (w >= current_weight) {
                dp[i][w] = max(dp[i][w], dp[i - 1][w - current_weight] + current_value);
            }
        }
    }
    
    return dp[n][capacity];
}
```

2. Space optimized 1D DP

```cpp
#include<bits/stdc++.h>
using namespace std;

long long knapsack_1d(int capacity, const vector<int>& weights, const vector<int>& values) {
    int n = weights.size();
    
    // dp[w]: max value for a knapsack of capacity 'w'
    vector<long long> dp(capacity + 1, 0);

    // Iterate through each item
    for (int i = 0; i < n; ++i) {
        // Iterate backwards through capacities to ensure we use each item at most once
        for (int w = capacity; w >= weights[i]; --w) {
            // Decide whether to include the i-th item or not
            dp[w] = max(dp[w], dp[w - weights[i]] + values[i]);
        }
    }
    
    return dp[capacity];
}
```

***

### Complexity Analysis

* Time Complexity: $$O(N⋅C)$$
  * Where $$N$$ is the number of items and $$C$$ is the maximum capacity of the knapsack. The complexity comes from the two nested loops: one iterating through the items and the other through the capacities.
* Space Complexity: $$O(C)$$
  * The space-optimized version uses a single 1D array of size `C+1` to store the DP states. (The unoptimized 2D version would require $$O(N⋅C)$$ space).

***

### Problems

[CSES Task 1158](https://cses.fi/problemset/task/1158)

[416. Partition Equal Subset Sum (Medium)](https://leetcode.com/problems/partition-equal-subset-sum/description/)

[474. Ones and Zeroes (Medium)](https://leetcode.com/problems/ones-and-zeroes/description/)

[494. Target Sum (Medium)](https://leetcode.com/problems/target-sum/description/)

