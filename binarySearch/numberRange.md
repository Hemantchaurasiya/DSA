# Number Range

The **Number Range** problem generally involves finding the minimum and maximum values in an array or a segment of numbers, or sometimes involves counting the numbers within a certain range. Here, we'll focus on the classic problem: **finding the minimum and maximum elements in an array** using various approaches, progressing from brute-force to optimal solutions.

---

## 1. Brute Force Approach

### **Core Idea:**

Iterate through the entire array twice — once to find the minimum element and once to find the maximum element.

### **Algorithm:**

1. Initialize two variables, `min` and `max`, to the first element of the array.
2. Traverse the array:
    - Update `min` if the current element is less than `min`.
    - Update `max` if the current element is greater than `max`.
3. After traversal, `min` and `max` hold the minimum and maximum values respectively.

### **Java Code:**

```java
public class NumberRange {
    public static int[] findMinMax(int[] arr) {
        int min = arr[0];
        int max = arr[0];

        for (int i = 1; i < arr.length; i++) {
            if (arr[i] < min)
                min = arr[i];
            if (arr[i] > max)
                max = arr[i];
        }
        return new int[]{min, max};
    }
}

```

### **Complexity Analysis:**

- **Time Complexity:** `O(n)` — Single pass through the array.
- **Space Complexity:** `O(1)` — Only a few variables used.

### **Dry Run:**

- Input: `[3, 5, 1, 8, -2, 7]`
- Initially: `min=3`, `max=3`
- i=1: `arr[1]=5` -> `max=5`
- i=2: `arr[2]=1` -> `min=1`
- i=3: `arr[3]=8` -> `max=8`
- i=4: `arr[4]=-2` -> `min=-2`
- i=5: `arr[5]=7` -> no change
- Final: `min=-2`, `max=8`

---

## 2. Better Solution (Pairwise Comparison)

### **Core Idea:**

Reduce the total number of comparisons by processing elements in pairs, updating min and max accordingly.

### **Algorithm:**

1. Initialize `min` and `max`:
    - Compare the first two elements, assign `min` and `max` accordingly.
2. Loop through the array in steps of 2:
    - For each pair:
        - Compare the two elements to find the local min and max.
        - Compare local min with global `min`.
        - Compare local max with global `max`.
3. Handle odd element if array length is odd.

### **Java Code:**

```java
public class NumberRange {
    public static int[] findMinMax(int[] arr) {
        int min, max, i;

        int n = arr.length;
        if (n % 2 == 0) {
            if (arr[0] < arr[1]) {
                min = arr[0];
                max = arr[1];
            } else {
                min = arr[1];
                max = arr[0];
            }
            i = 2;
        } else {
            min = max = arr[0];
            i = 1;
        }

        while (i < n - 1) {
            if (arr[i] < arr[i + 1]) {
                if (arr[i] < min)
                    min = arr[i];
                if (arr[i + 1] > max)
                    max = arr[i + 1];
            } else {
                if (arr[i + 1] < min)
                    min = arr[i + 1];
                if (arr[i] > max)
                    max = arr[i];
            }
            i += 2;
        }
        return new int[]{min, max};
    }
}

```

### **Complexity Analysis:**

- **Time Complexity:** `O(n)` — Approximately `3n/2` comparisons, which is better than `2n`.
- **Space Complexity:** `O(1)` — Only variables used.

### **Dry Run:**

- Input: `[3, 5, 1, 8, -2, 7]`
- Pairwise comparison:
    - First pair: `(3, 5)` -> `min=3`, `max=5`
    - Next pair: `(1, 8)` -> compare with current min/max, update `min=1`, `max=8`
    - Last pair: `(-2, 7)` -> compare with current min/max, update `min=-2`, `max=8`
- Final: `min=-2`, `max=8`

---

## 3. Most Optimal Solution: Divide and Conquer

### **Core Idea:**

Use divide and conquer to recursively split the array into halves, find min and max in subarrays, and combine results efficiently.

### **Algorithm:**

1. If the array has only one element:
    - Return that element as both min and max.
2. If the array has two elements:
    - Compare and assign min and max.
3. Else:
    - Divide the array into two halves.
    - Recursively find min and max in both halves.
    - The overall min is the smaller of the two mins.
    - The overall max is the larger of the two maxes.

### **Java Code:**

```java
public class NumberRange {

    private static class Pair {
        int min, max;

        Pair(int min, int max) {
            this.min = min;
            this.max = max;
        }
    }

    public static Pair findMinMax(int[] arr, int low, int high) {
        if (low == high) {
            // Only one element
            return new Pair(arr[low], arr[low]);
        } else if (high == low + 1) {
            // Two elements
            if (arr[low] < arr[high]) {
                return new Pair(arr[low], arr[high]);
            } else {
                return new Pair(arr[high], arr[low]);
            }
        } else {
            int mid = (low + high) / 2;
            Pair left = findMinMax(arr, low, mid);
            Pair right = findMinMax(arr, mid + 1, high);

            int min = Math.min(left.min, right.min);
            int max = Math.max(left.max, right.max);

            return new Pair(min, max);
        }
    }
}

```

### **Complexity Analysis:**

- **Time Complexity:** `O(n)` — Each element is compared at most once during the divide and conquer process.
- **Space Complexity:** `O(log n)` — Due to recursive call stack.

### **Dry Run:**

- Input: `[3, 5, 1, 8, -2, 7]`
- Recursive splits:
    - `[3, 5, 1]`, `[8, -2, 7]`
    - `[3, 5]` and `[1]`:
        - `[3, 5]` -> min=3, max=5
        - `[1]` -> min=1, max=1
    - Combine: min=1, max=5
    - `[8, -2, 7]`:
        - `[8, -2]` -> min=-2, max=8
        - `[7]` -> min=7, max=7
        - Combine: min=-2, max=8
- Final combine:
    - Overall min = min(1, -2) = -2
    - Overall max = max(5, 8) = 8

---

# Summary Table

| Approach | Comparisons | Time Complexity | Space Complexity | Remarks |
| --- | --- | --- | --- | --- |
| Brute Force | 2n | `O(n)` | `O(1)` | Simple, straightforward, two passes |
| Pairwise Comparison | ~1.5n | `O(n)` | `O(1)` | Fewer comparisons, efficient |
| Divide and Conquer | ~n - 1 comparisons | `O(n)` | `O(log n)` | Recursive, optimal for large datasets |

---

# Quick Tips for Interview:

- For small arrays, brute force suffices.
- Use pairwise comparison to optimize comparison count.
- Divide and conquer approach is elegant and efficient for large datasets.
- Always consider the trade-off between implementation complexity and performance gains.

---

**End of DSA Revision Note for "Number Range" problem.**
