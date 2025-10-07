# Number of Inversions

### Problem Statement

An inversion in an array is a pair of indices `(i, j)` such that `i < j` and `arr[i] > arr[j]`. In simpler terms, it's a pair of elements that are out of order.

Goal: Given an array of integers, your task is to count the total number of inversions in it.

Example: Consider the array `arr = [3, 1, 2]`. The inversions are:

* `(3, 1)` because `index 0 < index 1` and `arr[0] > arr[1]`
* `(3, 2)` because `index 0 < index 2` and `arr[0] > arr[2]`

The total number of inversions is 2.

***

### Approach

A brute-force approach of checking every possible pair of elements would take $$O(N^2)$$ time. A much more efficient method uses the Merge Sort algorithm, bringing the time complexity down to $$O(NlogN)$$

The core idea is to leverage the "divide and conquer" nature of Merge Sort. The total number of inversions can be split into three groups:

1. Inversions that occur entirely in the left half of the array.
2. Inversions that occur entirely in the right half of the array.
3. Inversions where one element is in the left half and the other is in the right half (cross inversions).

The `mergeSort` function recursively handles points 1 and 2. The key is to count the cross inversions during the `merge` step. Now, let us understand the `merge` logic:

Let's say we have pointers `left` for the `left_half` and `right` for the `right_half`.

1. We compare `arr[left]` and `arr[right]`.
2. Case 1: `arr[left] <= arr[right]`
   * This is the correct order, so it does not form an inversion.
   * We simply add `arr[left]` to our temporary sorted array and move the `left` pointer forward.
3. Case 2: `arr[left] > arr[right]`
   * An inversion is found! The element `arr[right]` is smaller than `arr[left]`.
   * Crucial Insight: Since the `left_half` (`arr[low...mid]`) is already sorted, if `arr[right]` is smaller than `arr[left]`, then it must also be smaller than all the remaining elements in the `left_half` (from `arr[left]` to `arr[mid]`).
   * Therefore, we can count all these inversions at once. The number of remaining elements in the left half is `(mid - left + 1)`.
   * We add this count to our total: `cnt += (mid - left + 1);`
   * Then, we add `arr[right]` to our temporary array and move the `right` pointer forward.

By adding up the counts from the recursive calls and the `merge` step, we get the total number of inversions in the array.

***

### C++ Implementation

```cpp
#include <bits/stdc++.h>
using namespace std;

int merge(vector<int>& arr, int low, int mid, int high) {
    vector<int> temp;
    int left = low;
    int right = mid + 1;

    int cnt=0; 

    while (left <= mid && right <= high) {
        if (arr[left] <= arr[right]) {
            temp.push_back(arr[left]);
            left++;
        }
        else {
            temp.push_back(arr[right]);
            cnt += mid - left + 1;
            right++;
        }
    }

    // If elements on the left half are still left
    while (left <= mid) {
        temp.push_back(arr[left]);
        left++;
    }

    // If elements on the right half are still left
    while (right <= high) {
        temp.push_back(arr[right]);
        right++;
    }

    // Transferring all elements from temporary to arr
    for (int i = low; i <= high; i++) {
        arr[i] = temp[i - low];
    }

    return cnt;
}

int mergeSort(vector<int>& arr, int low, int high) {
	int cnt=0;
    if (low >= high) return cnt;
    int mid = (low + high) / 2;
    cnt+=mergeSort(arr, low, mid);      // Left half
    cnt+=mergeSort(arr, mid + 1, high); // Right half
    cnt+=merge(arr, low, mid, high);    // Merging sorted halves
    return cnt;
}

int numberOfInversions(vector<int>& arr, int n) {
	return mergeSort(arr,0,n-1);
}
```

***

### Complexity Analysis

* Time Complexity - $$O(NlogN)$$
* Space Complexity - $$O(N)$$
