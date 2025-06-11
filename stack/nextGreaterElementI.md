# Next Greater Element I

---

## Problem Overview
Given two arrays `nums1` and `nums2`, where `nums1` is a subset of `nums2`, find the **Next Greater Element** for each element in `nums1` within `nums2`.  
The **Next Greater Element** for an element `x` in `nums2` is the first element to the right of `x` in `nums2` that is greater than `x`. If no such element exists, return `-1`.

**Example:**
- Input: `nums1 = [4,1,2]`, `nums2 = [1,3,4,2]`
- Output: `[-1, 3, -1]`
- Explanation:
  - For `4`: no greater element to the right in `nums2` → `-1`.
  - For `1`: next greater element is `3`.
  - For `2`: no greater element to the right → `-1`.

---

## Approach 1: Brute Force

### **Core Idea**
For each element in `nums1`, scan through `nums2` to find the position of that element, then look to the right to find the next greater element.

### **Algorithm**
1. For each element `x` in `nums1`:
   - Find the index of `x` in `nums2`.
   - Starting from that index, scan forward in `nums2`:
     - If a greater element is found, record it.
     - If not, record `-1`.

### **Java Code**
```java
public int[] nextGreaterElement(int[] nums1, int[] nums2) {
    int[] result = new int[nums1.length];
    for (int i = 0; i < nums1.length; i++) {
        int index = -1;
        // Find index of nums1[i] in nums2
        for (int j = 0; j < nums2.length; j++) {
            if (nums2[j] == nums1[i]) {
                index = j;
                break;
            }
        }
        // Search for next greater element
        int nextGreater = -1;
        for (int j = index + 1; j < nums2.length; j++) {
            if (nums2[j] > nums1[i]) {
                nextGreater = nums2[j];
                break;
            }
        }
        result[i] = nextGreater;
    }
    return result;
}
```

### **Complexity Analysis**
- **Time Complexity:** *O(n * m)*, where `n` is the length of `nums1` and `m` is the length of `nums2`, due to nested loops.
- **Space Complexity:** *O(n)* for the output array.

### **Dry Run**
Input: `nums1 = [4,1,2]`, `nums2 = [1,3,4,2]`

| Step | For `4`            | Find index in `nums2`: 2 | Search to right: no greater element | Result: `-1` |
|-------|---------------------|----------------------------|--------------------------------------|--------------|
| For `1` | Find index: 0       | Search right: 3,4,2 | Next greater: 3 at index 1 | Result: `3` |
| For `2` | Find index: 3       | Search right: none | No greater element → `-1` | Result: `-1` |

---

## Approach 2: Using Monotonic Stack (Optimal)

### **Core Idea**
Use a **monotonically decreasing stack** to process `nums2` once:
- Traverse `nums2` from right to left.
- For each element:
  - Pop elements from the stack until the top is greater than the current element or the stack is empty.
  - The top of the stack (if any) becomes the **Next Greater Element**.
  - Push the current element onto the stack.
- Store the results in a map for quick lookup for elements in `nums1`.

### **Algorithm**
1. Initialize an empty stack.
2. Create a HashMap to map each number in `nums2` to its Next Greater Element.
3. Traverse `nums2` from right to left:
   - While the stack is not empty and the top is less than or equal to the current element, pop from the stack.
   - The top of the stack (if exists) is the Next Greater Element; otherwise, `-1`.
   - Map `nums2[i]` to its Next Greater Element.
   - Push `nums2[i]` onto the stack.
4. For each element in `nums1`, retrieve its Next Greater Element from the map.

### **Java Code**
```java
import java.util.*;

public int[] nextGreaterElement(int[] nums1, int[] nums2) {
    Map<Integer, Integer> nextGreaterMap = new HashMap<>();
    Deque<Integer> stack = new ArrayDeque<>();
    
    // Process nums2 from right to left
    for (int i = nums2.length - 1; i >= 0; i--) {
        while (!stack.isEmpty() && stack.peek() <= nums2[i]) {
            stack.pop();
        }
        nextGreaterMap.put(nums2[i], stack.isEmpty() ? -1 : stack.peek());
        stack.push(nums2[i]);
    }
    
    // Prepare result for nums1 using the map
    int[] result = new int[nums1.length];
    for (int i = 0; i < nums1.length; i++) {
        result[i] = nextGreaterMap.getOrDefault(nums1[i], -1);
    }
    return result;
}
```

### **Complexity Analysis**
- **Time Complexity:** *O(n + m)*, where `n` is length of `nums1` and `m` is length of `nums2`, as each element pushes and pops from the stack at most once.
- **Space Complexity:** *O(m)* for the HashMap and stack.

### **Dry Run**
Input: `nums1 = [4,1,2]`, `nums2 = [1,3,4,2]`

- Process `nums2` from right to left:
  - i=3 (`2`): stack empty → map `2`→`-1`, push `2`.
  - i=2 (`4`): pop `2` (since 2 ≤ 4), stack empty → map `4`→`-1`, push `4`.
  - i=1 (`3`): top of stack is `4` (> 3), map `3`→`4`, push `3`.
  - i=0 (`1`): top of stack is `3` (> 1), map `1`→`3`, push `1`.

Resulting map:
```plaintext
1 -> 3
3 -> 4
4 -> -1
2 -> -1
```

For `nums1`:
- `4` → `-1`
- `1` → `3`
- `2` → `-1`

---

## **Summary Table**

| Approach                     | Time Complexity               | Space Complexity | Comments                                              |
|------------------------------|------------------------------|------------------|--------------------------------------------------------|
| Brute Force                  | O(n * m)                     | O(n)             | Inefficient for large inputs                          |
| Monotonic Stack (Optimal)    | O(n + m)                     | O(m)             | Single pass, efficient, widely used                   |

---

## **Final Tips for Interviews**
- Use the stack-based approach for optimal efficiency.
- Precompute Next Greater Elements for `nums2` and then directly answer queries for `nums1`.
- Pay attention to edge cases like empty arrays or arrays with duplicate values.
- Practice similar problems like "Next Greater Element II" and variations with circular arrays.

---

**Happy coding!**
