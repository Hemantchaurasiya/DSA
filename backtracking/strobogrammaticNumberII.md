# Strobogrammatic Number II

---

## Problem Overview
Given an integer `n`, generate **all** strobogrammatic numbers of length `n`.  
A **strobogrammatic number** is a number that looks the same when rotated 180 degrees, e.g., "69", "88", "962", "818".

---

## Approach 1: Brute Force Enumeration

### Core Idea
Generate all numbers of length `n` and check if each is strobogrammatic.

### Algorithm
1. Generate all numbers with length `n`:
   - For each number from `10^(n-1)` to `10^n - 1` (or `1` to `10^n - 1` if `n=1`), convert to string.
2. For each string, check if it is strobogrammatic:
   - Map each digit to its rotated counterpart.
   - Verify if the reversed string with mapped digits equals the original.
3. Collect all valid numbers.

### Recursion Tree Diagram
(Not practical for large `n`, but conceptual for small `n`):
```
For each position:
  - Choose a digit (0-9)
  - Proceed to next position
Check if the number is strobogrammatic.
```

### Java Code
```java
public List<String> findStrobogrammatic(int n) {
    List<String> result = new ArrayList<>();
    int start = (int) Math.pow(10, n - 1);
    int end = (int) Math.pow(10, n);
    for (int num = start; num < end; num++) {
        String s = String.valueOf(num);
        if (isStrobogrammatic(s))
            result.add(s);
    }
    return result;
}

private boolean isStrobogrammatic(String s) {
    Map<Character, Character> map = new HashMap<>();
    map.put('0', '0');
    map.put('1', '1');
    map.put('8', '8');
    map.put('6', '9');
    map.put('9', '6');
    int left = 0, right = s.length() - 1;
    while (left <= right) {
        if (!map.containsKey(s.charAt(left)) || map.get(s.charAt(left)) != s.charAt(right))
            return false;
        left++;
        right--;
    }
    return true;
}
```

### Complexity Analysis
- **Time Complexity:**  
O(10^n * n), since generating all numbers with `n` digits is `O(10^n)`, and checking each takes `O(n)`.
- **Space Complexity:**  
O(n), for string storage during checking.

---

## Approach 2: Recursive Backtracking (Constructive Approach)

### Core Idea
Build all strobogrammatic numbers of length `n` by adding pairs of digits symmetrically from the outside inward.

### Algorithm
1. Define a recursive function `helper(n, totalLength)`:
   - If `n == 0`: return `[""]` (for even length) or `["0"]` (for the inner part).
   - If `n == 1`: return `["0", "1", "8"]` (center digits for odd length).
2. For each recursive call:
   - Generate smaller list for `n - 2`.
   - For each string in the smaller list:
     - Add valid digit pairs at the start and end:
       - For the outer layer, avoid adding `'0'` if it leads to leading zeros unless `n == totalLength`.
3. Collect and return all generated strings.

### Recursion Tree (for n=3 as example)
```
Level 1 (n=3):
  - Generate inner: n=1 -> ["0", "1", "8"]
  - Wrap with pairs:
    - ("1", "1")
    - ("8", "8")
    - ("6", "9")
    - ("9", "6")
    - ("0", "0") (skip if leading zero and n==totalLength)
```

### Java Code
```java
public List<String> findStrobogrammatic(int n) {
    return helper(n, n);
}

private List<String> helper(int n, int totalLength) {
    if (n == 0) return Arrays.asList("");
    if (n == 1) return Arrays.asList("0", "1", "8");
    
    List<String> list = helper(n - 2, totalLength);
    List<String> result = new ArrayList<>();
    for (String s : list) {
        if (n != totalLength) {
            result.add("0" + s + "0");
        }
        result.add("1" + s + "1");
        result.add("8" + s + "8");
        result.add("6" + s + "9");
        result.add("9" + s + "6");
    }
    return result;
}
```

### Complexity Analysis
- **Time Complexity:**  
O(5^(n/2)), since at each level, up to 5 pairs are added (excluding leading zeros).  
This is efficient for small to moderate `n`.
- **Space Complexity:**  
O(n * 5^(n/2)), due to recursion stack and storage of generated strings.

