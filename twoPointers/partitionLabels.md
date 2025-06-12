# Partition Labels

---

## Problem Description
Given a string `s`, partition it into as many parts as possible so that each letter appears in **at most one** part. Return a list of integers representing the **lengths** of these parts.

**Example:**
```plaintext
Input: "ababcbacadefegdehijhklij"
Output: [9, 7, 8]
Explanation: The partitions are "ababcbaca", "defegde", "hijhklij".
```

---

## Approach 1: Brute Force (Naive)

### **Core Idea**
For each position, check the last occurrence of the current character and ensure the current partition extends to include all characters up to the last occurrence of each character encountered.

### **Algorithm**
1. For each starting index `i`:
   - Find the last occurrence of `s[i]`.
   - Extend the current partition end to the maximum last occurrence of any character within this range.
   - Once the current index reaches the partition's end, record the size.
2. Repeat for the next segment starting after the current partition.

*Note:* This approach is inefficient, with repeated scans for last occurrence.

### **Java Code**
```java
import java.util.ArrayList;
import java.util.List;

public List<Integer> partitionLabelsBruteForce(String s) {
    List<Integer> result = new ArrayList<>();
    int n = s.length();
    int i = 0;
    while (i < n) {
        int end = s.lastIndexOf(s.charAt(i));
        int j = i;
        while (j < end) {
            end = Math.max(end, s.lastIndexOf(s.charAt(j)));
            j++;
        }
        result.add(end - i + 1);
        i = end + 1;
    }
    return result;
}
```

### **Complexity Analysis**
- **Time Complexity:** `O(n^2)` — For each character, potentially scanning for last occurrence.
- **Space Complexity:** `O(1)` — Extra variables only.

### **Dry Run**
Input: `"ababcbacadefegdehijhklij"`

| Step | `i` | Current char | Last occurrence | Partition end | Result list | Comments                            |
|-------|-------|----------------|------------------|----------------|--------------|-------------------------------------|
| 1     | 0 | 'a' | 8 | 8 | [8] | Partition from index 0 to 8 ("ababcbaca") |
| 2     | 9 | 'd' | 14 | 14 | [8,7] | Next partition "defegde" |
| 3     | 15 | 'h' | 15 | 15 | [8,7,8] | Last partition "hijhklij" |

---

## Approach 2: Optimized Using Last Occurrences

### **Core Idea**
Precompute the last occurrence of each character. Use two pointers:
- `start` to mark the beginning of a partition.
- `end` to mark the furthest last occurrence of any character in the current segment.

Scan the string:
- Update `end` as the maximum last occurrence among current characters.
- When the current index reaches `end`, record the partition size and start a new partition.

### **Algorithm**
1. Create a map/array to store last occurrence index of each character.
2. Initialize `start = 0`, `end = 0`.
3. For each index `i`:
   - Update `end` to `max(end, lastOccurrence[s.charAt(i)])`.
   - If `i == end`:
     - Record the size (`end - start + 1`).
     - Update `start = i + 1`.
4. Continue until the end of the string.

### **Java Code**
```java
import java.util.ArrayList;
import java.util.List;

public List<Integer> partitionLabels(String s) {
    int[] last = new int[26]; // For lowercase letters
    int n = s.length();
    
    // Store last occurrence of each character
    for (int i = 0; i < n; i++) {
        last[s.charAt(i) - 'a'] = i;
    }
    
    List<Integer> result = new ArrayList<>();
    int start = 0, end = 0;
    for (int i = 0; i < n; i++) {
        end = Math.max(end, last[s.charAt(i) - 'a']);
        if (i == end) {
            result.add(end - start + 1);
            start = i + 1;
        }
    }
    return result;
}
```

### **Complexity Analysis**
- **Time Complexity:** `O(n)` — Single pass to find last occurrences and partition.
- **Space Complexity:** `O(1)` — Fixed size array for last occurrence storage.

### **Dry Run**
Input: `"ababcbacadefegdehijhklij"`

| Step | `i` | Char | `last[char]` | `end` | `i == end`? | Result | Partition size | Comments                                  |
|-------|-------|-------|--------------|--------|-------------|---------|------------------|-------------------------------------------|
| 0     | 0 | 'a' | 8 | 8 | No | - | - | Update end to last 'a' position |
| 1     | 1 | 'b' | 5 | 8 | No | - | - | Update end to max(8,5)=8 |
| 2     | 2 | 'a' | 8 | 8 | No | - | - | No change |
| 3     | 3 | 'b' | 5 | 8 | No | - | - | No change |
| 4     | 4 | 'c' | 7 | 8 | No | - | - | No change |
| 5     | 5 | 'b' | 5 | 8 | No | - | - | No change |
| 6     | 6 | 'a' | 8 | 8 | No | - | - | No change |
| 7     | 7 | 'c' | 7 | 8 | No | - | - | No change |
| 8     | 8 | 'a' | 8 | 8 | Yes | [9] | 8-0+1=9 | Partition "ababcbaca" |
| 9     | 9 | 'd' | 14 | 14 | Yes | [9,7] | 14-9+1=6 | Partition "defegde" |
| 10    | 10 | 'e' | 15 | 15 | No | - | - | Update end to 15 |
| 11    | 11 | 'f' | 11 | 15 | No | - | - | No change |
| 12    | 12 | 'e' | 15 | 15 | No | - | - | No change |
| 13    | 13 | 'g' | 15 | 15 | Yes | [9,7,8] | 15-15+1=1 | Partition "hijhklij" |

---

## Summary

| Approach | Key Idea | Time Complexity | Space Complexity | Notes |
|------------|--------------|----------------------|-----------------------|--------|
| Brute Force | Repeatedly find last occurrence for each character | `O(n^2)` | `O(1)` | Inefficient, not suitable for large strings |
| Optimized (Last Occurrence Map) | Precompute last positions, then partition in a single pass | `O(n)` | `O(1)` | Most efficient and recommended |

---

## Final Tips
- Use the last occurrence array to avoid repeated scans.
- The partition point is when the current index reaches the maximum last occurrence of characters seen so far.
- This problem demonstrates efficient string traversal and precomputing character positions.

---

**Happy Coding!**
