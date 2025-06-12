# Group Anagrams

## Problem Statement
Given an array of strings, group anagrams together.  
*Anagram groups* consist of strings that are permutations of each other, i.e., they contain the same characters in any order.

---

## Approach 1: Brute Force

### Core Idea
Check every pair of strings to see if they are anagrams, and group them accordingly.

### Algorithm
1. Initialize a list to hold groups of anagrams.
2. Maintain a visited set to ensure each string is processed only once.
3. For each string:
   - If not visited, create a new group.
   - Compare with other strings:
     - If they are anagrams (by sorting or character count), add to the current group and mark as visited.
4. Continue until all strings are processed.

### Java Code
```java
import java.util.*;

public List<List<String>> groupAnagrams(String[] strs) {
    List<List<String>> result = new ArrayList<>();
    boolean[] visited = new boolean[strs.length];

    for (int i = 0; i < strs.length; i++) {
        if (visited[i]) continue;
        List<String> group = new ArrayList<>();
        group.add(strs[i]);
        visited[i] = true;

        for (int j = i + 1; j < strs.length; j++) {
            if (visited[j]) continue;
            if (areAnagrams(strs[i], strs[j])) {
                group.add(strs[j]);
                visited[j] = true;
            }
        }
        result.add(group);
    }
    return result;
}

private boolean areAnagrams(String s1, String s2) {
    if (s1.length() != s2.length()) return false;
    char[] arr1 = s1.toCharArray();
    char[] arr2 = s2.toCharArray();
    Arrays.sort(arr1);
    Arrays.sort(arr2);
    return Arrays.equals(arr1, arr2);
}
```

### Complexity Analysis
- **Time Complexity:**  
  **O(n^2 * k log k)**, where `n` is the number of strings and `k` is the maximum length of a string, due to nested comparisons and sorting.
- **Space Complexity:**  
  **O(n * k)**, for storing the groups and temporary arrays.

### Dry Run
| Input: ["eat", "tea", "tan", "ate", "nat", "bat"] |  
|-------------------------------------------------|  
| Process "eat": compare with "tea" (anagram), group "eat"/"tea" |  
| Process "tan": compare with "nat" (anagram), group "tan"/"nat" |  
| "bat" remains alone, group "bat" |  
| Final groups: [["eat", "tea", "ate"], ["tan", "nat"], ["bat"]] |

---

## Approach 2: Sorting-Based Grouping (Most Common Approach)

### Core Idea
Strings that are anagrams, when sorted, become identical. Use this property to group.

### Algorithm
1. Initialize a hash map: `Map<String, List<String>>`.
2. For each string:
   - Sort the string.
   - Use the sorted string as the key in the map.
   - Append the original string to the list corresponding to the key.
3. Return the list of values from the map.

### Java Code
```java
import java.util.*;

public List<List<String>> groupAnagrams(String[] strs) {
    Map<String, List<String>> map = new HashMap<>();

    for (String s : strs) {
        char[] arr = s.toCharArray();
        Arrays.sort(arr);
        String key = new String(arr);
        map.computeIfAbsent(key, k -> new ArrayList<>()).add(s);
    }

    return new ArrayList<>(map.values());
}
```

### Complexity Analysis
- **Time Complexity:**  
  **O(n * k log k)**, since each string is sorted individually.
- **Space Complexity:**  
  **O(n * k)**, for storing all strings in the map.

### Dry Run
| Input: ["eat", "tea", "tan", "ate", "nat", "bat"] |  
|-------------------------------------------------|  
| "eat" sorted: "aet" → map: {"aet": ["eat"]} |  
| "tea" sorted: "aet" → map: {"aet": ["eat", "tea"]} |  
| "tan" sorted: "ant" → map: {"aet": ["eat", "tea"], "ant": ["tan"]} |  
| "ate" sorted: "aet" → map: {"aet": ["eat", "tea", "ate"], "ant": ["tan"]} |  
| "nat" sorted: "ant" → map: {"aet": [...], "ant": ["tan", "nat"]} |  
| "bat" sorted: "abt" → map: {"aet": [...], "ant": [...], "abt": ["bat"]} |  
| Final groups: [["eat", "tea", "ate"], ["tan", "nat"], ["bat"]] |

---

## Approach 3: Character Count Hashing (Most Optimal for Large Data)

### Core Idea
Use character counts as signatures. Strings with identical character counts belong to the same group.

### Algorithm
1. Initialize a hash map: `Map<String, List<String>>`.
2. For each string:
   - Count the frequency of each character.
   - Convert the count array into a string signature (e.g., "a2b1c0...").
   - Use this signature as the key in the map.
3. Collect all groups from the map values.

### Java Code
```java
import java.util.*;

public List<List<String>> groupAnagrams(String[] strs) {
    Map<String, List<String>> map = new HashMap<>();

    for (String s : strs) {
        int[] count = new int[26]; // assuming only lowercase
        for (char c : s.toCharArray()) {
            count[c - 'a']++;
        }
        StringBuilder keyBuilder = new StringBuilder();
        for (int cCount : count) {
            keyBuilder.append('#');
            keyBuilder.append(cCount);
        }
        String key = keyBuilder.toString();
        map.computeIfAbsent(key, k -> new ArrayList<>()).add(s);
    }

    return new ArrayList<>(map.values());
}
```

### Complexity Analysis
- **Time Complexity:**  
  **O(n * k)**, as counting characters is linear per string.
- **Space Complexity:**  
  **O(n * k)**, for storing the groups.

### Dry Run
| Input: ["eat", "tea", "tan", "ate", "nat", "bat"] |  
|-------------------------------------------------|  
| "eat": count = [a=1, e=1, t=1], key: "#1#0#0#0#1#0#0#0#0#0#0#0#0#0#0#0#0#0#0#0#0#0#0#0#0#0" |  
| "tea": same signature |  
| "tan": count = [a=1, n=1, t=1], key: "#1#0#0#0#0#0#0#0#0#0#0#0#0#0#0#0#0#0#0#0#1#0#0#0#0#0" |  
| "ate": same as "eat" |  
| "nat": same as "tan" |  
| "bat": count = [a=0, b=1, t=1, ...], new key |  
| Final groups: [["eat", "tea", "ate"], ["tan", "nat"], ["bat"]] |

---

## Summary Table

| Approach | Core Idea | Time Complexity | Space Complexity | Best Use Case |
|--------------|--------------|-------------------|------------------|--------------|
| Brute Force | Pairwise comparison with sorting | O(n^2 * k log k) | O(n * k) | Small datasets, educational purposes |
| Sorting-based | Sort each string and group by sorted key | O(n * k log k) | O(n * k) | General, moderate input size |
| Character Count Hashing | Count characters and use as signature | O(n * k) | O(n * k) | Large datasets, optimal performance |

---

## Final Tips
- For efficiency, prefer **Character Count Hashing** when dealing with large datasets or performance-critical applications.
- Use sorting when simplicity and readability are priorities.
- Always handle edge cases like empty strings or null inputs.

---

**Happy Coding!**
