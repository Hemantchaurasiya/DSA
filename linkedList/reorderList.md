# 143. Reorder List

---

## Problem Overview

Given a singly linked list, **rearrange** it so that:

- The list's nodes are reordered as follows:
    
    `L0 → L1 → L2 → ... → Ln-1 → Ln`
    
    should become:
    
    `L0 → Ln → L1 → Ln-1 → L2 → Ln-2 → ...`
    

**Goal:** Reorder the list **in-place** without altering node values, only adjusting pointers.

---

## Approach 1: Brute Force (Using Extra Space)

### Core Idea

Use additional space to temporarily store nodes, then reconstruct the list in the required order.

### Algorithm

1. Traverse the list, store all nodes in an array or list.
2. Use two pointers:
    - `left` starting at the beginning,
    - `right` starting at the end.
3. Reconstruct the list:
    - Connect `left` node to `right` node.
    - Increment `left`, decrement `right`.
    - Connect `right` node to the next `left` node.
    - Continue until pointers meet or cross.
4. Set the next of the last node to `null` to terminate.

### Java Code

```java
public void reorderList(ListNode head) {
    if (head == null || head.next == null) return;

    List<ListNode> nodes = new ArrayList<>();
    ListNode current = head;

    // Step 1: Store nodes in list
    while (current != null) {
        nodes.add(current);
        current = current.next;
    }

    int left = 0;
    int right = nodes.size() - 1;

    // Step 2 & 3: Reorder using two pointers
    while (left < right) {
        nodes.get(left).next = nodes.get(right);
        left++;
        if (left == right) break; // For odd length list
        nodes.get(right).next = nodes.get(left);
        right--;
    }
    // Terminate list
    nodes.get(left).next = null;
}

```

### Complexity Analysis

- **Time Complexity:** **O(N)**, traverses list once to store nodes, then reorders.
- **Space Complexity:** **O(N)**, stores all nodes in an auxiliary list.

### Dry Run

Input:

```
1 -> 2 -> 3 -> 4

```

- List of nodes: [1, 2, 3, 4]
- Reordering:
    - 1 → 4 → 2 → 3
    - Final list: 1 → 4 → 2 → 3 → null

---

## Approach 2: Optimal In-Place Solution

### Core Idea

Perform the reordering **without extra space** by:

1. Finding the middle of the list.
2. Reversing the second half.
3. Merging the two halves alternately.

### Algorithm

1. **Find the middle node:**
    - Use slow and fast pointers.
2. **Reverse the second half:**
    - Reverse the list starting from the middle.
3. **Merge the two halves:**
    - Alternately connect nodes from the first and second halves.

### Java Code

```java
public void reorderList(ListNode head) {
    if (head == null || head.next == null) return;

    // Step 1: Find the middle
    ListNode slow = head, fast = head;
    while (fast.next != null && fast.next.next != null) {
        slow = slow.next;
        fast = fast.next.next;
    }

    // Step 2: Reverse second half
    ListNode second = reverse(slow.next);
    slow.next = null; // Split the list

    // Step 3: Merge two halves
    ListNode first = head;
    while (second != null) {
        ListNode temp1 = first.next;
        ListNode temp2 = second.next;

        first.next = second;
        second.next = temp1;

        first = temp1;
        second = temp2;
    }
}

private ListNode reverse(ListNode head) {
    ListNode prev = null;
    ListNode current = head;
    while (current != null) {
        ListNode nextTemp = current.next;
        current.next = prev;
        prev = current;
        current = nextTemp;
    }
    return prev;
}

```

### Complexity Analysis

- **Time Complexity:** **O(N)**, each step (finding middle, reversing, merging) is linear.
- **Space Complexity:** **O(1)**, only pointers are used, no extra data structures.

### Dry Run

Input:

```
1 -> 2 -> 3 -> 4

```

- **Find middle:** node `2`
- **Reverse second half:** `4 -> 3`
- **Merge:**
    - 1 → 4 → 2 → 3
- List after merge:

```
1 -> 4 -> 2 -> 3

```

---

## Approach 3: Recursive (Less common, more conceptual)

### Core Idea

Recursively reorder the list by connecting nodes from the start and end, reducing the problem size each call.

### Algorithm

- Use recursion to:
    - Find the last node.
    - Connect the first node to the last node.
    - Recurse on the sublist excluding the connected nodes.

*Note: This approach is less efficient and more complex to implement correctly for large lists.*

---

## Summary

| Approach | Time Complexity | Space Complexity | Key Points |
| --- | --- | --- | --- |
| Brute Force (Auxiliary List) | O(N) | O(N) | Simple to implement; uses extra space for node storage. |
| In-Place Reversal & Merge | O(N) | O(1) | Efficient, no extra space, optimal for large data. |
| Recursive (Conceptual) | O(N^2) or O(N) | O(N) or O(Depth) | Elegant but less practical; more complex to code. |

---

## Final Tips for Interviews

- Understand the problem and the goal clearly.
- Practice the **find middle + reverse + merge** approach for optimal in-place solution.
- Be aware of edge cases: empty list, single node, list with even/odd length.
- Emphasize in your explanation how the list is manipulated step-by-step.

---

**Happy coding!**
