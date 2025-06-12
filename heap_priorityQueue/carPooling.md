# Car Pooling

The **Car Pooling** problem involves determining whether it is possible to pick up and drop off all passengers given a set of trips, each with a number of passengers, start location, and end location, while maintaining the capacity constraint of the vehicle.

---

## Problem Statement
Given an array of trips `trips`, where each trip is represented as `[numPassengers, startLocation, endLocation]`, and an integer `capacity` representing the total capacity of the vehicle, determine if it is possible to complete all trips without exceeding the capacity at any point.

---

## Approach Overview

---

### 1. **Brute Force Approach (Line Sweep with Array)**

**Core Idea:**
- Track the number of passengers in the vehicle at each location.
- For each trip, add the number of passengers at the start location and remove them at the end location.
- Check if at any point, the total passengers exceed capacity.

**Algorithm Steps:**
1. Find the maximum location coordinate among all trips.
2. Create an array `diff` of size `maxLocation + 1` initialized to 0.
3. For each trip:
   - Add `numPassengers` at `startLocation`.
   - Subtract `numPassengers` at `endLocation`.
4. Iterate through `diff`, maintaining a running sum to simulate the number of passengers in the vehicle at each point.
5. If at any point, the sum exceeds capacity, return `false`.
6. If the loop completes without exceeding capacity, return `true`.

---

### **Java Code:**
```java
public class Solution {
    public boolean carPooling(int[][] trips, int capacity) {
        int maxLocation = 0;
        for (int[] trip : trips) {
            maxLocation = Math.max(maxLocation, trip[2]);
        }
        int[] diff = new int[maxLocation + 1];

        for (int[] trip : trips) {
            diff[trip[1]] += trip[0]; // add passengers at start
            if (trip[2] < diff.length) {
                diff[trip[2]] -= trip[0]; // remove passengers at end
            }
        }

        int currentPassengers = 0;
        for (int i = 0; i < diff.length; i++) {
            currentPassengers += diff[i];
            if (currentPassengers > capacity) {
                return false;
            }
        }
        return true;
    }
}
```

---

### **Complexity Analysis:**
- **Time Complexity:** O(N + M), where N is the number of trips and M is the maximum location coordinate. The main operations are iterating through trips and the `diff` array.
- **Space Complexity:** O(M), for the difference array.

---

### 2. **Priority Queue (Line Sweep with Events)**

**Core Idea:**
- Use a priority queue to process start and end events sorted by location.
- Keep track of current passengers and ensure it does not exceed capacity.

**Algorithm Steps:**
1. Create a list of events `[location, change_in_passengers]` for each trip:
   - `(startLocation, +numPassengers)`
   - `(endLocation, -numPassengers)`
2. Sort the events by location.
3. Initialize `currentPassengers = 0`.
4. Iterate through sorted events:
   - Update `currentPassengers` by adding the event's passenger change.
   - If `currentPassengers > capacity`, return `false`.
5. Return `true` if all events are processed without capacity breach.

---

### **Java Code:**
```java
import java.util.*;

public class Solution {
    public boolean carPooling(int[][] trips, int capacity) {
        List<int[]> events = new ArrayList<>();
        for (int[] trip : trips) {
            events.add(new int[]{trip[1], trip[0]}); // start
            events.add(new int[]{trip[2], -trip[0]}); // end
        }
        // Sort events by location
        events.sort(Comparator.comparingInt(a -> a[0]));
        int currentPassengers = 0;
        for (int[] event : events) {
            currentPassengers += event[1];
            if (currentPassengers > capacity) {
                return false;
            }
        }
        return true;
    }
}
```

---

### **Complexity Analysis:**
- **Time Complexity:** O(N log N), due to sorting of events.
- **Space Complexity:** O(N), for storing events.

---

### 3. **Most Optimal Solution: Using a Min-Heap for Drop-offs**

