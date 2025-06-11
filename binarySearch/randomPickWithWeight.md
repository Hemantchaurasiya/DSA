# Random Pick with Weight

This problem involves selecting an index randomly, where each index has a probability proportional to its weight. It is a classic problem illustrating probabilistic selection and efficient data structures.

---

## 1. Brute Force Approach

### **Core Idea:**

Use a simple method to select an index based on weights by expanding the weights into a list and randomly choosing an index.

### **Algorithm:**

1. Given an array of weights `weights`.
2. Compute the total sum of weights.
3. Generate a random number `rand` between `1` and `sum of weights`.
4. Loop through the weights and keep a cumulative sum.
5. Return the index where `rand` is less than or equal to the cumulative sum.

### **Java Code:**

```java
import java.util.Random;

public class Solution {
    private int[] prefixSums;
    private int totalSum;
    private Random rand = new Random();

    public Solution(int[] weights) {
        int n = weights.length;
        prefixSums = new int[n];
        int sum = 0;
        for (int i = 0; i < n; i++) {
            sum += weights[i];
            prefixSums[i] = sum;
        }
        totalSum = sum;
    }

    public int pickIndex() {
        int randVal = rand.nextInt(totalSum) + 1;
        for (int i = 0; i < prefixSums.length; i++) {
            if (randVal <= prefixSums[i]) {
                return i;
            }
        }
        return -1; // Should never reach here
    }
}

```

### **Complexity Analysis:**

- **Time Complexity:**
    - Constructor: O(N) (to build prefix sums)
    - `pickIndex`: O(N) (to iterate through prefix sums)
- **Space Complexity:**
    - O(N) (for prefix sums array)

---

## 2. Better Solution: Using Binary Search

### **Core Idea:**

Optimize the `pickIndex` operation by using binary search on the prefix sums instead of linear search, reducing the lookup time.

### **Algorithm:**

1. Precompute prefix sums of weights.
2. Generate a random number `rand` between `1` and total sum.
3. Use binary search on the prefix sums array to find the smallest index `i` such that `prefixSums[i] >= rand`.
4. Return index `i`.

### **Java Code:**

```java
import java.util.Random;

public class Solution {
    private int[] prefixSums;
    private int totalSum;
    private Random rand = new Random();

    public Solution(int[] weights) {
        int n = weights.length;
        prefixSums = new int[n];
        int sum = 0;
        for (int i = 0; i < n; i++) {
            sum += weights[i];
            prefixSums[i] = sum;
        }
        totalSum = sum;
    }

    public int pickIndex() {
        int randVal = rand.nextInt(totalSum) + 1;
        // Binary search for randVal in prefixSums
        int low = 0, high = prefixSums.length - 1;
        while (low < high) {
            int mid = low + (high - low) / 2;
            if (prefixSums[mid] < randVal) {
                low = mid + 1;
            } else {
                high = mid;
            }
        }
        return low;
    }
}

```

### **Complexity Analysis:**

- **Time Complexity:**
    - Constructor: O(N)
    - `pickIndex`: O(log N) (binary search)
- **Space Complexity:**
    - O(N)

---

## 3. Most Optimal Solution: Segment Tree / Binary Indexed Tree (Fenwick Tree)

### **Core Idea:**

Use advanced data structures like Segment Tree or Fenwick Tree to achieve efficient updates and querying, especially beneficial if the weights change dynamically.

### **Algorithm (Fenwick Tree example):**

1. Build a Fenwick Tree (Binary Indexed Tree) for prefix sums.
2. To pick an index, generate a random number `rand`.
3. Use Fenwick Tree to find the index corresponding to `rand` via prefix sums.
4. Return that index.

### **Java Code (Fenwick Tree):**

```java
import java.util.Random;

public class Solution {
    private int[] fenwickTree;
    private int n;
    private int totalSum;
    private Random rand = new Random();

    public Solution(int[] weights) {
        n = weights.length;
        fenwickTree = new int[n + 1]; // 1-based indexing
        for (int i = 0; i < n; i++) {
            update(i + 1, weights[i]);
        }
        totalSum = fenwickTree[n];
    }

    private void update(int i, int val) {
        while (i <= n) {
            fenwickTree[i] += val;
            i += i & (-i);
        }
    }

    private int prefixSum(int i) {
        int sum = 0;
        while (i > 0) {
            sum += fenwickTree[i];
            i -= i & (-i);
        }
        return sum;
    }

    public int pickIndex() {
        int randVal = rand.nextInt(totalSum) + 1;
        int index = 0;
        int bitMask = 1 << (31 - Integer.numberOfLeadingZeros(n));
        int sum = 0;

        for (int i = bitMask; i > 0; i >>= 1) {
            int nextIndex = index + i;
            if (nextIndex <= n && fenwickTree[nextIndex] < randVal) {
                randVal -= fenwickTree[nextIndex];
                index = nextIndex;
            }
        }
        return index;
    }
}

```

### **Complexity Analysis:**

- **Time Complexity:**
    - Constructor: O(N log N) (building Fenwick Tree)
    - `pickIndex`: O(log N)
- **Space Complexity:**
    - O(N)

---

## **Summary Table**

| Approach | Construction Time | `pickIndex` Time | Space Complexity | Notes |
| --- | --- | --- | --- | --- |
| Brute Force | O(N) | O(N) | O(N) | Linear search, simple but inefficient for large data |
| Binary Search | O(N) | O(log N) | O(N) | More efficient lookup, suitable for static weights |
| Fenwick Tree / Segment Tree | O(N log N) | O(log N) | O(N) | Best for dynamic weights, complex implementation |

---

## **Dry Run Example**

Suppose `weights = [1, 3, 2, 4]`

### Step 1: Prefix sums

```
Index:        0   1   2   3
weights:      1   3   2   4
prefixSums:   [1, 4, 6, 10]
totalSum = 10

```

### Step 2: Random pick

- Generate `rand` between 1 and 10.
- For example, `rand = 5`.

### Step 3: Binary Search

- Find the smallest `i` such that `prefixSums[i] >= rand`.
- `prefixSums`: [1, 4, 6, 10]
- `rand = 5`:
    - Binary search:
        - mid = 2, prefixSums[2] = 6 >= 5 → high=2
        - mid = 1, prefixSums[1] = 4 < 5 → low=2
    - Result: index 2 (since 0-based), corresponding to weight 2.

### **Result:**

- The index `2` is returned, which has weight 2.

---

## **Final Notes:**

- Use binary search for efficient lookups after prefix sum calculation.
- For dynamic updates to weights, Fenwick Tree or Segment Tree is preferred.
- Always handle edge cases (e.g., empty array, zero weights) as per problem constraints.

---

This concludes the comprehensive revision for **Random Pick with Weight** problem!
