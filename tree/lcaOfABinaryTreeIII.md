# LCA of a Binary Tree III

This guide covers various approaches to solve the problem of **finding the Lowest Common Ancestor (LCA)** of two nodes in a **Binary Tree with parent pointers** (often called *LCA of a Binary Tree III*). The problem involves identifying the **closest shared ancestor** of two given nodes where each node has a pointer to its parent.

---

## Problem Definition
Given two nodes `p` and `q` in a binary tree where each node has a parent pointer, find their **Lowest Common Ancestor**.

---

## Approach 1: Brute Force (Using Ancestor Set)

### **Core Idea**
Trace the ancestors of one node and store them in a set, then traverse the ancestors of the other node until a common ancestor is found.

### **Algorithm**
1. Initialize an empty set to store ancestors of `p`.
2. Traverse **upwards** from node `p` to the root, adding each ancestor to the set.
3. Traverse **upwards** from node `q`. For each ancestor:
   - Check if it exists in the set of `p`'s ancestors.
   - The first common ancestor encountered is the **LCA**.
4. Return that node.

### **Java Code**
```java
public class Node {
    public int val;
    public Node parent;
    // constructor
}

public Node lowestCommonAncestor(Node p, Node q) {
    Set<Node> ancestors = new HashSet<>();
    while (p != null) {
        ancestors.add(p);
        p = p.parent;
    }

    while (q != null) {
        if (ancestors.contains(q))
            return q;
        q = q.parent;
    }
    return null; // in case no common ancestor is found
}
```

### **Complexity Analysis**
- **Time Complexity:**  
  O(h) where h is the height of the tree (worst case, traversing to the root for both nodes).
- **Space Complexity:**  
  O(h) for storing ancestors of `p`.

---

### **Dry Run Example**
Suppose the tree is:
```
        3
       / \
      5   1
     / \ / \
    6  2 0  8
      / \
     7   4
```

Find LCA of nodes `7` and `4`.

- Ancestors of `7`: 7 -> 2 -> 5 -> 3
- Ancestors of `4`: 4 -> 2 -> 5 -> 3

Step-by-step:
- Store `7`, `2`, `5`, `3` in set.
- Traverse ancestors of `4`: `4` (not in set), `2` (in set) → LCA is node `2`.

---

## Approach 2: Two Pointers Method (Optimized, No Extra Space)

### **Core Idea**
Use two pointers starting at `p` and `q`. Move each pointer to its parent at each step. When a pointer reaches the root (null), redirect it to the other node. This way, both pointers traverse equal lengths, ensuring they meet at the LCA.

### **Algorithm**
1. Initialize two pointers `a` and `b` at `p` and `q`.
2. While `a != b`:
   - If `a` is null, set `a` to `q`.
   - Else, move `a` to `a.parent`.
   - If `b` is null, set `b` to `p`.
   - Else, move `b` to `b.parent`.
3. When `a == b`, that's the LCA.

### **Java Code**
```java
public Node lowestCommonAncestor(Node p, Node q) {
    Node a = p, b = q;
    while (a != b) {
        a = (a == null) ? q : a.parent;
        b = (b == null) ? p : b.parent;
    }
    return a;
}
```

### **Complexity Analysis**
- **Time Complexity:**  
  O(h), where h is the height of the tree. Each pointer traverses at most twice the height.
- **Space Complexity:**  
  O(1), no extra space used.

---

### **Dry Run Example**
Using the same tree as above, find LCA of nodes `7` and `4`.

- Initialize `a = 7`, `b = 4`.
- Iteration 1:
  - `a != b`: move `a` to parent → `a = 2`.
  - `b != a`: move `b` to parent → `b = 2`.
- Now `a = b = 2`, so the LCA is node `2`.

---

## Approach 3: Path Comparison (Optional, if paths are known)

### **Core Idea**
Build paths from each node up to the root, then compare the paths from the end to find the first divergence point.

### **Algorithm**
1. Build a list of ancestors for `p` (including `p` itself).
2. Build a list of ancestors for `q`.
3. Reverse both lists.
4. Iterate simultaneously to find the last common node before divergence.

### **Note:** Less efficient if storing entire paths, but straightforward.

---

## Summary Table

| Approach | Core Idea | Time Complexity | Space Complexity | Use Cases |
|------------|--------------|-------------------|-------------------|------------|
| Brute Force (Set) | Store ancestors of `p` in a set, then traverse `q`'s ancestors | O(h) | O(h) | Small space acceptable, simplicity |
| Two Pointers | Equalize traversal lengths by switching pointers | O(h) | O(1) | Optimal, no extra space |
| Path Comparison | Build full ancestor paths and compare | O(h) | O(h) | When paths are pre-stored or easy to retrieve |

---

## Final Notes
- The **two pointers method** is generally preferred due to its **O(1) space** and **simplicity**.
- The **set-based approach** is intuitive and suitable for understanding basic ancestor-tracing logic.
- Always consider constraints like tree height and memory when choosing the approach.

---

*Happy Revising!*
