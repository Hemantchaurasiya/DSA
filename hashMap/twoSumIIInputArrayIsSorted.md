# Two Sum II - Input Array Is Sorted

## Problem Statement
Given a **sorted** array of integers `numbers` and a target value `target`, find the **indices** (1-based) of the two numbers that add up to `target`. Assume exactly one solution exists, and do not use the same element twice.

---

## Approach 1: Brute Force (Not recommended, but foundational)

### **Core Idea**
Check all pairs to find two numbers summing to `target`.

### **Algorithm**
1. Use two nested loops:
   - Outer loop from index `i=0` to `n-2`.
   - Inner loop from `j=i+1` to `n-1`.
2. Check if `numbers[i] + numbers[j] == target`.
3. If yes, return `[i+1, j+1]` (1-based indices).
4. If no pair matches, return an empty array or handle accordingly.

### **Java Code**
```java
public int[] twoSum(int[] numbers, int target) {
    int n = numbers.length;
    for (int i = 0; i < n - 1; i++) {
        for (int j = i + 1; j < n; j++) {
            if (numbers[i] + numbers[j] == target) {
                return new int[] {i + 1, j + 1}; // 1-based indices
            }
        }
    }
    return new int[0]; // No solution
}
```

### **Complexity Analysis**
- **Time Complexity:**  
  **O(n^2)**, due to nested loops checking all pairs.
- **Space Complexity:**  
  **O(1)**, no extra space besides variables.

### **Dry Run**
| Input: numbers = [2, 7, 11, 15], target = 9 |  
|-------------------------------------------|  
| i=0, j=1: 2 + 7 = 9 → Return [1, 2] |  

---

## Approach 2: Two-Pointer Technique (Most Optimal)

### **Core Idea**
Use two pointers starting from both ends of the sorted array:
- If the sum is greater than `target`, move the right pointer left.
- If the sum is less than `target`, move the left pointer right.
- Continue until the pair is found.

This approach leverages the sorted property to achieve linear time complexity.

### **Algorithm**
1. Initialize two pointers:
   - `left = 0`
   - `right = n - 1`
2. While `left < right`:
   - Calculate `sum = numbers[left] + numbers[right]`.
   - If `sum == target`, return `[left + 1, right + 1]`.
   - Else if `sum < target`, increment `left`.
   - Else, decrement `right`.
3. If no pair is found, return empty.

### **Java Code**
```java
public int[] twoSum(int[] numbers, int target) {
    int left = 0, right = numbers.length - 1;
    while (left < right) {
        int sum = numbers[left] + numbers[right];
        if (sum == target) {
            return new int[] {left + 1, right + 1}; // 1-based indices
        } else if (sum < target) {
            left++;
        } else {
            right--;
        }
    }
    return new int[0]; // No solution
}
```

### **Complexity Analysis**
- **Time Complexity:**  
  **O(n)**, since each element is visited at most once.
- **Space Complexity:**  
  **O(1)**, only constant extra space.

### **Dry Run**
| Input: numbers = [2, 7, 11, 15], target=9 |  
|-------------------------------------------|  
| left=0 (2), right=3 (15): sum=17 > 9 → move right to 2 |  
| left=0 (2), right=2 (11): sum=13 > 9 → move right to 1 |  
| left=0 (2), right=1 (7): sum=9 → return [1, 2] |  

---

## Approach 3: Binary Search (Less Efficient, but educational)

### **Core Idea**
For each element, perform a binary search for `target - numbers[i]` in the subarray to the right.

### **Algorithm**
1. Loop through each element `numbers[i]`:
   - Calculate `complement = target - numbers[i]`.
   - Perform binary search for `complement` in `numbers` from index `i+1` to `n-1`.
2. If found, return `[i+1, foundIndex+1]`.

### **Java Code**
```java
public int[] twoSum(int[] numbers, int target) {
    for (int i = 0; i < numbers.length - 1; i++) {
        int complement = target - numbers[i];
        int index = binarySearch(numbers, complement, i + 1, numbers.length - 1);
        if (index != -1) {
            return new int[] {i + 1, index + 1};
        }
    }
    return new int[0];
}

private int binarySearch(int[] arr, int target, int left, int right) {
    while (left <= right) {
        int mid = left + (right - left) / 2;
        if (arr[mid] == target) {
            return mid;
        } else if (arr[mid] < target) {
            left = mid + 1;
        } else {
            right = mid - 1;
        }
    }
    return -1;
}
```

### **Complexity Analysis**
- **Time Complexity:**  
  **O(n log n)**, due to binary searches for each element.
- **Space Complexity:**  
  **O(1)**, no extra space aside from variables.

### **Dry Run**
| Input: [2, 7, 11, 15], target=9 |  
|--------------------------------|  
| i=0, value=2, binary search for 7 → found at index 1 → return [1, 2] |  

---

## **Summary Table**

| Approach | Core Idea | Time Complexity | Space Complexity | Use Case |
|------------|--------------|-------------------|------------------|----------|
| Brute Force | Check all pairs | O(n^2) | O(1) | Small arrays or initial understanding |
| Two-Pointer | Exploit sorted array | O(n) | O(1) | Large arrays, optimal for sorted data |
| Binary Search | For each element, binary search complement | O(n log n) | O(1) | When binary search is preferred |

---

## **Final Tips**
- Always leverage the sorted property for optimal solutions.
- The **two-pointer** approach is the most efficient and concise for sorted arrays.
- Remember to return 1-based indices as per problem statement.
- Handle edge cases where no solution exists (though problem guarantees a solution).

---

**Happy Coding!**
