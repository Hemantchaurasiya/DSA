# K-th Smallest Prime Fraction

This problem involves finding the **k-th smallest prime fraction** formed from a sorted array of prime numbers. The fractions are formed as `arr[i] / arr[j]` with `i < j`. The challenge is to efficiently identify the desired fraction without enumerating all possibilities.

---

## 1. Brute Force Approach

### **Approach Name:**  
**Enumerate All Fractions + Sorting**

### **Core Idea:**  
Generate all possible fractions, sort them, and select the k-th smallest.

### **Algorithm:**  
1. Iterate over all pairs `(i, j)` where `i < j`.
2. Calculate the fraction `arr[i] / arr[j]`.
3. Store each fraction along with indices `(i, j)` in a list.
4. Sort the list based on the fraction value.
5. Return the fraction at position `k-1`.

### **Java Code:**  
```java
import java.util.ArrayList;
import java.util.Collections;
import java.util.List;

public class KthSmallestPrimeFraction {
    public int[] kthSmallestPrimeFraction(int[] arr, int k) {
        List<Fraction> fractions = new ArrayList<>();
        int n = arr.length;
        for (int i = 0; i < n; i++) {
            for (int j = i + 1; j < n; j++) {
                fractions.add(new Fraction(arr[i], arr[j]));
            }
        }
        Collections.sort(fractions);
        Fraction result = fractions.get(k - 1);
        return new int[]{result.numerator, result.denominator};
    }

    private static class Fraction implements Comparable<Fraction> {
        int numerator, denominator;
        Fraction(int num, int den) {
            this.numerator = num;
            this.denominator = den;
        }
        public int compareTo(Fraction other) {
            return Integer.compare(this.numerator * other.denominator, other.numerator * this.denominator);
        }
    }
}
```

### **Complexity Analysis:**  
- **Time Complexity:** `O(n^2 log(n^2))` due to generating and sorting all fractions.  
- **Space Complexity:** `O(n^2)` for storing all fractions.

### **Dry Run:**  
- Input: `arr = [1, 2, 3, 5], k=3`  
- Fractions: `[1/2, 1/3, 1/5, 2/3, 2/5, 3/5]`  
- Sorted: `[1/5, 1/3, 1/2, 2/5, 2/3, 3/5]`  
- K=3: fraction `1/2`

---

## 2. Better Solution: Binary Search on Fraction Value

### **Approach Name:**  
**Binary Search on the Fraction Value**

### **Core Idea:**  
Use binary search over the range of possible fraction values (between 0 and 1). For each mid value, count how many fractions are less than or equal to it, to narrow down to the k-th smallest.

### **Algorithm:**  
1. Set `low=0.0`, `high=1.0`.
2. While `high - low > epsilon` (for precision):
   - Compute `mid = (low + high) / 2`.
   - Count how many fractions `<= mid`.
   - If count `< k`, set `low = mid`.
   - Else, set `high = mid`.
3. After binary search converges, find the closest fraction `<= high` with the highest value.

### **Java Code:**  
```java
public class KthSmallestPrimeFraction {
    public int[] kthSmallestPrimeFraction(int[] arr, int k) {
        int n = arr.length;
        double low = 0.0, high = 1.0;
        double epsilon = 1e-9;
        int numerator = 0, denominator = 1;

        while (high - low > epsilon) {
            double mid = (low + high) / 2;
            int count = 0;
            int maxNumerator = 0;
            int maxDenominator = 1;
            int j = 0;

            // Count fractions <= mid
            for (int i = 0; i < n; i++) {
                while (j < n && (double)arr[i] / arr[j] > mid) j++;
                if (j == n) break;
                count += (n - j);
                // Track the closest fraction <= mid
                if ((double)arr[i] / arr[j] > (double)maxNumerator / maxDenominator) {
                    maxNumerator = arr[i];
                    maxDenominator = arr[j];
                }
            }

            if (count < k) {
                low = mid;
            } else {
                // Save the best numerator and denominator for final answer
                numerator = maxNumerator;
                denominator = maxDenominator;
                high = mid;
            }
        }
        return new int[]{numerator, denominator};
    }
}
```

