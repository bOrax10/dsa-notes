---
title: Time Complexity - Space Com...
---



* Time Complexity - $$O(klogn)$$ - The complexity is dominated by the modular exponentiation. The `power` function takes $$O(logn)$$ time. This is repeated a few times inside `millerRabinTest` and then repeated `k` times for different witnesses. Since `k` is a small constant, the performance is excellent.
* Space Complexity - $$O(1)$$&#x20;
