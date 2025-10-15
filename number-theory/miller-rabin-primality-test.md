# Miller-Rabin Primality Test

Given a large integer n, the task is to efficiently determine if n is a prime number. The Miller-Rabin test provides a high-probability answer, making it much more reliable than the Fermat test and faster than deterministic methods for large input

***

### Approach

#### ## Approach 🧐

The Miller-Rabin test refines Fermat's Little Theorem by adding a check based on the property of square roots of unity modulo a prime.

**Core Mathematical Properties**:

1. If **p** is a prime number, the only solutions to the equation $$x^2 \equiv 1 \pmod{p}$$ are $$x \equiv 1$$ or $$x \equiv p-1$$.
2. Any number `n-1` can be expressed as $$2^s \cdot d$$, where `d` is an odd number.

The test combines these facts. If `n` is prime, Fermat's Little Theorem says $$a^{n-1} \equiv 1 \pmod{n}$$. Substituting the decomposition, we get $$a^{2^s \cdot d} \equiv 1 \pmod{n}$$. This creates a sequence of square roots:

> $$a^d, \quad a^{2d}, \quad a^{4d}, \quad \dots, \quad a^{2^s d}$$ If `n` is prime, the last term is 1. When we look at this sequence, the first time we see a `1`, the term right before it must have been `n-1` (the other square root of 1).

**The Test Logic**: To test if `n` is prime, we pick a random base `a` (called a "witness") and check the following:

1. First, represent `n-1` as $$2^s \cdot d$$ where `d` is odd.
2. Calculate $$x = a^d \pmod{n}$$.
3. If $$x=1$$ or $$x=n-1$$, `n` passes the test for this base and is **probably prime**.
4. Otherwise, we repeatedly square `x` (up to `s-1` times) and check if it becomes `n-1`.
   * If at any point $$x$$ becomes `n-1`, `n` passes the test and is **probably prime**.
   * If we complete the squaring and `x` never becomes `n-1` (meaning it became 1 without being preceded by `n-1`), then **n** is **definitely composite**.

We repeat this process for several different bases `a` to increase our confidence. Unlike the Fermat test, there are no "Carmichael numbers" for Miller-Rabin; for any composite `n`, _at least 75%_ of the possible bases will prove it's composite.

***

### C++ Implementation

```cpp
#include<bits/stdc++.h>
using namespace std;

using u64 = uint64_t;
using u128 = __uint128_t;

u64 binpower(u64 base, u64 e, u64 mod) {
    u64 result = 1;
    base %= mod;
    while (e) {
        if (e & 1)
            result = (u128)result * base % mod;
        base = (u128)base * base % mod;
        e >>= 1;
    }
    return result;
}

bool check_composite(u64 n, u64 a, u64 d, int s) {
    u64 x = binpower(a, d, n);
    if (x == 1 || x == n - 1)
        return false;
    for (int r = 1; r < s; r++) {
        x = (u128)x * x % n;
        if (x == n - 1)
            return false;
    }
    return true;
};

bool MillerRabin(u64 n, int iter=5) { // returns true if n is probably prime, else returns false.
    if (n < 4)
        return n == 2 || n == 3;

    int s = 0;
    u64 d = n - 1;
    while ((d & 1) == 0) {
        d >>= 1;
        s++;
    }

    for (int i = 0; i < iter; i++) {
        int a = 2 + rand() % (n - 3);
        if (check_composite(n, a, d, s))
            return false;
    }
    return true;
}
```

***

### Complexity Analysis

{% include "../.gitbook/includes/time-complexity-space-com....md" %}

***

### Problems

[Prime or Not](https://www.spoj.com/problems/PON/)
