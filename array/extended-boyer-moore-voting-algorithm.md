# Extended Boyer Moore Voting Algorithm

### Problem Statement

Given an array of **N integers**, find all elements that appear more than **N/3** times.

***

### Algorithm

* Initialize `cnt1, cnt2 = 0` and `el1, el2 = INT_MIN`.
* Traverse the array:
  * If `cnt1 == 0` and `v[i] != el2`, set `el1 = v[i], cnt1 = 1`
  * Else if `cnt2 == 0` and `v[i] != el1`, set `el2 = v[i], cnt2 = 1`
  * Else if `v[i] == el1`, increment `cnt1`
  * Else if `v[i] == el2`, increment `cnt2`
  * Else decrement `cnt1` and `cnt2`
* Verify if `el1` and `el2` occur > N/3 and return them.

***

### C++ Implementation

```cpp
#include <bits/stdc++.h>
using namespace std;

vector<int> majorityElement(vector<int> &v) {
    int n = v.size();

    int cnt1 = 0, cnt2 = 0;
    int el1 = INT_MIN;
    int el2 = INT_MIN;

    for (int i = 0; i < n; i++) {
        if (cnt1 == 0 && el2 != v[i]) {
            cnt1 = 1;
            el1 = v[i];
        }
        else if (cnt2 == 0 && el1 != v[i]) {
            cnt2 = 1;
            el2 = v[i];
        }
        else if (v[i] == el1) cnt1++;
        else if (v[i] == el2) cnt2++;
        else {
            cnt1--, cnt2--;
        }
    }

    vector<int> ls;

    cnt1 = 0, cnt2 = 0;
    for (int i = 0; i < n; i++) {
        if (v[i] == el1) cnt1++;
        if (v[i] == el2) cnt2++;
    }

    int mini = int(n / 3) + 1;
    if (cnt1 >= mini) ls.push_back(el1);
    if (cnt2 >= mini) ls.push_back(el2);

    // if it is told to sort the answer array:
    // sort(ls.begin(), ls.end()); //TC --> O(2*log2) ~ O(1);

    return ls;
}
```

***

### Complexity Analysis

* Time Complexity - $$O(N)$$
* Space Complexity - $$O(1)$$

***

### Problems

[CF 2149G - Buratsuta 3 (Hard)](https://codeforces.com/contest/2149/problem/G)
