# Minimum Number of Days to Make m Bouquets

---

## Problem Overview

Given an array `bloomDay` where `bloomDay[i]` represents the day when the `i`-th flower blooms, and two integers `m` (number of bouquets) and `k` (flowers per bouquet), determine the **minimum number of days** needed to make **exactly `m` bouquets**, each consisting of `k` consecutive flowers. If it’s impossible, return `-1`.

---

## Approach 1: Brute Force

### **Core Idea**

Check each possible day starting from the earliest to the latest bloom day, verifying if it’s possible to make `m` bouquets with flowers blooming on or before that day.

### **Algorithm**

1. Find the maximum bloom day in the `bloomDay` array.
2. Iterate from day = 1 to max bloom day:
    - For each day, count how many bouquets can be formed:
        - Traverse the array and count consecutive flowers that bloom on or before that day.
        - When `k` consecutive flowers are found, increment bouquet count and reset counter.
    - If the bouquet count reaches `m`, return the current day.
3. If no day satisfies making `m` bouquets, return `1`.

---

### **Java Code**

```java
public int minDaysBruteForce(int[] bloomDay, int m, int k) {
    int maxDay = 0;
    for (int day : bloomDay) {
        maxDay = Math.max(maxDay, day);
    }
    for (int day = 1; day <= maxDay; day++) {
        int bouquets = 0;
        int count = 0;
        for (int b : bloomDay) {
            if (b <= day) {
                count++;
                if (count == k) {
                    bouquets++;
                    count = 0;
                }
            } else {
                count = 0;
            }
        }
        if (bouquets >= m) {
            return day;
        }
    }
    return -1;
}

```

### **Complexity Analysis**

- **Time Complexity:** `O(maxDay * n)` where `maxDay` is the maximum bloom day and `n` is the length of `bloomDay`. In the worst case, this can be very large if bloom days are large.
- **Space Complexity:** `O(1)` — no extra space used apart from variables.

### **Dry Run**

- Input:
    
    ```
    bloomDay = [1, 10, 3, 10, 2]
    m = 3, k = 1
    
    ```
    
- Max day = 10
- For each day from 1 to 10:
    - Check how many bouquets can be formed.
- For day=3:
    - Bloomed flowers: positions 0, 2, 4 (days 1, 3, 2)
    - Count bouquets:
        - Day 1: bloom -> count=1, bouquet formed.
        - Day 2: bloom -> count=1, bouquet formed.
        - Day 3: bloom -> count=1, bouquet formed.
    - 3 bouquets formed; return day=3.

---

## Approach 2: Binary Search (Most Optimal Solution)

### **Core Idea**

The problem involves searching for the minimum day `D` such that it’s possible to make `m` bouquets. Since the bloom days are bounded, we can use **binary search** over the days:

- **Search Space:** from `min(bloomDay)` to `max(bloomDay)`.
- For each mid value:
    - Check if we can make `m` bouquets with flowers blooming on or before `mid`.
    - Narrow down the search based on the feasibility.

### **Algorithm**

1. Initialize `low = min(bloomDay)`, `high = max(bloomDay)`.
2. While `low < high`:
    - Let `mid = (low + high) / 2`.
    - Check feasibility:
        - Count how many bouquets can be formed with flowers blooming on or before `mid`.
        - If `m` bouquets can be formed, move `high` to `mid`.
        - Else, move `low` to `mid + 1`.
3. Return `low` if feasible, else `1`.

### **Java Code**

```java
public int minDaysBinarySearch(int[] bloomDay, int m, int k) {
    int n = bloomDay.length;
    if (m * k > n) return -1; // Impossible to form m bouquets

    int low = Integer.MAX_VALUE;
    int high = Integer.MIN_VALUE;

    for (int day : bloomDay) {
        low = Math.min(low, day);
        high = Math.max(high, day);
    }

    while (low < high) {
        int mid = low + (high - low) / 2;
        int bouquets = 0;
        int count = 0;

        for (int b : bloomDay) {
            if (b <= mid) {
                count++;
                if (count == k) {
                    bouquets++;
                    count = 0;
                }
            } else {
                count = 0;
            }
        }

        if (bouquets >= m) {
            high = mid;
        } else {
            low = mid + 1;
        }
    }

    // Final check if we can make `m` bouquets at `low`
    int bouquets = 0;
    int count = 0;
    for (int b : bloomDay) {
        if (b <= low) {
            count++;
            if (count == k) {
                bouquets++;
                count = 0;
            }
        } else {
            count = 0;
        }
    }

    return (bouquets >= m) ? low : -1;
}

```

### **Complexity Analysis**

- **Time Complexity:** `O(n log(max(bloomDay)))`
    - Binary search over days (`log(max(bloomDay))`)
    - Each feasibility check traverses the array (`O(n)`)
- **Space Complexity:** `O(1)` — only variables used for binary search and counters.

### **Dry Run**

- Input:
    
    ```
    bloomDay = [1, 10, 3, 10, 2]
    m = 3, k = 1
    
    ```
    
- Min bloom day = 1, Max bloom day = 10
- Binary search steps:
    - mid = 5:
        - Count flowers blooming on or before 5:
            - days: 1, 3, 2 → 3 flowers, enough for 3 bouquets (since k=1).
        - bouquets = 3, feasible → move high to 5.
    - mid = 3:
        - Check flowers with bloom day <= 3: 1, 3, 2
        - Count bouquets: 3 (each flower individually), feasible → move high to 3.
    - mid = 2:
        - Flowers: 1, 2
        - bouquets: only 2 (not enough), move low to 3.
- Final answer: 3

---

## Summary

| Approach | Idea | Key Points | Time Complexity | Space Complexity | Best Use Case |
| --- | --- | --- | --- | --- | --- |
| Brute Force | Check each day from 1 to max bloom day | Simple but inefficient for large inputs | `O(maxDay * n)` | `O(1)` | Small input constraints or initial understanding |
| Binary Search | Search over days using feasibility check | Efficient for large constraints | `O(n log(maxDay))` | `O(1)` | Large inputs, performance-critical |

---

## Final Notes

- Always consider the constraints to choose the optimal approach.
- Binary search reduces the search space significantly, making it the preferred method for large input sizes.
- The feasibility check within binary search is crucial; it should be efficient and accurate.

---

**Happy revising!**
