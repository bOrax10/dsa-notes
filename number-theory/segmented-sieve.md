# Segmented Sieve

The goal is to find all prime numbers within a given range `[L, R]`, where `R` can be very large (e.g., up to $$1012$$) but the size of the range (`R - L`) is relatively small (e.g., up to $$106$$).

***

### Approach

A standard sieve up to `R` would require an impractically large boolean array. The Segmented Sieve solves this by breaking the process into two main stages. The core idea is that any composite number `n` in the range `[L, R]` must have a prime factor less than or equal to $$R​$$.

1\. Stage 1: Pre-compute Base Primes First, we generate all prime numbers up to $$R​$$ using a standard Sieve of Eratosthenes. These "base primes" are the only numbers we need to sieve the main range.

2\. Stage 2: Sieve the Segment Next, we focus only on the range `[L, R]`.

* We create a boolean array, `segment`, of size `R - L + 1`. This array represents the numbers from `L` to `R`, where `segment[i]` corresponds to the number `L + i`.
* We assume all numbers in this segment are prime initially.
* For each base prime `p` found in Stage 1, we mark off its multiples within our segment. To do this, we find the first multiple of `p` that is greater than or equal to `L`. A simple formula for this is `start = floor(L / p) * p`. If `start < L`, we move to the next multiple, `start += p`.
* We then iterate from this `start` value up to `R` in steps of `p`, marking each multiple as not prime in our `segment` array (at index `multiple - L`).

After iterating through all base primes, any number `L + i` corresponding to an entry `segment[i]` that is still marked as `true` is a prime number.

***

### C++ Implementation

```cpp
#include<bits/stdc++.h>
using namespace std;

vector<long long> segmentedSieve(long long L, long long R) {
    // Stage 1: Generate primes up to sqrt(R) needed for sieving
    long long limit = sqrt(R);
    vector<bool> isPrime(limit + 1, true);
    isPrime[0] = isPrime[1] = false;
    for (long long p = 2; p * p <= limit; ++p) {
        if (isPrime[p]) {
            for (long long i = p * p; i <= limit; i += p)
                isPrime[i] = false;
        }
    }
    vector<long long> basePrimes;
    for (long long p = 2; p <= limit; ++p) {
        if (isPrime[p]) {
            basePrimes.push_back(p);
        }
    }

    // Stage 2: Sieve the actual range [L, R]
    vector<bool> segment(R - L + 1, true);
    if (L == 1) { // 1 is not a prime number
        segment[0] = false;
    }

    for (long long p : basePrimes) {
        // Find the first multiple of p that is >= L
        long long start = max(p * p, (L + p - 1) / p * p);
        for (long long j = start; j <= R; j += p) {
            if (j >= L) {
                segment[j - L] = false;
            }
        }
    }

    // Collect all prime numbers from the sieved segment
    vector<long long> primesInRange;
    for (long long i = 0; i < segment.size(); ++i) {
        if (segment[i]) {
            primesInRange.push_back(L + i);
        }
    }
    
    return primesInRange;
}
```

***

### Complexity Analysis

* Time Complexity: $$O(R​loglogR​+(R−L)loglogR)$$
  * The pre-computation sieve takes $$O(R​loglogR​)$$.
  * Sieving the segment takes time proportional to the range size for each base prime.
* Space Complexity: $$O(R​+(R−L))$$
  * $$O(R​)$$ is needed to store the base primes.
  * $$O(R−L)$$ is needed for the segment array. This is the key advantage, as it avoids the $$O(R)$$ space of a standard sieve.

***

### Problems

[KPRIMES2](https://www.spoj.com/problems/KPRIMES2/)

[PRIME1](https://www.spoj.com/problems/PRIME1/)
