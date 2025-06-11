# 234. Palindrome Linked List

---

## Problem Overview

Given the head of a singly linked list, determine if the list is a palindrome (reads the same forward and backward).

---

## 1. Brute Force Approach

### Core Idea

Extract all node values into a list/array, then check if the array is a palindrome.

### Algorithm

1. Traverse the linked list and store each node's value in an array.
2. Use two pointers: one at the start and one at the end of the array.
3. Move both pointers towards the middle, comparing values.
4. If all corresponding values match, the list is a palindrome; otherwise, it's not.

### Java Code

```java
public boolean isPalindrome(ListNode head) {
    List<Integer> vals = new ArrayList<>();
    ListNode current = head;
    while (current != null) {
        vals.add(current.val);
        current = current.next;
    }
    int left = 0, right = vals.size() - 1;
    while (left < right) {
        if (!vals.get(left).equals(vals.get(right))) {
            return false;
        }
        left++;
        right--;
    }
    return true;
}

```

### Complexity Analysis

- **Time Complexity:**
O(N), where N is the number of nodes, since we traverse the list once and check the array.
- **Space Complexity:**
O(N), due to storing node values in an auxiliary list.

### Dry Run

- Input: `[1, 2, 2, 1]`
- Extracted array: `[1, 2, 2, 1]`
- Pointers:
    - left = 0, right = 3
    - Compare `vals[0]` and `vals[3]`: 1 == 1 → OK
    - Move inward: left = 1, right = 2
    - Compare `vals[1]` and `vals[2]`: 2 == 2 → OK
    - Pointers cross: list is palindrome → return true

---

## 2. Better Solution (Using Fast & Slow Pointers + Reversing Second Half)

### Core Idea

Find the middle of the list using fast and slow pointers, reverse the second half, then compare the first and second halves.

### Algorithm

1. Use two pointers:
    - `slow` moves one step at a time.
    - `fast` moves two steps at a time.
2. When `fast` reaches the end, `slow` points to the middle.
3. Reverse the second half starting from `slow.next`.
4. Compare the first half and the reversed second half node-by-node.
5. (Optional) Restore the list to its original state.
6. Return whether the list is a palindrome.

### Java Code

```java
public boolean isPalindrome(ListNode head) {
    if (head == null || head.next == null) return true;

    // Find middle
    ListNode slow = head, fast = head;
    while (fast.next != null && fast.next.next != null) {
        slow = slow.next;
        fast = fast.next.next;
    }

    // Reverse second half
    ListNode secondHalfHead = reverseList(slow.next);
    ListNode firstHalfHead = head;

    // Compare halves
    ListNode p1 = firstHalfHead;
    ListNode p2 = secondHalfHead;
    boolean result = true;
    while (p2 != null) {
        if (p1.val != p2.val) {
            result = false;
            break;
        }
        p1 = p1.next;
        p2 = p2.next;
    }

    // Optional: restore the list
    slow.next = reverseList(secondHalfHead);

    return result;
}

// Helper method to reverse a linked list
private ListNode reverseList(ListNode head) {
    ListNode prev = null;
    ListNode curr = head;
    while (curr != null) {
        ListNode nextTemp = curr.next;
        curr.next = prev;
        prev = curr;
        curr = nextTemp;
    }
    return prev;
}

```

### Complexity Analysis

- **Time Complexity:**
O(N), as we traverse the list to find the middle, reverse, and compare.
- **Space Complexity:**
O(1), since no extra space is used apart from pointers.

### Dry Run

- Input: `[1, 2, 2, 1]`
- Find middle:
    - slow = node with value 2 (second node)
    - fast = node with value 1 (last node)
- Reverse second half:
    - Second half: `[2, 1]` → reversed: `[1, 2]`
- Compare:
    - First half: `[1, 2]`
    - Reversed second half: `[1, 2]`
- All match → list is palindrome.

---

## 3. Most Optimal Solution (Using Fast & Slow Pointers + In-place Reversal, No Extra Space)

### Core Idea

Same as the previous approach but emphasis on **constant space** and **in-place operations** — the most optimal for large inputs.

### Algorithm

1. Use fast and slow pointers to find the middle.
2. Reverse the second half in-place.
3. Compare the first and second half nodes.
4. (Optional) Restore the list if needed.
5. Return the result.

**Note:** This approach minimizes space and does not require extra data structures.

### Java Code

*As shown above in approach 2.*

### Complexity Analysis

- **Time Complexity:**
O(N)
- **Space Complexity:**
O(1)

### Dry Run

- Same as previous dry run.

---

# Summary

| Approach | Core Idea | Algorithm | Code | Time Complexity | Space Complexity | Notes |
| --- | --- | --- | --- | --- | --- | --- |
| **Brute Force** | Store list values in array and check palindrome | Convert list to array and compare with two pointers | Provided above | O(N) | O(N) | Simple but uses extra space |
| **Optimal (Middle + Reversal)** | Find middle, reverse second half, compare | Fast/slow pointers + in-place reversal | Provided above | O(N) | O(1) | Most space-efficient, suitable for large lists |

---

## Final Tips for Interview

- Always consider the constraints: large lists favor in-place solutions.
- Remember to handle edge cases: empty list, single node.
- Restoring the list after check is optional unless specified.
- Practice pointer manipulations carefully, especially reversal.

---

**Happy Revise & Practice!**
