# Reverse Nodes in k-Group

---

## Problem Overview

Given a linked list, reverse the nodes of the list **k** at a time and return its modified list. If the number of nodes remaining is less than **k**, leave them as is.

---

## Approach 1: Brute Force (Reversing Entire List and Reversing Back in Chunks)

### Core Idea:

This naive approach involves reversing the entire list multiple times, which is inefficient. More practically, it's better to identify chunks of size **k** and reverse them directly.

*Note:* This approach sets the stage but is not optimal.

---

## Approach 2: Recursive Reversal in k-Groups (Better Solution)

### Core Idea:

- Recursively process the list in chunks of size **k**.
- Reverse the first **k** nodes.
- Recursively call the function for the remaining list.
- Connect the reversed chunk to the result of the recursive call.

### Algorithm:

1. **Check if there are at least k nodes** ahead:
    - Traverse **k** nodes.
    - If fewer than **k** nodes remain, return the head as is.
2. **Reverse the first k nodes:**
    - Reverse nodes within this chunk.
3. **Recursive call:**
    - Call the function for the list starting after the **k** nodes.
4. **Connect:**
    - Connect the tail of the reversed chunk to the result of the recursive call.
5. Return the new head.

### Java Code:

```java
public class ListNode {
    int val;
    ListNode next;
    ListNode(int val) { this.val = val; }
}

public class Solution {
    public ListNode reverseKGroup(ListNode head, int k) {
        if (head == null) return null;

        ListNode node = head;
        int count = 0;
        // Check if there are at least k nodes
        while (count < k && node != null) {
            node = node.next;
            count++;
        }
        if (count < k) {
            return head; // Less than k nodes, no reversal
        }

        // Reverse first k nodes
        ListNode prev = null;
        ListNode curr = head;
        for (int i = 0; i < k; i++) {
            ListNode nextNode = curr.next;
            curr.next = prev;
            prev = curr;
            curr = nextNode;
        }

        // Recursively process remaining list
        head.next = reverseKGroup(curr, k);
        return prev; // prev is the new head after reversal
    }
}

```

### Complexity Analysis:

- **Time Complexity:** O(N), each node is visited at most twice (once during reversal, once during recursion).
- **Space Complexity:** O(N/k) due to recursion stack depth, which simplifies to O(N) in the worst case.

### Dry Run:

Suppose the list is: `1 -> 2 -> 3 -> 4 -> 5`, with `k=2`.

| Step | Current List | Action | Resulting List | Notes |
| --- | --- | --- | --- | --- |
| 1 | 1 -> 2 -> 3 -> 4 -> 5 | Check first 2 nodes (1,2) | Reverse first 2 | After reversal: 2 -> 1 |
| 2 | 2 -> 1 -> 3 -> 4 -> 5 | Recurse starting from node after 2 | Continue with 3 -> 4 -> 5 | Connect 1 to recursive result |
| 3 | 3 -> 4 -> 5 | Check first 2 nodes (3,4) | Reverse first 2 | After reversal: 4 -> 3 |
| 4 | 4 -> 3 -> 5 | Recurse starting from node after 4 | Remaining: 5 | Connect 3 to 5 (no reversal needed) |
| 5 | 5 | Less than k nodes, return as is | 5 | End recursion |

Final list: **2 -> 1 -> 4 -> 3 -> 5**

---

## Approach 3: Iterative Solution (Most Optimal)

### Core Idea:

- Use **loop-based** approach to reverse nodes in chunks of **k**.
- Use pointers to manage the previous and current nodes.
- Carefully handle the connections between reversed chunks and remaining parts.

### Algorithm:

1. Initialize dummy node pointing to head for easier management.
2. Use pointers:
    - `groupPrev` to mark the node before the group.
    - `curr` to traverse the list.
3. For each group:
    - Check if there are at least **k** nodes ahead.
    - Reverse the **k** nodes.
    - Connect the previous group's tail to the new head of the reversed chunk.
4. Repeat until less than **k** nodes are remaining.

### Java Code:

```java
public class Solution {
    public ListNode reverseKGroup(ListNode head, int k) {
        if (head == null || k == 1) return head;

        ListNode dummy = new ListNode(0);
        dummy.next = head;
        ListNode groupPrev = dummy;

        while (true) {
            ListNode kth = getKthNode(groupPrev, k);
            if (kth == null) break; // Less than k nodes remaining

            ListNode groupNext = kth.next;
            // Reverse group
            ListNode prev = groupNext;
            ListNode curr = groupPrev.next;

            while (curr != groupNext) {
                ListNode temp = curr.next;
                curr.next = prev;
                prev = curr;
                curr = temp;
            }

            // Connect previous part with reversed group
            ListNode temp = groupPrev.next; // old head of the group
            groupPrev.next = kth;
            groupPrev = temp; // move to end of reversed group
        }

        return dummy.next;
    }

    private ListNode getKthNode(ListNode curr, int k) {
        while (curr != null && k > 0) {
            curr = curr.next;
            k--;
        }
        return curr;
    }
}

```

### Complexity Analysis:

- **Time Complexity:** O(N), each node is processed exactly once.
- **Space Complexity:** O(1), iterative approach uses constant extra space.

### Dry Run:

Using the same list: `1->2->3->4->5`, `k=2`.

| Step | Current List | Action | Connection Changes | Resulting List | Notes |
| --- | --- | --- | --- | --- | --- |
| 1 | 1->2->3->4->5 | Check next 2 nodes | Reverse 1 and 2 | 2->1->3->4->5 | Connect 2 to 3 |
| 2 | 3->4->5 | Next 2 nodes | Reverse 3 and 4 | 4->3->5 | Connect 1 to 4 |
| 3 | 5 | Less than k nodes | Stop | No change | End |

Final list: **2 -> 1 -> 4 -> 3 -> 5**

---

## Summary:

| Approach | Time Complexity | Space Complexity | Key Points |
| --- | --- | --- | --- |
| Brute Force | O(N^2) (inefficient) | O(1) | Not practical, just conceptual |
| Recursive | O(N) | O(N/k) (recursion stack) | Elegant, simpler code |
| Iterative | O(N) | O(1) | Most efficient, preferred in production |

---

## Tips for Interviews:

- Clarify if **k** divides the length of the list.
- Be careful with pointer manipulations when reversing.
- Use a dummy node to simplify edge cases.
- Practice both recursive and iterative approaches for flexibility.

---

**End of Revision Note**
