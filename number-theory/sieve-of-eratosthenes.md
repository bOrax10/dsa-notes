# Sieve of Eratosthenes

The Sieve of Eratosthenes is a highly efficient ancient algorithm used to find all prime numbers up to a specified integer `n`(<=1e6).

***

### Approach

Instead of testing each number for primality individually (which is slow), the sieve works by assuming all numbers are prime and then progressively marking off the multiples of each prime number it finds.

The core idea is an elimination process:

1. Create a boolean list, `isPrime`, of size `n + 1`, and initialize all entries to `true`.
2. Mark `0` and `1` as not prime.
3. Start with the first prime number, `p = 2`.
4. While `p * p <= n`:
   * If `p` is still marked as prime, it is a prime number.
   * Mark all multiples of `p` (starting from `p * p`) as not prime. We start from `p * p` because any smaller multiple, like `2 * p`, would have already been marked off by a smaller prime (in this case, 2).
5. After the process is complete, any number `i` for which `isPrime[i]` is still `true` is a prime number.

This "sieving" or marking-off process efficiently eliminates all composite numbers, leaving only the primes.

***

### C++ Implementation

<pre class="language-cpp"><code class="lang-cpp">#include&#x3C;bits/stdc++.h>
using namespace std;

vector&#x3C;int> primes;
<strong>
</strong>vector&#x3C;bool> sieveOfEratosthenes(int n) {
    // isPrime[i] will be true if i is prime, and false otherwise.
    vector&#x3C;bool> isPrime(n + 1, true);

    isPrime[0] = isPrime[1] = false; // 0 and 1 are not prime numbers

    for (long long p = 2; p * p &#x3C;= n; ++p) {
        // If isPrime[p] is still true, then p is a prime
        if (isPrime[p]) {
            // Mark all multiples of p as not prime, starting from p*p
            for (long long i = p * p; i &#x3C;= n; i += p) {
                isPrime[i] = false;
            }
        }
    }
    
    // store all primes
    for(int i=2;i&#x3C;=n;i++) {
	if(isPrime[i]==1) primes.push_back(i);
    }
    
    return isPrime;
}
</code></pre>

***

### Complexity Analysis

* Time Complexity: $$O(NloglogN)$$ - The algorithm is extremely fast. The total number of marking operations is proportional to `n/2 + n/3 + n/5 + ...`, which is a harmonic series of primes. This sum converges to `O(n log log n)`, making it very close to linear time.
* Space Complexity: $$O(N)$$

***

### Problems

[204. Count Primes (Medium)](https://leetcode.com/problems/count-primes/description/)

[CSES Task 1713](https://cses.fi/problemset/task/1713)

[TDPRIMES](https://www.spoj.com/problems/TDPRIMES/)

[SIOENES](https://www.spoj.com/problems/SIOENES/)

[TDKPRIME](https://www.spoj.com/problems/TDKPRIME/)
