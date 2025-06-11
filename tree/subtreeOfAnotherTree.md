# Subtree of Another Tree

---

## Problem Overview
Given two binary trees `s` (the larger tree) and `t` (the potential subtree), determine if `t` is a subtree of `s`.  
**Definition:** `t` is a subtree of `s` if there exists a node in `s` such that the subtree rooted at that node is identical to `t`.

---

## Approach 1: Brute Force

### **Core Idea**
Traverse every node of `s`, and for each node, check if the subtree rooted at that node matches `t`. If a match is found, return `true`; otherwise, continue traversal.

### **Algorithm**
1. Traverse each node in `s` using DFS.
2. For each node, invoke a subtree comparison function:
   - If both nodes are `null`, they match.
   - If either is `null` or their values differ, they don't match.
   - Recursively compare left and right subtrees.
3. If any comparison returns `true`, `t` is a subtree.

### **Java Code**
```java
public class Solution {
    public boolean isSubtree(TreeNode s, TreeNode t) {
        if (s == null) return false;
        if (areIdentical(s, t)) return true;
        return isSubtree(s.left, t) || isSubtree(s.right, t);
    }

    private boolean areIdentical(TreeNode s, TreeNode t) {
        if (s == null && t == null) return true;
        if (s == null || t == null) return false;
        if (s.val != t.val) return false;
        return areIdentical(s.left, t.left) && areIdentical(s.right, t.right);
    }
}
```

### **Complexity Analysis**
- **Time Complexity:**  
  **O(N * M)**, where `N` is the number of nodes in `s`, and `M` is the number of nodes in `t`.  
  For each node in `s`, we perform a subtree comparison which can take up to `M` time.

- **Space Complexity:**  
  **O(H_s + H_t)**, where `H_s` and `H_t` are the heights of the trees `s` and `t`, respectively, due to recursion stack.

---

## Approach 2: Optimized Using Tree Serialization and String Matching

### **Core Idea**
Serialize both trees into strings using preorder traversal with markers for null nodes. Then, check if the serialized string of `t` is a substring of the serialized string of `s`. This reduces the problem to a string matching problem.

### **Algorithm**
1. Serialize both `s` and `t` using preorder traversal, including null markers.
2. Use substring search (e.g., KMP algorithm) to check if the serialized `t` is a substring of serialized `s`.
3. If yes, `t` is a subtree; else, not.

### **Java Code**
```java
public class Solution {
    public boolean isSubtree(TreeNode s, TreeNode t) {
        String sStr = serialize(s);
        String tStr = serialize(t);
        return sStr.contains(tStr);
    }

    private String serialize(TreeNode root) {
        StringBuilder sb = new StringBuilder();
        serializeHelper(root, sb);
        return sb.toString();
    }

    private void serializeHelper(TreeNode root, StringBuilder sb) {
        if (root == null) {
            sb.append("null#");
            return;
        }
        sb.append(root.val).append("#");
        serializeHelper(root.left, sb);
        serializeHelper(root.right, sb);
    }
}
```

### **Complexity Analysis**
- **Time Complexity:**  
  **O(N + M)** for serialization plus substring search. The substring search (like KMP) runs in linear time relative to the length of strings, so overall **O(N + M)**.

- **Space Complexity:**  
  **O(N + M)** for storing serialized strings.

---

## Approach 3: Tree Hashing (More Advanced, Optional)

### **Core Idea**
Compute a hash for each subtree in `s` and `t`. If any hash matches the hash of `t`, verify if the subtrees are identical (to avoid false positives due to hash collisions). This can be optimized further with rolling hash techniques.

### **Algorithm**
1. Compute a hash for the subtree `t`.
2. Traverse `s`, computing hashes for subtrees.
3. If any hash matches `t`'s hash, verify subtree equality.
4. Return true if a match is found; false otherwise.

*(Note: Implementation complexity and potential for hash collisions make this approach more suitable for advanced use cases.)*

---

## **Summary Table**

| Approach | Core Idea | Time Complexity | Space Complexity | Notes |
|------------|--------------|---------------------|-----------------------|--------|
| Brute Force | Check all subtrees for match | O(N * M) | O(H_s + H_t) | Simple, straightforward |
| Serialization + String Matching | Serialize trees, use substring search | O(N + M) | O(N + M) | More efficient for large trees |
| Tree Hashing | Hash subtrees for quick comparison | O(N + M) | O(H_s + H_t) | Advanced, risk of hash collisions |

---

## **Dry Run Example**

### Input:
```plaintext
s:       3
        /   \
       4     5
      / \
     1   2

t:     4
      / \
     1   2
```

---

### **Approach 1: Brute Force (Step-by-step)**

1. Traverse node `3`:
   - Check if subtree rooted at `3` matches `t`:
     - `3` vs `4` → no match.
2. Traverse node `4`:
   - Check if subtree rooted at `4` matches `t`:
     - Compare `4` with `4` → match.
     - Check left child: `1` vs `1` → match.
     - Check right child: `2` vs `2` → match.
     - Entire subtree matches → return `true`.
   
**Result:** `t` is a subtree of `s`.

---

### **Approach 2: Serialization**

- Serialize `s`:
  - `"3#4#1#null#null#2#null#null#5#null#null#"`
- Serialize `t`:
  - `"4#1#null#null#2#null#null#"`

- Check if serialized `t` is a substring of serialized `s`:
  - Yes, it is.
- Result: `true`.

---

## **Final Notes**
- For small trees, brute force is simple and sufficient.
- For larger trees, serialization + string matching offers better efficiency.
- Tree hashing is an advanced technique suitable for very large datasets or performance-critical applications.

---

**Good luck with your interview prep!**
