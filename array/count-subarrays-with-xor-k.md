# Count subarrays with xor k

### Problem Statement

Given `k`, find the number of subarrays with the `xor` equal to `k`.

***

### Approach

We solve this problem using the **prefix XOR** technique.

* The **prefix XOR** up to index `i` (`xr`) is the XOR of all elements from the beginning of the array to index `i`.
* Suppose we want a subarray ending at `i` whose XOR is equal to `k`. If such a subarray exists, then the XOR of the remaining part (before this subarray) must be `xr ^ k`.

This gives us a key observation:\
For each position `i`, the number of subarrays ending at `i` with XOR `k` is equal to the number of times the prefix XOR `xr ^ k` has appeared before.

Therefore, instead of explicitly searching for subarrays, we:

1. Maintain a **map (hash table)** to store prefix XOR values along with their frequency.
2. At each index `i`, compute the current prefix XOR `xr`.
3. Look up `xr ^ k` in the map → the frequency tells us how many subarrays ending at `i` have XOR `k`.
4. Add this frequency to the total count.
5. Update the map by increasing the count of the current prefix XOR `xr`.

Finally, we repeat this for all indices `0` to `n-1`.

***

### C++ Implementation

```cpp
#include <bits/stdc++.h>
using namespace std;

int subarraysWithXorK(vector<int> &a, int k) {
    int n = a.size();
    int xr = 0;
    map<int, int> mpp;
    mpp[xr]++;.
    int cnt = 0;

    for (int i = 0; i < n; i++) {
        // prefix XOR till index i:
        xr = xr ^ a[i];
        int x = xr ^ k;

        // add the occurrence of xr^k to the count:
        cnt += mpp[x];

        // Insert the prefix xor till index i into the map:
        mpp[xr]++;
    }
    return cnt;
}
```

***

### Complexity Analysis

* Time Complexity - $$O(NlogN)$$
* Space Complexity - $$O(N)$$
