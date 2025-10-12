# Fermat Primality Test

Given a large integer n, the task is to efficiently determine if n is a prime number. Since deterministic tests for very large numbers can be slow, this test provides a high probability of correctness.

***

### Approach

The test is based on a fundamental result in number theory called Fermat's Little Theorem.

The Theorem: If p is a prime number, then for any integer a such that $$1≤a<p$$, the following congruence holds:

$$a^{p−1}≡1$$ (mod $$p$$)

The primality test essentially flips this theorem around:

1. To test a number n, we pick a random integer a (called a "base" or "witness") in the range $$[2,n−2]$$.
2. We then calculate $$a^{n-1}$$ using binary exponentiation (also known as exponentiation by squaring) because `n-1` can be very large.
3. Check the result:
   * If $$a^{n-1}  \not\equiv$$ $$1$$ (mod $$n$$), then by the theorem, n is definitely composite.
   * If $$a^{n−1}≡1$$ (mod $$n$$), then n is probably prime.

To increase our confidence, we can repeat the test with several different random bases a. If n passes for multiple bases, the probability of it being prime becomes very high.

The Weakness: The test is probabilistic, not deterministic, because of a special class of composite numbers called Carmichael numbers (e.g., 561). These are non-prime numbers that satisfy the congruence $$a^{n−1}≡1$$ (mod $$n$$) for all bases _a_ that are coprime to _n_. This makes them "Fermat liars," but they are relatively rare.

***

### C++ Implementation

```cpp
#include<bits/stdc++.h>
using namespace std;

// Function for modular exponentiation: (base^exp) % mod
long long power(long long base, long long exp, long long mod) {
    long long res = 1;
    base %= mod;
    while (exp > 0) {
        if (exp % 2 == 1) res = (__int128)res * base % mod;
        base = (__int128)base * base % mod;
        exp /= 2;
    }
    return res;
}

bool isProbablyPrime(long long n, int k = 5) {
    if (n <= 1 || n == 4) return false;
    if (n <= 3) return true;

    for (int i = 0; i < k; ++i) {
        // Pick a random base 'a' in the range [2, n-2]
        long long a = 2 + rand() % (n - 3);

        // Check the congruence a^(n-1) % n == 1
        if (power(a, n - 1, n) != 1) {
            return false; // Definitely composite
        }
    }

    return true; // Probably prime
}
```

***

### Complexity Analysis

* Time Complexity: O(k log n)
  * The complexity is dominated by the modular exponentiation function, which runs in $$O(logn)$$ time.
  * This is repeated _k_ times for the different bases. Since _k_ is a small constant (e.g., 5-10), the overall performance is excellent.
* Space Complexity: O(1)

***

### Problems

[Prime or Not](https://www.spoj.com/problems/PON/)
