# Single Element in a Sorted Array

This problem involves finding the element that appears only once in a sorted array where every other element appears twice. The array is sorted, which provides opportunities for optimized solutions.

---

## 1. Brute Force Approach

### **Core Idea**

Check each element and count its occurrences; the one with a count of 1 is the answer.

### **Algorithm**

1. Iterate through the array.
2. For each element, count how many times it appears.
3. If an element appears only once, return it.

### **Java Code**

```java
public int singleNonDuplicate(int[] nums) {
    for (int i = 0; i < nums.length; i++) {
        int count = 0;
        for (int j = 0; j < nums.length; j++) {
            if (nums[i] == nums[j]) {
                count++;
            }
        }
        if (count == 1) {
            return nums[i];
        }
    }
    return -1; // Should not reach here for valid input
}

```

### **Complexity Analysis**

- **Time Complexity:** **O(n^2)** — For each element, we scan the entire array to count occurrences.
- **Space Complexity:** **O(1)** — No extra space used.

### **Dry Run**

- Input: `[1,1,2,3,3]`
- Iteration:
    - i=0, element=1, count=2 → skip
    - i=1, element=1, count=2 → skip
    - i=2, element=2, count=1 → **return 2**

---

## 2. Better Solution: Using XOR

### **Core Idea**

XOR of two identical numbers is 0, and XOR of 0 with a number is the number itself. Since all elements except one appear twice, XORing all elements will cancel out duplicates, leaving the single element.

### **Algorithm**

1. Initialize a variable `result` to 0.
2. Traverse the array, XOR each element with `result`.
3. After the loop, `result` contains the unique element.

### **Java Code**

```java
public int singleNonDuplicate(int[] nums) {
    int result = 0;
    for (int num : nums) {
        result ^= num;
    }
    return result;
}

```

### **Complexity Analysis**

- **Time Complexity:** **O(n)** — Single pass through the array.
- **Space Complexity:** **O(1)** — Only one extra variable.

### **Dry Run**

- Input: `[1,1,2,3,3]`
- Process:
    - result=0
    - result ^= 1 → 1
    - result ^= 1 → 0
    - result ^= 2 → 2
    - result ^= 3 → 1
    - result ^= 3 → 0
- Final result: `2`

---

## 3. Most Optimal Solution: Binary Search

### **Core Idea**

Leverage the sorted nature of the array:

- Elements appear in pairs, with the first occurrence at even indices and second at odd indices.
- When the unique element is present, this pattern breaks.
- Use binary search to find the break point where this pattern is disrupted.

### **Algorithm**

1. Initialize `low=0`, `high=length-1`.
2. While `low < high`:
    - Find `mid = (low + high) / 2`.
    - Adjust `mid` to be even if it's odd (to compare pairs).
    - If `nums[mid] == nums[mid+1]`, the unique element is in the right half (`mid+2` to `high`).
    - Else, it is in the left half (`low` to `mid`).
3. When `low == high`, return `nums[low]`.

### **Java Code**

```java
public int singleNonDuplicate(int[] nums) {
    int low = 0, high = nums.length - 1;
    while (low < high) {
        int mid = low + (high - low) / 2;
        // Make mid even for comparison
        if (mid % 2 != 0) mid--;
        if (nums[mid] == nums[mid + 1]) {
            low = mid + 2;
        } else {
            high = mid;
        }
    }
    return nums[low];
}

```

### **Complexity Analysis**

- **Time Complexity:** **O(log n)** — Binary search reduces the search space by half each iteration.
- **Space Complexity:** **O(1)** — No extra space apart from variables.

### **Dry Run**

- Input: `[1,1,2,3,3]`
- Initial: low=0, high=4
- Iteration 1:
    - mid=2, even, compare nums[2]=2 and nums[3]=3
    - nums[2]!=nums[3], so unique is in left part → high=2
- Iteration 2:
    - low=0, high=2
    - mid=1, odd, make even → mid=0
    - compare nums[0]=1 and nums[1]=1 → equal, move right: low=2
- Now low=2, high=2 → loop ends
- Return nums[2]=2

---

# Summary Table

| Approach | Idea | Time Complexity | Space Complexity | Suitable for |
| --- | --- | --- | --- | --- |
| Brute Force | Count occurrences for each element | O(n²) | O(1) | Small arrays, initial understanding |
| XOR Method | XOR all elements to cancel duplicates | O(n) | O(1) | Larger inputs, simple implementation |
| Binary Search | Use array's sorted property to locate break point | O(log n) | O(1) | Large inputs, optimal performance |

---

## Final Tips:

- Use **binary search** for the most efficient solution in sorted arrays.
- Always analyze whether the problem's constraints allow for binary search.
- Practice dry runs to understand how the array's pattern breaks when the unique element is present.

---

This comprehensive guide should help you efficiently solve and revise the **Single Element in a Sorted Array** problem, especially for interviews.
