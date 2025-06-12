# Backspace String Compare

The **Backspace String Compare** problem requires determining if two strings are equal after processing backspace characters (`'#'`). Each `'#'` deletes the character immediately before it if any.

---

## 1. Brute Force Approach

### **Approach Name:**  
Simulation Using String Construction

### **Core Idea:**  
Process both strings by simulating the effect of backspaces to generate the final strings, then compare these final results.

### **Algorithm:**  
1. Define a helper function to process a string:
   - Use a `StringBuilder` or stack.
   - Iterate over each character:
     - If the character is not `'#'`, append it.
     - If it is `'#'`, remove the last character if exists.
2. Process both input strings using this helper.
3. Compare the processed strings for equality.

### **Java Code:**
```java
public class BackspaceStringCompare {
    private String process(String s) {
        StringBuilder sb = new StringBuilder();
        for (char c : s.toCharArray()) {
            if (c != '#') {
                sb.append(c);
            } else if (sb.length() > 0) {
                sb.deleteCharAt(sb.length() - 1);
            }
        }
        return sb.toString();
    }

    public boolean backspaceCompare(String s, String t) {
        return process(s).equals(process(t));
    }
}
```

### **Complexity Analysis:**  
- **Time Complexity:** **O(n + m)**, where `n` and `m` are lengths of the two strings, as each character is processed once.
- **Space Complexity:** **O(n + m)**, for the processed strings stored in StringBuilder.

### **Dry Run:**  
Input: `s = "ab#c"`, `t = "ad#c"`.

- Process `s`:
  - `'a'` → `a`
  - `'b'` → `ab`
  - `'#'` → remove `'b'` → `a`
  - `'c'` → `ac`
- Process `t`:
  - `'a'` → `a`
  - `'d'` → `ad`
  - `'#'` → remove `'d'` → `a`
  - `'c'` → `ac`
- Both processed strings: `"ac"` → **equal**, return `true`.

---

## 2. Better Approach: Two Pointer Technique (Optimized Solution)

### **Approach Name:**  
Reverse Iteration with Two Pointers

### **Core Idea:**  
Instead of explicitly constructing the final strings, traverse both strings from the end, skipping characters affected by backspaces on-the-fly, and compare characters directly.

### **Algorithm:**  
1. Initialize two pointers: `i` for `s`, `j` for `t`, starting from the end.
2. For each string:
   - Count the number of backspaces encountered.
   - Move the pointer backwards, skipping characters when `backspace` count > 0.
   - Stop when a valid character (not erased by backspace) is found or the start is reached.
3. Compare the current characters:
   - If both are valid characters, compare:
     - If different, return `false`.
   - If one is valid and the other is not, return `false`.
4. Move both pointers backward and repeat until both strings are exhausted.
5. If both pointers reach the beginning simultaneously, return `true`; else, `false`.

### **Java Code:**
```java
public class BackspaceStringCompare {
    private boolean isValidChar(String s, int index) {
        int backspaceCount = 0;
        while (index >= 0) {
            if (s.charAt(index) == '#') {
                backspaceCount++;
                index--;
            } else if (backspaceCount > 0) {
                backspaceCount--;
                index--;
            } else {
                break;
            }
        }
        return index >= 0 && s.charAt(index) != '#';
    }

    public boolean backspaceCompare(String s, String t) {
        int i = s.length() - 1;
        int j = t.length() - 1;

        while (i >= 0 || j >= 0) {
            // Find next valid char in s
            while (i >= 0 && (s.charAt(i) == '#' || (i > 0 && s.charAt(i - 1) == '#'))) {
                if (s.charAt(i) == '#') {
                    i -= 2; // Skip the character before '#' and the '#'
                } else {
                    i--;
                }
            }
            // Find next valid char in t
            while (j >= 0 && (t.charAt(j) == '#' || (j > 0 && t.charAt(j - 1) == '#'))) {
                if (t.charAt(j) == '#') {
                    j -= 2;
                } else {
                    j--;
                }
            }

            // Check if both characters are valid and compare
            if (i >= 0 && j >= 0) {
                if (s.charAt(i) != t.charAt(j))
                    return false;
            } else {
                if (i >= 0 || j >= 0)
                    return false;
            }
            i--;
            j--;
        }
        return true;
    }
}
```

(Note: The above code snippet is a conceptual outline. The actual implementation should carefully handle skipping characters with a robust approach, as shown below.)

### **Refined Implementation:**
```java
public class BackspaceStringCompare {
    public boolean backspaceCompare(String s, String t) {
        int i = s.length() - 1, j = t.length() - 1;
        while (i >= 0 || j >= 0) {
            int skipS = 0, skipT = 0;
            // Find next valid char in s
            while (i >= 0) {
                if (s.charAt(i) == '#') {
                    skipS++;
                    i--;
                } else if (skipS > 0) {
                    skipS--;
                    i--;
                } else {
                    break;
                }
            }
            // Find next valid char in t
            while (j >= 0) {
                if (t.charAt(j) == '#') {
                    skipT++;
                    j--;
                } else if (skipT > 0) {
                    skipT--;
                    j--;
                } else {
                    break;
                }
            }
            // Compare characters
            if (i >= 0 && j >= 0) {
                if (s.charAt(i) != t.charAt(j))
                    return false;
            } else {
                if (i >= 0 || j >= 0)
                    return false;
            }
            i--;
            j--;
        }
        return true;
    }
}
```

### **Complexity Analysis:**  
- **Time Complexity:** **O(n + m)**, as each character is processed at most once from the end.
- **Space Complexity:** **O(1)**, with only variables for pointers and counters.

### **Dry Run:**  
Input: `s = "ab#c"`, `t = "ad#c"`.

- Start from end:
  - For `s`:
    - `i=3`, `'c'` → valid.
  - For `t`:
    - `j=3`, `'c'` → valid.
- Compare `'c'` and `'c'` → match.
- Move `i=2`, `j=2`.
- For `s`:
  - `' #'` at position 2:
    - skip 1 character `'b'` (i=1).
  - Now at `'a'` at position 0.
- For `t`:
  - `' #'` at position 2:
    - skip `'d'` (j=1).
  - Now at `'a'` at position 0.
- Compare `'a'` and `'a'` → match.
- Move `i=-1`, `j=-1`.
- Both pointers at start → **true**.

---

## **Summary & Key Points:**

| Approach                     | Time Complexity | Space Complexity | Description                                                        |
|------------------------------|------------------|------------------|--------------------------------------------------------------------|
| String Construction Method   | O(n + m)        | O(n + m)        | Process both strings to build final versions, then compare.       |
| Two Pointer Reverse Traversal | O(n + m)        | O(1)            | Traverse from the end, skipping characters affected by backspaces. |

---

## **Final Tips for Interview:**
- The two-pointer approach is more optimal due to constant space usage.
- Carefully handle skipping characters with counters.
- Validate edge cases: empty strings, all backspaces, no backspaces, etc.
- Practice both methods to understand their trade-offs.

Feel free to implement and test with various input cases for mastery!
