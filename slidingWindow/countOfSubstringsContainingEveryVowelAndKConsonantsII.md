# Count of Substrings Containing Every Vowel and K Consonants II

This comprehensive guide covers multiple approaches to solve the problem of counting substrings that contain **all vowels** (`'a'`, `'e'`, `'i'`, `'o'`, `'u'`) and **at least `K` consonants**. It progresses from brute-force to optimal solutions, designed for quick revision and interview preparation.

---

## Problem Summary
Given a string `s`, count the number of substrings that satisfy:
- **Contain all vowels** (`'a'`, `'e'`, `'i'`, `'o'`, `'u'`)
- **Have at least `K` consonants** (characters other than vowels)

---

## Approach 1: Brute Force

### **Core Idea:**
Iterate over all possible substrings, check if they contain all vowels and at least `K` consonants, then count the valid ones.

---

### **Algorithm:**
1. Generate all substrings:
   - For each starting index `i`
   - For each ending index `j ≥ i`
2. For each substring `s[i..j]`:
   - Count vowels and consonants
   - Check if all vowels are present and consonants ≥ `K`
3. Increment count if valid.

---

### **Java Code:**
```java
public int countSubstrings(String s, int K) {
    int n = s.length();
    int totalCount = 0;
    String vowels = "aeiou";

    for (int i = 0; i < n; i++) {
        for (int j = i; j < n; j++) {
            int vowelCount = 0;
            int consonantCount = 0;
            boolean[] vowelPresent = new boolean[5];

            for (int k = i; k <= j; k++) {
                char ch = s.charAt(k);
                if (vowels.indexOf(ch) >= 0) {
                    vowelPresent[vowels.indexOf(ch)] = true;
                } else {
                    consonantCount++;
                }
            }

            // Check if all vowels are present
            boolean allVowelsPresent = true;
            for (boolean present : vowelPresent) {
                if (!present) {
                    allVowelsPresent = false;
                    break;
                }
            }

            // Check consonant count
            if (allVowelsPresent && consonantCount >= K) {
                totalCount++;
            }
        }
    }
    return totalCount;
}
```

---

### **Complexity Analysis:**

- **Time Complexity:**  
  **O(n^3)** — Generating all substrings (`O(n^2)`) and counting characters (`O(n)`).

- **Space Complexity:**  
  **O(1)** — Fixed size counters and boolean array.

---

### **Dry Run:**

**Input:**
```plaintext
s = "aeioubc", K=1
```

- Generate each substring, check for all vowels and consonant count.
- For example, substring `"aeiou"` contains all vowels, consonants = 0 → invalid if K > 0.
- Substring `"aeioub"` contains all vowels, consonants=1 → valid if K=1.

---

## Approach 2: Sliding Window with Two Pointers (Optimized)

### **Core Idea:**
Use a sliding window to find substrings that contain all vowels and at least `K` consonants efficiently.

### **Algorithm:**
1. Initialize two pointers: `left` and `right`.
2. Maintain counts of vowels and consonants within the window.
3. Expand `right`:
   - Update counts.
   - When all vowels are included and consonants ≥ `K`:
     - Record the number of valid starting positions (`left` to current `right`).
     - Shrink window from `left` to find minimal valid window.
4. Accumulate total valid substrings.

---

### **Java Code:**
```java
public int countSubstrings(String s, int K) {
    int n = s.length();
    int totalCount = 0;
    int left = 0;

    int vowelCount = 0;
    int consonantCount = 0;
    int[] vowelFreq = new int[5];

    String vowels = "aeiou";

    for (int right = 0; right < n; right++) {
        char ch = s.charAt(right);
        if (vowels.indexOf(ch) >= 0) {
            vowelFreq[vowels.indexOf(ch)]++;
            if (vowels.indexOf(ch) >= 0) {
                vowelCount++;
            }
        } else {
            consonantCount++;
        }

        // Check if window contains all vowels and consonants >= K
        while (vowelCount == 5 && consonantCount >= K) {
            // All current substrings starting from left to right are valid
            totalCount += n - right;

            // Shrink window from the left
            char leftChar = s.charAt(left);
            if (vowels.indexOf(leftChar) >= 0) {
                vowelFreq[vowels.indexOf(leftChar)]--;
                if (vowelFreq[vowels.indexOf(leftChar)] == 0) {
                    vowelCount--;
                }
            } else {
                consonantCount--;
            }
            left++;
        }
    }
    return totalCount;
}
```

---

### **Complexity Analysis:**

- **Time Complexity:**  
  **O(n)** — Each character is visited at most twice.

- **Space Complexity:**  
  **O(1)** — Fixed size counters and frequency array.

---

### **Dry Run:**

**Input:**
```plaintext
s = "aeioubc", K=1
```

- Expand `right`:
  - When window contains all vowels and at least 1 consonant, count substrings starting at `left`.
  - Shrink from `left` to find minimal window, then move `right` forward.

---

## Approach 3: Prefix Counts + Efficient Counting (Most Optimal)

### **Core Idea:**
Precompute prefix counts of vowels and consonants, then for each position, determine how many substrings ending there contain all vowels and at least `K` consonants.

### **Algorithm:**
1. Build prefix counts for vowels and consonants.
2. For each position `i`, check:
   - How many vowels are included in the prefix.
   - How many consonants are in the prefix.
3. Use these counts to determine how many substrings ending at `i` satisfy the condition.
4. Sum over all positions.

---

### **Java Code:**
```java
public int countSubstrings(String s, int K) {
    int n = s.length();
    int[] prefixVowels = new int[n + 1];
    int[] prefixConsonants = new int[n + 1];

    String vowels = "aeiou";

    for (int i = 0; i < n; i++) {
        prefixVowels[i + 1] = prefixVowels[i] + (vowels.indexOf(s.charAt(i)) >= 0 ? 1 : 0);
        prefixConsonants[i + 1] = prefixConsonants[i] + (vowels.indexOf(s.charAt(i)) >= 0 ? 0 : 1);
    }

    int totalCount = 0;
    for (int end = 1; end <= n; end++) {
        // For each end index, find the earliest start index where
        // substring contains all vowels and >= K consonants
        for (int start = 0; start < end; start++) {
            int vowelCount = prefixVowels[end] - prefixVowels[start];
            int consonantCount = prefixConsonants[end] - prefixConsonants[start];
            if (vowelCount == 5 && consonantCount >= K) {
                totalCount++;
            }
        }
    }
    return totalCount;
}
```

**Note:** This approach is not optimal for large inputs but demonstrates the counting logic.

---

### **Complexity Analysis:**

- **Time Complexity:**  
  **O(n^2)** — For each position, checking all start indices.

- **Space Complexity:**  
  **O(n)** — Arrays for prefix counts.

---

## Summary of Approaches:

| Approach | Time Complexity | Space Complexity | Notes |
|------------|-------------------|--------------------|--------|
| Brute Force | **O(n^3)** | **O(1)** | Naive, suitable for small inputs. |
| Sliding Window | **O(n)** | **O(1)** | Efficient, handles large strings well. |
| Prefix Counts + Counting | **O(n^2)** | **O(n)** | Useful for understanding, less optimal for large data. |

---

## Final Tips:
- Use the sliding window method for optimal performance.
- Precompute prefix sums if multiple queries are involved.
- Validate with varying `K` values and string compositions.
- Remember to handle edge cases where no valid substrings exist.

---

This guide provides a structured overview for solving **Count of Substrings Containing Every Vowel and K Consonants II**, equipping you with strategies for interview success.
