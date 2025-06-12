# Array With Elements Not Equal to Average of Neighbors

This problem involves identifying elements in an array that are **not equal** to the **average of their neighbors**. Specifically, for each element (except the first and last), check if:
\[ \text{arr[i]} \neq \frac{\text{arr[i-1]} + \text{arr[i+1]}}{2} \]

---

## 1. Brute Force Approach

### **Approach Name:**  
Direct Comparison of Each Element with Average of Neighbors

### **Core Idea:**  
Iterate through the array, for each element (excluding first and last), compute the average of neighbors and compare it with the current element.

### **Algorithm:**  
1. Loop through the array from index `1` to `n-2`.
2. For each element:
   - Calculate the average of its neighbors: `(arr[i-1] + arr[i+1]) / 2`.
   - Check if `arr[i] !=` this average.
   - If not equal, mark it as satisfying the condition.
3. Collect all such elements or count them as needed.

### **Java Code:**
```java
public class ArrayElementsNotEqualToAverage {
    public List<Integer> findElements(int[] arr) {
        List<Integer> result = new ArrayList<>();
        for (int i = 1; i < arr.length - 1; i++) {
            double avg = (arr[i - 1] + arr[i + 1]) / 2.0;
            if (arr[i] != avg) {
                result.add(arr[i]);
            }
        }
        return result;
    }
}
```

### **Complexity Analysis:**  
- **Time Complexity:** **O(n)**, as we traverse the array once.
- **Space Complexity:** **O(k)**, where `k` is number of elements satisfying the condition (or `O(1)` if only counting).

### **Dry Run:**  
Suppose:
- `arr = [2, 4, 6, 8, 10]`

Steps:
- For `i=1`: average of neighbors `(2 + 6)/2 = 4`. `arr[1]=4` → equal, no.
- For `i=2`: `(4 + 8)/2=6`. `arr[2]=6` → equal, no.
- For `i=3`: `(6 + 10)/2=8`. `arr[3]=8` → equal, no.

Result: `[]` (no elements satisfy the condition).

---

## 2. Optimized Approach: Single Pass with Early Exit

This is similar to the brute-force approach but emphasizes minimizing any redundant calculations. Since the calculation is straightforward, the main optimization is avoiding unnecessary data structures or repeated calculations.

### **Approach Name:**  
Single Pass, Direct Comparison

### **Core Idea:**  
Same as brute-force, but with a focus on immediate processing and minimal overhead.

### **Algorithm:**  
Same as above, no additional optimization needed because the problem is straightforward.

### **Java Code:**
```java
public class ArrayElementsNotEqualToAverage {
    public List<Integer> findElements(int[] arr) {
        List<Integer> result = new ArrayList<>();
        for (int i = 1; i < arr.length - 1; i++) {
            int leftNeighbor = arr[i - 1];
            int rightNeighbor = arr[i + 1];
            double avg = (leftNeighbor + rightNeighbor) / 2.0;
            if (arr[i] != avg) {
                result.add(arr[i]);
            }
        }
        return result;
    }
}
```

### **Complexity Analysis:**  
- Same as brute-force:  
  - **Time Complexity:** **O(n)**  
  - **Space Complexity:** **O(k)**

---

## **Summary & Key Points:**

| Approach                     | Time Complexity | Space Complexity | Description                                              |
|------------------------------|------------------|------------------|----------------------------------------------------------|
| Brute Force (Direct Comparison) | O(n)            | O(k)             | Check each element against the average of neighbors.    |
| Optimized (Early Exit, Single Pass) | O(n)       | O(k)             | Same as brute-force; minimal overhead, straightforward. |

---

## **Final Tips for Interview:**
- Focus on clarity: understand the problem involves simple neighbor comparisons.
- Remember to handle edge cases: arrays with length < 3 (no middle elements).
- Clarify whether you need to return elements, count, or a boolean (based on problem statement).
- Practice with various datasets to ensure correctness.

This problem is primarily straightforward; the key is efficient iteration and edge case handling!