### **Complexity Analysis:**  
- **Time Complexity:** `O(n log(1/epsilon))` due to binary search iterations, each counting fractions in `O(n)`.  
- **Space Complexity:** `O(1)`.

### **Dry Run:**  
- Input: `arr=[1,2,3,5], k=3`  
- Binary search narrows down the value between 0 and 1, repeatedly counting fractions `<= mid`.  
- Ultimately, it identifies the fraction `2/5` as the 3rd smallest.

---

## 3. **Most Optimal Solution: Binary Search + Two Pointers / Greedy Counting**

### **Approach Name:**  
**Binary Search coupled with Two Pointers for Counting**

### **Core Idea:**  
Combine binary search on the fraction value with a two-pointer approach to efficiently count how many fractions are `<= mid` and track the maximum such fraction.

### **Algorithm:**  
1. Set `low=0.0`, `high=1.0`.  
2. While `high - low > epsilon`:
   - Calculate `mid`.
   - Use two pointers:
     - For each `i`, move `j` from previous position to find the largest `j` where `arr[i]/arr[j] <= mid`.
     - Keep track of the maximum fraction `<= mid`.
3. Narrow down based on the count of fractions `<= mid`.
4. Return the fraction corresponding to the maximum found during the binary search.

### **Java Code:**  
```java
public class KthSmallestPrimeFraction {
    public int[] kthSmallestPrimeFraction(int[] arr, int k) {
        int n = arr.length;
        double low = 0.0, high = 1.0;
        double epsilon = 1e-9;
        int num = 0, den = 1;

        while (high - low > epsilon) {
            double mid = (low + high) / 2;
            int count = 0;
            int maxNum = 0;
            int maxDen = 1;
            int j = 0;

            for (int i = 0; i < n; i++) {
                while (j < n && (double)arr[i] / arr[j] > mid) j++;
                if (j == n) break;
                count += (n - j);
                // Track max fraction <= mid
                if ((double)arr[i] / arr[j] > (double)maxNum / maxDen) {
                    maxNum = arr[i];
                    maxDen = arr[j];
                }
            }

            if (count < k) {
                low = mid;
            } else {
                num = maxNum;
                den = maxDen;
                high = mid;
            }
        }
        return new int[]{num, den};
    }
}
```

### **Complexity Analysis:**  
- **Time Complexity:** `O(n log(1/epsilon))`.  
- **Space Complexity:** `O(1)`.

### **Dry Run:**  
- Input: `[1,2,3,5], k=3`  
- Binary search converges to fractions close to `2/5`.  
- Final answer: `[2,5]`.

---

# Summary Table

| Approach                                                  | Strategy                                    | Time Complexity                   | Space Complexity | Remarks                                               |
|-----------------------------------------------------------|--------------------------------------------|-----------------------------------|------------------|--------------------------------------------------------|
| Enumerate All Fractions + Sorting                        | Generate all, sort, pick kth             | `O(n^2 log n^2)`                  | `O(n^2)`         | Inefficient for large `n`                              |
| Binary Search on Fraction Value                            | Search in [0,1], count fractions <= mid | `O(n log(1/epsilon))`             | `O(1)`           | Good for large `n`, precision-based                    |
| Binary Search + Two Pointers / Greedy Counting            | Efficient counting within binary search | Same as above, optimized counting | `O(1)`           | Most efficient, combines binary search with two pointers |

---

## Final Tips:
- For large input sizes, prefer **binary search with two pointers** for efficiency.
- Use **precision (`epsilon`)** in binary search to balance accuracy and performance.
- Always keep track of the **max fraction <= mid** during counting to identify the answer.

---

**Happy Revising!**
