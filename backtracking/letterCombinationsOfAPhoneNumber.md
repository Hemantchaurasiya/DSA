# Letter Combinations of a Phone Number

---

## Problem Overview
Given a string containing digits from 2 to 9, return all possible letter combinations that the number could represent based on the classic phone keypad mapping:

| Digit | Letters          |
|--------|------------------|
| 2      | a, b, c        |
| 3      | d, e, f        |
| 4      | g, h, i        |
| 5      | j, k, l        |
| 6      | m, n, o        |
| 7      | p, q, r, s     |
| 8      | t, u, v        |
| 9      | w, x, y, z     |

**Example:**  
Input: `"23"`  
Output: `["ad","ae","af","bd","be","bf","cd","ce","cf"]`

---

## 1. Brute Force Approach

### **Core Idea:**  
Generate all possible combinations by cartesian product of the characters mapped to each digit, using nested loops or recursion.

### **Algorithm:**  
- Map each digit to its corresponding characters.  
- Use recursive backtracking to build all possible strings:
  - For each digit, iterate over its corresponding characters.
  - Append each character to the current combination.
  - Recurse for the next digit until the length matches the input string length.
- Collect all combinations in a result list.

### **Recursion Tree Diagram:**

```
For input "23":
Start:
          ""
        /    |    \
       a     b     c (for '2')
      / \    / \    / \
     d e f  d e f  d e f (for '3')
```

### **Java Code:**

```java
import java.util.*;

public class LetterCombinationsBruteForce {
    private static final Map<Character, String> digitToChars = Map.of(
        '2', "abc", '3', "def", '4', "ghi",
        '5', "jkl", '6', "mno", '7', "pqrs",
        '8', "tuv", '9', "wxyz"
    );

    public List<String> letterCombinations(String digits) {
        List<String> result = new ArrayList<>();
        if (digits == null || digits.isEmpty()) return result;
        backtrack(digits, 0, new StringBuilder(), result);
        return result;
    }

    private void backtrack(String digits, int index, StringBuilder current, List<String> result) {
        if (index == digits.length()) {
            result.add(current.toString());
            return;
        }
        String possibleChars = digitToChars.get(digits.charAt(index));
        for (char c : possibleChars.toCharArray()) {
            current.append(c);
            backtrack(digits, index + 1, current, result);
            current.deleteCharAt(current.length() - 1);
        }
    }
}
```

### **Complexity Analysis:**  
- **Time Complexity:** **O(3^n * 4^m)**, where `n` is the number of digits with 3 options and `m` with 4 options. In the worst case (digits 7 and 9), the total combinations are exponential.  
- **Space Complexity:** **O(n)** (recursion stack) + space for storing all combinations.

---

## 2. Backtracking (Most Common and Efficient)

### **Core Idea:**  
Use recursive backtracking to efficiently generate only valid combinations without exploring unnecessary options.

### **Algorithm:**  
- Map each digit to its corresponding characters.  
- For each digit, iterate over its characters.
- Append each character to a temporary string builder.
- Recurse for the next digit.
- When the current combination length equals the input length, add it to the result list.

### **Recursion Tree Diagram:**

Similar to the brute-force, but more efficient due to pruning:

```
Start:
          ""
        /    |    \
       a     b     c (for '2')
      / \    / \    / \
     d e f  d e f  d e f (for '3')
```

### **Java Code:**

(See above code under brute force, as it's the same efficient backtracking implementation.)

### **Complexity Analysis:**  
- **Time Complexity:** **O(3^n * 4^m)** (same as brute-force, but the pruning reduces unnecessary branches)  
- **Space Complexity:** **O(n)** (recursion depth and current combination storage).

---

## 3. Iterative (Breadth-First Search) Approach

### **Core Idea:**  
Use a queue to iteratively build combinations by appending characters for each digit.

### **Algorithm:**  
- Initialize a queue with an empty string.  
- For each digit:
  - For each string in the queue, append every possible character mapped to the digit.
  - Replace the queue contents with these new strings.
- Return the queue contents after processing all digits.

### **Java Code:**

```java
import java.util.*;

public class LetterCombinationsIterative {
    private static final Map<Character, String> digitToChars = Map.of(
        '2', "abc", '3', "def", '4', "ghi",
        '5', "jkl", '6', "mno", '7', "pqrs",
        '8', "tuv", '9', "wxyz"
    );

    public List<String> letterCombinations(String digits) {
        if (digits == null || digits.isEmpty()) return new ArrayList<>();
        Queue<String> queue = new LinkedList<>();
        queue.offer("");

        for (char digit : digits.toCharArray()) {
            List<String> temp = new ArrayList<>();
            String chars = digitToChars.get(digit);
            while (!queue.isEmpty()) {
                String prev = queue.poll();
                for (char c : chars.toCharArray()) {
                    temp.add(prev + c);
                }
            }
            queue.addAll(temp);
        }
        return new ArrayList<>(queue);
    }
}
```

### **Complexity Analysis:**  
- **Time Complexity:** **O(3^n * 4^m)**, as each digit multiplies the combination count.  
- **Space Complexity:** **O(3^n * 4^m)** for storing all combinations.

---

## 4. Dry Run Example

### Input:
```plaintext
digits = "23"
```

### Step-by-step walkthrough (Using backtracking):

| Step | Current Combination | Processing Digit | Choices | Resulting Combinations | Notes |
|-------|------------------------|-------------------|---------|------------------------|--------|
| 1     | []                     | '2'               | a, b, c | ["a", "b", "c"]        | Start with empty, process first digit |
| 2     | "a"                    | '3'               | d, e, f | ["ad", "ae", "af"]     | Append for 'a' |
| 3     | "b"                    | '3'               | d, e, f | ["bd", "be", "bf"]     | Append for 'b' |
| 4     | "c"                    | '3'               | d, e, f | ["cd", "ce", "cf"]     | Append for 'c' |

Final output: `["ad", "ae", "af", "bd", "be", "bf", "cd", "ce", "cf"]`

---

## **Summary of Approaches**

| Approach                 | Core Idea                                   | Time Complexity                     | Space Complexity                     | Notes                                                 |
|--------------------------|---------------------------------------------|-------------------------------------|--------------------------------------|--------------------------------------------------------|
| Brute Force              | Generate all combinations via recursion     | O(3^n * 4^m)                       | O(n) + output storage                | Inefficient for large inputs                         |
| Backtracking             | Recursive, pruning unnecessary paths       | O(3^n * 4^m)                       | O(n)                                | Most practical and common approach                     |
| Iterative (BFS) Approach | Use queue to build combinations iteratively | O(3^n * 4^m)                       | O(3^n * 4^m)                        | Suitable for iterative style; intuitive for some cases |

---

## **Final Tips for Interview Preparation**

- Start with the backtracking approach, as it is the most straightforward and efficient for this problem.
- Understand the mapping of digits to characters thoroughly.
- Practice dry runs on small inputs to solidify your understanding of how combinations are built.
- Be prepared to optimize or modify the approach depending on constraints or specific interview questions.

---

**Happy Coding!**
