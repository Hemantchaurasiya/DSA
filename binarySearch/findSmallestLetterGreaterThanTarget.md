# Find Smallest Letter Greater Than Target

This problem asks: given a sorted list of **letters** (e.g., `['c', 'f', 'j']`), and a **target letter**, find the **smallest letter** in the list that is **greater** than the target. If no such letter exists, return the **first letter** (considering circular wrap-around).

---

## Approach 1: Brute Force

### Core Idea:

Check each letter in the list, and find the one that is greater than the target with the **minimum** value.

### Algorithm:

1. Initialize a variable `result` as `null` or a placeholder for the smallest letter greater than target.
2. Loop through each letter in the array:
    - If the letter is **greater** than the target:
        - If `result` is `null` or the current letter is **smaller** than `result`, update `result`.
3. If after the loop, `result` is still `null`, it means no letter greater than target was found, so return the **first** letter in the list (wrap-around).
4. Else, return `result`.

### Java Code:

```java
public char nextGreatestLetter(char[] letters, char target) {
    Character result = null;
    for (char letter : letters) {
        if (letter > target) {
            if (result == null || letter < result) {
                result = letter;
            }
        }
    }
    return result != null ? result : letters[0];
}

```

### Complexity Analysis:

- **Time Complexity:** **O(n)**, where n is the length of `letters`, because we traverse the entire list once.
- **Space Complexity:** **O(1)**, no extra space used apart from variables.

### Dry Run:

| Input | letters = ['c', 'f', 'j'], target = 'a' |
| --- | --- |
| Step 1 | result = null |
| Step 2 | 'c' > 'a'? Yes, result = 'c' |
| Step 3 | 'f' > 'a'? Yes, 'f' < 'c'? No, so skip |
| Step 4 | 'j' > 'a'? Yes, 'j' < 'c'? No, skip |
| Final | Return 'c' |

---

## Approach 2: Binary Search (Optimized Solution)

### Core Idea:

Use **binary search** on the sorted array to find the **smallest letter greater than target** efficiently. Since the array is sorted, binary search can locate the position of the target or the next greater element in **O(log n)** time.

### Algorithm:

1. Initialize `low` = 0, `high` = length of array - 1.
2. While `low` <= `high`:
    - Calculate `mid = low + (high - low) / 2`.
    - If `letters[mid]` <= `target`:
        - Move `low` to `mid + 1` (search in the right half).
    - Else:
        - Move `high` to `mid - 1` (search in the left half).
3. After the loop:
    - If `low` is within bounds, `letters[low]` is the smallest letter greater than target.
    - If `low` exceeds bounds, wrap around and return `letters[0]`.

### Java Code:

```java
public char nextGreatestLetter(char[] letters, char target) {
    int low = 0, high = letters.length - 1;
    while (low <= high) {
        int mid = low + (high - low) / 2;
        if (letters[mid] <= target) {
            low = mid + 1;
        } else {
            high = mid - 1;
        }
    }
    // If low is within array bounds, it points to the smallest greater letter
    // Else, wrap around to the first element
    return low < letters.length ? letters[low] : letters[0];
}

```

### Complexity Analysis:

- **Time Complexity:** **O(log n)**, since binary search halves the search space each iteration.
- **Space Complexity:** **O(1)**, no extra space apart from pointers.

### Dry Run:

| Input | letters = ['c', 'f', 'j'], target = 'd' |
| --- | --- |
| Step 1 | low = 0, high = 2 |
| Step 2 | mid = 1 ('f') |
|  | 'f' > 'd'? Yes, high = 0 |
| Step 3 | low = 0, high = 0 |
| Step 4 | mid = 0 ('c') |
|  | 'c' <= 'd'? Yes, low = 1 |
| Loop ends since low = 1 > high = 0 |  |
| Final | low = 1, within array bounds, return `letters[1] = 'f'` |

---

## Summary:

| Approach | Time Complexity | Space Complexity | When to Use |
| --- | --- | --- | --- |
| **Brute Force** | O(n) | O(1) | Small arrays or quick implementation |
| **Binary Search** | O(log n) | O(1) | Large, sorted arrays for optimal performance |

---

This revision note covers the core ideas, step-by-step algorithms, code, complexity, and dry runs for both brute-force and binary search solutions to the **Find Smallest Letter Greater Than Target** problem. Use the binary search approach for efficiency in large datasets!
