# Longest Happy String

The **Longest Happy String** problem involves constructing the longest possible string using characters `'a'`, `'b'`, and `'c'`, given maximum limits for each character, such that:
- The string contains no three consecutive identical characters (e.g., `'aaa'` is not allowed).
- The string is as long as possible.

---

## Problem Statement
Given integers `a`, `b`, and `c` representing the maximum counts of `'a'`, `'b'`, and `'c'` respectively, build the longest happy string without three consecutive identical characters.

---

## Approach Overview

---

### 1. **Brute Force (Naive) / Greedy with Priority Queue**

**Core Idea:**
- Always pick the character with the highest remaining count that doesn't violate the "no three in a row" rule.
- Use a max-heap (priority queue) to keep track of characters based on their remaining counts.
- At each step:
  - Pick the character with the highest count.
  - Append it if it doesn't cause three consecutive same characters.
  - Reduce its count.
  - If the character can't be used, pick the next best character.
- Continue until no more characters can be used.

**Algorithm Steps:**
1. Initialize a max-heap with pairs `(count, character)` for `'a'`, `'b'`, `'c'`.
2. While the heap is not empty:
   - Extract the character with the highest count.
   - Check if adding this character would create three consecutive same characters.
   - If yes, try the next character in the heap.
   - If no, append this character to the result, decrement its count.
   - If after decrement, the count is still > 0, push it back into the heap.
3. Stop when no characters can be added without violating the rule.

---

### **Java Code:**
```java
import java.util.*;

public class Solution {
    public String longestHappyString(int a, int b, int c) {
        PriorityQueue<int[]> maxHeap = new PriorityQueue<>((x, y) -> y[0] - x[0]);
        if (a > 0) maxHeap.offer(new int[]{a, 'a'});
        if (b > 0) maxHeap.offer(new int[]{b, 'b'});
        if (c > 0) maxHeap.offer(new int[]{c, 'c'});

        StringBuilder sb = new StringBuilder();

        while (!maxHeap.isEmpty()) {
            int[] first = maxHeap.poll();

            int n = sb.length();
            // Check if last two characters are same as the current candidate
            if (n >= 2 && sb.charAt(n - 1) == first[1] && sb.charAt(n - 2) == first[1]) {
                if (maxHeap.isEmpty()) {
                    break; // No other character to pick
                }
                int[] second = maxHeap.poll();
                sb.append((char)second[1]);
                second[0]--;
                if (second[0] > 0) {
                    maxHeap.offer(second);
                }
                maxHeap.offer(first); // Put back the first character
            } else {
                sb.append((char)first[1]);
                first[0]--;
                if (first[0] > 0) {
                    maxHeap.offer(first);
                }
            }
        }

        return sb.toString();
    }
}
```

---

### **Complexity Analysis:**
- **Time Complexity:** O(N), where N is the length of the constructed string. Each character is processed at most once. Each heap operation is O(log 3) = O(1) since max heap contains only 3 elements.
- **Space Complexity:** O(1), as the heap size is fixed (~3 elements), and the output string can be at most `a + b + c`.

---

### 2. **More Optimized Approach: Using Greedy Strategy with Sorted Counts**

**Core Idea:**
- Repeatedly pick the character with the highest remaining count.
- Use a greedy approach to prevent three consecutive identical characters.
- After choosing a character, if it causes three-in-a-row, pick the next available character.
- Update counts and continue until no characters can be added.

This is conceptually similar to the previous approach but implemented with simpler control flow.

---

### **Java Code:**
```java
public class Solution {
    public String longestHappyString(int a, int b, int c) {
        StringBuilder sb = new StringBuilder();
        int[] counts = {a, b, c};
        char[] chars = {'a', 'b', 'c'};

        while (true) {
            // Find the index of the max count character
            int maxIdx = -1;
            for (int i = 0; i < 3; i++) {
                if (counts[i] > 0) {
                    if (maxIdx == -1 || counts[i] > counts[maxIdx]) {
                        maxIdx = i;
                    }
                }
            }
            if (maxIdx == -1) break; // no characters left

            int n = sb.length();

            // Check for three consecutive same characters
            if (n >= 2 && sb.charAt(n - 1) == chars[maxIdx] && sb.charAt(n - 2) == chars[maxIdx]) {
                // Find next best character
                int nextIdx = -1;
                for (int i = 0; i < 3; i++) {
                    if (i != maxIdx && counts[i] > 0) {
                        if (nextIdx == -1 || counts[i] > counts[nextIdx]) {
                            nextIdx = i;
                        }
                    }
                }

                if (nextIdx == -1) break; // no other character to use

                sb.append(chars[nextIdx]);
                counts[nextIdx]--;
            } else {
                // Use the max count character
                sb.append(chars[maxIdx]);
                counts[maxIdx]--;
            }
        }

        return sb.toString();
    }
}
```

---

### **Complexity Analysis:**
- **Time Complexity:** O(a + b + c), since each character is appended at most once.
- **Space Complexity:** O(a + b + c), for the output string.

---

## **Dry Run with Sample Input**

### Example:
```
a = 1, b = 1, c = 7
```

### Step-by-step:
| Step | Current String | Counts (a,b,c) | Selected Char | Explanation |
|---------|------------------|----------------|-----------------|--------------|
| 1       | ""               | (1,1,7)        | 'c'             | Max is 'c' (7) |
| 2       | "c"              | (1,1,6)        | 'c'             | Append 'c', count c=6 |
| 3       | "cc"             | (1,1,6)        | 'c'             | Last two 'c's, can't pick 'c' again |
|         |                  |                | Next max is 'a' or 'b' (both 1) | Pick 'a' (say) |
| 4       | "cca"            | (0,1,6)        | 'c'             | Append 'c', count c=5 |
| 5       | "ccac"           | (0,1,6)        | 'b'             | Append 'b', count b=0 |
| 6       | "ccacb"          | (0,0,6)        | 'c'             | Append 'c', count c=5 |
| Continue similarly | | | | Final string maximizes length without 3 consecutive identical characters |

---

## **Summary**

| Approach | Core Idea | Data Structures | Pros | Cons | Time Complexity | Space Complexity |
|------------|--------------|-------------------|-------|-------|-----------------|------------------|
| Naive | Repeated linear scan | Arrays + iteration | Simple | Inefficient for large input | O(N^2) | O(1) |
| Greedy with Priority Queue | Use max-heap to pick characters | PriorityQueue | Efficient, handles large counts | Slightly complex implementation | O(N) | O(1) |
| Greedy with Sorted Counts | Pick max count char, handle constraints | Arrays + greedy logic | Conceptually simple | Slightly repetitive code | O(a + b + c) | O(1) |

---

## **Final Tips**
- Always prioritize characters with the highest remaining count.
- Prevent three consecutive same characters by checking the last two characters.
- Use a priority queue (max-heap) for efficient selection in more complex implementations.
- For small constraints, simple greedy with checks is sufficient.

---

**End of DSA Revision Note for Longest Happy String**
