# Interval Assignment

### Problem Statement

Imagine you have **`k` jobs** to fill, numbered `0, 1, ..., k-1`. You have `n` available candidates. Each candidate _i_ is only willing to accept a job _p_ if it falls within their specific range of preference, denoted as **Li ≤&#x20;**_**p**_**&#x20;≤ Ri**.

The question is: can you hire **`k` candidates** and assign each one to a **unique job** that they are willing to take?

***

### Approach

Think of the jobs `0,1,...,k-1` as slots we must fill in increasing order. At step `r` we must pick one candidate whose preference range covers job `r`. A candidate _i_ is eligible at step `r` if **L\[i] ≤ r ≤ R\[i]**.

A natural greedy strategy works: when filling job `r`, among all eligible candidates (`L[i] <= r`), choose the one with the **smallest R\[i]**. Why the smallest R? Because candidates with tighter preference ranges may not be available for later jobs, so we assign them early. This is essentially an earliest-deadline-first strategy.

#### Priority Queue

1. Group candidates by their minimum job (`L[i]`): `byL[x]` stores the `R[i]` values of all candidates who become eligible starting at job `x`.
2. Initialize a min-priority queue `available_candidates` that always contains the eligible candidates for the current job. The top of the queue will be the candidate with the smallest R\[i] (most constrained).
3. At each step `r = 0..k-1`:
   * Add all candidates from `byL[r]` into `available_candidates`.
   * If the queue is empty, no candidate can fill this job → impossible.
   * Otherwise, pick the candidate with the smallest `R[i]` from the queue and assign them to job `r`.

#### DSU

To implement efficiently:

1. Group candidates by their `L[i]`: `byL[x]` contains candidates who become eligible starting at job `x`.
2. Maintain buckets `bucketR[t]` of candidates whose latest acceptable job is `t`. When candidates become available at `r`, push them into `bucketR[R[i]]`.
3. To assign job `r`, find the smallest `t >= r` such that `bucketR[t]` is non-empty (candidate whose range includes `r`).
4. Use a DSU-like structure (parent array) to skip empty buckets quickly; `findNext(x)` returns the next non-empty bucket index >= x.

At each step `r`:

* Add newly eligible candidates `byL[r]` into `bucketR[R]`
* Query `t = findNext(r)`
* If `t >= k`, no candidate is available -> impossible
* Otherwise, assign a candidate from `bucketR[t]` to job `r`

***

### C++ implementation

#### Priority Queue

```cpp
#include <bits/stdc++.h>
using namespace std;

/*
@param n - The total number of candidates
@param k - The number of jobs to fill
@params L,R - The interval of preference for ith candidate
*/

bool isPossible(int n, int k, const vector<int>& L, const vector<int>& R) {
    if (k > n) return false;

    vector<vector<int>> byL(k);
    for (int i = 0; i < n; ++i) {
        if (L[i] <= R[i]) byL[L[i]].push_back(R[i]);
    }

    priority_queue<int, vector<int>, greater<int>> available_candidates;
    
    for (int r = 0; r < k; r++) {
        for (int candidate_R : byL[r]) {
            available_candidates.push(candidate_R);
        }
        
        if (available_candidates.empty()) {
            return false;
        }

        int smallest_R = available_candidates.top();
        available_candidates.pop();

        if (smallest_R < r) {
            return false;
        }
    }
    return true;
}
```

#### DSU

<pre class="language-cpp"><code class="lang-cpp"><strong>#include &#x3C;bits/stdc++.h>
</strong>using namespace std;

/*
@param n - The total number of candidates
@param k - The number of jobs to fill
@params L,R - The interval of preference for ith candidate
*/

bool isPossible(int n, int k, const vector&#x3C;int>&#x26; L, const vector&#x3C;int>&#x26; R) {
    if (k > n) return false;

    vector&#x3C;vector&#x3C;int>> byL(k);
    for (int i = 0; i &#x3C; n; ++i) {
        if (L[i] &#x3C;= R[i]) byL[L[i]].push_back(i);
    }

    vector&#x3C;vector&#x3C;int>> bucketR(k);
    vector&#x3C;int> parent(k + 1);
    for (int i = 0; i &#x3C;= k; ++i) parent[i] = i;

    function&#x3C;int(int)> findNext = [&#x26;](int x) -> int {
        if (x >= k) return k;
        if (parent[x] == x) {
            if (bucketR[x].empty()) {
                parent[x] = findNext(x + 1);
                return parent[x];
            } else return x;
        }
        parent[x] = findNext(parent[x]);
        return parent[x];
    };

    for (int r = 0; r &#x3C; k; ++r) {
        for (int id : byL[r]) {
            bucketR[R[id]].push_back(id);
        }
        int t = findNext(r);
        if (t >= k) return false;
        bucketR[t].pop_back();
        if (bucketR[t].empty()) parent[t] = t + 1;
    }
    return true;
}
</code></pre>

***

### Complexity Analysis

Let `n` be the number of candidates, `k` the number of jobs.

| Approach                | Time Complexity       | Space Complexity |
| ----------------------- | --------------------- | ---------------- |
| **Priority Queue (PQ)** | `O(n * log n)`        | `O(n + k)`       |
| **DSU-like Structure**  | `O(n + k * alpha(k))` | `O(n + k)`       |

Here, $$α(k)$$ is the inverse Ackermann function, which grows so slowly that it's considered a very small constant for all practical purposes (its value is less than 5 for any input size you'd ever encounter). Therefore, the DSU-like solution's time complexity is effectively linear.

***

### Notes

* To get the actual assignment, maintain an array `assign[r] = candidate_id` when popping from buckets/priority queue.
* Can handle arbitrary ranges using coordinate compression.
* This method generalizes to any slot-filling problem with availability windows: always assign the tightest-constraint element first.
