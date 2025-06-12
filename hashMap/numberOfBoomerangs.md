# Number of Boomerangs

This guide provides a comprehensive overview of the **Number of Boomerangs** problem, starting from naive approaches to the most optimized solutions. It includes step-by-step algorithms, code snippets, complexity analysis, and dry runs for effective interview preparation and quick revision.

---

## 1. Brute Force Approach

### **Approach Name:**  
**Naive Pairwise Distance Calculation**

### **Core Idea:**  
For every point, calculate the distances to all other points. Count the number of pairs of points that are equidistant from the current point, since each such pair forms a boomerang with the current point.

### **Algorithm:**  
1. Initialize a counter `result = 0`.
2. For each point `i`:
   - For each other point `j`:
     - Calculate the squared distance between `points[i]` and `points[j]`.
     - Store the frequency of each distance in a hash map `distanceMap`.
   - For each distance in `distanceMap`:
     - If the frequency `f` > 1, then the number of boomerangs contributed by this distance is `f * (f - 1)` (permutations of pairs).
     - Add this to `result`.
3. Return `result`.

### **Java Code:**

```java
import java.util.*;

class Solution {
    public int numberOfBoomerangs(int[][] points) {
        int n = points.length;
        int result = 0;

        for (int i = 0; i < n; i++) {
            Map<Integer, Integer> distanceMap = new HashMap<>();

            for (int j = 0; j < n; j++) {
                if (i == j) continue;
                int dist = squaredDistance(points[i], points[j]);
                distanceMap.put(dist, distanceMap.getOrDefault(dist, 0) + 1);
            }

            for (int count : distanceMap.values()) {
                if (count > 1) {
                    result += count * (count - 1);
                }
            }
        }
        return result;
    }

    private int squaredDistance(int[] a, int[] b) {
        return (a[0] - b[0]) * (a[0] - b[0]) + (a[1] - b[1]) * (a[1] - b[1]);
    }
}
```

### **Complexity Analysis:**

- **Time Complexity:**  
  **O(n^2)**, since for each point, distances to all other points are computed.

- **Space Complexity:**  
  **O(n)**, for the hash map storing distances for each point (worst case).

### **Dry Run:**

Input: `points = [[0,0], [1,0], [2,0]]`

- For point `[0,0]`:
  - Distances:
    - To `[1,0]`: 1
    - To `[2,0]`: 4
  - Map: `{1:1, 4:1}` → no pairs with >1 frequency, result remains 0.
- For point `[1,0]`:
  - To `[0,0]`: 1
  - To `[2,0]`: 1
  - Map: `{1:2}` → pairs: 2 * 1 = 2 boomerangs
- For point `[2,0]`:
  - To `[0,0]`: 4
  - To `[1,0]`: 1
  - Map: `{1:1, 4:1}` → no pairs with >1 frequency.

Total boomerangs: 2

---

## 2. Improved Approach: Using a HashMap for Distance Frequencies

### **Approach Name:**  
**Use a HashMap for Distance Frequency Counting per Point**

### **Core Idea:**  
Same as above, but emphasizing that for each point, a fresh hash map is created to count distances, leading to clear separation of calculations for each point.

### **Algorithm:**  
Identical to the brute-force approach, but with clearer separation, emphasizing that the main logic remains the same.

### **Java Code:**

*(Same as above)*

---

## 3. Most Optimal Solution: Using Squared Distances and HashMap

### **Approach Name:**  
**Count Boomerangs Using Distance Map per Point**

### **Core Idea:**  
For each point, compute distances to all other points, count how many points share the same distance, and use combinatorial counting to calculate the number of boomerangs contributed by each distance group.

### **Algorithm:**  
1. For each point `i`, initialize a hash map `distanceCount`.
2. For each other point `j`, compute the squared distance.
3. Increment the count of points at that distance.
4. For each distance with count `f`, add `f * (f - 1)` to the answer.
5. Return the total count.

### **Java Code:**

*(Same as above)*

### **Complexity Analysis:**

- **Time Complexity:**  
  **O(n^2)**, for pairwise distance calculations.

- **Space Complexity:**  
  **O(n)**, for the distance count map per point.

---

## **Summary Table**

| Approach | Data Structures | Time Complexity | Space Complexity | Notes |
|------------|---------------------|-------------------|--------------------|--------|
| Naive Pairwise | HashMap for distances | O(n^2) | O(n) | Straightforward, clear logic |
| Distance Counting | HashMap per point | O(n^2) | O(n) | Similar to naive, emphasizes separation |
| Optimized Counting | Same as above | O(n^2) | O(n) | Efficient combinatorial counting |

---

## **Final Tips for Interviews:**
- Focus on the pairwise distance calculation and counting frequency of distances.
- Use squared distances to avoid floating-point inaccuracies.
- Remember that the number of boomerangs contributed by a group of `f` points at the same distance is `f * (f - 1)`.
- Edge cases: all points same, points on a line, minimal points.

---

This completes the detailed revision for **Number of Boomerangs**. Practice these approaches to efficiently solve similar pairwise counting problems involving distances and permutations!
