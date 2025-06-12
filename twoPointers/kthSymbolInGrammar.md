# K-th Symbol in Grammar

This problem involves determining the **k-th symbol** in a special grammar sequence generated iteratively. The sequence starts with `'0'` at row 1, and each subsequent row is formed by replacing each `'0'` with `'0 1'` and each `'1'` with `'1 0'`.

---

## 1. Brute Force Approach

### **Approach Name:**  
Iterative Simulation of Grammar Expansion

### **Core Idea:**  
Simulate the process of generating the sequence up to row `n`, then directly access the `k`-th symbol.

### **Algorithm:**  
1. Initialize the sequence as `'0'`.
2. For each row from `1` to `n-1`:
   - For each symbol in the current sequence:
     - Replace `'0'` with `'0 1'`.
     - Replace `'1'` with `'1 0'`.
3. After building the sequence for row `n`, return the symbol at position `k`.

### **Java Code:**
```java
import java.util.*;

public class KthSymbolInGrammar {
    public int kthGrammar(int n, int k) {
        String sequence = "0";
        for (int i = 1; i < n; i++) {
            StringBuilder sb = new StringBuilder();
            for (char c : sequence.toCharArray()) {
                if (c == '0') {
                    sb.append("01");
                } else {
                    sb.append("10");
                }
            }
            sequence = sb.toString();
        }
        return sequence.charAt(k - 1) - '0';
    }
}
```

### **Complexity Analysis:**  
- **Time Complexity:** **O(2^n)** because each row doubles the length of the sequence.
- **Space Complexity:** **O(2^n)** for storing the sequence.

### **Dry Run:**  
Suppose:
- `n=3`, `k=3`

Steps:
- Row 1: `'0'`
- Row 2: `'01'`
- Row 3: `'0110'`
- Access position `k=3`: `'1'`

Result: `1`

---

## 2. Better Approach: Recursive Mathematical Pattern

### **Approach Name:**  
Recursive Pattern Exploitation

### **Core Idea:**  
Observe the pattern:
- The sequence for each row is generated from the previous row, but the problem's symmetry allows us to determine the symbol based on position `k` and the previous row recursively.

### **Algorithm:**  
1. Recognize base case:
   - If `n=1`, the sequence is `'0'`, so return `0`.
2. For each recursive call:
   - Calculate the parent position: `parent = (k+1)//2`.
   - Recurse to find the symbol at position `parent` in the previous row.
3. Determine the symbol based on the position:
   - If `k` is odd, the symbol is the same as the parent.
   - If `k` is even, the symbol is the complement of the parent.

### **Java Code:**
```java
public class KthSymbolInGrammar {
    public int kthGrammar(int n, int k) {
        if (n == 1) return 0;

        int parent = (k + 1) / 2;
        int parentSymbol = kthGrammar(n - 1, parent);

        if (k % 2 == 1) {
            return parentSymbol;
        } else {
            return 1 - parentSymbol;
        }
    }
}
```

### **Complexity Analysis:**  
- **Time Complexity:** **O(n)**, since each recursive call reduces `n` by 1.
- **Space Complexity:** **O(n)** due to recursion stack.

### **Dry Run:**  
Suppose:
- `n=4`, `k=5`

Steps:
- `k=5` (odd), parent=3
- Recurse for `n=3`, `k=3`:
  - parent=2
  - recurse for `n=2`, `k=2`:
    - parent=1
    - recurse for `n=1`, `k=1`:
      - base case: return `0`
    - `k=2` (even): complement of `0` → `1`
  - `k=3` (odd): same as parent → `1`
- `k=5` (odd): same as parent → `1`

Result: `1`

---

## 3. Most Optimal Approach: Mathematical Insight (XOR Pattern)

### **Approach Name:**  
Bitwise XOR Pattern Recognition

### **Core Idea:**  
The symbol at position `k` in the `n`-th row can be determined by counting the number of `1`s in the binary representation of `k-1`.  
- If the number of `1`s in `(k-1)`'s binary form is even, the symbol is `0`.
- If odd, the symbol is `1`.

This pattern arises because each step flips the symbol based on the binary representation.

### **Algorithm:**  
1. Compute the number of set bits (`1`s) in `k-1`.
2. Return `0` if the count is even; otherwise, return `1`.

### **Java Code:**
```java
public class KthSymbolInGrammar {
    public int kthGrammar(int n, int k) {
        int countOnes = Integer.bitCount(k - 1);
        return countOnes % 2;
    }
}
```

### **Complexity Analysis:**  
- **Time Complexity:** **O(1)**, as `Integer.bitCount()` is a built-in optimized operation.
- **Space Complexity:** **O(1)**.

### **Dry Run:**  
Suppose:
- `k=5`
- `k-1=4`, binary `100`, has 1 set bit.
- Count of `1`s = 1 (odd), so symbol is `1`.

Result: `1`

---

## **Summary & Key Points:**

| Approach                        | Time Complexity            | Space Complexity | Key Idea                                              |
|---------------------------------|----------------------------|------------------|-------------------------------------------------------|
| Simulation of sequence expansion | O(2^n)                     | O(2^n)           | Generate entire sequence iteratively.                |
| Recursive pattern recognition   | O(n)                       | O(n)             | Recurse based on position and previous symbol.      |
| XOR / Binary pattern            | O(1) (bitwise)             | O(1)             | Use binary properties to directly compute symbol.   |

---

## **Final Tips for Interview:**
- Understanding the recursive pattern and the binary approach is crucial.
- The XOR method is the most efficient and elegant for large `n` and `k`.
- Clarify constraints: for large `n`, avoid sequence simulation.
- Use the recursive or bitwise approach for optimal solutions.

Mastering these approaches will help you solve similar pattern-based or recursive sequence problems efficiently!
