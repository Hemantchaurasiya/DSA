# Koko Eating Bananas

---

## Problem Statement

Given an array `piles` where each element represents the number of bananas in a pile, and an integer `h` representing the number of hours Koko has to eat all bananas, determine the **minimum eating speed** `k` such that Koko can eat all bananas within `h` hours.

---

## Approach 1: Brute Force

### Core Idea:

Try every possible eating speed starting from 1 banana/hour up to the maximum number of bananas in any pile. For each speed, check if Koko can finish within `h` hours.

### Algorithm:

1. Find the maximum pile size, `maxPile`.
2. For each speed `k` from 1 to `maxPile`:
    - Calculate total hours needed to eat all piles at speed `k`.
    - If total hours ≤ `h`, return `k` as the minimum speed.
3. Return the smallest such `k`.

### Java Code:

```java
public int minEatingSpeed(int[] piles, int h) {
    int maxPile = 0;
    for (int pile : piles) {
        maxPile = Math.max(maxPile, pile);
    }
    int result = maxPile;
    for (int k = 1; k <= maxPile; k++) {
        int hours = 0;
        for (int pile : piles) {
            hours += (pile + k - 1) / k; // ceiling division
        }
        if (hours <= h) {
            result = k;
            break; // Since we are increasing k, first match is minimal
        }
    }
    return result;
}

```

### Complexity Analysis:

- **Time Complexity:** `O(maxPile * n)` where `n` is the number of piles.
    - For each speed `k`, we iterate over all piles to compute hours.
- **Space Complexity:** `O(1)` as only a few variables are used.

### Dry Run:

- **Input:** `piles = [3, 6, 7, 11]`, `h = 8`
- `maxPile = 11`
- Check speeds from 1 to 11:
    - For `k=1`: hours = 3 + 6 + 7 + 11 = 27 > 8 → Not feasible
    - For `k=2`: hours = 2 + 3 + 4 + 6 = 15 > 8 → Not feasible
    - For `k=3`: hours = 1 + 2 + 3 + 4 = 10 > 8 → Not feasible
    - For `k=4`: hours = 1 + 2 + 2 + 3 = 8 ≤ 8 → Feasible, result = 4
- Return `4`.

---

## Approach 2: Better Solution (Using Binary Search)

### Core Idea:

Use **binary search** over the range of possible eating speeds. The minimum speed is 1, and the maximum is `maxPile`. For each mid value, check if Koko can finish within `h` hours. Narrow down the search space based on feasibility.

### Algorithm:

1. Set `low = 1`, `high = maxPile`.
2. While `low < high`:
    - Calculate `mid = (low + high) / 2`.
    - Compute total hours needed at speed `mid`.
    - If hours ≤ `h`, try to find a smaller speed by setting `high = mid`.
    - Else, set `low = mid + 1`.
3. After the loop, `low` will be the minimal feasible speed.

### Java Code:

```java
public int minEatingSpeed(int[] piles, int h) {
    int maxPile = 0;
    for (int pile : piles) {
        maxPile = Math.max(maxPile, pile);
    }
    int low = 1, high = maxPile;
    while (low < high) {
        int mid = low + (high - low) / 2;
        int hours = 0;
        for (int pile : piles) {
            hours += (pile + mid - 1) / mid; // ceiling division
        }
        if (hours <= h) {
            high = mid; // try to find a smaller speed
        } else {
            low = mid + 1; // need a higher speed
        }
    }
    return low;
}

```

### Complexity Analysis:

- **Time Complexity:** `O(n log M)` where `n` is the number of piles, and `M` is the maximum pile size.
    - Binary search performs `O(log M)` iterations.
    - Each iteration requires `O(n)` to compute hours.
- **Space Complexity:** `O(1)`.

### Dry Run:

- **Input:** `piles = [3, 6, 7, 11]`, `h = 8`
- `maxPile = 11`
- Binary search:
    - `low=1`, `high=11`
    - `mid=6`: hours = 1 + 1 + 2 + 2 = 6 ≤ 8 → high=6
    - `low=1`, `high=6`
    - `mid=3`: hours = 1 + 2 + 3 + 4 = 10 > 8 → low=4
    - `low=4`, `high=6`
    - `mid=5`: hours = 1 + 2 + 2 + 3 = 8 ≤ 8 → high=5
    - `low=4`, `high=5`
    - `mid=4`: hours = 1 + 2 + 2 + 3 = 8 ≤ 8 → high=4
- Loop ends, `low=4`.
- Return `4`.

---

## Approach 3: Optimized Binary Search (Most Efficient)

This is essentially the same as Approach 2, but emphasizes the binary search's efficiency and clarity.

### Summary:

- Use binary search on speed.
- Check feasibility at each mid point.
- Narrow down the search to find the minimal speed that allows finishing within `h`.

---

# Final Notes:

- The **binary search approach** is the most optimal for large input sizes.
- Always verify edge cases:
    - When `piles` contain only one pile.
    - When `h` is very large (can finish with speed 1).
    - When `h` equals the total number of bananas (must eat at max speed).

---

## Quick Reference:

| Approach | Time Complexity | Space Complexity | Key Idea |
| --- | --- | --- | --- |
| Brute Force | `O(maxPile * n)` | `O(1)` | Try all speeds from 1 to maxPile |
| Binary Search | `O(n log M)` | `O(1)` | Binary search over possible speeds |

---

Feel free to use this as a quick revision guide or a detailed explanation for interviews!
