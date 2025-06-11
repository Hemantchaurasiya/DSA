# Simplify Path

---

## Problem Overview
Given an absolute Unix-style file path, simplify it to its canonical form. The rules are:
- Use `'/'` as a separator.
- The path should start with `'/'`.
- Any `'..'` means go up one directory (if possible).
- Any `'.'` or empty string components should be ignored.
- Remove redundant slashes.

**Example:**
- Input: `"/a/./b/../../c/"`
- Output: `"/c"`

---

## Approach 1: Brute Force (Splitting and Processing)

### **Core Idea**
Split the path by `'/'`, then process each component:
- Ignore empty components or `'.'`.
- For `'..'`, remove the last directory if any.
- For valid directory names, add to a stack.

### **Algorithm**
1. Split the input path string by `'/'`.
2. Initialize a stack to keep track of directories.
3. For each component:
   - If empty or `'.'`, skip.
   - If `'..'`, pop from the stack if not empty.
   - Else, push the directory name onto the stack.
4. Construct the canonical path from the stack:
   - Join all elements with `'/'` and prepend `'/'`.

### **Java Code**
```java
public String simplifyPath(String path) {
    String[] components = path.split("/");
    Deque<String> stack = new ArrayDeque<>();
    
    for (String dir : components) {
        if (dir.equals("") || dir.equals(".")) {
            continue;
        } else if (dir.equals("..")) {
            if (!stack.isEmpty()) {
                stack.pop();
            }
        } else {
            stack.push(dir);
        }
    }
    
    StringBuilder result = new StringBuilder();
    while (!stack.isEmpty()) {
        result.insert(0, "/" + stack.pop());
    }
    return result.length() == 0 ? "/" : result.toString();
}
```

### **Complexity Analysis**
- **Time Complexity:** *O(n)*, where `n` is the length of the path string, as we process each component once.
- **Space Complexity:** *O(n)* for the stack in worst case (all directories are valid).

### **Dry Run**
Input: `"/a/./b/../../c/"`

| Step | Components             | Stack             | Explanation                                            |
|-------|------------------------|-------------------|--------------------------------------------------------|
| 1     | `""`                   | []                | Empty component, skip                                |
| 2     | `"a"`                  | ["a"]             | Push `'a'`                                            |
| 3     | `"."`                  | ["a"]             | Ignore                                              |
| 4     | `"b"`                  | ["a", "b"]        | Push `'b'`                                            |
| 5     | `".."`                 | ["a"]             | Pop `'b'`                                           |
| 6     | `".."`                 | []                | Pop `'a'`                                           |
| 7     | `"c"`                  | ["c"]             | Push `'c'`                                            |

Final path: `"/c"`.

---

## Approach 2: Using String Builder and Stack (Similar to Approach 1)

This is essentially the same as above, with minor variations for implementation style.

---

## Approach 3: In-Place Path Modification (Optimized)

### **Core Idea**
Use a `StringBuilder` and process the path in a single pass without splitting:
- Maintain an index pointer.
- Build the path by handling `'/'`, `'..'`, and `'.'` inline.
- Use a list or stack to keep track of directory segments.

### **Algorithm**
1. Initialize an empty list or stack to store directory segments.
2. Use a pointer to iterate through the path string:
   - Skip `'/'` characters.
   - Extract each directory component between `'/'`.
   - If component is `'.'` or empty, ignore.
   - If `'..'`, remove the last directory if exists.
   - Else, add the component to the list.
3. Construct the simplified path from the list.

### **Java Code**
```java
public String simplifyPath(String path) {
    List<String> stack = new ArrayList<>();
    int i = 0;
    while (i < path.length()) {
        while (i < path.length() && path.charAt(i) == '/') i++;
        int start = i;
        while (i < path.length() && path.charAt(i) != '/') i++;
        String dir = path.substring(start, i);
        if (dir.equals("") || dir.equals(".")) {
            // do nothing
        } else if (dir.equals("..")) {
            if (!stack.isEmpty()) stack.remove(stack.size() - 1);
        } else {
            stack.add(dir);
        }
    }
    StringBuilder result = new StringBuilder();
    for (String dir : stack) {
        result.append('/').append(dir);
    }
    return result.length() == 0 ? "/" : result.toString();
}
```

### **Complexity Analysis**
- **Time Complexity:** *O(n)*, processing each character once.
- **Space Complexity:** *O(n)* for the list of directories.

---

## **Summary Table**

| Approach                    | Time Complexity            | Space Complexity | Comments                                              |
|------------------------------|----------------------------|------------------|--------------------------------------------------------|
| Split & Stack Processing     | O(n)                       | O(n)             | Simple and clean, easy to understand                   |
| In-place String Processing   | O(n)                       | O(n)             | Slightly optimized, avoids splitting explicitly       |

---

## **Final Tips for Interviews**
- Always handle edge cases: empty path, path with only `'/'`, paths with multiple `'/'`.
- Use a stack or list to easily handle `'..'` operations.
- Remember to reconstruct the path at the end with `'/'`.
- Practice both splitting-based and in-place parsing approaches.

---

**Happy coding!**
