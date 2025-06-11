# 83. Remove Duplicates I

This problem involves removing duplicate elements from a sorted linked list so that each element appears only once.

---

## Problem Overview

Given the head of a sorted linked list, **remove all duplicates such that each element appears only once**. Return the modified linked list.

---

## Approach 1: Brute Force (Not optimal for linked list, mainly conceptual for arrays)

### Core Idea:

- For arrays: Use nested loops to check every pair for duplicates.
- For linked lists: Since the list is sorted, duplicates are adjacent, so a simple traversal suffices.

*Note:* For linked lists, the brute-force approach isn't practical as it involves nested iteration, but since the list is sorted, we can directly leverage the sorted property.

---

## Approach 2: **Optimal Solution Using Single Pass (Most Common & Efficient)**

### Core Idea:

- Since the list is sorted, **duplicates are always adjacent**.
- Traverse the list once, compare the current node's value with the next node's value.
- If they are the same, **skip** the next node.
- Continue until reaching the end of the list.

### Algorithm:

1. Initialize a pointer `current` at the head of the list.
2. While `current` is not null and `current.next` is not null:
    - If `current.val == current.next.val`:
        - Remove the duplicate by linking `current.next` to `current.next.next`.
    - Else:
        - Move `current` to `current.next`.
3. Return the head of the modified list.

---

## Java Code:

```java
class ListNode {
    int val;
    ListNode next;
    ListNode(int val) {
        this.val = val;
        this.next = null;
    }
}

public class Solution {
    public ListNode deleteDuplicates(ListNode head) {
        ListNode current = head;
        while (current != null && current.next != null) {
            if (current.val == current.next.val) {
                // Skip the duplicate node
                current.next = current.next.next;
            } else {
                // Move to the next node
                current = current.next;
            }
        }
        return head;
    }
}

```

---

## Complexity Analysis:

### Time Complexity:

- **O(n)**, where `n` is the number of nodes in the list.
- We traverse the list only once, comparing adjacent elements.

### Space Complexity:

- **O(1)** (constant space).
- No extra data structures are used; modifications are done in-place.

---

## Dry Run:

### Sample Input:

```
1 -> 1 -> 2 -> 3 -> 3

```

### Step-by-Step Walkthrough:

| Step | `current` Node | List State | Action |
| --- | --- | --- | --- |
| 1 | Node with val=1 | 1 -> 1 -> 2 -> 3 -> 3 | `current.val == current.next.val` (1 == 1) → skip next |
|  |  | -> 2 -> 3 -> 3 | `current.next` now points to node with val=2 |
| 2 | Node with val=1 | 1 -> 2 -> 3 -> 3 | Move `current` to next (val=2) |
| 3 | Node with val=2 | 1 -> 2 -> 3 -> 3 | `current.val != current.next.val` (2 != 3) → move forward |
| 4 | Node with val=3 | 1 -> 2 -> 3 -> 3 | `current.val == current.next.val` (3 == 3) → skip next |
|  |  | -> 1 -> 2 -> 3 | `current.next` now points to null (end of list) |
| 5 | Node with val=3 | 1 -> 2 -> 3 | End of list reached, stop traversal |

**Final List:**

```
1 -> 2 -> 3

```

---

## Summary:

| Aspect | Details |
| --- | --- |
| **Approach** | Single-pass traversal exploiting the sorted property. |
| **Key Insight** | Adjacent duplicates can be removed by adjusting pointers. |
| **Optimality** | O(n) time, O(1) space. |
| **Use Case** | When input list is sorted, this approach is most efficient. |

---

This comprehensive guide covers multiple approaches, their reasoning, code, and dry run, providing a ready reference for interview preparation and quick revision.
