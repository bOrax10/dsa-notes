# Interval Partitioning

### Problem Statement

You are given a collection of $$n$$ intervals, where each interval is defined by a start time and an end time.

Your task is to partition the set of all intervals into the minimum possible number of groups. The rule for creating these groups is that all intervals within a single group must be non-overlapping. Each group can then be assigned to a single, reusable resource (like a room or a machine).

You need to find two things:

1. The minimum number of groups (and therefore, resources) required for this partition.
2. A valid assignment showing which group (or resource number) each interval belongs to.

***

### Approach

The core idea is to process customers in the order of their arrival and always try to assign them the earliest available room.

1. Sorting: The first and most crucial step is to sort all customers based on their arrival day in ascending order. This ensures we are always dealing with the next customer to arrive, which is essential for making a correct greedy decision.
2. Managing Occupied Rooms with a Min-Priority Queue: A min-priority queue is used to keep track of the rooms that are currently occupied.
   * The priority queue stores pairs of `{departure_day, room_number}`.
   * Because it's a min-priority queue ordered by the departure day, the room at the top (`pq.top()`) is always the one that will become free the earliest.
3. The Greedy Choice: As we iterate through the sorted customers, we consider one customer at a time. For a customer arriving on day `a`:
   * We look at the room at the top of the priority queue. This room's departure day is `pq.top().first`.
   * Case 1: Reuse a Room. If this room's departure day is _earlier than_ the new customer's arrival day (`pq.top().first < a`), it means the room is now vacant. We can reuse this room. We assign it to the new customer and update its entry in the priority queue with the new customer's departure day. This is the optimal choice because it reuses a room, preventing the unnecessary allocation of a new one.
   * Case 2: Allocate a New Room. If the priority queue is empty or if even the earliest-freeing room is not available yet (`pq.top().first >= a`), then no existing room can be reused. We are forced to allocate a new room. We increment our total room count and assign this new room number to the customer, adding its `{departure_day, room_number}` pair to the priority queue.

By the end of the process, the total number of rooms we had to allocate (`roomsUsed`) is the minimum required. The assignments are stored in a separate answer vector, indexed by the customers' original IDs.

***

### C++ Implementation

```cpp
#include<bits/stdc++.h>
using namespace std;

pair<int, vector<int>> partitionIntervals(const vector<pair<int, int>>& customer_times) {
    int n = customer_times.size();
    if (n == 0) {
        return {0, {}};
    }

    // (arrival, departure, index)
    vector<tuple<int, int, int>> customers;
    customers.reserve(n);
    for (int i = 0; i < n; ++i) {
        customers.emplace_back(customer_times[i].first, customer_times[i].second, i);
    }

    // Sort customers primarily by arrival day
    sort(customers.begin(), customers.end());

    // Min-priority queue stores pairs of {departure_day, room_number}
    priority_queue<pair<int, int>, vector<pair<int, int>>, greater<pair<int, int>>> pq;
    vector<int> room_assignments(n);
    int rooms_used = 0;

    // Process customers in order of arrival
    for (const auto& customer : customers) {
        int arrival = get<0>(customer);
        int departure = get<1>(customer);
        int original_index = get<2>(customer);

        // Check if the earliest-freeing room is available
        if (!pq.empty() && pq.top().first < arrival) {
            // If yes, reuse this room
            int room_number = pq.top().second;
            pq.pop(); // Remove the old entry
            room_assignments[original_index] = room_number;
            pq.emplace(departure, room_number); // Add the new entry for the current customer
        } else {
            // Otherwise, allocate a new room
            rooms_used++;
            room_assignments[original_index] = rooms_used;
            pq.emplace(departure, rooms_used);
        }
    }

    return {rooms_used, room_assignments};
}
```

***

### Complexity Analysis

* Time Complexity - $$O(NlogN)$$
* Space Complexity - $$O(N)$$

***

### Problems

[CSES Task 1164](https://cses.fi/problemset/task/1164)
