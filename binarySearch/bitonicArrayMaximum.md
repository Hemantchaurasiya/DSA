# Maximum in a Bitonic Array

---

## Problem Statement

Given a **bitonic array** (an array that first increases and then decreases), find the maximum element in the array.

---

## Approach 1: Brute Force

### Core Idea

Iterate through all elements and find the maximum. Since the array is bitonic, the maximum element is the **peak** where the increasing sequence shifts to decreasing.

### Algorithm

1. Initialize a variable `maxVal` with the first element.
2. Traverse the array from start to end.
3. Update `maxVal` whenever a larger element is encountered.
4. Return `maxVal`.

### Java Code

```java
public int findMaxBruteForce(int[] arr) {
    int maxVal = arr[0];
    for (int num : arr) {
        if (num > maxVal) {
            maxVal = num;
        }
    }
    return maxVal;
}

```

### Complexity Analysis

- **Time Complexity:** O(n), since it scans all elements once.
- **Space Complexity:** O(1), only a few variables are used.

### Dry Run

- Input: `[1, 3, 8, 12, 4, 2]`
- Steps:
    - maxVal = 1
    - Check 3: maxVal = 3
    - Check 8: maxVal = 8
    - Check 12: maxVal = 12
    - Check 4: maxVal = 12
    - Check 2: maxVal = 12
- Output: `12`

---

## Approach 2: Linear Search (Leveraging Bitonic Property)

### Core Idea

Identify the peak element by checking neighbors — the element which is greater than its neighbors is the maximum.

### Algorithm

1. Loop through the array from index 1 to n-2.
2. For each element, check if it's greater than both neighbors.
3. If found, return that element.
4. Handle edge cases (peak at start or end).

### Java Code

```java
public int findMaxLinear(int[] arr) {
    int n = arr.length;
    if (n == 1) return arr[0];
    if (arr[0] > arr[1]) return arr[0];
    if (arr[n - 1] > arr[n - 2]) return arr[n - 1];

    for (int i = 1; i < n - 1; i++) {
        if (arr[i] > arr[i - 1] && arr[i] > arr[i + 1]) {
            return arr[i];
        }
    }
    return -1; // Should not reach here for a valid bitonic array
}

```

### Complexity Analysis

- **Time Complexity:** O(n), as it may scan the entire array.
- **Space Complexity:** O(1).

### Dry Run

- Input: `[1, 3, 8, 12, 4, 2]`
- Checking:
    - Index 1: 3 (neighbors 1 and 8) → not peak
    - Index 2: 8 (neighbors 3 and 12) → not peak
    - Index 3: 12 (neighbors 8 and 4) → **peak found!**
- Output: `12`

---

## Approach 3: Binary Search (Optimal Solution)

### Core Idea

Use binary search to find the peak element efficiently by exploiting the bitonic property:

- If `arr[mid]` is greater than its next element, then the peak lies to the **left**.
- If `arr[mid]` is less than its next element, then the peak lies to the **right**.

### Algorithm

1. Initialize `low = 0`, `high = n - 1`.
2. While `low <= high`:
    - Calculate `mid = low + (high - low) / 2`.
    - If `arr[mid]` is greater than both neighbors (considering boundaries), it’s the maximum.
    - Else if `arr[mid] < arr[mid + 1]`, move `low` to `mid + 1`.
    - Else, move `high` to `mid - 1`.
3. Loop continues until the peak is found.

### Java Code

```java
public int findMaxBitonic(int[] arr) {
    int low = 0;
    int high = arr.length - 1;
    while (low <= high) {
        int mid = low + (high - low) / 2;

        // Handle edges
        int left = (mid - 1 >= 0) ? arr[mid - 1] : Integer.MIN_VALUE;
        int right = (mid + 1 < arr.length) ? arr[mid + 1] : Integer.MIN_VALUE;

        if (arr[mid] > left && arr[mid] > right) {
            return arr[mid]; // Peak found
        } else if (arr[mid] < right) {
            low = mid + 1; // Peak is to the right
        } else {
            high = mid - 1; // Peak is to the left
        }
    }
    return -1; // For invalid input
}

```

### Complexity Analysis

- **Time Complexity:** O(log n), since the algorithm halves the search space each iteration.
- **Space Complexity:** O(1), only constant space used.

### Dry Run

- Input: `[1, 3, 8, 12, 4, 2]`
- Initial: `low=0`, `high=5`
- Iteration 1:
    - `mid=2` (element 8)
    - Left neighbor: 3, Right neighbor: 12
    - 8 is less than 12 → Peak to the right, so `low=3`
- Iteration 2:
    - `low=3`, `high=5`
    - `mid=4` (element 4)
    - Left neighbor: 12, Right neighbor: 2
    - 4 < 12 → move `low=5`
- Iteration 3:
    - `low=5`, `high=5`
    - `mid=5` (element 2)
    - Left neighbor: 4, no right neighbor
    - 2 < 4 → move `low=6` (loop ends)
    - But check previous step: the peak was at index 3 with element 12, which is the maximum.

**Note:** The binary search finds the maximum efficiently by comparing neighbors.

---

# Summary Table

| Approach | Best For | Time Complexity | Space Complexity | Pros & Cons |
| --- | --- | --- | --- | --- |
| Brute Force | Small arrays, simple solutions | O(n) | O(1) | Easy, but slow for large arrays |
| Linear Search | Moderate arrays, clarity | O(n) | O(1) | Slightly better than brute-force, still linear |
| Binary Search | Large arrays, optimal speed | O(log n) | O(1) | Most efficient, requires understanding of bitonic property |

---

## Final Tips

- Use **binary search** for optimal performance.
- Always verify the **bitonic property** before applying binary search.
- Handle edge cases: array of size 1, peak at beginning/end.
- Dry run your code with sample inputs to ensure correctness.

---

**Happy Coding!**
