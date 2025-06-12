# Number of Substrings Containing All Three Characters

This guide offers a detailed overview of approaches to solve the problem of counting substrings that contain all three characters `'a'`, `'b'`, and `'c'`. It progresses from brute-force to optimal solutions, structured for quick revision and interview prep.

---

## Problem Summary
Given a string `s` consisting of characters `'a'`, `'b'`, and `'c'`, count the number of substrings that contain **at least one** `'a'`, **at least one** `'b'`, and **at least one** `'c'`.

---

## Approach 1: Brute Force

### **Core Idea:**
Check every possible substring, verify if it contains all three characters, and count it if it does.

---

### **Algorithm:**
1. Generate all substrings of the string `s`.
2. For each substring:
   - Count the occurrences of `'a'`, `'b'`, and `'c'`.
   - If all three are present at least once, increment the count.
3. Return the total count.

---

### **Java Code:**
```java
public int numberOfSubstrings(String s) {
    int count = 0;
    int n = s.length();

    for (int i = 0; i < n; i++) {
        int countA = 0, countB = 0, countC = 0;
        for (int j = i; j < n; j++) {
            char ch = s.charAt(j);
            if (ch == 'a') countA++;
            else if (ch == 'b') countB++;
            else if (ch == 'c') countC++;

            if (countA > 0 && countB > 0 && countC > 0) {
                count++;
            }
        }
    }

    return count;
}
```

---

### **Complexity Analysis:**

- **Time Complexity:**  
  **O(n^2)** — For each starting point, we scan the remaining string, which results in quadratic time.

- **Space Complexity:**  
  **O(1)** — Only counters are used.

---

### **Dry Run:**

**Input:**
```plaintext
s = "abc"
```

| i | j | Substring | countA | countB | countC | Contains all? | Count | Explanation |
|---|---|--------------|---------|---------|---------|--------------|--------|--------------|
| 0 | 0 | "a"          | 1       | 0       | 0       | no           | 0      | |
| 0 | 1 | "ab"         | 1       | 1       | 0       | no           | 0      | |
| 0 | 2 | "abc"        | 1       | 1       | 1       | yes          | 1      | |
| 1 | 1 | "b"          | 0       | 1       | 0       | no           | 0      | |
| 1 | 2 | "bc"         | 0       | 1       | 1       | no           | 0      | |
| 2 | 2 | "c"          | 0       | 0       | 1       | no           | 0      | |

Total = 1.

---

## Approach 2: Sliding Window with Two Pointers

### **Core Idea:**
Use two pointers to dynamically adjust the window to contain all three characters, counting valid substrings ending at each position.

### **Algorithm:**
1. Initialize two pointers: `left` and `right` at 0.
2. Maintain counters for `'a'`, `'b'`, and `'c'` within the window.
3. Expand `right`:
   - Update character counts.
   - While the window contains all three characters:
     - Count the number of valid substrings ending at `right` (which is `left + 1` to `right + 1`).
     - Shrink the window from the left:
       - Decrement counts.
       - Move `left` forward.
4. Accumulate the total count of such substrings.

---

### **Java Code:**
```java
public int numberOfSubstrings(String s) {
    int n = s.length();
    int count = 0;
    int left = 0;
    int[] counts = new int[3]; // For 'a', 'b', 'c'

    for (int right = 0; right < n; right++) {
        counts[s.charAt(right) - 'a']++;

        while (counts[0] > 0 && counts[1] > 0 && counts[2] > 0) {
            // Count substrings ending at right with current window
            count += n - right;

            // Shrink window from the left
            counts[s.charAt(left) - 'a']--;
            left++;
        }
    }
    return count;
}
```

---

### **Complexity Analysis:**

- **Time Complexity:**  
  **O(n)** — Each character is visited at most twice (once when expanding `right`, once when moving `left`).

- **Space Complexity:**  
  **O(1)** — Counters array of fixed size.

---

### **Dry Run:**

**Input:**
```plaintext
s = "abc"
```

| right | Char | Counts after update | Condition? | Count | left | Counts after shrink | Explanation |
|--------|--------|---------------------|------------|--------|-------|---------------------|--------------|
| 0      | 'a'    | a=1, b=0, c=0       | no         | 0      | 0     | —                   | |
| 1      | 'b'    | a=1, b=1, c=0       | no         | 0      | 0     | —                   | |
| 2      | 'c'    | a=1, b=1, c=1       | yes        | count += 1 (n - right= 3-2=1) -> count=1 | 0 | shrink from left: 'a' -> a=0, b=1, c=1 | |

Total count = 1.

---

## Approach 3: Optimized Mathematical Approach (Using Prefix Counts)

### **Core Idea:**
Count the number of substrings ending at each position that contain all three characters by tracking the **earliest** position where all three characters are present.

### **Algorithm:**
1. Maintain three pointers: `aIndex`, `bIndex`, and `cIndex`, to record the last occurrence of `'a'`, `'b'`, and `'c'`.
2. For each position `i`, update the last occurrence of the current character.
3. The number of valid substrings ending at `i` is determined by the minimum of the three last occurrence indices + 1.
4. Sum these values over all positions.

---

### **Java Code:**
```java
public int numberOfSubstrings(String s) {
    int aIndex = -1, bIndex = -1, cIndex = -1;
    int result = 0;

    for (int i = 0; i < s.length(); i++) {
        char ch = s.charAt(i);
        if (ch == 'a') aIndex = i;
        else if (ch == 'b') bIndex = i;
        else if (ch == 'c') cIndex = i;

        int minIndex = Math.min(aIndex, Math.min(bIndex, cIndex));
        if (minIndex != -1) {
            result += minIndex + 1;
        }
    }
    return result;
}
```

---

### **Complexity Analysis:**

- **Time Complexity:**  
  **O(n)** — Single pass through the string.

- **Space Complexity:**  
  **O(1)** — Constant extra variables.

---

### **Dry Run:**

**Input:**
```plaintext
s = "abc"
```

| i | Char | aIndex | bIndex | cIndex | minIndex | Result | Explanation                     |
|---|--------|---------|---------|---------|----------|---------|---------------------------------|
| 0 | 'a'    | 0       | -1      | -1      | -1       | 0       | Not all three yet             |
| 1 | 'b'    | 0       | 1       | -1      | -1       | 0       | Not all three yet             |
| 2 | 'c'    | 0       | 1       | 2       | 0       | 0 + 1 + 1 = 2 | Valid substrings ending here: "abc", "bc" |

Total = 2 (corresponds to "abc" and "bc" substrings).

---

## Summary of Approaches:

| Approach | Time Complexity | Space Complexity | Notes |
|------------|-------------------|--------------------|--------|
| Brute Force | **O(n^3)** (if counting each substring's characters naively) | **O(1)** | Not efficient for large strings. |
| Sliding Window | **O(n)** | **O(1)** | Efficient and commonly used. |
| Mathematical Counting | **O(n)** | **O(1)** | Very optimized, ideal for large inputs. |

---

## Final Tips:
- For large strings, prefer the sliding window or mathematical approach.
- Use character last occurrence tracking for constant-time updates.
- Test with edge cases, such as strings with only `'a'`, `'b'`, `'c'`, or missing one character.

---

This guide provides a comprehensive overview of strategies to solve "Number of Substrings Containing All Three Characters," enabling quick revision and confident interview performance.
