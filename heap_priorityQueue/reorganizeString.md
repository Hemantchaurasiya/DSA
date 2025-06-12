# Reorganize String

The **Reorganize String** problem involves rearranging the characters of a string such that no two identical characters are adjacent. If such a rearrangement is impossible, return an empty string.

---

## Problem Statement
Given a string `s`, rearrange the characters so that no two adjacent characters are the same. If such an arrangement is not possible, return an empty string.

---

## Approaches to Solve the Problem

---

### 1. **Brute Force Approach**

**Core Idea:**
Try all possible permutations of the string and check whether any permutation satisfies the condition that no two identical characters are adjacent.

---

### 2. **Greedy + Max Heap (Priority Queue) Approach**

**Core Idea:**
Always pick the most frequent remaining character to place next, ensuring no two same characters are adjacent. Use a max heap to efficiently select the next character.

---

### 3. **Optimal Solution: Frequency Counting + Greedy with Priority Queue**

**Core Idea:**
- Count character frequencies.
- Use a max heap to always pick the two most frequent characters alternately.
- Place characters while ensuring no two identical characters are adjacent.
- If at any point, it's impossible to place characters without repeating adjacency, return an empty string.

---

## 1. Brute Force Approach

### **Algorithm:**
- Generate all permutations of the string.
- For each permutation, check if no two adjacent characters are the same.
- Return the first valid permutation found.
- If none found, return an empty string.

### **Java Code:**
```java
import java.util.*;

public class Solution {
    public String reorganizeString(String s) {
        List<String> permutations = new ArrayList<>();
        permute(s.toCharArray(), 0, permutations);
        for (String perm : permutations) {
            if (isValid(perm)) {
                return perm;
            }
        }
        return "";
    }

    private void permute(char[] arr, int index, List<String> permutations) {
        if (index == arr.length) {
            permutations.add(new String(arr));
            return;
        }
        for (int i = index; i < arr.length; i++) {
            swap(arr, i, index);
            permute(arr, index + 1, permutations);
            swap(arr, i, index);
        }
    }

    private boolean isValid(String s) {
        for (int i = 1; i < s.length(); i++) {
            if (s.charAt(i) == s.charAt(i - 1)) return false;
        }
        return true;
    }

    private void swap(char[] arr, int i, int j) {
        char temp = arr[i]; arr[i] = arr[j]; arr[j] = temp;
    }
}
```

### **Complexity Analysis:**
- **Time Complexity:** O(N!), due to generating all permutations.
- **Space Complexity:** O(N!) for storing permutations (not practical for large strings).

### **Dry Run:**
- For `s = "aab"`, permutations include `"aab"`, `"aba"`, `"baa"`.
- Valid permutations: `"aba"` (since no two same adjacent).
- Output: `"aba"`.

---

### 2. **Greedy + Max Heap Approach**

### **Algorithm:**
- Count the frequency of each character.
- Insert characters into a max heap based on their frequency.
- Repeatedly:
  - Extract the top two characters (most frequent).
  - Append them to the output string.
  - Decrement their counts and reinsert if still > 0.
- If at any point, only one character remains with frequency > 1, and it cannot be placed without adjacency, return "".

### **Java Code:**
```java
import java.util.*;

public class Solution {
    public String reorganizeString(String s) {
        int[] counts = new int[26];
        for (char c : s.toCharArray()) {
            counts[c - 'a']++;
        }

        PriorityQueue<int[]> maxHeap = new PriorityQueue<>((a, b) -> b[1] - a[1]);
        for (int i = 0; i < 26; i++) {
            if (counts[i] > 0) {
                maxHeap.offer(new int[]{i, counts[i]});
            }
        }

        StringBuilder result = new StringBuilder();
        while (maxHeap.size() > 1) {
            int[] first = maxHeap.poll();
            int[] second = maxHeap.poll();

            result.append((char)(first[0] + 'a'));
            result.append((char)(second[0] + 'a'));

            if (--first[1] > 0) maxHeap.offer(first);
            if (--second[1] > 0) maxHeap.offer(second);
        }

        if (!maxHeap.isEmpty()) {
            int[] last = maxHeap.poll();
            if (last[1] > 1) return ""; // Cannot place remaining same chars adjacently
            result.append((char)(last[0] + 'a'));
        }

        return result.toString();
    }
}
```

