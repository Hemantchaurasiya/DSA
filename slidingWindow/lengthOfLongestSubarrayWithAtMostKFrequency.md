# Length of Longest Subarray With At Most K Frequency

This guide covers various approaches to solve the problem of finding the **length of the longest subarray where no element appears more than `K` times**. It progresses from brute-force methods to optimal sliding window solutions, designed for quick understanding and interview preparation.

---

## Problem Summary
Given an array `arr` and an integer `K`, find the length of the longest subarray such that **no element appears more than `K` times** within that subarray.

---

## Approach 1: Brute Force

### **Core Idea:**
Check all possible subarrays, count the frequency of each element in each, and determine if the maximum frequency ≤ `K`.

---

### **Algorithm:**
1. Generate all subarrays:
   - For each starting index `i`, iterate over ending index `j ≥ i`.
2. For each subarray `arr[i..j]`:
   - Count the frequency of all elements.
   - Check if any element's frequency exceeds `K`.
   - If not, update maximum length.

---

### **Java Code:**
```java
public int longestSubarrayWithAtMostKFreq(int[] arr, int K) {
    int n = arr.length;
    int maxLength = 0;

    for (int i = 0; i < n; i++) {
        Map<Integer, Integer> freqMap = new HashMap<>();
        for (int j = i; j < n; j++) {
            freqMap.put(arr[j], freqMap.getOrDefault(arr[j], 0) + 1);
            if (freqMap.get(arr[j]) > K) {
                break; // No need to continue further as constraint violated
            }
            maxLength = Math.max(maxLength, j - i + 1);
        }
    }
    return maxLength;
}
```

---

### **Complexity Analysis:**

- **Time Complexity:**  
  **O(n^3)** — For each subarray (`O(n^2)`), counting frequencies takes `O(n)`.

- **Space Complexity:**  
  **O(n)** — Frequency map size in worst case.

---

### **Dry Run:**

**Input:**
```plaintext
arr = [1, 2, 2, 1, 2], K=2
```

- For the subarray `[1, 2, 2]`, frequencies: 1→1, 2→2 → valid.
- For `[2, 2, 1]`, frequencies: 2→2, 1→1 → valid.
- For `[1, 2, 2, 1]`, frequencies: 1→2, 2→2 → valid.
- For `[2, 2, 1, 2]`, frequencies: 2→3 → invalid, stop.

---

## Approach 2: Sliding Window (Most Efficient)

### **Core Idea:**
Use a sliding window with two pointers to efficiently find the longest valid subarray where no element exceeds `K` frequency.

### **Algorithm:**
1. Initialize two pointers: `left = 0`, `right = 0`.
2. Use a HashMap to keep track of element frequencies within the window.
3. Expand `right`:
   - Add `arr[right]` to the frequency map.
   - If any element's frequency exceeds `K`, shrink from `left`:
     - Remove or decrement `arr[left]`'s frequency.
     - Move `left` forward until all frequencies are ≤ `K`.
4. Track the maximum window size during the process.

---

### **Java Code:**
```java
public int longestSubarrayWithAtMostKFreq(int[] arr, int K) {
    int n = arr.length;
    int left = 0, maxLength = 0;
    Map<Integer, Integer> freqMap = new HashMap<>();

    for (int right = 0; right < n; right++) {
        freqMap.put(arr[right], freqMap.getOrDefault(arr[right], 0) + 1);

        // Shrink window if any element exceeds K
        while (hasElementWithFrequencyGreaterThanK(freqMap, K)) {
            freqMap.put(arr[left], freqMap.get(arr[left]) - 1);
            if (freqMap.get(arr[left]) == 0) {
                freqMap.remove(arr[left]);
            }
            left++;
        }
        maxLength = Math.max(maxLength, right - left + 1);
    }
    return maxLength;
}

private boolean hasElementWithFrequencyGreaterThanK(Map<Integer, Integer> map, int K) {
    for (int freq : map.values()) {
        if (freq > K) {
            return true;
        }
    }
    return false;
}
```

*Optimization Tip:* Maintain a variable to track if any element exceeds `K` without scanning the entire map every time.

---

### **Complexity Analysis:**

- **Time Complexity:**  
  **O(n)** — Each element is visited at most twice (once when expanding `right`, once when moving `left`).

- **Space Complexity:**  
  **O(n)** — HashMap size in worst case.

---

### **Dry Run:**

**Input:**
```plaintext
arr = [1, 2, 2, 1, 2], K=2
```

- Expand `right`, updating frequencies:
  - `[1]`: freq: 1→1 → valid, max length=1
  - `[1, 2]`: freq: 1→1, 2→1 → valid, max length=2
  - `[1, 2, 2]`: freq: 1→1, 2→2 → valid, max length=3
  - `[1, 2, 2, 1]`: freq: 1→2, 2→2 → valid, max length=4
  - `[1, 2, 2, 1, 2]`: freq: 1→2, 2→3 → invalid, move `left` and adjust.

---

## Approach 3: Binary Search + Sliding Window (Advanced)

### **Core Idea:**
Use binary search on the length of the subarray:
- For a mid value, check whether there exists a subarray of length `mid` where no element exceeds `K` frequency.
- Adjust binary search bounds accordingly.

### **Algorithm:**
1. Set `low=1`, `high=n`.
2. While `low ≤ high`:
   - Compute `mid = (low + high) / 2`.
   - Check whether any subarray of length `mid` satisfies the condition.
   - If yes, move `low` to `mid+1` (try for longer); else `high` to `mid-1`.
3. Result is `high` after the loop.

---

### **Note:**
This approach is more complex to implement but offers a way to find the maximum length efficiently for large `n`.

---

## Final Tips:
- For large arrays, the sliding window approach is most practical.
- Always maintain counts efficiently to avoid unnecessary scans.
- Use binary search for very large constraints where a direct approach is slow.

---

This completes the structured revision for **Length of Longest Subarray With At Most K Frequency**. Focus on understanding the sliding window solution as it is the most optimal for typical constraints.
