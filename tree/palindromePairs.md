# Palindrome Pairs

The **Palindrome Pairs** problem involves finding all pairs of unique indices `(i, j)` in a list of words such that the concatenation `words[i] + words[j]` forms a palindrome.

---

## 1. Brute Force Approach

### **Core Idea**
Check every pair `(i, j)` to see if `words[i] + words[j]` is a palindrome.

### **Algorithm**
1. Loop through all pairs `(i, j)` where `i != j`.
2. Concatenate `words[i]` and `words[j]`.
3. Check if the concatenated string is a palindrome.
4. If yes, record the pair `(i, j)`.

### **Java Code**
```java
import java.util.*;

public class PalindromePairs {
    public List<List<Integer>> palindromePairs(String[] words) {
        List<List<Integer>> result = new ArrayList<>();
        int n = words.length;

        for (int i = 0; i < n; i++) {
            for (int j = 0; j < n; j++) {
                if (i != j) {
                    String concat = words[i] + words[j];
                    if (isPalindrome(concat)) {
                        result.add(Arrays.asList(i, j));
                    }
                }
            }
        }
        return result;
    }

    private boolean isPalindrome(String s) {
        int left = 0, right = s.length() -1;
        while (left < right) {
            if (s.charAt(left) != s.charAt(right))
                return false;
            left++;
            right--;
        }
        return true;
    }
}
```

### **Complexity Analysis**
- **Time Complexity:** `O(n^2 * k)`  
  For each of the `O(n^2)` pairs, checking palindrome takes `O(k)` where `k` is the maximum length of a word.
- **Space Complexity:** `O(1)` or `O(k)` depending on palindrome check implementation.

### **Dry Run with Example**
Suppose `words = ["bat", "tab", "cat"]`.

| Step | Pairs Checked | Concatenation | Palindrome? | Resulted Pairs |
|-------|----------------|---------------|--------------|----------------|
| `(0,1)` | "bat" + "tab" | "battab"     | Yes          | `(0,1)`        |
| `(1,0)` | "tab" + "bat" | "tabbat"     | Yes          | `(1,0)`        |
| `(0,2)` | "bat" + "cat" | "batcat"     | No           | -              |
| `(2,0)` | "cat" + "bat" | "catbat"     | No           | -              |
| `(1,2)` | "tab" + "cat" | "tabcat"     | No           | -              |
| `(2,1)` | "cat" + "tab" | "cattab"     | No           | -              |

---

## 2. Better Solution (Using Hash Map for Reverse Lookup)

### **Core Idea**
Instead of checking all pairs blindly, leverage a hash map for reverse lookups:
- Store each word and its index in a hash map.
- For each word, consider all possible splits into prefix and suffix.
- Check if prefix or suffix forms a palindrome, and whether the corresponding reverse exists in the map.

### **Algorithm**
1. Build a hash map `word -> index`.
2. For each word:
   - For each possible split position `k`:
     - If `prefix` is palindrome and the reverse of `suffix` exists in the map, then `(index of reverse, current index)` forms a palindrome pair.
     - If `suffix` is palindrome and the reverse of `prefix` exists in the map, then `(current index, index of reverse)` forms a palindrome pair.
3. Handle empty string case separately if present, since concatenating with empty string can produce palindromes.

