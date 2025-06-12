# Merge Two 2D Arrays by Summing Values

---

## Problem Statement:
Given two 2D arrays `arr1` and `arr2`, each containing subarrays with two elements `[key, value]`, merge them into a single array where:
- If both arrays contain the same key, their values are summed.
- If a key exists in only one array, include it as is.

**Example:**
```plaintext
Input:
arr1 = [[1, 2], [3, 4], [5, 6]]
arr2 = [[1, 3], [2, 5], [3, 7]]

Output:
[[1, 5], [2, 5], [3, 11], [5, 6]]
```

---

## Approach 1: Brute Force (Nested Loop)

### **Core Idea:**
For each element in `arr1`, compare it with every element in `arr2`. If keys match, sum their values; otherwise, include the key-value pair as is.

### **Algorithm:**
1. Initialize an empty result list.
2. For each `[key1, val1]` in `arr1`:
   - Search for `[key2, val2]` in `arr2` with the same key.
   - If found, add `[key1, val1 + val2]` to the result.
   - If not found, add `[key1, val1]`.
3. For every element in `arr2` whose key wasn't matched, add it to the result.

**Note:** To handle keys in `arr2` that were not matched in `arr1`, keep track of matched keys and add remaining unmatched pairs.

### **Java Code:**
```java
import java.util.*;

public int[][] mergeArrays(int[][] arr1, int[][] arr2) {
    List<int[]> result = new ArrayList<>();
    boolean[] matched = new boolean[arr2.length];

    // Merge matching keys from arr1
    for (int[] pair1 : arr1) {
        int key1 = pair1[0], val1 = pair1[1];
        boolean found = false;
        for (int j = 0; j < arr2.length; j++) {
            if (pair1[0] == arr2[j][0]) {
                result.add(new int[]{key1, val1 + arr2[j][1]});
                matched[j] = true;
                found = true;
                break;
            }
        }
        if (!found) {
            result.add(new int[]{key1, val1});
        }
    }

    // Add remaining pairs from arr2
    for (int i = 0; i < arr2.length; i++) {
        if (!matched[i]) {
            result.add(arr2[i]);
        }
    }

    return result.toArray(new int[0][]);
}
```

### **Complexity Analysis:**
- **Time Complexity:** O(n * m), where `n` and `m` are the lengths of `arr1` and `arr2`, due to nested loops.
- **Space Complexity:** O(n + m) for the result list.

### **Dry Run:**
- Input:
```plaintext
arr1 = [[1, 2], [3, 4], [5, 6]]
arr2 = [[1, 3], [2, 5], [3, 7]]
```
- Step-by-step:
  - For `[1, 2]` in `arr1`, find `[1, 3]` in `arr2`, sum → `[1, 5]`.
  - For `[3, 4]`, find `[3, 7]`, sum → `[3, 11]`.
  - For `[5, 6]`, no match, add as is.
  - Remaining in `arr2` is `[2, 5]`, add it.
- Final result:
```plaintext
[[1, 5], [3, 11], [5, 6], [2, 5]]
```

---

## Approach 2: Using HashMap for Efficient Merging (Optimal Solution)

### **Core Idea:**
Use a `HashMap` to store keys and their aggregated values for efficient lookups and merging.

### **Algorithm:**
1. Initialize a `HashMap<Integer, Integer>`.
2. Iterate through `arr1`:
   - Insert or update the key with its value.
3. Iterate through `arr2`:
   - If key exists, sum the values.
   - If not, insert the key-value pair.
4. Convert the `HashMap` entries into a 2D array.

### **Java Code:**
```java
import java.util.*;

public int[][] mergeArrays(int[][] arr1, int[][] arr2) {
    Map<Integer, Integer> map = new HashMap<>();
    // Add arr1 data
    for (int[] pair : arr1) {
        map.put(pair[0], pair[1]);
    }
    // Merge arr2 data
    for (int[] pair : arr2) {
        map.put(pair[0], map.getOrDefault(pair[0], 0) + pair[1]);
    }
    // Convert map to array
    int[][] result = new int[map.size()][2];
    int index = 0;
    for (Map.Entry<Integer, Integer> entry : map.entrySet()) {
        result[index][0] = entry.getKey();
        result[index][1] = entry.getValue();
        index++;
    }
    return result;
}
```

### **Complexity Analysis:**
- **Time Complexity:** O(n + m), where `n` and `m` are the lengths of the input arrays, due to single pass insertions and lookups.
- **Space Complexity:** O(n + m) for the HashMap and result array.

### **Dry Run:**
- Input:
```plaintext
arr1 = [[1, 2], [3, 4], [5, 6]]
arr2 = [[1, 3], [2, 5], [3, 7]]
```
- Process:
  - Insert from `arr1`: {1=2, 3=4, 5=6}
  - Merge `arr2`:
    - key=1: existing 2 → 2+3=5
    - key=2: new → 5
    - key=3: existing 4 → 4+7=11
- Final map: {1=5, 2=5, 3=11, 5=6}
- Convert to array:
```plaintext
[[1, 5], [2, 5], [3, 11], [5, 6]]
```

---

## Summary & Best Practices:
- Using a `HashMap` is the most efficient way when merging based on keys.
- Always handle key insertion and updating carefully.
- Converting the map back to a 2D array is straightforward.
- This approach handles large inputs efficiently.

---

## Final Tips:
- When merging datasets based on keys, prefer hash-based data structures for optimal performance.
- Be mindful of key existence checks using `getOrDefault` or `containsKey`.
- Practice similar problems involving merging or aggregating datasets for better problem-solving skills.

Happy coding!
