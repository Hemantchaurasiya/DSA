# Restore IP Addresses

---

## Problem Summary:
Given a string containing only digits, restore all possible valid IP address combinations. An IP address consists of **four** segments, each ranging from **0** to **255**, separated by dots.

---

## Approach 1: Brute Force

### 1. Core Idea:
Generate all possible ways to split the string into 4 parts, then validate each part for IP validity.

### 2. Algorithm:
- Use 3 nested loops to determine the positions of the three dots, dividing the string into four segments.
- For each partition:
  - Extract the four segments.
  - Validate each segment:
    - Not empty.
    - No leading zeros unless the segment is "0".
    - Integer value between 0 and 255.
- Collect and return all valid IP addresses.

### 3. Recursion Tree Diagram:
Since this is brute-force, the recursion tree resembles all possible placements of 3 dots within the string, leading to \( O(n^3) \) possibilities.

```
For string of length n:
- Place first dot (i from 1 to 3)
  - Place second dot (j from i+1 to i+3)
    - Place third dot (k from j+1 to j+3)
      - Validate segments s[0:i], s[i:j], s[j:k], s[k:]
```

### 4. Java Code:
```java
public List<String> restoreIpAddresses(String s) {
    List<String> result = new ArrayList<>();
    int n = s.length();
    for (int i = 1; i < Math.min(4, n); i++) {
        for (int j = i + 1; j < Math.min(i + 4, n); j++) {
            for (int k = j + 1; k < Math.min(j + 4, n); k++) {
                String s1 = s.substring(0, i);
                String s2 = s.substring(i, j);
                String s3 = s.substring(j, k);
                String s4 = s.substring(k);
                if (isValid(s1) && isValid(s2) && isValid(s3) && isValid(s4)) {
                    result.add(s1 + "." + s2 + "." + s3 + "." + s4);
                }
            }
        }
    }
    return result;
}

private boolean isValid(String s) {
    if (s.length() == 0 || s.length() > 3) return false;
    if (s.startsWith("0") && s.length() > 1) return false;
    int val = Integer.parseInt(s);
    return val >= 0 && val <= 255;
}
```

### 5. Complexity Analysis:
- **Time Complexity:** \( O(3^4 \times n) \) (roughly \( O(n^3) \)) due to the nested loops and validation.
- **Space Complexity:** \( O(1) \) or \( O(n) \) depending on output storage, as the recursion stack is not used here.

### 6. Dry Run (Sample Input: `"25525511135"`):
| Step | i | j | k | Segments | Valid? | Resulting IPs |
|-------|---|---|---|--------------|---------|--------------|
| Initial | - | - | - | - | - | - |
| First split at i=1 (`2`) | | | | "2", "5525511135" | No (second segment invalid) |
| i=1, j=2 (`25`) | | | | "25", "5", "25511135" | Check validity |
| i=1, j=3 (`255`) | | | | "255", "2", "5511135" | Valid? Yes. |
| ... | | | | ... | ... |

*(The process continues for all combinations.)*

---

## Approach 2: Backtracking (Recursive DFS)

### 1. Core Idea:
Use recursion to explore all possible segmentations of the string into 4 parts, pruning invalid paths early.

### 2. Algorithm:
- Keep track of current position, number of segments formed, and current path.
- If 4 segments are formed:
  - Check if entire string is consumed.
  - If yes, add the formed IP to results.
- Else:
  - For each possible segment (1 to 3 digits), check validity.
  - If valid, recurse with updated position, segment count, and path.

### 3. Recursion Tree Diagram:
Each node represents a current position and number of segments:
```
start -> pick 1-3 digits -> recurse (segment+1)
       -> pick 1-3 digits -> recurse (segment+1)
       ...
```

### 4. Java Code:
```java
public List<String> restoreIpAddresses(String s) {
    List<String> result = new ArrayList<>();
    backtrack(s, 0, new ArrayList<>(), result);
    return result;
}

private void backtrack(String s, int start, List<String> segments, List<String> result) {
    if (segments.size() == 4) {
        if (start == s.length()) {
            result.add(String.join(".", segments));
        }
        return;
    }

    for (int len = 1; len <= 3; len++) {
        if (start + len > s.length()) break;
        String segment = s.substring(start, start + len);
        if (isValid(segment)) {
            segments.add(segment);
            backtrack(s, start + len, segments, result);
            segments.remove(segments.size() - 1);
        }
    }
}

private boolean isValid(String s) {
    if (s.length() == 0 || s.length() > 3) return false;
    if (s.startsWith("0") && s.length() > 1) return false;
    int val = Integer.parseInt(s);
    return val >= 0 && val <= 255;
}
```

### 5. Complexity Analysis:
- **Time Complexity:** \( O(3^4 \times n) \) in the worst case, since at each step, we explore up to 3 options for segment length, and there are 4 segments.
- **Space Complexity:** \( O(n) \) for recursion stack and result storage.

### 6. Dry Run:
Input: `"25525511135"`

- **Start at index 0:**
  - Pick `"2"` (valid), recurse with remaining string `"5525511135"`.
  - Pick `"25"` (valid), recurse.
  - Pick `"255"` (valid), recurse.
- Each recursive call continues to pick segments, validating, and backtracking when invalid.

---

## Approach 3: Optimized Backtracking with Pruning

### 1. Core Idea:
Add early pruning based on remaining characters and segments needed to avoid unnecessary recursive calls.

### 2. Algorithm:
- During recursion, calculate remaining characters and segments.
- If remaining characters are not enough or too many for remaining segments, prune early.
- Proceed with recursive exploration only if it's feasible.

### 3. Java Code:
```java
public List<String> restoreIpAddresses(String s) {
    List<String> result = new ArrayList<>();
    backtrack(s, 0, 0, new ArrayList<>(), result);
    return result;
}

private void backtrack(String s, int start, int segments, List<String> current, List<String> result) {
    if (segments == 4 && start == s.length()) {
        result.add(String.join(".", current));
        return;
    }
    if (segments >= 4 || start >= s.length()) return;

    int remainingChars = s.length() - start;
    int remainingSegments = 4 - segments;

    // Prune if remaining characters are too few or too many
    if (remainingChars < remainingSegments || remainingChars > remainingSegments * 3) return;

    for (int len = 1; len <= 3; len++) {
        if (start + len > s.length()) break;
        String segment = s.substring(start, start + len);
        if (isValid(segment)) {
            current.add(segment);
            backtrack(s, start + len, segments + 1, current, result);
            current.remove(current.size() - 1);
        }
    }
}
```

### 4. Complexity Analysis:
Same as previous backtracking approach, but pruning reduces unnecessary recursive calls, improving efficiency.

---

## Summary:
| Approach | Core Idea | Pros | Cons | Use Case |
|------------|--------------|-------|--------|----------|
| Brute Force | Generate all splits, validate | Simple to implement | Inefficient for large strings | Small input sizes |
| Backtracking | Recursive exploration with early pruning | More efficient, clean | Slightly complex | Larger inputs, performance critical |
| Optimized Backtracking | Pruning based on remaining characters | Most efficient | Slightly complex | Large inputs, time-critical |

---

## Final Tips:
- Always validate segments for leading zeros and range.
- Use backtracking with pruning for better performance.
- Visualize recursion tree to understand the exploration process.
- Test with various inputs to ensure correctness.

---

**Happy Revising!**
