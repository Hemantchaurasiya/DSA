# Shortest Route I

---

## Problem Overview
Given an **integer array `distance[]`**, where `distance[i]` represents the distance of segment `i`, and two cities **`start`** and **`end`**, find the **shortest distance** to travel from `start` to `end`. You can travel **forward or backward** along the array (circular), i.e., from city `i` to `i+1` or from `i` to `i-1`, considering the array as circular.

---

## Approach 1: Brute Force (Simulate Both Directions)

### **Core Idea:**
Calculate the total distance for both possible routes:
- **Clockwise** from `start` to `end`.
- **Counter-clockwise** from `start` to `end`.
Then, return the minimum of the two.

### **Algorithm:**
1. **Calculate total sum** of all distances.
2. **Calculate clockwise distance**:
   - Start from `start`, move forward until `end`, summing distances.
3. **Calculate counter-clockwise distance**:
   - Subtract the clockwise distance from total sum to get the counter-clockwise distance.
4. Return the minimum of these two distances.

---

### **Java Code:**
```java
public class ShortestRoute {
    public int shortestDistance(int[] distance, int start, int end) {
        int totalDistance = 0;
        for (int d : distance) {
            totalDistance += d;
        }
        
        int n = distance.length;
        int clockwiseDistance = 0, i = start;
        
        while (i != end) {
            clockwiseDistance += distance[i];
            i = (i + 1) % n;
        }
        
        int counterClockwiseDistance = totalDistance - clockwiseDistance;
        return Math.min(clockwiseDistance, counterClockwiseDistance);
    }
}
```

---

### **Complexity Analysis:**
- **Time Complexity:** `O(n)` — We traverse the array once to compute total and once to compute the distance.
- **Space Complexity:** `O(1)` — Only a few variables are used; no extra space proportional to input size.

---

### **Dry Run:**
**Input:** `distance = [1, 2, 3, 4]`, `start = 0`, `end = 2`

| Step | Variables/States | Explanation |
|-------|------------------|--------------|
| Total sum | 1 + 2 + 3 + 4 = 10 | Sum of all distances |
| Clockwise from `0` to `2` |  distance[0] + distance[1] = 1 + 2 = 3 | Moving forward from index 0 to 2 |
| Counter-clockwise | 10 - 3 = 7 | Remaining distance in the opposite direction |
| Result | min(3, 7) = 3 | Shortest distance is 3 |

---

## Approach 2: **(Optional)** Using Prefix Sum for Optimization

*Note:* For very large inputs or multiple queries, precomputing prefix sums can make repeated calculations faster.

### **Core Idea:**
Precompute prefix sums to quickly calculate the sum of any subarray, enabling constant-time distance calculations.

### **Algorithm:**
1. Precompute prefix sums array `prefixSum` where `prefixSum[i]` is sum of `distance[0..i-1]`.
2. Calculate the total sum of distances.
3. For a route from `start` to `end`, determine if moving forward or backward yields a shorter route:
   - Forward distance: `prefixSum[end] - prefixSum[start]` (considering circularity).
   - Backward distance: total sum - forward distance.
4. Return the minimum.

---

### **Java Code:**
```java
public class ShortestRoute {
    public int shortestDistance(int[] distance, int start, int end) {
        int n = distance.length;
        int[] prefixSum = new int[n + 1];
        for (int i = 0; i < n; i++) {
            prefixSum[i + 1] = prefixSum[i] + distance[i];
        }

        int totalSum = prefixSum[n];

        // Calculate forward distance
        int forwardDist;
        if (end >= start) {
            forwardDist = prefixSum[end] - prefixSum[start];
        } else {
            forwardDist = totalSum - (prefixSum[start] - prefixSum[end]);
        }

        // Calculate the other direction
        int backwardDist = totalSum - forwardDist;

        return Math.min(forwardDist, backwardDist);
    }
}
```

---

### **Complexity Analysis:**
- **Time Complexity:** `O(n)` for prefix sum computation + `O(1)` for calculation.
- **Space Complexity:** `O(n)` for storing prefix sums.

---

## Summary:
| Approach | Key Idea | Time Complexity | Space Complexity |
|------------|--------------|---------------------|-----------------------|
| Brute Force | Calculate both directions directly | `O(n)` | `O(1)` |
| Prefix Sum | Precompute prefix sums for quick calculations | `O(n)` | `O(n)` |

---

This comprehensive guide should help in understanding, implementing, and optimizing solutions for the **Shortest Route I** problem efficiently during interviews.
