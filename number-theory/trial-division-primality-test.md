# Trial Division Primality Test

Given an integer n, the task is to determine if n is a prime number. A prime number is a natural number greater than 1 that has no positive divisors other than 1 and itself.

***

### Approach

The algorithm works by checking for factors. We systematically "try to divide" the number `n` by smaller integers.

The core idea is based on the definition of a composite number. A number `n` is composite if it can be divided evenly by some integer other than 1 and itself. The key optimization comes from the following insight:

* If a number `n` has a divisor `d` that is greater than $$\sqrt n$$, then it must also have a corresponding divisor `n/d` which is _less than_ $$\sqrt{n}$$.

This means we don't need to check for divisors all the way up to `n-1`. We only need to check for divisors from 2 up to $$\sqrt n$$. If we don't find any factors in this range, we can be certain that no factors exist, and therefore, the number is prime. We also handle the edge case that 1 is not a prime number.

***

### C++ Implementation

```cpp
#include<bits/stdc++.h>
using namespace std;

bool isPrime(int n) {
    if (n <= 1) {
        return false;
    }

    // Check for factors from 2 up to the square root of n.
    // i*i <= n is an efficient way to check i <= sqrt(n) without using floating-point math.
    for (int i = 2; i * i <= n; i++) {
        if (n % i == 0) {
            return false;
        }
    }

    return true;
}
```

***

### Complexity Analysis

* Time Complexity - $$O(\sqrt N)$$
* Space Complexity - $$O(1)$$
