# Move Zeroes

---

## Problem Description
Given an array `nums`, write a function to **move all zeroes to the end** of the array **while maintaining the relative order** of the non-zero elements.

**Example:**
```plaintext
Input: [0, 1, 0, 3, 12]
Output: [1, 3, 12, 0, 0]
```

---

## Approach 1: Brute Force

### Core Idea
Traverse the array, count the number of zeros, then rebuild the array by placing all non-zero elements first followed by zeros.

### Algorithm
1. Count the total number of zeros in the array.
2. Create a new list or modify the original array:
   - First, copy all non-zero elements.
   - Then, append zeros at the end based on the count.
3. Copy back to the original array if needed.

### Java Code
```java
public void moveZeroesBruteForce(int[] nums) {
    int zeroCount = 0;
    int n = nums.length;
    
    // Count zeros
    for (int num : nums) {
        if (num == 0) zeroCount++;
    }

    int index = 0;
    // Add non-zero elements
    for (int num : nums) {
        if (num != 0) {
            nums[index++] = num;
        }
    }
    // Fill remaining with zeros
    while (index < n) {
        nums[index++] = 0;
    }
}
```

### Complexity Analysis
- **Time Complexity:** `O(n)` — Traverses the array twice (once to count, once to rebuild).
- **Space Complexity:** `O(1)` — Modifies the array in-place, no additional space used.

### Dry Run
Input: `[0, 1, 0, 3, 12]`

| Step | Count zeros | Array after moving non-zero elements | Zeros appended at end |
|-------|--------------|----------------------------------------|----------------------|
| Count zeros | 2 | `[0, 1, 0, 3, 12]` | - |
| Rebuild array: | - | `[1, 3, 12, ?, ?]` | Fill zeros at end |
| Final array | - | `[1, 3, 12, 0, 0]` | - |

---

## Approach 2: Better Solution (Two Pointers)

### Core Idea
Use two pointers:
- One (`lastNonZeroFoundAt`) to keep track of the position where the next non-zero element should be placed.
- Iterate through the array; whenever a non-zero element is encountered, swap it with the element at `lastNonZeroFoundAt` and increment `lastNonZeroFoundAt`.

### Algorithm
1. Initialize `lastNonZeroFoundAt` to 0.
2. Traverse the array:
   - If the current element is non-zero:
     - Swap it with the element at `lastNonZeroFoundAt`.
     - Increment `lastNonZeroFoundAt`.
3. Continue until the end.

### Java Code
```java
public void moveZeroesTwoPointers(int[] nums) {
    int lastNonZeroFoundAt = 0;
    for (int i = 0; i < nums.length; i++) {
        if (nums[i] != 0) {
            // Swap only if i != lastNonZeroFoundAt
            if (i != lastNonZeroFoundAt) {
                int temp = nums[i];
                nums[i] = nums[lastNonZeroFoundAt];
                nums[lastNonZeroFoundAt] = temp;
            }
            lastNonZeroFoundAt++;
        }
    }
}
```

### Complexity Analysis
- **Time Complexity:** `O(n)` — Single pass through the array.
- **Space Complexity:** `O(1)` — In-place swaps, no extra space.

### Dry Run
Input: `[0, 1, 0, 3, 12]`

| i | nums | lastNonZeroFoundAt | Operation                     | Array after operation     |
|---|-------|---------------------|------------------------------|---------------------------|
| 0 | [0,1,0,3,12] | 0 | nums[0]=0 → skip | `[0, 1, 0, 3, 12]` |
| 1 | [0,1,0,3,12] | 0 | nums[1]=1 ≠ 0 → swap with nums[0] | `[1, 0, 0, 3, 12]` | lastNonZeroFoundAt=1 |
| 2 | [1,0,0,3,12] | 1 | nums[2]=0 → skip | `[1, 0, 0, 3, 12]` |
| 3 | [1,0,0,3,12] | 1 | nums[3]=3 ≠ 0 → swap with nums[1] | `[1, 3, 0, 0, 12]` | lastNonZeroFoundAt=2 |
| 4 | [1,3,0,0,12] | 2 | nums[4]=12 ≠ 0 → swap with nums[2] | `[1, 3, 12, 0, 0]` | lastNonZeroFoundAt=3 |

---

## Approach 3: Optimal Solution (Single Pass, In-Place)

### Core Idea
Use one pass:
- Maintain a pointer (`insertPos`) to indicate the position to insert the next non-zero element.
- Iterate through the array:
  - When a non-zero element is found, swap it with the element at `insertPos`, then increment `insertPos`.
- After the first pass, all non-zero elements are at the front, zeros are at the end.

### Algorithm
1. Initialize `insertPos` to 0.
2. Traverse the array:
   - If the current element is non-zero:
     - Swap with element at `insertPos`.
     - Increment `insertPos`.
3. No extra steps needed, as the zeros naturally move to the end.

### Java Code
```java
public void moveZeroesOptimal(int[] nums) {
    int insertPos = 0;
    for (int i = 0; i < nums.length; i++) {
        if (nums[i] != 0) {
            // Swap only if i != insertPos
            if (i != insertPos) {
                int temp = nums[i];
                nums[i] = nums[insertPos];
                nums[insertPos] = temp;
            }
            insertPos++;
        }
    }
}
```

### Complexity Analysis
- **Time Complexity:** `O(n)` — Single traversal.
- **Space Complexity:** `O(1)` — In-place operation.

### Dry Run
Input: `[0, 1, 0, 3, 12]`

| i | nums | insertPos | Operation                     | Array after operation     |
|---|-------|-------------|------------------------------|---------------------------|
| 0 | [0,1,0,3,12] | 0 | nums[0]=0 → skip | `[0, 1, 0, 3, 12]` |
| 1 | [0,1,0,3,12] | 0 | nums[1]=1 ≠ 0 → swap with nums[0] | `[1, 0, 0, 3, 12]` | insertPos=1 |
| 2 | [1,0,0,3,12] | 1 | nums[2]=0 → skip | `[1, 0, 0, 3, 12]` |
| 3 | [1,0,0,3,12] | 1 | nums[3]=3 ≠ 0 → swap with nums[1] | `[1, 3, 0, 0, 12]` | insertPos=2 |
| 4 | [1,3,0,0,12] | 2 | nums[4]=12 ≠ 0 → swap with nums[2] | `[1, 3, 12, 0, 0]` | insertPos=3 |

---

## Summary

| Approach | Key Idea | Time Complexity | Space Complexity | Notes |
|------------|--------------|----------------------|-----------------------|--------|
| Brute Force | Count zeros, rebuild array | `O(n)` | `O(1)` | Extra pass, rebuilds array |
| Two Pointers | Swap non-zero elements forward | `O(n)` | `O(1)` | Efficient, preserves order |
| Optimal Single Pass | Single traversal with in-place swaps | `O(n)` | `O(1)` | Most efficient, maintains order |

---

## Final Tip
- Always prefer the **single-pass in-place solution** for optimal performance.
- Understand the difference between swapping and overwriting to avoid unnecessary operations.

---

**Happy Coding!**
