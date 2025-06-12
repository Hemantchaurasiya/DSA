# Kth Smallest Element in a Sorted Matrix

This problem involves finding the **kth smallest element** in an `n x n` matrix where each row and each column is sorted in ascending order. Efficient solutions leverage the sorted nature of the matrix.

---

## 1. Brute Force Approach

### **Core Idea:**
Flatten the matrix into a single list, sort it, and pick the kth element.

### **Algorithm:**
1. Traverse all elements of the matrix and store them in a list.
2. Sort the list.
3. Return the element at index `k-1`.

### **Java Code:**
```java
import java.util.ArrayList;
import java.util.Collections;

public class KthSmallestInMatrix {
    public int kthSmallest(int[][] matrix, int k) {
        ArrayList<Integer> list = new ArrayList<>();
        for (int[] row : matrix) {
            for (int num : row) {
                list.add(num);
            }
        }
        Collections.sort(list);
        return list.get(k - 1);
    }
}
```

### **Complexity Analysis:**
- **Time Complexity:** `O(n^2 log(n^2)) = O(n^2 log n)` due to flattening and sorting.
- **Space Complexity:** `O(n^2)` for storing all elements.

### **Dry Run:**
- Input:  
  ```
  matrix = [
    [1, 5, 9],
    [10, 11, 13],
    [12, 13, 15]
  ], k=8
  ```
- Flattened list: `[1, 5, 9, 10, 11, 13, 12, 13, 15]`
- Sorted list: `[1, 5, 9, 10, 11, 12, 13, 13, 15]`
- 8th element: `13`

---

## 2. Better Solution: Min-Heap (Priority Queue)

### **Core Idea:**
Use a min-heap to efficiently extract the smallest elements, leveraging the sorted nature of rows.

### **Algorithm:**
1. Initialize a min-heap.
2. Insert the first element of each row (or the first `min(k, n)` elements) along with their row and column indices.
3. Extract the smallest element from the heap.
4. When extracting an element, insert the next element in the same row (if any).
5. Repeat until the `k`th extraction, which will be the answer.

### **Java Code:**
```java
import java.util.PriorityQueue;

public class KthSmallestInMatrix {
    static class Cell {
        int value, row, col;
        Cell(int v, int r, int c) {
            value = v;
            row = r;
            col = c;
        }
    }

    public int kthSmallest(int[][] matrix, int k) {
        int n = matrix.length;
        PriorityQueue<Cell> minHeap = new PriorityQueue<>((a, b) -> a.value - b.value);

        // Push first element of each row into heap
        for (int r = 0; r < Math.min(n, k); r++) {
            minHeap.offer(new Cell(matrix[r][0], r, 0));
        }

        Cell current = null;
        for (int i = 0; i < k; i++) {
            current = minHeap.poll();
            int row = current.row;
            int col = current.col;

            // If next element in the same row exists, add it to heap
            if (col + 1 < n) {
                minHeap.offer(new Cell(matrix[row][col + 1], row, col + 1));
            }
        }
        return current.value;
    }
}
```

### **Complexity Analysis:**
- **Time Complexity:** `O(k log n)` because each of the `k` extractions involves heap operations of `O(log n)`.
- **Space Complexity:** `O(n)` for the heap.

### **Dry Run:**
- Input:
  ```
  matrix = [
    [1, 5, 9],
    [10, 11, 13],
    [12, 13, 15]
  ], k=8
  ```
- Initial heap: `[ (1,0,0), (10,1,0), (12,2,0) ]`
- Extract smallest: `(1,0,0)`; insert next in row 0: `(5,0,1)`
- Extract smallest: `(5,0,1)`; insert `(9,0,2)`
- Extract smallest: `(9,0,2)`; no more in row 0
- Extract `(10,1,0)`; insert `(11,1,1)`
- Extract `(11,1,1)`; insert `(13,1,2)`
- Extract `(12,2,0)`; insert `(13,2,1)`
- Extract `(13,1,2)`; insert `(15,1,3)` (out of bounds, ignore)
- Extract `(13,2,1)`; insert `(15,2,2)`
- Extract `(15,1,3)`; out of bounds, stop
- The last extracted element: `13` (the 8th smallest)

---

## 3. **Optimal Solution: Binary Search with Counting**

### **Core Idea:**
Use binary search on the value range rather than on indices. For each mid value, count how many elements in the matrix are less than or equal to mid, leveraging the sorted property.

### **Algorithm:**
1. Set `low` = smallest element in matrix (`matrix[0][0]`) and `high` = largest element (`matrix[n-1][n-1]`).
2. While `low < high`:
   - Compute `mid = (low + high) / 2`.
   - Count how many elements are `<= mid`.
   - If count `< k`, set `low = mid + 1`.
   - Else, set `high = mid`.
3. `low` will be the kth smallest element.

### **Java Code:**
```java
public class KthSmallestInMatrix {
    public int kthSmallest(int[][] matrix, int k) {
        int n = matrix.length;
        int low = matrix[0][0], high = matrix[n - 1][n - 1];

        while (low < high) {
            int mid = low + (high - low) / 2;
            int count = countLessEqual(matrix, mid);

            if (count < k) {
                low = mid + 1;
            } else {
                high = mid;
            }
        }
        return low;
    }

    private int countLessEqual(int[][] matrix, int mid) {
        int count = 0;
        int n = matrix.length;
        int row = n - 1;
        int col = 0;

        while (row >= 0 && col < n) {
            if (matrix[row][col] <= mid) {
                count += row + 1; // All elements in this column from row 0 to row
                col++;
            } else {
                row--;
            }
        }
        return count;
    }
}
```

### **Complexity Analysis:**
- **Time Complexity:** `O(n log(max - min))` because binary search runs `O(log(max - min))` times, and counting each time takes `O(n)`.
- **Space Complexity:** `O(1)`.

### **Dry Run:**
- Input:
  ```
  matrix = [
    [1, 5, 9],
    [10, 11, 13],
    [12, 13, 15]
  ], k=8
  ```
- Initial low=1, high=15
- mid=8:
  - Count elements `<=8`: 1 (row 0), 5 (row 0), 9 > 8, so count=2
  - count=2 < 8, set low=9
- mid=12:
  - Count elements `<=12`: 1,5,10,11,12,13,13,15 -> count=7
  - count=7 < 8, set low=13
- mid=14:
  - Count elements `<=14`: 1,5,10,11,12,13,13 -> count=7
  - count=7 < 8, low=15
- low=15, high=15, loop ends, answer=15

---

# Summary Table

| Approach                      | Strategy                              | Time Complexity                | Space Complexity | Remarks                                               |
|--------------------------------|---------------------------------------|--------------------------------|------------------|-------------------------------------------------------|
| Brute Force                   | Flatten + Sort                        | `O(n^2 log n)`                 | `O(n^2)`         | Simple, but inefficient for large `n`               |
| Min-Heap (Priority Queue)     | Use a Min-Heap with `k` elements     | `O(k log n)`                   | `O(n)`           | Efficient for small `k` values, leverages sorted rows |
| Binary Search with Counting   | Search by value range, count elements | `O(n log(max - min))`          | `O(1)`           | Most efficient for large `n`, relies on sorted matrix |

---

## Final Tips:
- Use **flattening** for small matrices or quick implementations.
- Use **min-heap** when you need a balance between simplicity and efficiency.
- Use **binary search** for the best performance in large, sorted matrices.

---

**Happy Revising!**