### **Java Code**
```java
import java.util.*;

public class PalindromePairs {
    public List<List<Integer>> palindromePairs(String[] words) {
        List<List<Integer>> result = new ArrayList<>();
        Map<String, Integer> wordMap = new HashMap<>();

        for (int i = 0; i < words.length; i++) {
            wordMap.put(words[i], i);
        }

        for (int i = 0; i < words.length; i++) {
            String current = words[i];
            int n = current.length();

            for (int k = 0; k <= n; k++) {
                String prefix = current.substring(0, k);
                String suffix = current.substring(k);

                // Check if prefix is palindrome and reverse of suffix exists
                if (isPalindrome(prefix)) {
                    String reversedSuffix = new StringBuilder(suffix).reverse().toString();
                    if (wordMap.containsKey(reversedSuffix) && wordMap.get(reversedSuffix) != i) {
                        result.add(Arrays.asList(wordMap.get(reversedSuffix), i));
                    }
                }

                // Check if suffix is palindrome and reverse of prefix exists
                if (k != n && isPalindrome(suffix)) {
                    String reversedPrefix = new StringBuilder(prefix).reverse().toString();
                    if (wordMap.containsKey(reversedPrefix) && wordMap.get(reversedPrefix) != i) {
                        result.add(Arrays.asList(i, wordMap.get(reversedPrefix)));
                    }
                }
            }
        }

        return result;
    }

    private boolean isPalindrome(String s) {
        int left = 0, right = s.length() - 1;
        while (left < right) {
            if (s.charAt(left) != s.charAt(right))
                return false;
            left++;
            right--;
        }
        return true;
    }
}
```

### **Complexity Analysis**
- **Time Complexity:** `O(n * k^2)`  
  For each of the `n` words, for each split `(k)`, palindrome check takes `O(k)`, and reverse operations are `O(k)`.
- **Space Complexity:** `O(n * k)` for the hash map and auxiliary space.

### **Dry Run with Example**
`words = ["bat", "tab", "cat"]`

| Word | Splits | Palindrome? | Checks | Resulting pairs |
|--------|--------|--------------|---------|-----------------|
| "bat" | "" + "bat" | Yes ("" is palindrome)| reverse("bat")="tab" in map | (tab, bat) -> (1,0) |
|        | "b" + "at" | No | | |
|        | "ba" + "t" | No | | |
|        | "bat" + "" | Yes | reverse("")="", in map? No | |
| "tab" | "" + "tab" | Yes | reverse("tab")="bat" in map | (bat, tab) -> (0,1) |
|        | "t" + "ab" | No | | |
|        | "ta" + "b" | No | | |
|        | "tab" + "" | Yes | reverse("")="", not in map | |
| "cat" | similarly no pairs | | | |

---

## 3. Most Optimal Solution (Using Trie)

### **Core Idea**
Build a **Trie** of all words for efficient prefix/suffix lookups:
- Store words in a Trie, while recording indices of words that form palindromes at each node.
- For each word:
  - Search for its reverse in the Trie.
  - Use stored palindrome info to find pairs efficiently.

### **Algorithm**
1. Construct a Trie of all words.
2. For each word:
   - Traverse the Trie to find all words that can form a palindrome when appended.
   - Use the stored palindrome info at Trie nodes to identify valid pairs.
3. Collect all pairs found during traversal.

### **Implementation (Sketch)**
Due to complexity, code is lengthy. The key idea:
- Insert all words into a Trie, storing indices and palindrome info.
- For each word, search the Trie for matching suffixes and prefixes to find palindrome pairs quickly.

### **Complexity Analysis**
- **Time Complexity:** `O(n * k^2)` in the worst case, but typically faster due to Trie structure.
- **Space Complexity:** `O(n * k)` for Trie storage.

---

# Summary Table

| Approach | Core Idea | Time Complexity | Space Complexity | When to Use |
|------------|--------------|------------------|-------------------|--------------|
| Brute Force | Check all pairs directly | `O(n^2 * k)` | `O(1)` | Small input sizes, for correctness testing |
| Hash Map / Better Solution | Use reverse lookup & palindrome checks at splits | `O(n * k^2)` | `O(n * k)` | Larger input, optimized for performance |
| Trie-Based | Use Trie for prefix/suffix matches | Approximately `O(n * k^2)` | `O(n * k)` | Very large inputs, when fastest lookup needed |

---

## Final Tips for Interviews
- Start with brute force for clarity.
- Improve to hash map approach for better efficiency.
- Understand Trie approach for the most optimal, scalable solution.
- Always analyze input size constraints to choose the correct method.
- Practice palindrome check and string reversal operations efficiently.

---

**Happy coding!**
