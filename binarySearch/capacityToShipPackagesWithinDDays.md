# Capacity To Ship Packages Within D Days

---

## Problem Overview

Given an array `weights` (each representing the weight of a package) and an integer `D` (number of days), determine the **minimum capacity** of a ship such that all packages can be shipped within `D` days. The ship's capacity is the maximum weight it can carry in a single day.

---

## Approach 1: Brute Force

### Core Idea

Try all possible capacities starting from the maximum weight in `weights` up to the sum of all weights. For each capacity, check if it's possible to ship all packages within `D` days.

### Algorithm

1. Find the **minimum capacity** as the maximum weight in `weights`.
2. Find the **maximum capacity** as the sum of all weights.
3. Iterate from `min_capacity` to `max_capacity`.
4. For each capacity, simulate the shipping process:
    - Keep track of the current load.
    - Increment day count each time the load exceeds the capacity.
5. If the number of days needed exceeds `D`, increase capacity.
6. The smallest capacity that allows shipping within `D` days is the answer.

### Java Code

```java
public class Solution {
    public int shipWithinDays(int[] weights, int D) {
        int maxWeight = 0;
        int sumWeights = 0;
        for (int w : weights) {
            maxWeight = Math.max(maxWeight, w);
            sumWeights += w;
        }

        int result = sumWeights;
        for (int capacity = maxWeight; capacity <= sumWeights; capacity++) {
            if (canShip(weights, D, capacity)) {
                result = capacity;
                break;
            }
        }
        return result;
    }

    private boolean canShip(int[] weights, int D, int capacity) {
        int days = 1;
        int currentLoad = 0;
        for (int w : weights) {
            if (currentLoad + w <= capacity) {
                currentLoad += w;
            } else {
                days++;
                currentLoad = w;
                if (days > D) {
                    return false;
                }
            }
        }
        return true;
    }
}

```

### Complexity Analysis

- **Time Complexity:** `O((sum of weights - max weight) * n)`.
In worst case, for each capacity, we do a linear scan through `weights`.
- **Space Complexity:** `O(1)`.
Constant extra space used.

### Dry Run

**Input:** `weights = [1,2,3,4,5], D = 3`

- Max weight = 5, sum = 15
- Check capacities from 5 to 15:

| Capacity | Shipping Simulation | Days Needed | Valid? |
| --- | --- | --- | --- |
| 5 | 1+2=3, next 3>5? Yes; 4>5? Yes; 5>5? Yes; **Total days: 5** | 5 | No |
| 6 | 1+2+3=6; next 4>6? Yes; 5>6? Yes; **Total days: 4** | 4 | No |
| 7 | 1+2+3=6, next 4>7? No; 4+5=9>7? Yes; **Total days: 2** | 2 | Yes |

Answer: **7**

---

## Approach 2: Binary Search (Optimal Solution)

### Core Idea

Use binary search on the **possible capacity range**:

- Lower bound (`low`) = maximum weight in `weights` (minimum capacity needed).
- Upper bound (`high`) = sum of all weights (max capacity).

Check mid-capacity:

- Verify if it's possible to ship within `D` days.
- If yes, try to find a smaller capacity.
- If no, increase capacity.

This approach leverages the monotonic relation:

- Increasing capacity reduces the number of days needed.
- Using binary search reduces the search space efficiently.

### Algorithm

1. Set `low` = max weight, `high` = sum of weights.
2. While `low` < `high`:
    - Find `mid` = (low + high) / 2.
    - Check if shipping is possible with `mid` capacity.
    - If possible, set `high` = `mid`.
    - Else, set `low` = `mid + 1`.
3. Return `low` as the minimal capacity.

### Java Code

```java
public class Solution {
    public int shipWithinDays(int[] weights, int D) {
        int maxWeight = 0;
        int sumWeights = 0;
        for (int w : weights) {
            maxWeight = Math.max(maxWeight, w);
            sumWeights += w;
        }

        int low = maxWeight;
        int high = sumWeights;

        while (low < high) {
            int mid = low + (high - low) / 2;
            if (canShip(weights, D, mid)) {
                high = mid; // try to find smaller capacity
            } else {
                low = mid + 1; // need larger capacity
            }
        }
        return low;
    }

    private boolean canShip(int[] weights, int D, int capacity) {
        int days = 1;
        int currentLoad = 0;
        for (int w : weights) {
            if (currentLoad + w <= capacity) {
                currentLoad += w;
            } else {
                days++;
                currentLoad = w;
                if (days > D) {
                    return false;
                }
            }
        }
        return true;
    }
}

```

### Complexity Analysis

- **Time Complexity:** `O(n log(sum of weights - max weight))`.
Binary search runs in `O(log(sum))`, each check runs in `O(n)`.
- **Space Complexity:** `O(1)`.
Constant extra space.

### Dry Run

**Input:** `weights = [1,2,3,4,5], D = 3`

- Max weight = 5, sum = 15
- Binary search on [5, 15]:

| Step | mid | Check feasibility | Days needed | Result | Next search space |
| --- | --- | --- | --- | --- | --- |
| 1 | 10 | Can 10 ship? Yes (1+2+3+4=10, 5>10? No) | 2 | Yes | high=10 |
| 2 | 7 | Can 7 ship? 1+2+3=6, next 4>7? Yes, new day; 4, 5>7? Yes; total days=3 | Yes | high=7 |  |
| 3 | 6 | Can 6 ship? 1+2+3=6; next 4>6? Yes; new day; 4,5>6? Yes; total=3 | Yes | high=6 |  |
| 4 | 5 | Can 5 ship? 1+2=3, next 3? 3+3=6>5; new day; 4,5>5? Yes; total=3 | Yes | high=5 |  |

Answer: **5**

---

## Summary

- **Brute Force:** Check all capacities from max weight to total sum → **inefficient for large inputs**.
- **Binary Search:** Efficiently narrows down the minimum feasible capacity using a monotonic check → **optimal approach**.

---

**Tip for Interviews:**

Always consider whether the problem involves a monotonic property to apply binary search. It reduces complexity from linear to logarithmic in the search space.

---

If you'd like further explanations or variations, let me know!
