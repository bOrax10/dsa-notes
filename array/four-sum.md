# Four Sum

Given an array of $$n$$ integers and a target value $$x$$, the task is to find four distinct indices in the array whose corresponding values sum up to $$x$$. If a solution exists, return the indices; otherwise, indicate that no such solution is possible.

***

### Approach

1.  $$O(N^3)$$ Solution -

    1. Sort the Array: As with previous problems, we must preserve the original indices. We store the values along with their original 1-based positions (e.g., in a vector of pairs) and then sort this structure based on the values.
    2. Fix Two Elements: The core idea is to reduce the "Four Sum" problem to a "Two Sum" problem. We can do this by using nested loops to fix two elements, `arr[i]` and `arr[j]`.
    3. Two-Pointer Scan: For each pair of fixed elements `arr[i]` and `arr[j]`, our goal is to find two other elements in the rest of the array that sum up to the remaining target: `new_target = x - arr[i] - arr[j]`. We can find this pair efficiently using a two-pointer scan.
       * Initialize a `left` pointer to `j + 1` and a `right` pointer to the end of the array (`n - 1`).
       * Calculate the sum of the two pointers: `sum = arr[left] + arr[right]`.
         * If `sum` equals `new_target`, we have found our quadruplet.
         * If `sum` is less than `new_target`, we move `left` one step to the right to get a larger sum.
         * If `sum` is greater than `new_target`, we move `right` one step to the left to get a smaller sum.
       * This two-pointer scan continues until `left` and `right` cross.

    If the outer loops complete without finding a solution, no valid quadruplet exists.
2.  $$O(N^2logN)$$ Solution -&#x20;

    The core idea is to change the problem from finding four numbers (`a + b + c + d = x`) to finding two pairs. By rearranging the equation to `a + b = x - (c + d)`, we can see that we're looking for one pair whose sum is equal to the target minus another pair's sum.

    Here's how your code implements this:

    1. Store Pair Sums: The code uses a hash map (`std::map`) to store the sums of pairs of numbers it has already seen. The map's key is the sum, and the value is the pair of indices that produced that sum.
    2. Iterate and Check: The main part of the algorithm uses two nested loops (`i` and `j`) to form a `current_pair` (`a[i]`, `a[j]`).
       * For each `current_pair`, it calculates the `required_sum` needed from another pair to reach the target `x`.
         * It then performs a quick $$O(logN)$$ lookup in the hash map to see if a pair with this `required_sum` has been seen before.
    3. Ensure Distinct Indices: If a matching pair is found in the map, a final check is performed to ensure that all four indices (the two from the `current_pair` and the two from the pair found in the map) are distinct.
    4. Populate the Map: To avoid using the same element twice, the code cleverly populates the hash map. After checking for solutions for a given `i`, it then adds all pair sums involving `a[i]` and elements _before_ it (`a[k]` where `k < i`) to the map. This ensures that when checking for a pair `(i, j)`, the map only contains pairs with indices smaller than `i`, guaranteeing the four indices will be unique.

***

### C++ Implementation

1. $$O(N^3)$$ Solution -

```cpp
#include<bits/stdc++.h>
using namespace std;

tuple<int, int, int, int> findFourSum(const vector<int> &arr, long long x)
{
    int n = arr.size();
    if (n < 4)
    {
        return {-1, -1, -1, -1};
    }

    vector<pair<int, int>> indexed_arr(n);
    for (int i = 0; i < n; ++i)
    {
        indexed_arr[i] = {arr[i], i + 1}; // {value, 1-based_index}
    }

    sort(indexed_arr.begin(), indexed_arr.end());

    // Fix the first two elements and use two pointers for the other two
    for (int i = 0; i < n - 3; ++i)
    {
        for (int j = i + 1; j < n - 2; ++j)
        {
            int left = j + 1;
            int right = n - 1;

            while (left < right)
            {
                long long current_sum = (long long)indexed_arr[i].first + indexed_arr[j].first + indexed_arr[left].first + indexed_arr[right].first;

                if (current_sum == x)
                {
                    return {indexed_arr[i].second, indexed_arr[j].second, indexed_arr[left].second, indexed_arr[right].second};
                }
                else if (current_sum < x)
                {
                    left++;
                }
                else
                {
                    right--;
                }
            }
        }
    }

    return {-1, -1, -1, -1}; // No solution found
}
```

2. $$O(N^2logN)$$ Solution -

```cpp
#include<bits/stdc++.h>
using namespace std;

tuple<int, int, int, int> findFourSum(const vector<int>& arr, long long x) {
    int n = arr.size();
    if (n < 4) {
        return {-1, -1, -1, -1};
    }

    vector<pair<int, int>> a(n);
    for (int i = 0; i < n; ++i) {
        a[i].first = arr[i];
        a[i].second = i + 1;
    }

    map<long long, pair<int, int>> pair_sums;

    for (int i = 0; i < n; ++i) {
        for (int j = i + 1; j < n; ++j) {
            long long current_sum = (long long)a[i].first + a[j].first;
            long long required_sum = x - current_sum;

            if (pair_sums.count(required_sum)) {
                pair<int, int> p = pair_sums[required_sum];

                if (p.first != a[i].second && p.first != a[j].second && 
                    p.second != a[i].second && p.second != a[j].second) {
                    return {a[i].second, a[j].second, p.first, p.second};
                }
            }
        }

        for (int k = 0; k < i; ++k) {
            long long sum = (long long)a[k].first + a[i].first;
            pair_sums[sum] = {a[k].second, a[i].second};
        }
    }

    return {-1, -1, -1, -1};
}
```

***

### Complexity Analysis

| Approach                      | Time Complexity | Space Complexity |
| ----------------------------- | --------------- | ---------------- |
| 1. Sort + Nested Two-Pointers | $$O(N^3)$$      | $$O(N)$$         |
| 2. Pair Sums with `std::map`  | $$O(N^2logN)$$  | $$O(N^2)$$       |

You can use a custom hash with a Random Seed to optimize the time complexity of the second solution to $$O(N^2)$$.

***

### Problems

[CSES Task 1642](https://cses.fi/problemset/task/1642/)
