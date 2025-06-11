# Valid Palindrome II

The **Valid Palindrome II** problem asks whether a given string can become a palindrome after removing **at most one character**.

---

## 1. Brute Force Approach

### **Approach Name:**  
**Brute Force (Naive Check for Each Character Removal)**

### **Core Idea:**  
Try removing each character one by one and check if the resulting string is a palindrome.

### **Algorithm:**  
1. For each index `i` in the string:
   - Remove the character at index `i`.
   - Check if the remaining string is a palindrome.
2. If any removal results in a palindrome, return `true`.
3. Otherwise, return `false`.

### **Java Code:**  
```java
public boolean validPalindromeBruteForce(String s) {
    int n = s.length();
    for (int i = 0; i < n; i++) {
        StringBuilder sb = new StringBuilder(s);
        sb.deleteCharAt(i);
        if (isPalindrome(sb.toString())) {
            return true;
        }
    }
    return isPalindrome(s);
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
```

### **Complexity Analysis:**  
- **Time Complexity:** `O(n^3)` — For each of the `n` characters, checking if the string is a palindrome takes `O(n)` time.
- **Space Complexity:** `O(n)` for creating substrings or `StringBuilder` objects.

### **Dry Run:**  
- Input: `"abca"`  
- Remove `'a'` at index 0: `"bca"` → not a palindrome  
- Remove `'b'` at index 1: `"aca"` → palindrome → return `true`

---

## 2. Two-Pointer Approach (Most Optimal)

### **Approach Name:**  
**Two-Pointer with At Most One Mismatch Allowed**

### **Core Idea:**  
Use two pointers to traverse from both ends:
- If characters match, move inward.
- If characters don't match, try skipping either the left or right character (but only once).

### **Algorithm:**  
1. Initialize two pointers: `left=0`, `right=n-1`.
2. While `left < right`:
   - If `s.charAt(left) == s.charAt(right)`, move `left++` and `right--`.
   - Else:
     - Check if the substring `s[left+1 ... right]` is a palindrome.
     - Or check if the substring `s[left ... right-1]` is a palindrome.
     - If either is true, return `true`.
     - Else, return `false`.
3. If the entire string is traversed without mismatch or with a valid skip, return `true`.

### **Java Code:**  
```java
public boolean validPalindromeTwoPointer(String s) {
    int left = 0, right = s.length() - 1;
    while (left < right) {
        if (s.charAt(left) == s.charAt(right)) {
            left++;
            right--;
        } else {
            // Try skipping either left or right character
            return isPalindromeRange(s, left + 1, right) || isPalindromeRange(s, left, right - 1);
        }
    }
    return true;
}

private boolean isPalindromeRange(String s, int start, int end) {
    while (start < end) {
        if (s.charAt(start) != s.charAt(end))
            return false;
        start++;
        end--;
    }
    return true;
}
```

### **Complexity Analysis:**  
- **Time Complexity:** `O(n)` — At most two palindrome checks are performed, each in `O(n)`.
- **Space Complexity:** `O(1)` — No extra space used apart from variables.

### **Dry Run:**  
- Input: `"abca"`  
- `left=0`, `right=3`:
  - `'a' == 'a'`, move inward: `left=1`, `right=2`.
  - `'b' != 'c'`, check:
    - `isPalindromeRange(s, 2, 2)` → `'c'` → true
    - or `isPalindromeRange(s, 1, 1)` → `'b'` → true
  - Either is true, so return `true`.

---

## **Summary**

| Approach | Core Idea | Implementation | Time Complexity | Space Complexity |
| --- | --- | --- | --- | --- |
| **Brute Force** | Remove each character and check palindrome | Loop + check each substring | `O(n^3)` | `O(n)` |
| **Two-Pointer (Optimal)** | Use two pointers, allow one mismatch | Two pointers + conditional check | `O(n)` | `O(1)` |

---

This guide provides a clear understanding of different solutions for **Valid Palindrome II**, emphasizing the most efficient method for interview success and quick revision.
