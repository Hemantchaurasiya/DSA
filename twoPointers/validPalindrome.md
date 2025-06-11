# Valid Palindrome

This problem asks us to determine if a given string is a **valid palindrome** considering only alphanumeric characters and ignoring cases.

---

## 1. Brute Force

### **Core Idea:**
Clean the string by removing non-alphanumeric characters, convert to lowercase, then check if it reads the same forwards and backwards.

### **Algorithm:**
1. Normalize the string:
   - Remove all non-alphanumeric characters.
   - Convert all characters to lowercase.
2. Check if the normalized string is a palindrome:
   - Use two pointers: one at the start, one at the end.
   - Move inward, comparing characters.
   - If all pairs match, return `true`; otherwise, `false`.

### **Java Code:**
```java
public boolean isPalindromeBruteForce(String s) {
    StringBuilder filtered = new StringBuilder();
    for (char c : s.toCharArray()) {
        if (Character.isLetterOrDigit(c)) {
            filtered.append(Character.toLowerCase(c));
        }
    }
    String str = filtered.toString();
    int left = 0;
    int right = str.length() - 1;
    while (left < right) {
        if (str.charAt(left) != str.charAt(right)) {
            return false;
        }
        left++;
        right--;
    }
    return true;
}
```

### **Complexity Analysis:**
- **Time Complexity:** `O(n)` — where `n` is the length of the string, since we process each character once.
- **Space Complexity:** `O(n)` — for the filtered string stored in the StringBuilder.

### **Dry Run:**
- Input: `"A man, a plan, a canal: Panama"`
- Filtered: `"amanaplanacanalpanama"`
- Check:
  - Compare `a` and `a` → match
  - Compare `m` and `m` → match
  - Continue inward until all pairs match → return `true`

---

## 2. Two-Pointer Approach (Most Optimal)

### **Core Idea:**
Use two pointers starting from both ends of the original string:
- Skip non-alphanumeric characters.
- Convert characters to lowercase on-the-fly.
- Compare characters at pointers.
- Move inward until pointers meet or cross.

This approach avoids creating a new string, making it more space-efficient.

### **Algorithm:**
1. Initialize `left=0`, `right=s.length()-1`.
2. While `left < right`:
   - Increment `left` if `s.charAt(left)` is not alphanumeric.
   - Decrement `right` if `s.charAt(right)` is not alphanumeric.
   - Compare lowercase characters at `left` and `right`.
   - If mismatch, return `false`.
   - Else, move inward (`left++`, `right--`).
3. If all comparisons pass, return `true`.

### **Java Code:**
```java
public boolean isPalindromeTwoPointers(String s) {
    int left = 0;
    int right = s.length() - 1;
    while (left < right) {
        // Skip non-alphanumeric characters from the start
        while (left < right && !Character.isLetterOrDigit(s.charAt(left))) {
            left++;
        }
        // Skip non-alphanumeric characters from the end
        while (left < right && !Character.isLetterOrDigit(s.charAt(right))) {
            right--;
        }
        // Compare characters in lowercase
        if (Character.toLowerCase(s.charAt(left)) != Character.toLowerCase(s.charAt(right))) {
            return false;
        }
        left++;
        right--;
    }
    return true;
}
```

### **Complexity Analysis:**
- **Time Complexity:** `O(n)` — each character is visited at most once.
- **Space Complexity:** `O(1)` — no extra space used apart from variables.

### **Dry Run:**
- Input: `"A man, a plan, a canal: Panama"`
- Start: `left=0`, `right=29`
- Skip non-alphanumeric:
  - `left` points to `'A'`, `right` points to `'a'`
- Compare `'A'.toLowerCase() = 'a'` and `'a'` → match
- Move inward: `left=1`, `right=28`
- Repeat skipping non-alphanumeric and comparing:
  - Next valid characters: `' '` skipped, `' '` skipped
  - `'m'` vs `'m'` → match
  - Continue inward until all pairs match or mismatch is found.

---

# Summary

| Approach | Core Idea | Implementation | Time Complexity | Space Complexity |
| --- | --- | --- | --- | --- |
| **Brute Force** | Clean string, check palindrome | Filter + compare | `O(n)` | `O(n)` |
| **Two Pointer (Optimal)** | On-the-fly skipping non-alphanumerics, compare | Two pointers without extra space | `O(n)` | `O(1)` |

---

This guide provides multiple approaches, their intuition, implementation, and efficiency analysis, making it a comprehensive resource for quick revision and interview prep.
