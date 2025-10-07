# Moore's Voting Algorithm

### Problem Statement

Given an array of **N integers**, find an element that occurs more than **N/2** times. You may assume such an element always exists.

### Algorithm

* Initialize `count = 0` and `element`.
* Traverse the array:
  * If `count == 0`, set `element = current` and `count = 1`.
  * If `current == element`, increment `count`.
  * Else decrement `count`.
* After traversal, `element` is the majority candidate. Optionally, verify it occurs > N/2.

***

### C++ Implementation

```cpp
#include <bits/stdc++.h>
using namespace std;

int majorityElement(vector<int> v) {
    int n = v.size(), cnt = 0, el;
    for (int i = 0; i < n; i++) {
        if (cnt == 0) { cnt = 1; el = v[i]; }
        else if (el == v[i]) cnt++;
        else cnt--;
    }

    int cnt1 = 0;
    for (int i = 0; i < n; i++) if (v[i] == el) cnt1++;
    return cnt1 > n/2 ? el : -1;
}
```

***

## Complexity Analysis

* Time Complexity - $$O(N)$$
* Space Complexity - $$O(1)$$