**Core Idea:**
- Process trips in order of start location.
- Use a min-heap to keep track of end locations and passengers.
- For each start location:
  - Drop off passengers who have reached their end location before the current start.
  - Add current trip's passengers.
  - Check capacity constraints.

**Algorithm Steps:**
1. Sort trips by start location.
2. Use a min-heap to store `(endLocation, numPassengers)` for ongoing trips.
3. Initialize `currentPassengers = 0`.
4. For each trip:
   - While the heap is not empty and the earliest end location is ≤ current trip's start location:
     - Remove the trip from the heap.
     - Decrease `currentPassengers` accordingly.
   - Add current trip's passengers to `currentPassengers`.
   - If `currentPassengers > capacity`, return `false`.
   - Add the current trip's `(endLocation, numPassengers)` to the heap.
5. Return `true` after processing all trips.

---

### **Java Code:**
```java
import java.util.*;

public class Solution {
    public boolean carPooling(int[][] trips, int capacity) {
        Arrays.sort(trips, Comparator.comparingInt(a -> a[1]));
        PriorityQueue<int[]> minHeap = new PriorityQueue<>(Comparator.comparingInt(a -> a[0]));
        int currentPassengers = 0;

        for (int[] trip : trips) {
            int start = trip[1], end = trip[2], numPassengers = trip[0];

            // Drop off passengers who have reached their end location
            while (!minHeap.isEmpty() && minHeap.peek()[0] <= start) {
                currentPassengers -= minHeap.poll()[1];
            }

            // Add current trip's passengers
            currentPassengers += numPassengers;
            if (currentPassengers > capacity) {
                return false;
            }
            minHeap.offer(new int[]{end, numPassengers});
        }

        return true;
    }
}
```

---

### **Complexity Analysis:**
- **Time Complexity:** O(N log N), due to sorting and heap operations.
- **Space Complexity:** O(N), for heap storage.

---

## **Dry Run with Sample Input**

### Example:
```plaintext
trips = [[2, 1, 5], [3, 3, 7]], capacity = 4
```

### Step-by-step:

| Step | Sorted Trips | Current Passengers | Actions | Heap (EndLocation, Passengers) | Capacity Check | Result |
|---------|----------------|----------------------|---------|------------------------------|----------------|---------|
| 1       | [[2,1,5], [3,3,7]] | 0 | Start with first trip | [] | - | - |
| 2       | | | Process trip [2,1,5] | [(5,2)] | 2 ≤ 4 | OK |
| 3       | | | Process trip [3,3,7] | [(5,2), (7,3)] | 2+3=5 >4? NO, but check after dropping old trips | |
| 4       | Start at 1 | 0 | Drop trips ending before 1? | [] | - | - |
| 5       | | | Add first trip (start=1) | (5,2) | 2 ≤ 4 | OK |
| 6       | | | Add second trip (start=3) | (5,2), (7,3) | 2+3=5 >4? NO, so capacity exceeded | **Return false** |

### Explanation:
- At start location 3, total passengers would be 5, exceeding capacity 4.
- So, the answer is `false`.

---

## **Summary**

| Approach | Core Idea | Data Structures | Pros | Cons | Time Complexity | Space Complexity |
|------------|--------------|-------------------|-------|-------|-----------------|------------------|
| Array Difference | Mark +ve/-ve at start/end | Array | Simple, effective for small ranges | Inefficient for large ranges | O(N + M) | O(M) |
| Event Sorting | Process start/end events | List + Sorting | Clear logic, handles large inputs | Sorting overhead | O(N log N) | O(N) |
| Min-Heap (Drop-offs) | Process trips in order, drop passengers as they reach end | Priority Queue | Efficient, real-time capacity check | Slightly complex | O(N log N) | O(N) |

---

## **Final Tips**
- Sorting trips by start location simplifies processing.
- Use a min-heap to efficiently handle drop-offs.
- Always check capacity after each passenger addition or removal.
- For large inputs, the heap-based approach is most performant.

---

**End of DSA Revision Note for Car Pooling**
