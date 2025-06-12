# Two Sum

## Problem Statement
Given an array of integers `nums` and an integer `target`, return the indices of **two numbers** such that they add up to `target`.

---

## Approach 1: Brute Force

### **Core Idea**
Check all possible pairs to see if their sum equals `target`. This guarantees finding the solution but is inefficient for large arrays.

### **Algorithm**
1. Traverse the array with two nested loops:
   - Outer loop from index 0 to n-2.
   - Inner loop from index i+1 to n-1.
2. For each pair `(nums[i], nums[j])`, check if `nums[i] + nums[j] == target`.
3. If yes, return `[i, j]`.
4. If no such pair exists, return an empty array or handle accordingly.

### **Java Code**
```java
public int[] twoSum(int[] nums, int target) {
    int n = nums.length;
    for (int i = 0; i < n - 1; i++) {
        for (int j = i + 1; j < n; j++) {
            if (nums[i] + nums[j] == target) {
                return new int[] {i, j};
            }
        }
    }
    return new int[0]; // No solution found
}
```

### **Complexity Analysis**
- **Time Complexity:**  
  **O(n^2)**, since all pairs are checked.
- **Space Complexity:**  
  **O(1)**, no extra space apart from variables.

### **Dry Run**
| Input: nums = [2, 7, 11, 15], target = 9 |  
|------------------------------------------|  
| i=0, j=1: 2 + 7 = 9 → return [0, 1] |  

---

## Approach 2: Using a Hash Map (Most Optimal)

### **Core Idea**
Use a hash map to store the difference (`target - current element`) and its index as we traverse the array:
- For each element, check if its complement (target - element) exists in the hash map.
- If it exists, we've found our pair.
- Else, store the current element and its index in the hash map.

### **Algorithm**
1. Initialize an empty hash map: `Map<Integer, Integer>`.
2. Loop through each element `nums[i]`:
   - Compute `complement = target - nums[i]`.
   - Check if `complement` exists in the map:
     - If yes, return `[map.get(complement), i]`.
   - Else, add `nums[i]` and its index to the map.
3. If no pair is found, return an empty array.

### **Java Code**
```java
import java.util.*;

public int[] twoSum(int[] nums, int target) {
    Map<Integer, Integer> map = new HashMap<>();
    for (int i = 0; i < nums.length; i++) {
        int complement = target - nums[i];
        if (map.containsKey(complement)) {
            return new int[] {map.get(complement), i};
        }
        map.put(nums[i], i);
    }
    return new int[0]; // No solution
}
```

### **Complexity Analysis**
- **Time Complexity:**  
  **O(n)**, as each element is processed once.
- **Space Complexity:**  
  **O(n)**, for the hash map storing up to `n` elements.

### **Dry Run**
| Input: nums = [3, 2, 4], target = 6 |  
|-------------------------------------|  
| i=0: num=3, complement=3, map empty → store {3:0} |  
| i=1: num=2, complement=4, map={3:0} → not found |  
| i=2: num=4, complement=2, map={3:0, 2:1} → found 2 at index 1 |  
| Return [1, 2] |  

---

## Approach 3: Sorting (Less Efficient, Not Recommended)

### **Core Idea**
Sort the array while keeping track of original indices. Use two pointers to find two numbers that sum to the target, then map back to original indices.

### **Algorithm**
1. Create an array of pairs `(num, index)` to preserve original indices.
2. Sort this array based on the number.
3. Initialize two pointers: `left = 0`, `right = n-1`.
4. Loop:
   - Calculate `sum = nums[left] + nums[right]`.
   - If `sum == target`, return the original indices.
   - If `sum < target`, move `left` forward.
   - Else, move `right` backward.
5. If no pair found, return empty.

### **Java Code**
```java
import java.util.*;

public int[] twoSum(int[] nums, int target) {
    int n = nums.length;
    Pair[] arr = new Pair[n];
    for (int i = 0; i < n; i++) {
        arr[i] = new Pair(nums[i], i);
    }
    Arrays.sort(arr, Comparator.comparingInt(p -> p.num));
    int left = 0, right = n - 1;
    while (left < right) {
        int sum = arr[left].num + arr[right].num;
        if (sum == target) {
            return new int[] {arr[left].index, arr[right].index};
        } else if (sum < target) {
            left++;
        } else {
            right--;
        }
    }
    return new int[0];
}

class Pair {
    int num;
    int index;
    Pair(int num, int index) {
        this.num = num;
        this.index = index;
    }
}
```

### **Complexity Analysis**
- **Time Complexity:**  
  **O(n log n)**, due to sorting.
- **Space Complexity:**  
  **O(n)**, for the auxiliary array of pairs.

### **Dry Run**
| Input: nums=[3,2,4], target=6 |  
|----------------------------|  
| Sorted pairs: [(2,1), (3,0), (4,2)] |  
| left=0 (2), right=2 (4): sum=6 → match |  
| Return [1, 2] |  

---

## **Summary Table**

| Approach | Core Idea | Time Complexity | Space Complexity | Best Use Case |
|------------|--------------|-------------------|------------------|--------------|
| Brute Force | Check all pairs | O(n^2) | O(1) | Small input sizes or quick implementation |
| Hash Map | Use complement lookup | O(n) | O(n) | Large input sizes, optimal time |
| Sorting + Two pointers | Sort and use two pointers | O(n log n) | O(n) | When sorting is acceptable |

---

## **Final Tips**
- Use the **Hash Map** approach for the best average-case performance.
- For very large datasets, avoid the brute-force method.
- Remember the importance of handling edge cases such as no solution.

---

**Happy Coding!**
