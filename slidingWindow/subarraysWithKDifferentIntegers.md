# Subarrays with K Different Integers

## Problem Overview
Given an integer array `nums` and an integer `k`, **return the number of subarrays that contain exactly `k` distinct integers**.

---

## 1. Brute Force Approach

### **Core Idea**
- Generate all possible subarrays.
- Count the number of distinct integers in each subarray.
- Increment the count if the number of distinct integers equals `k`.

### **Algorithm**
1. Loop through each starting index `i`.
2. For each `i`, expand the end index `j` (`j >= i`).
3. Use a set or hash map to keep track of unique elements in `nums[i..j]`.
4. If the size of the set equals `k`, increment the count.
5. Continue until all subarrays are checked.

### **Java Code**

```java
public int subarraysWithKDistinctBruteForce(int[] nums, int k) {
    int count = 0;
    int n = nums.length;
    for (int start = 0; start < n; start++) {
        Set<Integer> distinct = new HashSet<>();
        for (int end = start; end < n; end++) {
            distinct.add(nums[end]);
            if (distinct.size() == k) {
                count++;
            } else if (distinct.size() > k) {
                break; // No need to check further as size will only grow
            }
        }
    }
    return count;
}
```

### **Complexity Analysis**
- **Time Complexity:** O(n²) (nested loops and set operations)
- **Space Complexity:** O(k) (for the set of distinct elements)

### **Dry Run (Example):**
`nums = [1, 2, 1, 2, 3], k=2`

| start | end | Subarray | Distinct Elements | Count? |
|--------|-------|------------|-------------------|---------|
| 0      | 0     | [1]        | {1}               | No      |
| 0      | 1     | [1, 2]     | {1, 2}            | Yes → count=1 |
| 0      | 2     | [1, 2, 1]  | {1, 2}            | Yes → count=2 |
| 0      | 3     | [1, 2, 1, 2]| {1, 2}          | Yes → count=3 |
| 0      | 4     | [1, 2, 1, 2, 3]| {1, 2, 3} | No (size > 2, break) |

And so on...

---

## 2. Better Solution: Sliding Window with Count of Exactly K Distinct

### **Core Idea**
- The problem asks for **exactly `k`** distinct integers.
- Use the fact that:  
  `subarrays with exactly k distinct = subarrays with at most k distinct - subarrays with at most (k-1) distinct`.

- Therefore, compute the number of subarrays with at most `k` distinct and at most `k-1` distinct, then subtract.

### **Algorithm**
1. Write a helper function `atMostKDistinct(nums, k)` that returns the count of subarrays with at most `k` distinct integers.
2. The answer = `atMostKDistinct(nums, k) - atMostKDistinct(nums, k-1)`.

### **Java Code**

```java
public int subarraysWithKDistinct(int[] nums, int k) {
    return atMostKDistinct(nums, k) - atMostKDistinct(nums, k - 1);
}

private int atMostKDistinct(int[] nums, int k) {
    Map<Integer, Integer> countMap = new HashMap<>();
    int left = 0, result = 0;

    for (int right = 0; right < nums.length; right++) {
        countMap.put(nums[right], countMap.getOrDefault(nums[right], 0) + 1);

        while (countMap.size() > k) {
            countMap.put(nums[left], countMap.get(nums[left]) - 1);
            if (countMap.get(nums[left]) == 0) {
                countMap.remove(nums[left]);
            }
            left++;
        }
        result += right - left + 1;
    }
    return result;
}
```

### **Complexity Analysis**
- **Time Complexity:** O(n) (each element is visited at most twice)
- **Space Complexity:** O(k) (hash map to store counts)

### **Dry Run (Example):**
`nums = [1, 2, 1, 2, 3], k=2`

- Count subarrays with at most 2 distinct: 10
- Count subarrays with at most 1 distinct: 5
- Result = 10 - 5 = 5

---

## 3. Summary of Approaches

| Approach                                   | Time Complexity | Space Complexity | Description                                              |
|--------------------------------------------|-----------------|------------------|----------------------------------------------------------|
| Brute Force                               | O(n²)          | O(k)             | Checks all subarrays directly                            |
| Sliding Window (atMostK - atMostK-1)     | O(n)           | O(k)             | Uses two sliding windows to compute exact count         |

---

## **Key Takeaways**
- The problem reduces to computing the difference between counts of subarrays with at most `k` and `k-1` distinct integers.
- Sliding window approach is efficient and suitable for large inputs.
- Always remember the relation:  
  `Exactly K = AtMostK - AtMost(K-1)`

---

**Happy coding!**
