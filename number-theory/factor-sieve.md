# Factor Sieve

The goal is to efficiently pre-compute four key number-theoretic functions for all integers from 1 up to a specified maximum value, `N`:

1. Smallest Prime Factor (SPF): The smallest prime number that divides a given integer.
2. Number of Distinct Prime Factors (NDPF): The count of unique prime numbers that divide an integer.
3. Number of Divisors (NOD): The total count of positive divisors.
4. Sum of Divisors (SOD): The sum of all positive divisors.

***

### Approach

The algorithm is a highly optimized, two-stage process. It first uses a modified sieve to find the prime factors and then uses those results to calculate the divisor properties through mathematical recurrence relations.

**Stage 1: Sieve for Prime Factors**

This stage is a variation of the Sieve of Eratosthenes, but instead of just marking primes, it calculates the Smallest Prime Factor (SPF) and the Number of Distinct Prime Factors (NDPF) for every number up to `n`.

* It iterates from `i = 2` to `n`. If a number `i` has not yet been assigned an SPF (`spf[i] == 0`), it must be a prime number.
* When a prime `i` is found, its SPF is set to itself (`spf[i] = i`), and its NDPF is 1.
* It then iterates through all multiples of this prime (`j = 2*i, 3*i, ...`) and:
  * Sets their SPF to `i` if they don't already have one (ensuring it's the _smallest_).
  * Increments their NDPF count because `i` is a distinct prime factor.

**Stage 2: Calculating NOD and SOD via Recurrence**

This stage leverages a key property of these functions: they are multiplicative. This means if we know the prime factorization of a number, we can easily compute them. Specifically, if a number `i` can be written as `i = p^a * m`, where `p` is the Smallest Prime Factor of `i` and `m` has no factors of `p`, then:

* NOD(i) = NOD(p^a) \* NOD(m) = (a + 1) \* NOD(m)
* SOD(i) = SOD(p^a) \* SOD(m) = (1 + p + p² + ... + p^a) \* SOD(m)

The code implements this by:

1. Iterating from `i = 2` to `n`.
2. For each `i`, it finds its SPF, `p`.
3. It then calculates the contribution of `p` by finding its exponent `a` in the factorization of `i`. This gives the `(a + 1)` term for NOD and the `(1 + p + ... + p^a)` term for SOD.
4. The remaining part of the number is `m = i / p^a` (`temp` in the code). Since we have already computed `NOD(m)` and `SOD(m)` in previous steps, we can find `NOD(i)` and `SOD(i)` by multiplying the two parts together.

***

### C++ Implementation

```cpp
#include<bits/stdc++.h>
using namespace std;

tuple<vector<int>, vector<int>, vector<int>, vector<long long>> 
divisorPropertiesSieve(int n) {
    vector<int> spf(n + 1);
    vector<int> ndpf(n + 1, 0);
    vector<int> nod(n + 1);
    vector<long long> sod(n + 1);

    // Sieve for SPF and NDPF
    spf[1] = 1; nod[1] = 1; sod[1] = 1; ndpf[1] = 0;

    for (int i = 2; i <= n; ++i) {
        if (spf[i] == 0) { // 'i' is prime
            spf[i] = i;
            ndpf[i] = 1;
            for (long long j = 2LL * i; j <= n; j += i) {
                if (spf[j] == 0) {
                    spf[j] = i;
                }
                ndpf[j]++;
            }
        }
    }

    // Stage 2: Calculate NOD and SOD using SPF
    for (int i = 2; i <= n; ++i) {
        int p = spf[i];
        int temp = i;
        int exponent = 0;
        long long term_sum = 1;
        long long p_power = 1;

        while (temp % p == 0) {
            exponent++;
            p_power *= p;
            term_sum += p_power;
            temp /= p;
        }

        nod[i] = (exponent + 1) * nod[temp];
        sod[i] = term_sum * sod[temp];
    }
    
    return {spf, ndpf, nod, sod};
}
```

***

### Complexity Analysis

{% include "../.gitbook/includes/time-complexity-space-com....md" %}

***

### Problems

[Counting Divisors](https://cses.fi/problemset/task/1713)

[1390. Four Divisors&#x20;(Medium)](https://leetcode.com/problems/four-divisors/description/)

[N-Factorful](https://www.spoj.com/problems/NFACTOR/)

[CF 154B - Colliders](https://codeforces.com/problemset/problem/154/B)
