# Maximum Length of a Concatenated String With Unique Characters

## Problem Overview
Given an array of strings, **arr**, find the maximum length of a string that can be formed by concatenating some of the strings such that the resulting string contains **only unique characters**.

---

## Approach 1: Brute Force

### Core Idea:
Generate all possible combinations (subsets) of the given strings and check which combination results in a string with all unique characters. Keep track of the maximum length among valid combinations.

### Algorithm:
1. Generate all subsets of the input array using backtracking.
2. For each subset, concatenate the strings.
3. Check if the concatenated string contains all unique characters.
4. Update the maximum length if the current concatenated string is valid and longer than the previous maximum.

### Recursion Tree Diagram:
```
Start with empty subset:
- Include arr[0]
  - Check validity
  - Recurse with next index
- Exclude arr[0]
  - Recurse with next index
Repeat for all elements.
```

### Java Code:
```java
public class Solution {
    int maxLen = 0;
    
    public int maxLength(List<String> arr) {
        backtrack(arr, 0, "");
        return maxLen;
    }
    
    private void backtrack(List<String> arr, int index, String current) {
        if (!isUnique(current)) {
            return;
        }
        maxLen = Math.max(maxLen, current.length());
        for (int i = index; i < arr.size(); i++) {
            backtrack(arr, i + 1, current + arr.get(i));
        }
    }
    
    private boolean isUnique(String s) {
        int[] count = new int[26];
        for (char c : s.toCharArray()) {
            if (count[c - 'a'] > 0) {
                return false;
            }
            count[c - 'a']++;
        }
        return true;
    }
}
```

### Complexity Analysis:
- **Time Complexity:** O(2^n * k), where n is the number of strings, and k is the maximum length of the concatenated string in each subset (to check uniqueness).  
- **Space Complexity:** O(n) for recursion stack and auxiliary data structures.

### Dry Run Example:
- Input: `["ab", "cd", "a"]`
- Subsets:
  - "" (valid, length 0)
  - "ab" (valid, length 2)
  - "ab" + "cd" = "abcd" (valid, length 4)
  - "ab" + "a" = "aba" (invalid, contains duplicate 'a')
  - "cd" (valid, length 2)
  - "a" (valid, length 1)
- Max Length: 4 ("abcd")

---

## Approach 2: Bitmask Optimization

### Core Idea:
Use bitmasking to efficiently check for unique characters and combine strings without repeated characters.

### Algorithm:
1. Convert each string into a bitmask representing its characters.
2. For each subset, combine bitmasks if the strings have no overlapping characters (bitwise AND == 0).
3. Track the maximum number of set bits (characters) in valid combined masks.

### Steps:
- Preprocess each string:
  - Check if the string has duplicates within itself; if yes, ignore.
  - Convert to bitmask.
- Use DFS (or backtracking) to combine masks:
  - For each string's mask, if it doesn't conflict with the current mask, include it.
  - Recursively proceed to find maximum length.

### Java Code:
```java
public class Solution {
    int maxLen = 0;
    
    public int maxLength(List<String> arr) {
        List<Integer> masks = new ArrayList<>();
        for (String s : arr) {
            int mask = 0;
            boolean isValid = true;
            for (char c : s.toCharArray()) {
                int bit = c - 'a';
                if ((mask & (1 << bit)) != 0) {
                    isValid = false; // duplicate character within string
                    break;
                }
                mask |= (1 << bit);
            }
            if (isValid) {
                masks.add(mask);
            }
        }
        backtrack(masks, 0, 0);
        return maxLen;
    }
    
    private void backtrack(List<Integer> masks, int index, int currentMask) {
        maxLen = Math.max(maxLen, Integer.bitCount(currentMask));
        for (int i = index; i < masks.size(); i++) {
            if ((currentMask & masks.get(i)) == 0) {
                backtrack(masks, i + 1, currentMask | masks.get(i));
            }
        }
    }
}
```

### Complexity Analysis:
- **Time Complexity:** O(2^n * L), where n is the number of strings, L is the maximum length of characters in strings (for bitmask conversions).
- **Space Complexity:** O(n) for storing masks and recursion stack.

### Dry Run Example:
- Input: `["ab", "cd", "a"]`
- Masks:
  - "ab" -> 00000011 (binary)
  - "cd" -> 00001100
  - "a" -> 00000001 (but skipped because 'a' is duplicate within itself if any)
- Combine masks:
  - Start with 0
  - Include "ab" (mask 3), max length = 2
  - Include "cd" (mask 12), no overlap, max length = 4
  - "a" is ignored because it overlaps with "ab"
- Result: 4

---

## Approach 3: Most Optimal Solution (Bitmask + Pruning)

### Core Idea:
Combine bitmasking with early pruning:
- Skip strings with internal duplicates.
- During recursion, prune branches where the current combination cannot beat the maximum length found so far.

### Algorithm:
Same as Approach 2, with added pruning:
- When the current maximum length is already larger than the remaining possibilities, prune early.

### Java Code:
(See Approach 2; pruning can be added by comparing remaining strings and current max length.)

---

## Summary:
| Approach | Core Idea | Key Points | Time Complexity | Space Complexity |
|------------|--------------|--------------|------------------|------------------|
| Brute Force | Generate all subsets | Checks all combinations, naive | O(2^n * k) | O(n) |
| Bitmask Optimization | Use bitmask to check conflicts efficiently | Fast validity check, suitable for small alphabet | O(2^n * L) | O(n) |
| Pruned Bitmask | Add pruning for efficiency | Further reduces search space | Similar to Approach 2 with pruning | O(n) |

---

## Final Tips for Interviews:
- Understand the problem constraints to choose the best approach.
- Bitmasking is highly efficient for character-based problems.
- Always preprocess to eliminate invalid strings early.
- Use recursion with pruning to optimize backtracking solutions.

---

**Happy Coding!**
