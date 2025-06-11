# Serialize and Deserialize Binary Tree

This problem involves converting a binary tree into a string (serialization) and then reconstructing the tree from the string (deserialization). It’s a common problem to test understanding of tree traversal, recursion, and data encoding/decoding.

---

## 1. Brute Force Approach

### **Core Idea:**
Use a straightforward traversal to serialize the tree into a string, and then parse this string to reconstruct the tree.

### **Algorithm:**
- **Serialization:**
  - Perform a **pre-order traversal** (node-left-right).
  - For each node:
    - Append its value to a string.
    - If a node is `null`, append a sentinel value (e.g., `"null"`).
  - Separate values with a delimiter (e.g., comma).
- **Deserialization:**
  - Split the serialized string into tokens.
  - Use a recursive function:
    - Read the next token.
    - If it is `"null"`, return `null`.
    - Otherwise, create a node with the value.
    - Recursively build its left and right subtrees.

### **Java Code:**

```java
public class Codec {
    private static final String SEP = ",";
    private static final String NULL = "null";

    // Serialize
    public String serialize(TreeNode root) {
        StringBuilder sb = new StringBuilder();
        serializeHelper(root, sb);
        return sb.toString();
    }

    private void serializeHelper(TreeNode root, StringBuilder sb) {
        if (root == null) {
            sb.append(NULL).append(SEP);
            return;
        }
        sb.append(root.val).append(SEP);
        serializeHelper(root.left, sb);
        serializeHelper(root.right, sb);
    }

    // Deserialize
    public TreeNode deserialize(String data) {
        String[] tokens = data.split(SEP);
        Queue<String> queue = new LinkedList<>(Arrays.asList(tokens));
        return deserializeHelper(queue);
    }

    private TreeNode deserializeHelper(Queue<String> queue) {
        String val = queue.poll();
        if (val.equals(NULL))
            return null;
        TreeNode node = new TreeNode(Integer.parseInt(val));
        node.left = deserializeHelper(queue);
        node.right = deserializeHelper(queue);
        return node;
    }
}
```

### **Complexity Analysis:**
- **Time Complexity:**  
  - **Serialization:** O(N), where N is number of nodes, since each node is visited once.  
  - **Deserialization:** O(N), as each token is processed once.
- **Space Complexity:**  
  - **Serialization:** O(N) for the string builder.  
  - **Deserialization:** O(N) for the recursion stack and token storage.

### **Dry Run with Example:**

Suppose the tree:

```
    1
   / \
  2   3
     / \
    4   5
```

- **Serialization:**

| Step | Node/Token | String Builder | Output String                         |
|---------|--------------|------------------|-------------------------------------|
| Start   | 1            | ""               | "1,"                                |
| Left of 1 | 2            | "1,2,"           | "1,2,"                              |
| Left of 2 | null         | "1,2,null,"     | "1,2,null,"                        |
| Right of 2 | null         | "1,2,null,null,"| "1,2,null,null,"                   |
| Right of 1 | 3            | "1,2,null,null,3,"| "1,2,null,null,3,"                |
| Left of 3 | 4            | "1,2,null,null,3,4,"| "1,2,null,null,3,4,"           |
| Left of 4 | null         | ...              | ...                                 |
| Right of 4 | null         | ...              | ...                                 |
| Right of 3 | 5            | "...5,"          | "...5,"                            |
| Left of 5 | null         | ...              | ...                                 |
| Right of 5 | null         | ...              | ...                                 |

Serialized string:  
`"1,2,null,null,3,4,null,null,5,null,null,"`

- **Deserialization:**
  - Parse tokens: `["1","2","null","null","3","4","null","null","5","null","null"]`.
  - Recurse to rebuild the tree following pre-order.

---

## 2. Improved (Cleaner) Approach

This is essentially the same as the brute-force but emphasizes code clarity and standardization.

### **Core Idea:**
Use recursive pre-order traversal with a queue or list to process tokens during deserialization.

### **Algorithm:**
Same as above, but often with cleaner separation of functions.

### **Java Code:**

*(Same as previous, emphasizing code clarity)*

---

## 3. Most Optimal Solution: **Iterative Approach with BFS (Level-order Traversal)**

### **Core Idea:**
Use a **level-order traversal** (BFS) for serialization, and reconstruct the tree iteratively, which can sometimes be more intuitive and efficient in certain scenarios.

---

### **Algorithm:**

- **Serialization:**
  - Use a queue to perform level-order traversal.
  - For each node:
    - Add its value to the output (or `"null"` if `null`).
    - Enqueue its children.
- **Deserialization:**
  - Split the string into tokens.
  - Use a queue to keep track of nodes whose children are to be assigned.
  - Iteratively assign left and right children based on tokens.

---

### **Java Code:**

```java
public class Codec {
    private static final String SEP = ",";
    private static final String NULL = "null";

    // Serialize
    public String serialize(TreeNode root) {
        if (root == null) return "";

        StringBuilder sb = new StringBuilder();
        Queue<TreeNode> queue = new LinkedList<>();
        queue.offer(root);

        while (!queue.isEmpty()) {
            TreeNode curr = queue.poll();
            if (curr == null) {
                sb.append(NULL).append(SEP);
                continue;
            }
            sb.append(curr.val).append(SEP);
            queue.offer(curr.left);
            queue.offer(curr.right);
        }

        return sb.toString();
    }

    // Deserialize
    public TreeNode deserialize(String data) {
        if (data.isEmpty()) return null;

        String[] tokens = data.split(SEP);
        Queue<TreeNode> queue = new LinkedList<>();
        TreeNode root = new TreeNode(Integer.parseInt(tokens[0]));
        queue.offer(root);
        int i = 1;

        while (i < tokens.length) {
            TreeNode parent = queue.poll();

            // Left child
            if (!tokens[i].equals(NULL)) {
                TreeNode leftChild = new TreeNode(Integer.parseInt(tokens[i]));
                parent.left = leftChild;
                queue.offer(leftChild);
            }
            i++;

            // Right child
            if (i < tokens.length && !tokens[i].equals(NULL)) {
                TreeNode rightChild = new TreeNode(Integer.parseInt(tokens[i]));
                parent.right = rightChild;
                queue.offer(rightChild);
            }
            i++;
        }

        return root;
    }
}
```

### **Complexity Analysis:**

- **Time Complexity:**  
  - **Serialization:** O(N), visits each node once.  
  - **Deserialization:** O(N), processes each token once.
- **Space Complexity:**  
  - O(N) for the queue and the string storage.

---

## **Summary & Tips for Interviews:**

- **Serialization strategies:**  
  - Pre-order traversal (recursive): simpler and common.  
  - Level-order traversal (iterative): slightly more complex but can be more intuitive in some cases.
- **Deserialization:**  
  - Use recursion or iteration based on the serialization method.
- **Edge Cases:**  
  - Empty tree (`null` root).  
  - Tree with only left or right children.

---

This structured approach ensures clarity, understanding of different methods, and readiness for interviews. Keep practicing both recursive and iterative solutions!