---

## Approach 3: Most Optimal (Backtracking with Pruning)

### Core Idea
Use backtracking to generate only valid strobogrammatic numbers without generating all possibilities first.

### Algorithm
1. Use a recursive function to build the number from outside in.
2. Maintain a list of valid digit pairs:
   - `("0", "0")`, `("1", "1")`, `("8", "8")`, `("6", "9")`, `("9", "6")`.
3. For each recursive step:
   - If `n == 0`: return `[""]` (for even length).
   - If `n == 1`: return `["0", "1", "8"]` (for odd length).
   - Otherwise, iterate over pairs:
     - Skip pairs with `'0'` at the start if `n == totalLength` to avoid leading zeros.
     - Place pairs at the outer positions, recurse for inner `n-2`.
4. Collect valid numbers.

### Recursion Tree (for n=4)
```
Level 1:
  - Generate inner with n=2:
    - Generate inner with n=0: [""].
    - Wrap with pairs:
      - "1" + "" + "1" -> "11"
      - "8" + "" + "8" -> "88"
      - "6" + "" + "9" -> "69"
      - "9" + "" + "6" -> "96"
    - For outer layer:
      - "0" + "11" + "0" -> "0110" (skip if leading zero)
      - etc.
```

### Java Code
```java
public List<String> findStrobogrammatic(int n) {
    return helper(n, n);
}

private List<String> helper(int n, int totalLength) {
    if (n == 0) return Arrays.asList("");
    if (n == 1) return Arrays.asList("0", "1", "8");

    List<String> prev = helper(n - 2, totalLength);
    List<String> result = new ArrayList<>();
    for (String s : prev) {
        for (String[] pair : pairs) {
            // Skip leading zero
            if (pair[0].equals("0") && n == totalLength) continue; 
            result.add(pair[0] + s + pair[1]);
        }
    }
    return result;
}

private static final String[][] pairs = {
    {"0", "0"},
    {"1", "1"},
    {"8", "8"},
    {"6", "9"},
    {"9", "6"}
};
```

### Complexity Analysis
- **Time Complexity:**  
O(5^(n/2)), since at each recursive level, up to 5 pairs are used.
- **Space Complexity:**  
O(n * 5^(n/2)), for recursion and storage.

---

## **Summary**

| Approach | Core Idea | Pros | Cons | Time Complexity | Space Complexity |
|------------|--------------|-------|--------|-----------------|------------------|
| Brute Force | Generate all numbers, check strobogrammatic | Simple to implement | Inefficient for large `n` | O(10^n * n) | O(n) |
| Recursive Construction | Build numbers from inside out using recursion | More efficient, avoids invalids | Slightly complex | O(5^(n/2)) | O(n * 5^(n/2)) |
| Backtracking with Pruning | Generate only valid candidates via pruning | Most optimal for large `n` | Implementation complexity | O(5^(n/2)) | O(n * 5^(n/2)) |

---

## **Dry Run Example**

### Input: `n = 3`

**Step-by-step:**

- Call `helper(3, 3)`.
- Since `n != 0` or `1`, generate from `helper(1, 3)`:
  - `helper(1, 3)` returns `["0", "1", "8"]`.
- For each inner string:
  - Wrap with pairs:
    - For `"0"`: add `"1" + "0" + "1"` -> `"101"`, `"8" + "0" + "8"` -> `"808"`, `"6" + "0" + "9"` -> `"609"`, `"9" + "0" + "6"` -> `"906"`.
    - For `"1"`: similarly with pairs.
    - For `"8"`: similarly with pairs.
- **Result:** `["101", "111", "181", "609", "619", "689", "906", "916", "986"]`

*(Note: Actual code may generate different valid combinations depending on implementation, but the core idea remains.)*

---

This concludes the detailed revision note on *Strobogrammatic Number II*. Focus on understanding the recursive build approach as it is the most optimal and commonly used solution in interviews.
