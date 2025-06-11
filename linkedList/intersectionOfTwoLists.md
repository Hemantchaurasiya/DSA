# 160. Intersection of Lists

---

## Problem Summary

Given two singly linked lists, determine the node at which they intersect. If they do not intersect, return `null`.

---

## Approach 1: Brute Force

### Core Idea

Compare each node of the first list with every node of the second list to check for intersection.

### Algorithm

1. Traverse the first list node-by-node.
2. For each node in the first list, traverse the second list from the head.
3. Check if the current nodes from both lists are the same (by reference).
4. If a match is found, return the intersecting node.
5. If no match is found after complete traversal, return `null`.

### Java Code

```java
public ListNode getIntersectionNode(ListNode headA, ListNode headB) {
    ListNode currA = headA;
    while (currA != null) {
        ListNode currB = headB;
        while (currB != null) {
            if (currA == currB) {
                return currA;
            }
            currB = currB.next;
        }
        currA = currA.next;
    }
    return null;
}

```

### Complexity Analysis

- **Time Complexity:**
    
    **O(m * n)**, where `m` and `n` are the lengths of the two lists.
    
    Each node in list A is compared against every node in list B.
    
- **Space Complexity:**
    
    **O(1)**, no extra space used apart from a few pointers.
    

### Dry Run

**Input:**

```
List A: 1 -> 2 -> 3 -> 4 -> 5
List B: 9 -> 4 -> 5 (shared nodes for 4 and 5)

```

*Note:* Assume nodes with value 4 and 5 are shared (intersection).

**Steps:**

- For node with value 1 in List A, check all nodes in List B → no match.
- For node with value 2 in List A, check all nodes in List B → no match.
- For node with value 3 in List A, check all nodes in List B → no match.
- For node with value 4 in List A, check all nodes in List B:
    - Node with value 9 → no
    - Node with value 4 (shared node) → **match found!**
- Return the node with value 4.

---

## Approach 2: Using Length Difference (Better Solution)

### Core Idea

Align the start of both lists by skipping the extra nodes in the longer list, then traverse together to find the intersection.

### Algorithm

1. Calculate the lengths of both lists.
2. Determine the difference in lengths (`diff`).
3. Advance the pointer in the longer list by `diff` nodes.
4. Traverse both lists in tandem:
    - If the nodes are the same, return that node.
    - Else, continue moving both pointers.
5. If no intersection occurs, return `null`.

### Java Code

```java
public ListNode getIntersectionNode(ListNode headA, ListNode headB) {
    int lenA = 0, lenB = 0;
    ListNode currA = headA, currB = headB;

    // Calculate length of list A
    while (currA != null) {
        lenA++;
        currA = currA.next;
    }
    // Calculate length of list B
    while (currB != null) {
        lenB++;
        currB = currB.next;
    }

    // Reset pointers
    currA = headA;
    currB = headB;

    // Advance the longer list by the difference
    if (lenA > lenB) {
        for (int i = 0; i < lenA - lenB; i++) {
            currA = currA.next;
        }
    } else {
        for (int i = 0; i < lenB - lenA; i++) {
            currB = currB.next;
        }
    }

    // Move both pointers until they meet or reach end
    while (currA != null && currB != null) {
        if (currA == currB) {
            return currA;
        }
        currA = currA.next;
        currB = currB.next;
    }

    return null;
}

```

### Complexity Analysis

- **Time Complexity:**
    
    **O(m + n)**, where `m` and `n` are the lengths of the lists.
    
    Calculating lengths and traversing both lists once.
    
- **Space Complexity:**
    
    **O(1)**, only a few pointers used.
    

### Dry Run

**Input:**

```
List A: 1 -> 2 -> 3 -> 4 -> 5
List B: 9 -> 4 -> 5 (shared nodes at 4 and 5)

```

- Length of List A = 5
- Length of List B = 3
- Advance pointer in List A by 2 nodes (since 5 - 3 = 2): pointer now at node with value 3.
- Traverse together:
    - List A node 3, List B node 9 → no
    - List A node 4, List B node 4 → **match!**
- Return node with value 4.

---

## Approach 3: Two Pointer Technique (Most Optimal)

### Core Idea

Utilize two pointers that traverse both lists, switching to the other list upon reaching the end, ensuring both pointers traverse equal total length.

### Algorithm

1. Initialize two pointers, `pA` at headA and `pB` at headB.
2. Traverse the lists:
    - If `pA` reaches null, redirect it to headB.
    - If `pB` reaches null, redirect it to headA.
3. Continue until `pA == pB` (either at intersection node or both null).
4. Return the intersection node or `null` if no intersection.

### Java Code

```java
public ListNode getIntersectionNode(ListNode headA, ListNode headB) {
    if (headA == null || headB == null) return null;

    ListNode pA = headA, pB = headB;

    while (pA != pB) {
        pA = (pA == null) ? headB : pA.next;
        pB = (pB == null) ? headA : pB.next;
    }

    return pA; // can be null or intersection node
}

```

### Complexity Analysis

- **Time Complexity:**
    
    **O(m + n)**, as each pointer traverses each list at most once.
    
- **Space Complexity:**
    
    **O(1)**, only pointers are used.
    

### Dry Run

**Input:**

```
List A: 1 -> 2 -> 3 -> 4 -> 5
List B: 9 -> 4 -> 5

```

- Initialize pA at 1, pB at 9.
- Move both:
    - pA: 1 → 2 → 3 → 4 → 5 → null → switch to headB
    - pB: 9 → 4 → 5 → null → switch to headA
- Next step:
    - pA at 9, pB at 1
- Continue:
    - pA: 9 → 4 → 5
    - pB: 1 → 2 → 3 → 4
- When both reach node with value 4, `pA == pB`, return that node.

---

## **Summary Table**

| Approach | Core Idea | Time Complexity | Space Complexity | Best Use Case |
| --- | --- | --- | --- | --- |
| Brute Force | Compare every node of list A with list B | **O(m * n)** | **O(1)** | Small lists, quick implementation |
| Length Difference | Align list starts based on length difference | **O(m + n)** | **O(1)** | Medium to large lists |
| Two Pointer Technique | Traverse both lists, switching heads to sync up | **O(m + n)** | **O(1)** | Most optimal, clean and efficient |

---

## Final Note:

The **Two Pointer Technique** is the most elegant and optimal approach for this problem, providing linear time complexity with constant space and no extra data structures.

---

**Happy Coding!**
