# Find the Kth Smallest Pair Distance

---

## Problem Statement:
Given an integer array `nums` of length `n`, find the **Kth smallest** pair distance among all pairs `(nums[i], nums[j])` where `i < j`. The **pair distance** is defined as `|nums[i] - nums[j]|`.

---

## Approach 1: Brute Force (Compute All Pair Distances)

### **Core Idea:**
Compute the distance for every pair, store all distances, sort them, and select the Kth smallest.

### **Algorithm:**
1. Generate all pairs `(i, j)` with `i < j`.
2. Calculate `|nums[i] - nums[j]|` for each pair.
3. Store all these distances in an array or list.
4. Sort the list of distances.
5. Return the element at index `K-1`.

### **Java Code:**
```java
public int smallestDistancePair(int[] nums, int k) {
    List<Integer> distances = new ArrayList<>();
    int n = nums.length;
    for (int i = 0; i < n; i++) {
        for (int j = i + 1; j < n; j++) {
            distances.add(Math.abs(nums[i] - nums[j]));
        }
    }
    Collections.sort(distances);
    return distances.get(k - 1);
}
```

### **Complexity Analysis:**
- **Time Complexity:** O(n² log n²) = O(n² log n) due to generating all pairs and sorting.
- **Space Complexity:** O(n²) for storing all pair distances.

### **Dry Run:**
- `nums = [1, 3, 1]`, `k=2`
- Pairs and distances:
  - `(1,3)` → 2
  - `(1,1)` → 0
  - `(3,1)` → 2
- Sorted distances: `[0, 2, 2]`
- K=2 → answer = 2

---

## Approach 2: Binary Search on Distance (Optimal Approach)

### **Core Idea:**
The pair distances are bounded between `0` and `max(nums) - min(nums)`. Use binary search on this range to find the **Kth smallest distance** by counting how many pairs are less than or equal to a mid-distance.

### **Algorithm:**
1. Sort the array `nums`.
2. Initialize `low = 0`, `high = max(nums) - min(nums)`.
3. While `low < high`:
   - Let `mid = (low + high) / 2`.
   - Count the number of pairs with distance ≤ `mid`.
   - If count ≥ `k`, move `high` to `mid`.
   - Else, move `low` to `mid + 1`.
4. When the loop ends, `low` is the Kth smallest distance.

### **Counting Pairs:**
- Use two pointers:
  - For each `i`, move `j` forward until `nums[j] - nums[i] > mid`.
  - The number of pairs with `nums[i]` as the first element and distance ≤ `mid` is `j - i - 1`.

### **Java Code:**
```java
public int smallestDistancePair(int[] nums, int k) {
    Arrays.sort(nums);
    int n = nums.length;
    int low = 0;
    int high = nums[n - 1] - nums[0];

    while (low < high) {
        int mid = low + (high - low) / 2;
        int count = 0;
        int j = 0;
        for (int i = 0; i < n; i++) {
            while (j < n && nums[j] - nums[i] <= mid) {
                j++;
            }
            count += j - i - 1;
        }
        if (count >= k) {
            high = mid;
        } else {
            low = mid + 1;
        }
    }
    return low;
}
```

### **Complexity Analysis:**
- **Time Complexity:** O(n log(max(nums) - min(nums))) — binary search over the range, each iteration counting pairs in O(n).
- **Space Complexity:** O(1) — in-place and auxiliary variables only.

### **Dry Run:**
- `nums = [1, 3, 1]`, `k=2`
- Sorted: `[1, 1, 3]`
- `max - min = 3 - 1 = 2`
- Binary search range: `[0, 2]`
- Mid = 1:
  - Count pairs with distance ≤ 1:
    - i=0 (value=1): j moves until `nums[j]-nums[i] > 1`
      - j=1 (value=1): difference=0 ≤1 → j=2
      - j=2 (value=3): difference=2 >1 → stop
      - count += 2 - 0 - 1 = 1
    - i=1 (value=1): j=2 (value=3), difference=2 >1 → j remains 2, count += 2 - 1 - 1=0
    - total count=1
  - Since count=1 < k=2, low=mid+1=2
- Now low=2, high=2 → loop ends, answer=2

---

## Summary & Best Practices:
- The **brute-force** approach is simple but only feasible for small inputs.
- The **binary search** approach is efficient and suitable for large inputs, leveraging the sorted array and counting pairs.
- Always sort `nums` before applying the binary search.
- Carefully implement the pair counting with two pointers to optimize performance.
- Use binary search over the value range instead of the array indices.

---

## Final Tips:
- Practice both approaches to understand their trade-offs.
- Master binary search on answer space for problems involving order statistics.
- Visualize the range of possible distances to understand the binary search boundaries.
- Write clean code with correct pointer updates to avoid off-by-one errors.

Happy coding!
