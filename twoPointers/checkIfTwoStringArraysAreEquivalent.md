# Check If Two String Arrays Are Equivalent

This problem involves verifying whether two string arrays, when concatenated, form identical strings.

---

## 1. Brute Force Approach

### **Approach Name:**  
Concatenate and Compare

### **Core Idea:**  
Convert both string arrays into complete strings by concatenation, then compare the resulting strings for equality.

### **Algorithm:**  
1. Initialize two empty strings: `str1` and `str2`.
2. Iterate over each string in the first array:
   - Append each string to `str1`.
3. Repeat for the second array, building `str2`.
4. Compare `str1` and `str2` for equality.
5. Return the comparison result.

### **Java Code:**
```java
public class StringArrayComparison {
    public boolean arrayStringsAreEqual(String[] word1, String[] word2) {
        StringBuilder sb1 = new StringBuilder();
        StringBuilder sb2 = new StringBuilder();

        for (String s : word1) {
            sb1.append(s);
        }
        for (String s : word2) {
            sb2.append(s);
        }
        return sb1.toString().equals(sb2.toString());
    }
}
```

### **Complexity Analysis:**  
- **Time Complexity:** **O(n + m)**, where `n` and `m` are the total number of characters in the two arrays, since concatenation involves processing all characters.
- **Space Complexity:** **O(n + m)**, for storing the concatenated strings.

### **Dry Run:**  
Suppose:
- `word1 = ["ab", "c"]`
- `word2 = ["a", "bc"]`

Steps:
- `sb1` builds `"abc"`.
- `sb2` builds `"abc"`.
- Compare `"abc"` and `"abc"` → **true**.

---

## 2. Optimal Approach: Two Pointers Technique

### **Approach Name:**  
Pointer-Based Comparison Without Concatenation

### **Core Idea:**  
Use two pointers to traverse both arrays without concatenating, comparing characters on-the-fly. This method avoids extra space for large strings.

### **Algorithm:**  
1. Initialize two pointers:
   - `i` for `word1` array, starting at 0.
   - `j` for `word2` array, starting at 0.
2. Maintain two internal indices:
   - `k1` for position within `word1[i]`.
   - `k2` for position within `word2[j]`.
3. Loop until both arrays are fully traversed:
   - Compare current characters at `word1[i][k1]` and `word2[j][k2]`.
   - If characters mismatch, return `false`.
   - Move `k1` and `k2` forward.
   - If `k1` reaches the end of `word1[i]`, move `i` to next string.
   - If `k2` reaches the end of `word2[j]`, move `j` to next string.
4. After traversing, check if both pointers are at the end of their respective arrays.
5. Return `true` if both arrays are fully traversed with matching characters; else, `false`.

### **Java Code:**
```java
public class StringArrayComparison {
    public boolean arrayStringsAreEqual(String[] word1, String[] word2) {
        int i = 0, j = 0;
        int k1 = 0, k2 = 0;
        while (i < word1.length && j < word2.length) {
            if (word1[i].charAt(k1) != word2[j].charAt(k2))
                return false;

            k1++;
            k2++;

            if (k1 == word1[i].length()) {
                i++;
                k1 = 0;
            }
            if (k2 == word2[j].length()) {
                j++;
                k2 = 0;
            }
        }

        // Check if both reached the end
        return i == word1.length && j == word2.length;
    }
}
```

### **Complexity Analysis:**  
- **Time Complexity:** **O(n + m)**, processing each character exactly once.
- **Space Complexity:** **O(1)**, only a few variables for indices.

### **Dry Run:**  
Suppose:
- `word1 = ["ab", "c"]`
- `word2 = ["a", "bc"]`

Steps:
- `i=0, j=0, k1=0, k2=0`:
  - Compare `'a'` and `'a'` → match.
  - Increment `k1=1`, `k2=1`.
- Next:
  - Compare `'b'` and `'b'` → match.
  - Increment `k1=2` (end of `"ab"`), `k2=2`:
    - Move `i=1`, `j=1`, reset `k1=0`, `k2=0`.
- Next:
  - Compare `'c'` and `'c'` → match.
  - Increment `k1=1`, `k2=1`.
  - Both reach end of strings, return `true`.

---

## **Summary & Key Points:**

| Approach                        | Time Complexity | Space Complexity | Description                                             |
|--------------------------------|------------------|------------------|---------------------------------------------------------|
| Concatenate and Compare        | O(n + m)        | O(n + m)        | Concatenate both arrays into strings, then compare.   |
| Two Pointers (On-the-fly compare)| O(n + m)        | O(1)            | Traverse both arrays simultaneously, compare characters directly without extra space. |

---

## **Final Tips for Interview:**
- The two-pointer approach is more space-efficient and scalable.
- Carefully handle indices when traversing within each string.
- Always verify if both arrays are exhausted at the end.
- Practice with different test cases, including empty arrays and arrays with multiple small strings.

Master both methods to be well-prepared for variations of this problem!
