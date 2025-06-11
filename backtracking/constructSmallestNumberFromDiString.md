# Construct Smallest Number From DI String

This note provides a comprehensive guide to solving the problem **"Construct Smallest Number From DI String"**. It covers multiple approaches, from brute-force to the most optimal solution, including explanations, algorithms, code, complexity analysis, and dry runs for better understanding.

---

## Problem Statement
Given a string `S` consisting of characters `'D'` and `'I'`, construct the **smallest number** (using digits 1-9, no repetitions) that satisfies the pattern:
- `'I'` indicates the next digit should be **greater** than the previous.
- `'D'` indicates the next digit should be **smaller** than the previous.

**Constraints:**
- Digits used are from 1 to 9.
- Pattern length is at most 8 (since digits are limited).

---

## Approach 1: Brute Force (Permutations)

### **Core Idea**
Generate all permutations of the digits [1..n+1], and find the permutation that satisfies the pattern with the smallest lexicographical order.

### **Algorithm**
1. Generate all permutations of `[1, 2, ..., n+1]`.
2. For each permutation:
   - Check if it satisfies the DI pattern:
     - For each index `i`, if `pattern[i] == 'I'`, then `permutation[i] < permutation[i+1]`.
     - If `pattern[i] == 'D'`, then `permutation[i] > permutation[i+1]`.
3. Keep track of the smallest permutation (lexicographically) that satisfies the pattern.
4. Return that permutation as the result.

### **Recursion Tree Diagram**
(Not practical for large `n`, as permutations grow factorially. For small `n`, recursion generates all permutations.)

---

### **Java Code**
```java
import java.util.*;

public class SmallestNumberFromDI {
    String pattern;
    String result;
    boolean[] used;

    public String findSmallestNumber(String pattern) {
        this.pattern = pattern;
        int n = pattern.length();
        used = new boolean[10]; // since digits 1-9
        List<Integer> permutation = new ArrayList<>();
        result = null;
        backtrack(permutation);
        return result;
    }

    private void backtrack(List<Integer> permutation) {
        if (permutation.size() == pattern.length() + 1) {
            StringBuilder sb = new StringBuilder();
            for (int num : permutation) {
                sb.append(num);
            }
            if (result == null || sb.toString().compareTo(result) < 0) {
                result = sb.toString();
            }
            return;
        }

        for (int digit = 1; digit <= 9; digit++) {
            if (!used[digit]) {
                if (permutation.isEmpty() || isValid(permutation, digit)) {
                    used[digit] = true;
                    permutation.add(digit);
                    backtrack(permutation);
                    permutation.remove(permutation.size() - 1);
                    used[digit] = false;
                }
            }
        }
    }

    private boolean isValid(List<Integer> permutation, int digit) {
        int index = permutation.size() - 1;
        char c = pattern.charAt(index);
        int prev = permutation.get(index);
        if (c == 'I') {
            return digit > prev;
        } else {
            return digit < prev;
        }
    }
}
```

### **Complexity Analysis**
- **Time Complexity:** O(n!), due to permutation generation.
- **Space Complexity:** O(n), for recursion stack and permutation storage.

### **Dry Run**
- Input: `"DID"`
- Generate all permutations of `[1,2,3,4]`.
- Check each permutation against pattern:
  - For example, permutation `[2,1,4,3]`:
    - D: 2 > 1 ✅
    - I: 1 < 4 ✅
    - D: 4 > 3 ✅
- Among all valid permutations, select the lexicographically smallest.

---

## Approach 2: Better Solution (Stack-based Approach)

### **Core Idea**
Use a **stack** to process the pattern:
- For `'I'`, output the current number and clear the stack.
- For `'D'`, push the current number onto the stack.
- At the end, pop remaining elements from the stack.

### **Intuition**
- Whenever we see an `'I'`, we need to finalize the current sequence and output the smallest number so far.
- `'D'` indicates a decreasing sequence; delaying output until the pattern flips or ends ensures the sequence is correctly ordered.

### **Algorithm**
1. Initialize a stack, push `1` (or start with `0` + 1).
2. Iterate through the pattern:
   - For each index `i`:
     - Push `i+1` onto the stack.
     - If pattern[i] == `'I'`:
       - Pop all elements from the stack and append to the answer.
   - After the loop, push `n+1` onto the stack.
3. Pop all remaining elements from the stack and append to the answer.

### **Note**
- Digits are from 1 to `n+1`.
- This approach guarantees the smallest lexicographical number satisfying the pattern.

---

### **Java Code**
```java
import java.util.*;

public class SmallestNumberFromDI {
    public String constructSmallestNumber(String pattern) {
        int n = pattern.length();
        Stack<Integer> stack = new Stack<>();
        StringBuilder result = new StringBuilder();

        for (int i = 0; i <= n; i++) {
            stack.push(i + 1);
            if (i == n || pattern.charAt(i) == 'I') {
                while (!stack.isEmpty()) {
                    result.append(stack.pop());
                }
            }
        }
        return result.toString();
    }
}
```

### **Complexity Analysis**
- **Time Complexity:** O(n), as each element is pushed and popped at most once.
- **Space Complexity:** O(n), for the stack and output string.

### **Dry Run**
- Input: `"DID"`
- Process:
  - i=0: push 1 -> stack=[1]
  - pattern[0]='D' -> continue
  - i=1: push 2 -> stack=[1,2]
  - pattern[1]='I' -> pop all: output= "21"
  - i=2: push 3 -> stack=[3]
  - pattern[2]='D' -> continue
  - i=3: push 4 -> stack=[3,4]
  - End of pattern: pop all: output= "21" + "4" + "3" (since last is D, we pop in order): "21" + "4" + "3" = "2143" (but since we add the pops at each I, the final output is "1 2" then "4 3" after last iteration). The main idea is the implementation ensures minimal lexicographical order.

---

## Approach 3: Most Optimal Solution (Greedy + Stack)

### **Core Idea**
The previous stack approach is already optimal in terms of time complexity (O(n)). The key is to implement it carefully to achieve the minimal number.

### **Algorithm Summary**
- Use a stack to handle `'D'` sequences.
- Push numbers sequentially.
- When `'I'` is encountered, pop and append all elements of the stack to the result (ensuring increasing order).
- Continue until all pattern characters are processed, then pop remaining elements.

---

## Final Notes:
- The **stack-based approach** is the most efficient and clean solution.
- It guarantees minimal lexicographical order.
- Handles all pattern cases with linear time complexity.
- No recursion or permutation generation needed, making it scalable for larger inputs.

---

## **Summary Table**

| Approach | Idea | Time | Space | Code | Remarks |
|------------|-------|-------|--------|-------|---------|
| Brute Force | Generate all permutations | O(n!) | O(n) | Permutation + validation | Not practical for larger n |
| Stack-based | Use stack to process pattern greedily | O(n) | O(n) | Efficient, clean | Optimal solution |

---

## Final Tips
- Always consider greedy and stack-based approaches for pattern-based string problems.
- Validate with multiple pattern inputs to ensure correctness.
- Remember the pattern constraints: only `'D'` and `'I'`, and digits from 1 to n+1.

---

Feel free to practice with different patterns and verify the correctness of the stack approach!
