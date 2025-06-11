# Convert Sorted Array to Binary Search Tree (BST)

This problem involves converting a sorted array into a height-balanced Binary Search Tree (BST). The key challenge is to ensure the BST is height-balanced, which generally involves choosing the middle element as the root at each step.

---

## 1. Brute Force Approach

### **Core Idea:**
- Generate all possible BSTs from the sorted array (inefficient for this problem).
- Alternatively, **incorrect** naive approach: repeatedly pick the middle element as root without ensuring balance, leading to skewed trees.

*Note:* The brute-force approach isn't practical for this problem; instead, the initial naive method is to pick the middle element as root and recursively build subtrees.

---

## 2. Recursive Approach (Most Common & Intuitive)

### **Core Idea:**
- The array is sorted, so the middle element should be the root for height balance.
- Recursively construct left and right subtrees from subarrays.

### **Algorithm:**
1. If the current subarray is empty, return `null`.
2. Find the middle index: `mid = (start + end) / 2`.
3. Create a `TreeNode` with value `nums[mid]`.
4. Recursively build the left subtree from `start` to `mid - 1`.
5. Recursively build the right subtree from `mid + 1` to `end`.
6. Return the constructed node.

### **Java Code:**
```java
public class Solution {
    public TreeNode sortedArrayToBST(int[] nums) {
        return buildBST(nums, 0, nums.length - 1);
    }

    private TreeNode buildBST(int[] nums, int start, int end) {
        if (start > end) {
            return null; // Base case: empty subarray
        }
        int mid = start + (end - start) / 2; // To prevent overflow
        TreeNode root = new TreeNode(nums[mid]);
        root.left = buildBST(nums, start, mid - 1);
        root.right = buildBST(nums, mid + 1, end);
        return root;
    }
}
```

### **Complexity Analysis:**
- **Time Complexity:**  
  **O(n)** — Each element is visited once during recursion to create nodes.
  
- **Space Complexity:**  
  **O(log n)** — Recursion stack depth in balanced tree is logarithmic.

---

## 3. Dry Run & Step-by-Step Example

### **Input:**
```plaintext
nums = [-10, -3, 0, 5, 9]
```

### **Step-by-step:**

| Step | Subarray Index Range | Middle Element | TreeNode Created | Left Subarray | Right Subarray |
|-------|------------------------|------------------|------------------|--------------|----------------|
| 1     | 0 to 4                 | 2 (index 2)      | 0                | -10, -3     | 5, 9          |
| 2     | 0 to 1                 | 0 (-10, -3)     | -3               | -10        | (null)        |
| 3     | 0 to 0                 | -10             | -10              | (null)     | (null)        |
| 4     | 2 to 1 (null)          | N/A              | null             | -10        | -3            |
| 2 (continued) | 3 to 4          | 7 (index 4)      | 9                | 5          | (null)        |
| 3 (continued) | 3 to 3          | 5               | 5                | (null)     | (null)        |

### **Resulting Tree:**
```
        0
       / \
    -3   9
    /     /
 -10     5
```

---

## Summary:
- The recursive approach is efficient and straightforward.
- Ensures height balance by choosing the middle as root.
- Suitable for interview and real-world usage.

---

## **Key Takeaways:**
- Always pick the middle element in sorted array for balanced BST.
- Recursion naturally helps in building subtrees.
- Careful with indices to avoid out-of-bound errors.
- The recursive depth is logarithmic in a balanced tree.

---

Feel free to ask for further clarifications or additional approaches!
