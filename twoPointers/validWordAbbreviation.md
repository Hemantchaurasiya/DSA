# Valid Word Abbreviation

The **Valid Word Abbreviation** problem checks whether a given string `abbr` is a valid abbreviation of another string `word` based on specific rules.

---

## 1. Brute Force Approach

### **Approach Name:**  
Naive Validation by Sequential Parsing

### **Core Idea:**  
Simulate the process of parsing both `word` and `abbr` from left to right, verifying that `abbr` correctly abbreviates `word`.

### **Algorithm:**  
1. Initialize two pointers: `i` for `word`, `j` for `abbr`.
2. Loop until either pointer reaches the end of its string:
   - If `abbr[j]` is a letter:
     - Check if it matches `word[i]`.
     - If yes, move both `i` and `j`.
     - Otherwise, return false.
   - If `abbr[j]` is a digit:
     - Parse the number starting at `j`.
     - Convert the substring of digits into an integer `count`.
     - Increment `i` by `count` (skip `count` characters in `word`).
     - Move `j` past the number.
3. After the loop, if both `i` and `j` reach the end of their strings, return true; else, false.

### **Java Code:**
```java
public class ValidWordAbbreviation {
    public boolean validWordAbbreviation(String word, String abbr) {
        int i = 0, j = 0;
        while (i < word.length() && j < abbr.length()) {
            if (Character.isLetter(abbr.charAt(j))) {
                if (word.charAt(i) != abbr.charAt(j)) {
                    return false;
                }
                i++;
                j++;
            } else {
                if (abbr.charAt(j) == '0') {
                    // Leading zeros are invalid
                    return false;
                }
                int num = 0;
                while (j < abbr.length() && Character.isDigit(abbr.charAt(j))) {
                    num = num * 10 + (abbr.charAt(j) - '0');
                    j++;
                }
                i += num;
            }
        }
        return i == word.length() && j == abbr.length();
    }
}
```

### **Complexity Analysis:**  
- **Time Complexity:** **O(n + m)**, where `n` is length of `word`, `m` is length of `abbr`, as we process each character at most once.  
- **Space Complexity:** **O(1)**, only variables for indices and temporary number.

### **Dry Run:**  
Input: `word = "international"`, `abbr = "i12l"`.

- `i=0`, `j=0`:
  - `abbr[0]='i'` matches `word[0]='i'`, move `i=1`, `j=1`.
- `abbr[1]='1'` is digit:
  - parse number: `12`.
  - `i=1+12=13`, `j` moves past `12`.
- `abbr[3]='l'` matches `word[13]='l'`, move `i=14`, `j=4`.
- End of strings reached, both pointers at end → **true**.

---

## 2. Improved Approach: Same as above with Validation Checks

### **Core Idea:**  
Same as brute force but with additional validation for leading zeros in numbers.

### **Algorithm:**  
- Same as above, but explicitly check that numbers do not start with '0' unless the number itself is zero.

### **Java Code:**  
(see above code snippet)

### **Complexity Analysis:**  
Same as above: **O(n + m)** time, **O(1)** space.

---

## 3. Most Optimal Solution: Same as above with Strict Validation

### **Core Idea:**  
The previous approach is already optimal in time and space since it processes both strings once efficiently.

### **Summary:**  
- Use a pointer-based simulation.
- Validate number parsing, especially leading zeros.
- Confirm both strings are fully processed at the end.

---

## **Key Points & Tips:**
- Always check for leading zeros when parsing numbers.
- Both strings should be exhausted simultaneously for a valid abbreviation.
- The approach is linear, processing characters once.

---

## **Final Notes:**
- The main challenge lies in correctly parsing numbers and matching characters.
- Handling edge cases such as leading zeros, empty strings, and mismatched lengths is crucial.
- This problem tests string parsing, pointer manipulation, and validation logic, fundamental for many string processing problems.

Practice with various test cases to strengthen your understanding!
