# Next Letter

---

## Problem Overview:

Given a string `letters` sorted in non-decreasing order and a target character `key`, find the smallest character in the `letters` that is **strictly greater** than `key`. If no such character exists, return the first character in the string (wrap around).

---

## Approach 1: **Brute Force**

### Core Idea:

Iterate through all characters in the array to find the first character that is greater than `key`. If none found, return the first character (wrap-around).

### Algorithm:

1. Loop through each character in the array.
2. Check if the current character is greater than `key`.
3. If yes, return that character immediately.
4. If the loop completes without finding such a character, return the first character in the array (wrap-around case).

### Java Code:

```java
public char nextGreatestLetter(char[] letters, char key) {
    for (char ch : letters) {
        if (ch > key) {
            return ch;
        }
    }
    return letters[0]; // Wrap-around case
}

```

### Complexity Analysis:

- **Time Complexity:** O(n), where n is the length of `letters`. In the worst case, we scan through the entire array.
- **Space Complexity:** O(1), no extra space used besides variables.

### Dry Run:

- **Input:** `letters = ['c', 'f', 'j'], key = 'a'`
- **Execution:**
    - 'c' > 'a'? Yes → return 'c'.
- **Output:** `'c'`

---

## Approach 2: **Binary Search (Optimized)**

### Core Idea:

Since `letters` is sorted, we can efficiently find the smallest character greater than `key` using binary search.

### Algorithm:

1. Initialize `low = 0` and `high = length of letters - 1`.
2. While `low <= high`:
    - Calculate `mid = low + (high - low) / 2`.
    - If `letters[mid]` is **less than or equal to** `key`, move `low` to `mid + 1`.
    - Else, move `high` to `mid - 1` to search in the left part.
3. After the loop ends, `low` will be at the position of the smallest character greater than `key`.
4. If `low` is equal to the length of the array, wrap around and return the first character.

### Java Code:

```java
public char nextGreatestLetter(char[] letters, char key) {
    int low = 0, high = letters.length - 1;
    while (low <= high) {
        int mid = low + (high - low) / 2;
        if (letters[mid] <= key) {
            low = mid + 1;
        } else {
            high = mid - 1;
        }
    }
    return low == letters.length ? letters[0] : letters[low];
}

```

### Complexity Analysis:

- **Time Complexity:** O(log n), due to binary search.
- **Space Complexity:** O(1), no extra space used.

### Dry Run:

- **Input:** `letters = ['c', 'f', 'j'], key = 'a'`
- **Execution:**
    - `low = 0`, `high = 2`
    - Calculate `mid = 1` (`letters[mid] = 'f'`)
    - `'f' > 'a'`? Yes → `high = mid - 1 = 0`
    - Now, `low=0`, `high=0`
    - Calculate `mid=0` (`letters[mid] = 'c'`)
    - `'c' > 'a'`? Yes → `high = mid - 1 = -1`
    - Loop ends (`low=0`, `high=-1`)
    - `low=0` which is within array bounds, so return `letters[0] = 'c'`.
- **Output:** `'c'`

---

## Summary:

| Approach | Core Idea | Algorithm | Code | Time Complexity | Space Complexity | Dry Run Example |
| --- | --- | --- | --- | --- | --- | --- |
| Brute Force | Linear scan for first char > key | Loop through array, check each char | Provided | O(n) | O(1) | `['c','f','j']`, key='a' → `'c'` |
| Binary Search | Use sorted property for efficient search | Binary search for first char > key | Provided | O(log n) | O(1) | `['c','f','j']`, key='a' → `'c'` |

---

## Final Notes:

- Use **binary search** for optimal performance in sorted arrays.
- Always consider **wrap-around** cases when no greater element exists.
- The problem demonstrates the power of leveraging sorted data for efficient searching.

---

**End of Notes**
