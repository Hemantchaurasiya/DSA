# Successful Pairs of Spells and Potions

This problem involves finding the number of pairs `(spell, potion)` such that their product is **at least** a given `success` threshold. We will explore multiple approaches, starting from brute-force to optimized solutions, to deepen understanding and prepare for interviews.

---

## Problem Statement (Summary)

Given two integer arrays:

- `spells` (length `n`)
- `potions` (length `m`)
and an integer `success`, find **for each spell**, how many potions **can be paired** with it such that the product is **at least** `success`.

---

## Approach 1: Brute Force

### 1. Core Idea:

Check all possible pairs `(spell, potion)` and count those where `spell * potion >= success`.

### 2. Algorithm:

- Initialize a result array `res` of size `n`.
- For each spell:
    - Iterate over all potions.
    - Count the number of potions satisfying the condition.
- Return the result array.

### 3. Java Code:

```java
public int[] successfulPairsBruteForce(int[] spells, int[] potions, long success) {
    int n = spells.length;
    int m = potions.length;
    int[] res = new int[n];

    for (int i = 0; i < n; i++) {
        int count = 0;
        for (int j = 0; j < m; j++) {
            if ((long) spells[i] * potions[j] >= success) {
                count++;
            }
        }
        res[i] = count;
    }
    return res;
}

```

### 4. Complexity Analysis:

- **Time Complexity:** `O(n * m)` because for each spell, we check all potions.
- **Space Complexity:** `O(n)` for the result array.

### 5. Dry Run:

Suppose:

```
spells = [10, 20]
potions = [1, 2, 3, 4, 5]
success = 50

```

Step-by-step:

- For spell 10:
    - 10*1=10 <50 -> no
    - 10*2=20 <50 -> no
    - 10*3=30 <50 -> no
    - 10*4=40 <50 -> no
    - 10*5=50 >=50 -> yes (count=1)
- For spell 20:
    - 20*1=20 <50 -> no
    - 20*2=40 <50 -> no
    - 20*3=60 >=50 -> yes
    - 20*4=80 >=50 -> yes
    - 20*5=100 >=50 -> yes

Result: `[1, 3]`

---

## Approach 2: Sorting + Binary Search (Better Solution)

### 1. Core Idea:

- Sort the `potions` array.
- For each spell, determine the minimum potion value needed to reach `success`.
- Use binary search to find the count of potions greater than or equal to that value.

### 2. Algorithm:

- Sort `potions`.
- For each spell `s`:
    - Calculate `minPotion = (success + s - 1) / s` (ceiling division).
    - Use binary search in `potions` to find the **lower bound** of `minPotion`.
    - The number of valid potions = `length of potions` - index found.
- Store the counts in the result array.

### 3. Java Code:

```java
import java.util.Arrays;

public int[] successfulPairsBinarySearch(int[] spells, int[] potions, long success) {
    Arrays.sort(potions);
    int n = spells.length;
    int m = potions.length;
    int[] result = new int[n];

    for (int i = 0; i < n; i++) {
        long minPotion = (success + spells[i] - 1) / spells[i]; // Ceil division
        int index = lowerBound(potions, minPotion);
        result[i] = m - index;
    }
    return result;
}

private int lowerBound(int[] arr, long target) {
    int low = 0, high = arr.length;
    while (low < high) {
        int mid = low + (high - low) / 2;
        if (arr[mid] >= target) {
            high = mid;
        } else {
            low = mid + 1;
        }
    }
    return low;
}

```

### 4. Complexity Analysis:

- **Time Complexity:** `O(n log m)` because:
    - Sorting `potions`: `O(m log m)`
    - For each spell, binary search: `O(log m)`
    - Total: `O(m log m + n log m)`
- **Space Complexity:** `O(m)` for sorting `potions`.

### 5. Dry Run:

Using previous example:

```
spells = [10, 20]
potions = [1, 2, 3, 4, 5]
success = 50

```

Sorted `potions` = `[1, 2, 3, 4, 5]`

- For spell 10:
    - minPotion = (50 + 10 - 1)/10 = 59/10 = 5.9 → 6
    - lowerBound([1,2,3,4,5], 6) = 5 (index beyond last element)
    - Count = 5 - 5 = 0
- For spell 20:
    - minPotion = (50 + 20 - 1)/20 = 69/20 = 3.45 → 4
    - lowerBound([1,2,3,4,5], 4) = 3
    - Count = 5 - 3 = 2 (potions at indices 3 and 4: 4,5)

Result: `[0, 2]`

---

## Approach 3: Optimized Solution Using Binary Search with Longs

### 1. Core Idea:

Same as Approach 2 but using `long` throughout to handle potential overflow during multiplication or division, especially with large input values.

### 2. Algorithm:

- Convert `spells` and `potions` to long if necessary.
- Sort `potions`.
- For each spell, compute the required potion value and binary search for lower bound.
- Count valid potions accordingly.

### 3. Java Code:

```java
import java.util.Arrays;

public int[] successfulPairsOptimized(int[] spells, int[] potions, long success) {
    Arrays.sort(potions);
    int n = spells.length;
    int m = potions.length;
    int[] result = new int[n];

    for (int i = 0; i < n; i++) {
        long spell = spells[i];
        long minPotion = (success + spell - 1) / spell; // Ceiling division
        int index = lowerBoundLong(potions, minPotion);
        result[i] = m - index;
    }
    return result;
}

private int lowerBoundLong(long[] arr, long target) {
    int low = 0, high = arr.length;
    while (low < high) {
        int mid = low + (high - low) / 2;
        if (arr[mid] >= target) {
            high = mid;
        } else {
            low = mid + 1;
        }
    }
    return low;
}

```

### 4. Complexity Analysis:

- **Time Complexity:** `O(n log m)` (sorting + binary searches).
- **Space Complexity:** `O(m)` for the sorted `potions`.

---

## Summary Table

| Approach | Method | Time Complexity | Space Complexity | Use Case |
| --- | --- | --- | --- | --- |
| Brute Force | Nested loops | `O(n * m)` | `O(n)` | Small input sizes, initial implementation |
| Sorting + Binary Search | Sort + Binary Search | `O((n + m) log m)` | `O(m)` | Larger inputs, better performance |
| Optimized with Longs | Same as above but with `long` | `O((n + m) log m)` | `O(m)` | Very large inputs, avoiding overflow |

---

## Final Tips:

- Always consider the input size for choosing the right approach.
- Use sorting + binary search for efficient solution.
- Handle potential overflow with `long` data type.
- When implementing binary search, think about the **lower bound** logic for counting elements satisfying a condition.

---

This guide provides a comprehensive understanding of the problem, multiple solution strategies, their implementations, and their complexities, enabling quick revision and effective interview preparation.
