# Monotonic Stack

A monotonic stack is a specialized stack data structure that preserves a specific order among its elements, either strictly increasing or strictly decreasing. It's an incredibly efficient tool for problems where you need to find the "next" or "previous" element that has a certain property (like being greater or smaller).

***

### Problem Statement

The archetypal problem solved by a monotonic stack is finding the Next Greater Element (NGE).

Given an array of numbers, `arr`, the goal is to find the first element to the right of each element `arr[i]` that is strictly greater than `arr[i]`. If no such element exists, we can use a placeholder like -1.

Example: For the input array `[4, 5, 2, 10, 8]`:

* The next greater element for `4` is `5`.
* The next greater element for `5` is `10`.
* The next greater element for `2` is `10`.
* The next greater element for `10` is not found, so it's `-1`.
* The next greater element for `8` is not found, so it's `-1`.

The final result would be `[5, 10, 10, -1, -1]`.

***

### Approach

The brute-force way to solve this would be a nested loop, which takes $$O(n^2)$$ time. A monotonic stack cleverly solves it in linear time, $$O(n)$$.

The core idea is to process the array elements one by one while maintaining a stack of elements that are still "waiting" for their next greater element. To find the Next Greater Element, we will maintain a monotonically decreasing stack. This sounds counter-intuitive, but it's the key.

Think of the elements as buildings and the stack as a viewpoint. We only keep buildings in our viewpoint that are shorter than the ones before them.

Here’s the logic step-by-step:

1. Initialize an empty stack. It's best to store indices of elements rather than the values themselves, as this gives you more information.
2. Iterate through the input array `arr` from left to right, with index `i` and value `current_element`.
3. For each `current_element`, look at the index at the top of the stack, let's call it `top_index`.
4. While the stack is not empty AND the `current_element` is greater than `arr[top_index]`:
   * This means we have found the Next Greater Element for the element at `top_index`. The answer for `arr[top_index]` is `current_element`.
   * Since `arr[top_index]` has found its answer, we pop it from the stack.
   * We repeat this process with the new top of the stack.
5. After the while loop, the stack is either empty or the `current_element` is smaller than or equal to the element at the top. This maintains the decreasing order.
6. Push the current index `i` onto the stack. It will now wait for its next greater element.
7. After the main loop finishes, any indices still left in the stack do not have a next greater element, so their result is -1.

***

### C++ Implementation

This example code specifically solves the Next Greater Element problem.

```cpp
#include<bits/stdc++.h>
using namespace std;

/*
 * @brief A general function to find Next/Previous Greater/Smaller elements.
 * @param arr The input array.
 * @param findNext Controls direction: true for "Next" (right), false for "Previous" (left).
 * @param findGreater Controls comparison: true for "Greater", false for "Smaller".
 * @return A vector containing the results.
 */
vector<int> findMonotonic(const vector<int>& arr, bool findNext, bool findGreater) {
    int n = arr.size();
    vector<int> result(n, -1);
    stack<int> s;

    // Define the comparison logic based on findGreater
    auto compare = [&](int a, int b) {
        return findGreater ? a > b : a < b;
    };

    // Determine the loop's start, end, and step based on findNext
    int start = findNext ? 0 : n - 1;
    int end = findNext ? n : -1;
    int step = findNext ? 1 : -1;

    for (int i = start; i != end; i += step) {
        while (!s.empty() && compare(arr[i], arr[s.top()])) {
            result[s.top()] = arr[i];
            s.pop();
        }
        s.push(i);
    }

    return result;
}
```

***

### Complexity Analysis

* Time Complexity - $$O(N)$$
* Space Complexity - $$O(N)$$

***

### Problems

[503. Next Greater Element II (Medium)](https://leetcode.com/problems/next-greater-element-ii/description/)

[901. Online Stock Span (Medium)](https://leetcode.com/problems/online-stock-span/description/)

[402. Remove K Digits (Medium)](https://leetcode.com/problems/remove-k-digits/description/)

[735. Asteroid Collision (Medium)](https://leetcode.com/problems/asteroid-collision/description/)

[1762 - Buildings With an Ocean View (Medium)](https://leetcode.ca/2021-04-14-1762-Buildings-With-an-Ocean-View/)

[739. Daily Temperatures (Medium)](https://leetcode.com/problems/daily-temperatures/description/)

[581. Shortest Unsorted Continuous Subarray (Medium)](https://leetcode.com/problems/shortest-unsorted-continuous-subarray/description/)

[2866. Beautiful Towers II (Medium)](https://leetcode.com/problems/beautiful-towers-ii/description/)

[2297 - Jump Game VIII (Medium)](https://leetcode.ca/2022-03-15-2297-Jump-Game-VIII/)

[2104. Sum of Subarray Ranges (Medium)](https://leetcode.com/problems/sum-of-subarray-ranges/description/)

[907. Sum of Subarray Minimums (Medium)](https://leetcode.com/problems/sum-of-subarray-minimums/description/)

[1856. Maximum Subarray Min-Product (Medium)](https://leetcode.com/problems/maximum-subarray-min-product/description/)

[3542. Minimum Operations to Convert All Elements to Zero (Medium)](https://leetcode.com/problems/minimum-operations-to-convert-all-elements-to-zero/description/)

[Count Subarrays with Current Element as Max at Start or End (Hard)](https://github.com/chandanagrawal23/DSA-Sheet/blob/main/FaceBook_Stack_Hard.cpp)

[3113. Find the Number of Subarrays Where Boundary Elements Are Maximum (Hard)](https://leetcode.com/problems/find-the-number-of-subarrays-where-boundary-elements-are-maximum/description/)

[2454. Next Greater Element IV (Hard)](https://leetcode.com/problems/next-greater-element-iv/description/)

[84. Largest Rectangle in Histogram (Hard)](https://leetcode.com/problems/largest-rectangle-in-histogram/description/)

[85. Maximal Rectangle (Hard)](https://leetcode.com/problems/maximal-rectangle/description/)

[CF 1913D - Array Collapse (Very Hard)](https://codeforces.com/contest/1913/problem/D)



