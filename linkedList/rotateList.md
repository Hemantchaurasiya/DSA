# 61 - Rotate List

---

## Problem Overview

Given a singly linked list, rotate the list to the right by `k` places.

**Example:**

```
Input: head = [1,2,3,4,5], k = 2
Output: [4,5,1,2,3]

```

---

## Approach 1: Brute Force (Repeated Rotation)

### Core Idea

Rotate the list one step to the right, `k` times. Each rotation involves moving the last element to the front.

### Algorithm

1. For each rotation:
    - Traverse the list until the second last node.
    - Detach the last node.
    - Make it the new head.
2. Repeat `k` times.

### Java Code

```java
public ListNode rotateRight(ListNode head, int k) {
    if (head == null || head.next == null || k == 0) return head;

    int length = 0;
    ListNode curr = head;

    // Find length
    while (curr != null) {
        length++;
        curr = curr.next;
    }

    k = k % length;
    for (int i = 0; i < k; i++) {
        ListNode prev = null, tail = head;
        // Traverse to second last node
        while (tail.next != null) {
            prev = tail;
            tail = tail.next;
        }
        // Make last node the new head
        prev.next = null;
        tail.next = head;
        head = tail;
    }
    return head;
}

```

### Complexity Analysis

- **Time Complexity:** `O(k * n)`
For each of the `k` rotations, we traverse almost the entire list (`n` nodes).
- **Space Complexity:** `O(1)`
No extra space is used apart from pointers.

### Dry Run

```
Input: 1 -> 2 -> 3 -> 4 -> 5, k=2

Iteration 1:
- Last element: 5
- List after rotation: 5 -> 1 -> 2 -> 3 -> 4
Iteration 2:
- Last element: 4
- List after rotation: 4 -> 5 -> 1 -> 2 -> 3

Output: 4 -> 5 -> 1 -> 2 -> 3

```

---

## Approach 2: Using Length Calculation and Modular Arithmetic

### Core Idea

Calculate the length of the list, then connect the tail to the head to form a circular list. Break the circle at the correct position.

### Algorithm

1. Traverse the list to determine its length (`n`).
2. Compute `k = k % n` (rotation beyond the length resets).
3. Connect the tail to the head to form a circular list.
4. Find the new tail: `(n - k - 1)`th node.
5. Break the circle after the new tail, and set the new head.

### Java Code

```java
public ListNode rotateRight(ListNode head, int k) {
    if (head == null || head.next == null || k == 0) return head;

    // Compute the length and get the tail
    ListNode curr = head;
    int length = 1;
    while (curr.next != null) {
        curr = curr.next;
        length++;
    }

    // Make the list circular
    curr.next = head;

    // Compute the new tail position
    k = k % length;
    int stepsToNewTail = length - k - 1;
    ListNode newTail = head;
    for (int i = 0; i < stepsToNewTail; i++) {
        newTail = newTail.next;
    }

    // Set the new head and break the circle
    ListNode newHead = newTail.next;
    newTail.next = null;

    return newHead;
}

```

### Complexity Analysis

- **Time Complexity:** `O(n)`
Single traversal to find length, and one traversal to find new tail.
- **Space Complexity:** `O(1)`
No extra space used.

### Dry Run

```
Input: 1 -> 2 -> 3 -> 4 -> 5, k=2

Length: 5
Make list circular: 1 -> 2 -> 3 -> 4 -> 5 -> (back to 1)
k % length = 2
steps to new tail = 5 - 2 - 1 = 2

Find new tail:
- Start at head (1), move 2 steps: arrive at node 3
Set new head: node 4
Break circle after node 3:
- 3.next = null

Result: 4 -> 5 -> 1 -> 2 -> 3

```

---

## Approach 3: Most Optimal Solution (Circular List + Two Pointers)

### Core Idea

Leverage the circular list approach with two pointers to efficiently find the new head position.

### Algorithm

1. Calculate length and connect tail to head to form a circle.
2. Find the new tail:
    - Move `(length - k % length - 1)` steps from the head.
3. Assign the new head as `newTail.next`.
4. Break the circle by setting `newTail.next = null`.

### Java Code

```java
public ListNode rotateRight(ListNode head, int k) {
    if (head == null || head.next == null || k == 0) return head;

    // Compute length and get tail
    ListNode tail = head;
    int length = 1;
    while (tail.next != null) {
        tail = tail.next;
        length++;
    }

    // Connect tail to head to make circular
    tail.next = head;

    // Find the new tail position
    k = k % length;
    int stepsToNewTail = length - k - 1;

    ListNode newTail = head;
    for (int i = 0; i < stepsToNewTail; i++) {
        newTail = newTail.next;
    }

    // Set new head
    ListNode newHead = newTail.next;

    // Break the circle
    newTail.next = null;

    return newHead;
}

```

### Complexity Analysis

- **Time Complexity:** `O(n)`
One pass to compute length and one to find the break point.
- **Space Complexity:** `O(1)`

### Dry Run

Same as previous approach with the same input, demonstrating the efficiency and elegance of the circular list approach.

---

## Summary of Approaches:

| Approach | Core Idea | Time Complexity | Space Complexity | Notes |
| --- | --- | --- | --- | --- |
| Brute Force | Rotate one step `k` times | `O(k * n)` | `O(1)` | Inefficient for large `k` |
| Length + Circular | Make list circular, break at correct position | `O(n)` | `O(1)` | Efficient, straightforward |
| Two Pointers + Circular | Similar to above; optimized pointer handling | `O(n)` | `O(1)` | Most optimal, clean implementation |

---

## Final Tips for Interview:

- Always check for edge cases: empty list, single node, `k=0`.
- Use the circular list approach for optimal performance.
- Remember to take `k % length` to handle rotations larger than list size.
- Practice dry runs with small and large inputs to ensure correctness.

---

Feel free to revisit this note for quick revision before interviews!
