# Maximum Number of Vowels in a Substring of Given Length

---

## Problem Statement
Given a string `s` and an integer `k`, find the maximum number of vowels in any substring of `s` with length `k`.

---

## Approach 1: Brute Force

### Core Idea
Check every possible substring of length `k` and count the vowels in each. The maximum count found is the answer.

### Algorithm
1. Loop through the string from index `0` to `s.length() - k`.
2. For each starting index, extract the substring of length `k`.
3. Count the vowels in this substring.
4. Keep track of the maximum vowel count encountered.
5. Return the maximum count after checking all substrings.

### Java Code
```java
public int maxVowelsBruteForce(String s, int k) {
    int maxVowels = 0;
    for (int i = 0; i <= s.length() - k; i++) {
        int count = 0;
        for (int j = i; j < i + k; j++) {
            if (isVowel(s.charAt(j))) {
                count++;
            }
        }
        maxVowels = Math.max(maxVowels, count);
    }
    return maxVowels;
}

private boolean isVowel(char c) {
    return "aeiou".indexOf(c) != -1;
}
```

### Complexity Analysis
- **Time Complexity:** *O(n * k)*  
  For each of the `n - k + 1` substrings, counting vowels takes `O(k)` time.
- **Space Complexity:** *O(1)*  
  Only a few variables used, no extra space proportional to input size.

### Dry Run
- Input: `s = "abciiidef"`, `k = 3`
- Step-by-step:
  - Substrings:
    - "abc" → vowels: 1
    - "bci" → vowels: 1
    - "cii" → vowels: 2
    - "iii" → vowels: 3
    - "iid" → vowels: 2
    - "ide" → vowels: 2
    - "def" → vowels: 0
  - Maximum vowels: **3**

---

## Approach 2: Sliding Window (Better Solution)

### Core Idea
Use a sliding window of size `k` to traverse the string, updating the vowel count as the window moves, avoiding recounting vowels in each new substring.

### Algorithm
1. Initialize `vowelCount` for the first window (`0` to `k - 1`).
2. Set `maxVowels` to `vowelCount`.
3. Loop from index `k` to `s.length() - 1`:
    - Remove the contribution of the outgoing character (`i - k`)
    - Add the contribution of the incoming character (`i`)
    - Update `maxVowels` if `vowelCount` is higher.
4. Return `maxVowels`.

### Java Code
```java
public int maxVowelsSlidingWindow(String s, int k) {
    int maxVowels = 0, vowelCount = 0;
    
    // Initialize the first window
    for (int i = 0; i < k; i++) {
        if (isVowel(s.charAt(i))) {
            vowelCount++;
        }
    }
    maxVowels = vowelCount;

    // Slide the window
    for (int i = k; i < s.length(); i++) {
        // Remove contribution of the outgoing character
        if (isVowel(s.charAt(i - k))) {
            vowelCount--;
        }
        // Add contribution of the incoming character
        if (isVowel(s.charAt(i))) {
            vowelCount++;
        }
        maxVowels = Math.max(maxVowels, vowelCount);
    }
    return maxVowels;
}
```

### Complexity Analysis
- **Time Complexity:** *O(n)*  
  Each character is visited at most twice (once when added, once when removed).
- **Space Complexity:** *O(1)*  
  Uses only a few variables.

### Dry Run
- Input: `s = "abciiidef"`, `k = 3`
- Initial window (`"abc"`): vowels = 1
- Move window:
  - Remove 'a': vowels still 1
  - Add 'i': vowels = 2
- Next:
  - Remove 'b': vowels = 2
  - Add 'i': vowels = 3 (max)
- Next:
  - Remove 'c': vowels = 3
  - Add 'i': vowels = 4 (but in this string, only 3 vowels in total, so max remains 3)
- Continue until end
- Final maximum vowels in any substring of length 3: **3**

---

## Approach 3: Optimized Sliding Window with Character Checks (Most Efficient)

This is essentially the same as Approach 2 but emphasizes the importance of efficient vowel check using a set for constant-time checks.

### Core Idea
Use a HashSet for vowels for O(1) checks, implement sliding window for optimal traversal.

### Algorithm
Same as Approach 2 but with a vowel set:
1. Initialize a `HashSet<Character>` for vowels.
2. Use sliding window to update `vowelCount`.
3. Return maximum.

### Java Code
```java
import java.util.HashSet;
import java.util.Set;

public int maxVowelsOptimized(String s, int k) {
    Set<Character> vowels = new HashSet<>();
    vowels.add('a');
    vowels.add('e');
    vowels.add('i');
    vowels.add('o');
    vowels.add('u');

    int maxVowels = 0, vowelCount = 0;
    // Initialize first window
    for (int i = 0; i < k; i++) {
        if (vowels.contains(s.charAt(i))) {
            vowelCount++;
        }
    }
    maxVowels = vowelCount;

    // Slide the window
    for (int i = k; i < s.length(); i++) {
        if (vowels.contains(s.charAt(i - k))) {
            vowelCount--;
        }
        if (vowels.contains(s.charAt(i))) {
            vowelCount++;
        }
        maxVowels = Math.max(maxVowels, vowelCount);
    }
    return maxVowels;
}
```

### Complexity Analysis
- **Time Complexity:** *O(n)*, with O(1) vowel checks due to HashSet.
- **Space Complexity:** *O(1)*, since the set of vowels is constant.

---

## Summary Table

| Approach | Core Idea | Time Complexity | Space Complexity | Key Points |
|------------|--------------|-------------------|---------------------|--------------|
| Brute Force | Check all substrings, count vowels each time | O(n * k) | O(1) | Simple but inefficient for large strings |
| Sliding Window | Maintain count as window slides, update incrementally | O(n) | O(1) | Efficient, avoids recounting |
| Optimized Sliding Window | Use HashSet for vowel checks, same as above | O(n) | O(1) | Fastest, best for large inputs |

---

## Final Notes
- Always prefer the sliding window approach for such substring problems to achieve linear time complexity.
- Use a set for quick vowel lookup.
- Carefully handle edge cases: empty string, `k` greater than string length, strings with no vowels, etc.

---

**Happy Revising!**