### **Complexity Analysis:**
- **Time Complexity:** O(N log K), where K is the number of unique characters (max 26).
- **Space Complexity:** O(K), for the heap and counts.

### **Dry Run:**
- Input: `"aab"`.
- Counts: a=2, b=1.
- Heap: [(a,2), (b,1)].
- Extract: a, b → result: "ab".
- Decrement counts: a=1, b=0.
- Reinsert a: (a,1).
- Remaining heap: (a,1).
- Append last 'a' → "aba".
- Valid output: `"aba"`.

---

### 3. **Optimal Solution: Frequency Counting + Greedy Placement**

### **Core Idea:**
- Count frequencies of characters.
- Check if the highest frequency exceeds `(n+1)`, making reorganization impossible.
- Use a max heap to select the most frequent characters, placing them alternately to avoid adjacency.
- Always pick the top two characters, append them to the result, decrement counts, and reinsert if still > 0.

### **Java Code:**
```java
import java.util.*;

public class Solution {
    public String reorganizeString(String s) {
        int[] counts = new int[26];
        int maxFreq = 0;
        for (char c : s.toCharArray()) {
            counts[c - 'a']++;
            maxFreq = Math.max(maxFreq, counts[c - 'a']);
        }

        if (maxFreq > (s.length() + 1) / 2) {
            return ""; // Impossible to reorganize
        }

        PriorityQueue<int[]> maxHeap = new PriorityQueue<>((a, b) -> b[1] - a[1]);
        for (int i = 0; i < 26; i++) {
            if (counts[i] > 0) {
                maxHeap.offer(new int[]{i, counts[i]});
            }
        }

        StringBuilder result = new StringBuilder();

        while (maxHeap.size() > 1) {
            int[] first = maxHeap.poll();
            int[] second = maxHeap.poll();

            result.append((char)(first[0] + 'a'));
            result.append((char)(second[0] + 'a'));

            if (--first[1] > 0) maxHeap.offer(first);
            if (--second[1] > 0) maxHeap.offer(second);
        }

        if (!maxHeap.isEmpty()) {
            int[] last = maxHeap.poll();
            if (last[1] > 1) return ""; // Cannot place remaining same chars adjacently
            result.append((char)(last[0] + 'a'));
        }

        return result.toString();
    }
}
```

### **Complexity Analysis:**
- **Time Complexity:** O(N log K), where K is the number of unique characters (max 26).
- **Space Complexity:** O(K).

### **Dry Run:**
- Input: `"aaabbc"`.
- Counts: a=3, b=2, c=1.
- Max frequency: 3 (`a`), which is not more than `(6+1)/2=3.5`, so possible.
- Heap: [(a,3), (b,2), (c,1)].
- Iteration:
  - Pick `a` and `b`: result `"ab"`.
  - Decrement counts: a=2, b=1.
  - Reinsert if > 0.
  - Next: pick `a` and `c`: `"abac"`.
  - Continue until all characters are placed without adjacency.

---

## **Summary Table**

| Approach | Core Idea | Implementation | Time Complexity | Space Complexity | Suitable for |
|------------|--------------|------------------|-------------------|------------------|--------------|
| Brute Force | Generate all permutations | Permutation generation and validation | O(N!) | O(N!) | Small strings, educational | 
| Greedy + Max Heap | Pick most frequent characters, alternate | Priority queue approach | O(N log K) | O(K) | Medium to large strings |
| Max Frequency + Greedy | Check max frequency, place characters greedily | Priority queue with max frequency check | O(N log K) | O(K) | Largest strings, optimal |

---

## **Final Tips:**
- The **greedy with max heap** approach is both intuitive and efficient.
- Always verify if the highest frequency exceeds `(length + 1) / 2`, which makes reorganization impossible.
- Use a max heap to always pick the most frequent characters to avoid adjacency issues.

---

**End of DSA Revision Note for Reorganize String**
