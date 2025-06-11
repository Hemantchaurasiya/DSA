# 82. Remove Duplicates II

---

## Problem Summary:

Given a **sorted** linked list, **remove all nodes that have duplicate numbers**, leaving only **distinct numbers** from the original list.

**Example:**

```
Input: 1->2->3->3->4->4->5
Output: 1->2->5

```

---

## Approach 1: Brute Force (Using Extra Space)

### Core Idea:

Traverse the list, count the frequency of each element, then reconstruct a new list containing only elements with frequency 1.

### Algorithm:

1. Use a HashMap to count occurrences of each value.
2. Traverse the linked list again:
    - For each node, check if its value has a count of 1.
    - If yes, append it to a new list.
3. Return the new list's head.

### Java Code:

```java
import java.util.HashMap;

public ListNode deleteDuplicates(ListNode head) {
    if (head == null) return null;

    HashMap<Integer, Integer> countMap = new HashMap<>();
    ListNode current = head;

    // Count frequency of each value
    while (current != null) {
        countMap.put(current.val, countMap.getOrDefault(current.val, 0) + 1);
        current = current.next;
    }

    // Dummy node for result list
    ListNode dummy = new ListNode(0);
    ListNode tail = dummy;
    current = head;

    // Rebuild list with only unique elements
    while (current != null) {
        if (countMap.get(current.val) == 1) {
            tail.next = current;
            tail = tail.next;
        }
        current = current.next;
    }
    tail.next = null; // terminate list
    return dummy.next;
}

```

### Complexity Analysis:

- **Time Complexity:** `O(n)` — Traverses the list twice.
- **Space Complexity:** `O(n)` — Extra space for HashMap.

---

## Approach 2: Iterative Two-Pointer (Without Extra Space)

### Core Idea:

Traverse the list once, using a **dummy head** and **two pointers**:

- `prev` to track the last node before a sequence of duplicates.
- `current` to traverse the list.

When duplicates are detected, skip all nodes with the same value, removing them from the list.

### Algorithm:

1. Initialize a dummy node pointing to head.
2. Set `prev` to dummy and `current` to head.
3. While `current` is not null:
    - If `current.next` exists and `current.val` == `current.next.val`:
        - Move `current` until the last duplicate node.
        - Set `prev.next` to `current.next` to remove all duplicates.
    - Else:
        - Move `prev` to `current`.
    - Move `current` forward.
4. Return `dummy.next`.

### Java Code:

```java
public ListNode deleteDuplicates(ListNode head) {
    if (head == null) return null;

    ListNode dummy = new ListNode(0);
    dummy.next = head;
    ListNode prev = dummy;
    ListNode current = head;

    while (current != null) {
        boolean isDuplicate = false;
        // Check for duplicates
        while (current.next != null && current.val == current.next.val) {
            isDuplicate = true;
            current = current.next;
        }
        if (isDuplicate) {
            // Skip all duplicates
            prev.next = current.next;
        } else {
            prev = current;
        }
        current = current.next;
    }
    return dummy.next;
}

```

### Complexity Analysis:

- **Time Complexity:** `O(n)` — Single traversal of list.
- **Space Complexity:** `O(1)` — No extra space; just pointers.

---

## Approach 3: Recursive Solution (Elegant and Concise)

### Core Idea:

Use recursion to remove duplicates:

- If current node has duplicates, recursively process the next node after skipping all duplicates.
- If current node is unique, link it to the result of recursive call on the remaining list.

### Algorithm:

1. Base case: if head is null or only one node, return head.
2. If current node value equals next node’s value:
    - Skip all nodes with this value.
    - Recursively process the list from the node after the last duplicate.
3. Else:
    - Keep current node, recursively process the rest, and link.

### Java Code:

```java
public ListNode deleteDuplicates(ListNode head) {
    if (head == null || head.next == null) return head;

    if (head.val == head.next.val) {
        // Skip all duplicates
        while (head.next != null && head.val == head.next.val) {
            head = head.next;
        }
        return deleteDuplicates(head.next);
    } else {
        head.next = deleteDuplicates(head.next);
        return head;
    }
}

```

### Complexity Analysis:

- **Time Complexity:** `O(n)` — Each node processed once.
- **Space Complexity:** `O(n)` — Recursion stack in worst case.

---

## Dry Run Example:

### Input:

```
1 -> 2 -> 3 -> 3 -> 4 -> 4 -> 5

```

### Approach 2 (Iterative Two-Pointer):

| Step | prev | current | List State | Action |
| --- | --- | --- | --- | --- |
| 1 | dummy(0) | 1 | dummy->1->2->3->3->4->4->5 | No duplicate, prev=1 |
| 2 | 0 | 2 | dummy->1->2->3->3->4->4->5 | No duplicate, prev=2 |
| 3 | 2 | 3 | dummy->1->2->3->3->4->4->5 | Found duplicate, skip all 3s |
|  |  |  | dummy->1->2->4->4->5 | prev.next = current.next (skip 3s) |
| 4 | 2 | 4 | dummy->1->2->4->4->5 | Found duplicate, skip all 4s |
|  |  |  | dummy->1->2->5 | prev.next = current.next (skip 4s) |
| 5 | 2 | 5 | dummy->1->2->5 | No duplicate, move prev to 5 |
| 6 | 5 | null | End | End traversal, return dummy.next |

**Final List:** `1 -> 2 -> 5`

---

## Summary:

| Approach | Time Complexity | Space Complexity | Key Idea |
| --- | --- | --- | --- |
| Brute Force | `O(n)` | `O(n)` | Count frequencies with HashMap, rebuild list |
| Iterative Two-Pointer | `O(n)` | `O(1)` | Use dummy node and two pointers to skip duplicates |
| Recursive | `O(n)` | `O(n)` | Recursively remove duplicates, elegant but stack-heavy |

---

## Final Tips:

- Since the list is sorted, duplicates are adjacent, making the two-pointer approach most efficient.
- Use dummy nodes to simplify edge cases (e.g., head removal).
- Recursive approach is concise but beware of stack overflow for very long lists.

---

**Happy Coding!**
