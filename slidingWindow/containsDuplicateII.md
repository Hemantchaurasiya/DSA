# Contains Duplicate II

---

## Problem Overview
Given an array of integers `nums` and an integer `k`, determine if there are **two distinct indices** `i` and `j` in the array such that:
- `nums[i] == nums[j]`, and
- `|i - j| <= k`.

**This problem checks for nearby duplicates within a specific range `k`.**

---

## Approach 1: Brute Force

### Core Idea
Compare every pair of elements to check if they are equal and within `k` distance.

### Algorithm
1. Loop through each element `i` in the array.
2. For each `i`, check subsequent elements `j` from `i+1` to `i+k` (or the end of array).
3. If `nums[i] == nums[j]` and `|i - j| <= k`, return `true`.
4. If no such pair is found after all iterations, return `false`.

### Java Code
```java
public boolean containsNearbyDuplicate(int[] nums, int k) {
    int n = nums.length;
    for (int i = 0; i < n; i++) {
        for (int j = i + 1; j <= i + k && j < n; j++) {
            if (nums[i] == nums[j]) {
                return true;
            }
        }
    }
    return false;
}
```

### Complexity Analysis
- **Time Complexity:** **O(n * k)**, since for each element, we may check up to `k` subsequent elements.
- **Space Complexity:** **O(1)**, no extra space used apart from variables.

### Dry Run
- Input: `nums = [1, 2, 3, 1], k = 3`
- Step-by-step:
  
| i | nums[i] | Checks j from i+1 to i+k | j | nums[j] | Match? | Result |
|---|---------|--------------------------|---|---------|---------|---------|
| 0 | 1       | 1 to 3                   | 1 | 2       | No      | Continue |
|   |         |                          | 2 | 3       | No      | Continue |
|   |         |                          | 3 | 1       | Yes     | Return `true` |

---

## Approach 2: Hash Map / Sliding Window (Optimal)

### Core Idea
Use a **HashMap** or **HashSet** to keep track of elements within the current window of size `k`. As we iterate:
- If an element repeats within the last `k` elements, return `true`.
- Use the sliding window technique to maintain only relevant elements.

### Algorithm
1. Initialize an empty HashMap or HashSet.
2. Iterate through the array:
   - If the element is already in the HashMap/HashSet, check if the difference in indices is within `k`.
   - If the element exists and the index difference is `<= k`, return `true`.
   - Insert the current element into the HashMap/HashSet.
   - If the size exceeds `k`, remove the element that is now outside the sliding window (i.e., at index `i - k`).
3. If no duplicates found within `k`, return `false`.

### Java Code
```java
import java.util.HashSet;

public boolean containsNearbyDuplicate(int[] nums, int k) {
    HashSet<Integer> set = new HashSet<>();
    for (int i = 0; i < nums.length; i++) {
        if (set.contains(nums[i])) {
            return true;
        }
        set.add(nums[i]);
        if (i >= k) {
            set.remove(nums[i - k]);
        }
    }
    return false;
}
```

### Complexity Analysis
- **Time Complexity:** **O(n)**, as each element is added and removed at most once.
- **Space Complexity:** **O(min(n, k))**, since the HashSet stores at most `k + 1` elements.

### Dry Run
- Input: `nums = [1, 2, 3, 1], k = 3`

| i | nums[i] | HashSet contents | Action | Explanation |
|---|---------|------------------|---------|-------------|
| 0 | 1       | {1}              | Add 1   | Insert 1 into set |
| 1 | 2       | {1, 2}           | Add 2   | Insert 2 into set |
| 2 | 3       | {1, 2, 3}        | Add 3   | Insert 3 into set |
| 3 | 1       | {1, 2, 3}        | Already exists | Return `true` |

---

## Summary
| Method | Approach | Time Complexity | Space Complexity | Key Points |
|---------|--------------|-------------------|---------------------|------------|
| 1 | Brute Force | **O(n * k)** | **O(1)** | Checks every pair within `k` range. Not efficient for large input. |
| 2 | Sliding Window + HashSet | **O(n)** | **O(k)** | Efficient, maintains a sliding window of size `k`. Suitable for large inputs. |

---

## Final Tips for Interviews
- Always consider the constraints on input size to choose the best approach.
- The sliding window approach is optimal for this problem.
- Practice variations like **contains Duplicate** (without range constraint) and **contains nearby almost duplicate** (using trees or balanced data structures).

---

**Happy Coding!**
