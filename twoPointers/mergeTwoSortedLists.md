# Merge Two Sorted Lists

---

## Problem Statement:
Given the heads of two sorted singly linked lists, merge them into a **single sorted linked list** and return the head of the merged list.

---

## Approach 1: Brute Force (Using Extra Space)

### Core Idea:
Extract all elements from both lists, sort them, and create a new linked list.

### Algorithm:
1. Traverse both linked lists and insert all node values into an array or list.
2. Sort the array of values.
3. Create a new linked list from sorted values.
4. Return the head of the new list.

### Java Code:
```java
public ListNode mergeTwoLists(ListNode l1, ListNode l2) {
    List<Integer> values = new ArrayList<>();
    ListNode temp = l1;
    while (temp != null) {
        values.add(temp.val);
        temp = temp.next;
    }
    temp = l2;
    while (temp != null) {
        values.add(temp.val);
        temp = temp.next;
    }
    Collections.sort(values);
    ListNode dummy = new ListNode(0);
    ListNode current = dummy;
    for (int val : values) {
        current.next = new ListNode(val);
        current = current.next;
    }
    return dummy.next;
}
```

### Complexity Analysis:
- **Time Complexity:** O((m + n) log(m + n)) — due to sorting `m + n` elements.
- **Space Complexity:** O(m + n) — for storing all node values in an array.

### Dry Run:
- Suppose `l1 = 1 -> 3 -> 5`, `l2 = 2 -> 4 -> 6`

**Steps:**
| Step | Extracted Values | Sorted Values | Constructed List | Explanation |
|-------|---------------------|----------------|------------------|--------------|
| Traverse l1 | [1, 3, 5] | | | Collect values from first list |
| Traverse l2 | [1, 3, 5, 2, 4, 6] | | | Collect from second list |
| Sorted | [1, 2, 3, 4, 5, 6] | | | Sorted array |
| List creation | 1 -> 2 -> 3 -> 4 -> 5 -> 6 | | | Final merged list |

---

## Approach 2: Iterative Merge (Optimal In-Place)

### Core Idea:
Since both lists are sorted, **merge them iteratively** by comparing head nodes and building the merged list on the fly.

### Algorithm:
1. Create a dummy node to serve as the start of the merged list.
2. Maintain a pointer `current` initially pointing to the dummy.
3. While both lists are non-empty:
   - Compare the current nodes of both lists.
   - Attach the smaller node to `current.next`.
   - Move the pointer of the list whose node was attached.
   - Move `current` forward.
4. After the loop, attach any remaining nodes from either list.
5. Return `dummy.next` as the head of the merged list.

### Java Code:
```java
public ListNode mergeTwoLists(ListNode l1, ListNode l2) {
    ListNode dummy = new ListNode(0);
    ListNode current = dummy;

    while (l1 != null && l2 != null) {
        if (l1.val <= l2.val) {
            current.next = l1;
            l1 = l1.next;
        } else {
            current.next = l2;
            l2 = l2.next;
        }
        current = current.next;
    }

    // Attach remaining nodes
    if (l1 != null) {
        current.next = l1;
    } else {
        current.next = l2;
    }

    return dummy.next;
}
```

### Complexity Analysis:
- **Time Complexity:** O(m + n) — each node is processed exactly once.
- **Space Complexity:** O(1) — in-place merging, no extra data structure used besides pointers.

### Dry Run:
- `l1 = 1 -> 3 -> 5`, `l2 = 2 -> 4 -> 6`

**Steps:**
| Step | `l1` / `l2` Nodes | `current` | Merged List | Explanation |
|-------|---------------------|--------------|--------------|--------------|
| Initial | (1 -> 3 -> 5), (2 -> 4 -> 6) | dummy | | Start with dummy |
| Step 1 | 1 <= 2? Yes | attach 1 | 0 -> 1 | Attach l1 node 1 |
| Step 2 | Now l1=3, l2=2 | attach 2 | 0 -> 1 -> 2 | Attach l2 node 2 |
| Step 3 | 3 <= 4? Yes | attach 3 | 0 -> 1 -> 2 -> 3 | Attach l1 node 3 |
| Step 4 | l1=5, l2=4 | attach 4 | 0 -> 1 -> 2 -> 3 -> 4 | Attach l2 node 4 |
| Step 5 | l1=5, l2=6 | attach 5 | 0 -> 1 -> 2 -> 3 -> 4 -> 5 | Attach l1 node 5 |
| Remaining | l1=null, l2=6 | attach 6 | 0 -> 1 -> 2 -> 3 -> 4 -> 5 -> 6 | Attach remaining node 6 |

**Final list:** `1 -> 2 -> 3 -> 4 -> 5 -> 6`

---

## Approach 3: Recursive Merge

### Core Idea:
Use recursion to merge the lists by comparing the head nodes at each step.

### Algorithm:
1. If either list is empty, return the other.
2. Compare the head nodes:
   - The smaller node becomes the head of the merged list.
   - Recursively merge the remaining parts.

### Java Code:
```java
public ListNode mergeTwoLists(ListNode l1, ListNode l2) {
    if (l1 == null) return l2;
    if (l2 == null) return l1;

    if (l1.val <= l2.val) {
        l1.next = mergeTwoLists(l1.next, l2);
        return l1;
    } else {
        l2.next = mergeTwoLists(l1, l2.next);
        return l2;
    }
}
```

### Complexity Analysis:
- **Time Complexity:** O(m + n) — each node is processed once.
- **Space Complexity:** O(m + n) — recursive call stack depth can be `O(m + n)` in worst case.

### Dry Run:
- `l1 = 1 -> 3 -> 5`, `l2 = 2 -> 4 -> 6`

**Steps:**
| Call | List1 Head | List2 Head | Result | Explanation |
|-------|--------------|--------------|---------|--------------|
| 1 | 1 | 2 | 1 -> merge(3->5, 2->4->6) | 1 is smaller, recurse |
| 2 | 3 | 2 | 2 -> merge(3->5, 4->6) | 2 is smaller, recurse |
| 3 | 3 | 4 | 3 -> merge(5, 4->6) | 3 is smaller, recurse |
| 4 | 5 | 4 | 4 -> merge(5, 6) | 4 is smaller, recurse |
| 5 | 5 | 6 | 5 -> merge(6, 6) | 5 is smaller, recurse |
| 6 | 6 | 6 | 6 -> merge(null, null) | Both are equal, pick one |

The final merged list is `1 -> 2 -> 3 -> 4 -> 5 -> 6`.

---

## Summary & Best Practices:
- For **in-place** merging, iterative approach is preferred due to its O(1) space complexity.
- Recursive approach is elegant but may lead to stack overflow for very long lists.
- Always compare the current nodes and advance pointers accordingly.
- Handle edge cases where one list is empty.
- Dry run with sample inputs to understand pointer movements.

---

## Final Tips:
- Use dummy nodes to simplify edge case handling.
- Remember the sorted property to choose the merging strategy.
- Practice recursive and iterative solutions for versatility.

Happy coding!
