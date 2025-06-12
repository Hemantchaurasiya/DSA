# Fruits into Basket

This problem involves finding the length of the longest contiguous subarray (or window) containing **at most 2 distinct types of fruits**.

---

## 1. Brute Force Approach

### **Core Idea:**
Check all possible subarrays and determine the maximum length subarray that contains at most 2 distinct fruits.

### **Algorithm:**
1. Generate all possible subarrays.
2. For each subarray:
   - Count the distinct fruits.
   - If the number of distinct fruits ≤ 2, update the maximum length.
3. Keep track of the maximum length among all valid subarrays.

### **Java Code:**
```java
public int totalFruit(int[] fruits) {
    int maxLen = 0;
    int n = fruits.length;

    for (int i = 0; i < n; i++) {
        Set<Integer> distinctFruits = new HashSet<>();
        for (int j = i; j < n; j++) {
            distinctFruits.add(fruits[j]);
            if (distinctFruits.size() > 2) break;
            maxLen = Math.max(maxLen, j - i + 1);
        }
    }
    return maxLen;
}
```

### **Complexity Analysis:**
- **Time Complexity:** **O(n^3)**  
  - Generating all subarrays: O(n^2)  
  - Counting distinct fruits for each subarray: O(n)  
  - Total: O(n^3)

- **Space Complexity:** **O(1)** (or O(2) for the set, negligible)

---

## 2. Better Solution (Sliding Window with HashMap)

### **Core Idea:**
Use a sliding window to maintain at most 2 distinct fruits, expanding the window until more than 2 fruits are included, then shrinking from the start.

### **Algorithm:**
1. Initialize two pointers: `start = 0`.
2. Use a HashMap (or frequency map) to track counts of fruits in the current window.
3. Iterate with `end` from 0 to the end of the array:
   - Add the current fruit to the map and increment its count.
   - If the number of distinct fruits exceeds 2:
     - Shrink the window from the `start`, decrementing counts.
     - Remove fruits from the map if their count reaches zero.
   - Update the maximum window size (`end - start + 1`) when valid.

### **Java Code:**
```java
public int totalFruit(int[] fruits) {
    Map<Integer, Integer> countMap = new HashMap<>();
    int start = 0, maxLen = 0;

    for (int end = 0; end < fruits.length; end++) {
        countMap.put(fruits[end], countMap.getOrDefault(fruits[end], 0) + 1);

        while (countMap.size() > 2) {
            countMap.put(fruits[start], countMap.get(fruits[start]) - 1);
            if (countMap.get(fruits[start]) == 0) {
                countMap.remove(fruits[start]);
            }
            start++;
        }
        maxLen = Math.max(maxLen, end - start + 1);
    }
    return maxLen;
}
```

### **Complexity Analysis:**
- **Time Complexity:** **O(n)**  
  - Each element is visited at most twice (once when expanding `end`, once when shrinking `start`).
- **Space Complexity:** **O(1)** or **O(3)** for the map, since only at most 2 fruits are stored.

---

## 3. Most Optimal Solution (Sliding Window with Count Array)

### **Core Idea:**
Since fruits are often represented as integers within a known small range, replace HashMap with an array for counting. Use a sliding window to track at most 2 distinct fruits efficiently.

### **Algorithm:**
1. Use two pointers: `start` and `end`.
2. Maintain a count array for fruit counts.
3. Track the number of distinct fruits in the window.
4. Expand `end`:
   - Increment the count for the current fruit.
   - If the number of distinct fruits exceeds 2:
     - Shrink from `start`, decrementing counts.
     - When a fruit count reaches zero, reduce the count of distinct fruits.
5. Record the maximum window size during iteration.

### **Java Code:**
```java
public int totalFruit(int[] fruits) {
    int[] count = new int[ fruits.length ]; // or fixed size if range is known
    int start = 0, maxLen = 0, distinctFruits = 0;

    for (int end = 0; end < fruits.length; end++) {
        if (count[fruits[end]] == 0) {
            distinctFruits++;
        }
        count[fruits[end]]++;

        while (distinctFruits > 2) {
            count[fruits[start]]--;
            if (count[fruits[start]] == 0) {
                distinctFruits--;
            }
            start++;
        }
        maxLen = Math.max(maxLen, end - start + 1);
    }
    return maxLen;
}
```

### **Complexity Analysis:**
- **Time Complexity:** **O(n)**  
  - Each element is processed once.
- **Space Complexity:** **O(k)**, where k is the range of fruit values, or O(1) if range is small.

---

## **Dry Run Example**

### Input:
`fruits = [1, 2, 1, 2, 3]`

| Step | `end` | Fruit | Count Array | `distinctFruits` | `start` | Window | Valid? | Max Length |
|-------|--------|--------|--------------|------------------|---------|--------|---------|--------------|
| 0     | 0      | 1      | {1:1}        | 1                | 0       | [1]    | Yes     | 1            |
| 1     | 1      | 2      | {1:1, 2:1}   | 2                | 0       | [1,2]  | Yes     | 2            |
| 2     | 2      | 1      | {1:2, 2:1}   | 2                | 0       | [1,2,1]| Yes     | 3            |
| 3     | 3      | 2      | {1:2, 2:2}   | 2                | 0       | [1,2,1,2]| Yes   | 4            |
| 4     | 4      | 3      | {1:2, 2:2, 3:1} | 3          | 0       | [1,2,1,2,3]| No  | (Shrink window) |
|        |        |        | Decrement count at start | 1 | 1 | [2,1,2,3] | 3 > 2 | Shrink | 4 |
|        |        |        | Now window start=1, counts: {1:1, 2:2, 3:1} | | | [2,1,2,3] | 3 > 2 | shrink again | 4 |
|        |        |        | Continue shrinking until only 2 distinct | start=2 | [1,2,3] | 3 > 2 | shrink | 3 |
|        |        |        | After shrinking, window is [2,3] with counts {2:1,3:1} | | | [2,3] | Yes | 2 |

**Maximum length:** **4**

---

## **Summary**

| Approach | Time Complexity | Space Complexity | Key Idea |
|------------|------------------|------------------|------------|
| Brute Force | O(n^3) | O(1) | Check all subarrays for at most 2 distinct fruits |
| Sliding Window with HashMap | O(n) | O(1) or O(3) | Expand/shrink window based on fruit counts in HashMap |
| Sliding Window with Count Array | O(n) | O(k) | Use fixed-size array for fruit counts, track distinct count |

---

## **Final Tips for Interviews:**
- Start with the brute-force to confirm correctness.
- Use sliding window techniques to optimize to linear time.
- When possible, replace HashMap with array for faster access if fruit range is small.
- Always maintain the invariant: at most 2 distinct fruits in the current window.
- Update maximum length during each valid window expansion.

---

**Happy revising!**
