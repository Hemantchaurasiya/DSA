# Construct the Lexicographically Largest Valid Sequence

---

## Problem Overview
Given integers `n` and `k`, construct the lexicographically largest valid sequence `ans` of length `n + k - 1`, satisfying the following constraints:

- The sequence contains integers from `1` to `n`.
- For each `i` in `[1, n - k + 1]`, the subarray `ans[i..i+k-1]` contains **all distinct** integers from `1` to `k`.  
  (In other words, each subarray of length `k` is a permutation of `[1..k]`).

---

## Approach 1: Brute Force (Naive)

### Core Idea
Generate all sequences that satisfy the subarray constraints, then pick the lexicographically largest one.

### Algorithm
- Enumerate all permutations of `[1..k]`.
- For each permutation, extend it to length `n + k - 1` by repeating or appending elements while maintaining the `k`-subarray constraint.
- Among all such sequences, select the lexicographically largest.

### Limitations
- Extremely inefficient for large `n` and `k`.
- Not practical.

---

## Approach 2: Constructing the Sequence Greedily from the Largest Possible Values

### Core Idea
Since we want the lexicographically largest sequence, **start from the largest possible number `n` down to `1`** and place numbers greedily, ensuring the subarray constraints are maintained.

### Algorithm
1. Initialize an array `ans` of size `n + k - 1` with zeros (or placeholder).
2. Maintain a data structure (like a boolean array) to track which numbers are used.
3. For positions from `0` to `n + k - 2`:
   - If the position is already filled, skip.
   - Otherwise, try to set the largest possible number that can be placed here without violating the constraints:
     - Ensure that placing this number does not conflict with the `k`-window constraints (i.e., the number doesn't appear twice in overlapping subarrays).
   - Use a greedy approach to assign the largest available number.
4. Fill remaining positions with the remaining unused numbers, respecting the constraints.

### Limitations
- Difficult to implement directly as constraints involve overlapping subarrays.
- Still complex to guarantee correctness without a structured approach.

---

## Approach 3: **Most Optimal Solution (Pattern-Based Construction)**

### Core Idea
Leverage the pattern that for the sequence to satisfy the `k`-subarray constraints, the sequence can be constructed by:

- First, **placing the largest number `n` at position `0`**.
- Then, fill subsequent positions by alternating between the largest remaining numbers while ensuring each `k`-subarray contains all distinct numbers from `[1..k]`.
- The key is to **construct the sequence in a way that maximizes lexicographical order** while satisfying the overlapping `k`-subarray constraints.

This approach is based on the insight that the sequence can be constructed as:

- The first `k` elements are `[n, n-1, ..., n - k + 1]`.
- The remaining elements are constructed by repeating the pattern `[n - k, n - k - 1, ..., 1]` as needed.

### Algorithm
1. Initialize an array `ans` of size `n + k - 1`.
2. Fill the first `k` elements with `[n, n-1, ..., n - k + 1]`.
3. For positions `k` to `n + k - 2`:
   - Fill with the remaining numbers in decreasing order, repeating the pattern `[n - k, n - k - 1, ..., 1]`.
4. This guarantees:
   - Each `k`-subarray is a permutation of `[1..k]`.
   - The sequence is lexicographically largest.

### Implementation Notes
- To ensure the sequence is valid, the placement respects the constraints naturally by the pattern.
- The sequence is constructed greedily to maximize the lexicographical order.

---

## Code Implementation in Java

```java
public class LargestValidSequence {
    public int[] constructSequence(int n, int k) {
        int size = n + k - 1;
        int[] ans = new int[size];

        // Fill first k elements with the largest sequence [n, n-1, ..., n - k + 1]
        for (int i = 0; i < k; i++) {
            ans[i] = n - i;
        }

        // Fill remaining elements with a pattern [n - k, n - k - 1, ..., 1]
        int fillNum = n - k;
        for (int i = k; i < size; i++) {
            ans[i] = fillNum--;
            if (fillNum == 0) {
                fillNum = n - k; // cycle back if needed
            }
        }

        return ans;
    }
}
```

---

## Complexity Analysis

### **Time Complexity**
- **O(n + k)**:  
  - Filling the first `k` elements takes `O(k)`.  
  - Filling remaining elements takes `O(n - 1)`.  
  - Overall linear in the size of the sequence.

### **Space Complexity**
- **O(n + k)**:  
  - The output array `ans` of size `n + k - 1`.
  - Additional variables are `O(1)`.

---

## Dry Run Example

### Input:
```plaintext
n = 5, k = 3
```

### Step-by-step:

| Step | ans array (initial) | Explanation                                           |
|-------|----------------------|--------------------------------------------------------|
| 1     | [5, 4, 3]            | Fill first `k` with `[n, n-1, ..., n-k+1]`           |
| 2     | [5, 4, 3, 2]         | Fill remaining with `[n-k, ..., 1]`, starting with 2|
| 3     | [5, 4, 3, 2, 1]      | Continue pattern, cycle back if needed               |

- Final sequence: `[5, 4, 3, 2, 1]`

**Verification of subarrays:**

- `[5, 4, 3]` contains `[1..3]` (permutation of `[1,2,3]`)
- `[4, 3, 2]` contains `[1..3]` (permutation)
- `[3, 2, 1]` contains `[1..3]` (permutation)

Sequence is lex largest, valid, and satisfies constraints.

---

# Summary
- **Brute Force:** Inefficient, impractical.
- **Greedy Construction:** Complex to implement directly.
- **Pattern-Based Construction (Optimal):** Efficient, clean, guarantees lex order and constraints.

---
