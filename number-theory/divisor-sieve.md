# Divisor Sieve

The goal is to create a lookup table that stores the total number of positive divisors for every integer from 1 up to a specified maximum value, `N`.

***

### Approach

Instead of factoring each number individually, the divisor sieve iterates through all possible divisors and marks their multiples. This "distributes" the work efficiently across the range.

The logic is straightforward:

1. Create an integer array, `divisors`, of size `N + 1`, and initialize all its entries to `0`.
2. Loop with a variable `i` from 1 up to `N`. This `i` represents a potential divisor.
3. For each `i`, start a nested loop that iterates through all multiples of `i` (let's call them `j`) up to `N`.
4. Since `i` is a divisor of `j`, we increment the count for that multiple: `divisors[j]++`.

After the loops complete, the entry `divisors[x]` will have been incremented once for every number that divides `x`. Therefore, it will hold the total count of divisors for `x`.

***

### C++ Implementation

```cpp
#include<bits/stdc++.h>
using namespace std;

vector<int> sieveDivisors(int n) {
    // store no of divisors
    vector<int> divisors(n + 1, 0);
    
    // if you want to store the divisors
    // vector<int> divisors[n + 1]
    
    for (int i = 1; i <= n; ++i) {
        for (int j = i; j <= n; j += i) {
            divisors[j]++;
            // if you want to store the divisors 
            // divisors[j].push_back(i);
        }
    }
    
    return divisors;
}
```

***

### Complexity Analysis

* Time Complexity: $$O(NlogN)$$ The total number of operations is the sum of `N/1 + N/2 + N/3 + ... + N/N`. This is the N-th harmonic number multiplied by N, which is mathematically approximated as `O(N log N)`.
* Space Complexity: $$O(N)$$&#x20;

***

### Problems

[Counting Divisors](https://cses.fi/problemset/task/1713)

[1390. Four Divisors&#x20;(Medium)](https://leetcode.com/problems/four-divisors/description/)

[CF 154B - Colliders](https://codeforces.com/problemset/problem/154/B)

