# Recursive Stack Sort

### Problem Statement

Given a stack of integers, sort it in **descending order** (largest element on top) using recursion. You are **not allowed to use any additional data structures**; only the recursion stack is allowed.

#### Example:

```
Input Stack (top to bottom): 5, -2, 9, -7, 3
Output Stack (top to bottom): 9, 5, 3, -2, -7
```

***

### Algorithm

The solution uses the concept of recursion to hold elements in the call stack and insert them back in sorted order:

1. **Hold all elements in the call stack** by popping them from the original stack until it becomes empty.
2. Use a helper function `sortedInsert()` to insert elements back into the stack in **sorted order**.
3. **sortedInsert(stack, current)**:
   * If the stack is empty or the current element is greater than the top of the stack, push the element.
   * Otherwise, pop the top element, recursively call `sortedInsert()` with the remaining stack and the current element.
   * Push the popped element back onto the stack.
4. **sortStack(stack)**:
   * Base case: if the stack is empty, return.
   * Pop the top element, recursively sort the remaining stack.
   * Insert the popped element back in sorted order using `sortedInsert()`.

#### Step-by-Step Example:

```
Initial Stack (top to bottom): 5, -2, 9, -7, 3

Recursion Call Stack (after popping all elements):
5 <- stack frame #1
-2 <- stack frame #2
9 <- stack frame #3
-7 <- stack frame #4
3 <- stack frame #5

Insert 3 using sortedInsert(): 3 (top)
Insert -7: -7 goes below 3 => stack: 3, -7
Insert 9: 9 > 3 => stack: 9, 3, -7
Insert -2: -2 < 3 => stack: 9, 3, -2, -7
Insert 5: 5 < 9 => stack: 9, 5, 3, -2, -7
Final sorted stack (top to bottom): 9, 5, 3, -2, -7
```

***

### C++ Implementation

```cpp
#include <bits/stdc++.h>
using namespace std;

// Inserts 'current' in the sorted stack
void sortedInsert(stack<int> &stack, int current) {
    if (stack.empty() || current > stack.top()) {
        stack.push(current);
        return;
    }

    int top = stack.top();
    stack.pop();
    sortedInsert(stack, current);
    stack.push(top);
}

// Recursively sorts the stack
void sortStack(stack<int> &stack) {
    if (stack.empty()) return;

    int top = stack.top();
    stack.pop();
    sortStack(stack);
    sortedInsert(stack, top);
}
```

***

### Complexity

* **Time Complexity:** $$O(N^2)$$, where N is the number of elements in the stack.
  * Each element may be recursively compared and inserted at the correct position.
* **Space Complexity:** $$O(N)$$, due to the recursion stack.
