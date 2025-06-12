# Assign Cookies

The **Assign Cookies** problem aims to maximize the number of children who get cookies based on their greed factor and cookie sizes.

---

## 1. Brute Force Approach

### **Core Idea:**
Try every possible assignment of cookies to children to find the maximum number of children satisfied.

### **Algorithm:**
- For each child, check all cookies.
- Assign the smallest cookie that satisfies the child's greed factor.
- Mark the cookie as used once assigned.
- Count how many children are satisfied.

*Note:* This approach is inefficient and not optimal, but illustrates the basic idea.

### **Java Code:**
```java
import java.util.*;

public class AssignCookiesBruteForce {
    public int findContentChildren(int[] greed, int[] cookies) {
        int count = 0;
        boolean[] used = new boolean[cookies.length];
        for (int g : greed) {
            for (int i = 0; i < cookies.length; i++) {
                if (!used[i] && cookies[i] >= g) {
                    used[i] = true; // assign cookie
                    count++;
                    break;
                }
            }
        }
        return count;
    }
}
```

### **Complexity Analysis:**
- **Time Complexity:** **O(n * m)**, where `n` is the number of children, `m` is the number of cookies, as for each child, we scan cookies.
- **Space Complexity:** **O(m)** for the `used` array.

### **Dry Run:**
Suppose `greed = [1, 2, 3]`, `cookies = [1, 1, 2]`.

- Child greed = 1:
  - Cookie 1 (size=1) available → assign.
  - Count=1.
- Child greed = 2:
  - Cookie 1 (size=1): already used.
  - Cookie 2 (size=1): already used.
  - Cookie 3 (size=2): available → assign.
  - Count=2.
- Child greed=3:
  - Cookies all used → no assignment.
- Result: 2 children satisfied.

---

## 2. Better Solution: Sorting + Greedy Approach

### **Core Idea:**
Sort both greed factors and cookie sizes, then assign cookies greedily starting from the smallest greed and smallest cookie to maximize the number satisfied.

### **Algorithm:**
1. Sort `greed` array.
2. Sort `cookies` array.
3. Initialize two pointers: `i` for children, `j` for cookies.
4. Iterate while both pointers are within array bounds:
   - If `cookies[j] >= greed[i]`, assign cookie:
     - Increment both `i` and `j`.
     - Increment count of satisfied children.
   - Else, move to next cookie (`j++`) to find a suitable one.
5. Return the count.

### **Java Code:**
```java
import java.util.*;

public class AssignCookiesGreedy {
    public int findContentChildren(int[] greed, int[] cookies) {
        Arrays.sort(greed);
        Arrays.sort(cookies);
        int i = 0, j = 0, count = 0;
        while (i < greed.length && j < cookies.length) {
            if (cookies[j] >= greed[i]) {
                count++;
                i++;
                j++;
            } else {
                j++;
            }
        }
        return count;
    }
}
```

### **Complexity Analysis:**
- **Time Complexity:** **O(n log n + m log m)** for sorting both arrays, where `n` and `m` are lengths of `greed` and `cookies`.
- **Space Complexity:** **O(1)** (sorting in-place).

### **Dry Run:**
Input: `greed=[1,2,3]`, `cookies=[1,1,2]`.

- Sorted `greed=[1,2,3]`, `cookies=[1,1,2]`.
- i=0, j=0:
  - cookies[0]=1 >= greed[0]=1 → assign, i=1, j=1, count=1.
- i=1, j=1:
  - cookies[1]=1 < greed[1]=2 → j=2.
- i=1, j=2:
  - cookies[2]=2 >= greed[1]=2 → assign, i=2, j=3, count=2.
- i=2, j=3 (out of bounds), stop.
- Result: 2 satisfied children.

---

## 3. Most Optimal Solution: Sorting + Two Pointers

### **Core Idea:**
Same as the second approach, but emphasizing the greedy assignment after sorting. The two-pointer method ensures maximum children satisfied with minimal iterations.

### **Algorithm:**
- Sort both arrays.
- Use two pointers: `child` (for greed array), `cookie` (for cookies array).
- For each cookie, if it satisfies the current child's greed, assign and move both pointers.
- Continue until either array is exhausted.

### **Java Code:**
*(Same as above, emphasizing the greedy matching)*

```java
import java.util.*;

public class AssignCookiesOptimal {
    public int findContentChildren(int[] greed, int[] cookies) {
        Arrays.sort(greed);
        Arrays.sort(cookies);
        int child = 0, cookie = 0;
        while (child < greed.length && cookie < cookies.length) {
            if (cookies[cookie] >= greed[child]) {
                child++;
            }
            cookie++;
        }
        return child;
    }
}
```

### **Complexity Analysis:**
- **Time Complexity:** **O(n log n + m log m)** for sorting, plus **O(n + m)** for traversal.
- **Space Complexity:** **O(1)**.

### **Dry Run:**
Same as above, results in satisfying maximum children using minimal cookies.

---

## **Summary & Key Points:**

| Approach | Time Complexity | Space Complexity | Description |
|------------|------------------|------------------|--------------|
| Brute Force | **O(n*m)** | **O(m)** | Check all possible assignments directly. |
| Sorting + Greedy | **O(n log n + m log m)** | **O(1)** | Sort both arrays, assign greedily from smallest to largest. |
| Most Optimal | Same as above | Same as above | Efficient greedy matching after sorting. |

---

## **Final Tips for Interview:**
- Always consider sorting first for greedy problems involving pairing or matching.
- Use two pointers for linear traversal after sorting.
- Greedy approach yields the optimal solution for this problem.
- Remember to handle edge cases like empty arrays.

Feel free to practice with different input scenarios to solidify understanding!
