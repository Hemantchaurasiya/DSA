# GCD Sort of an Array

---

## Problem Overview:
Given an array of integers, determine if it is possible to sort the array in non-decreasing order by swapping only elements whose values share a common factor greater than 1 (i.e., have a GCD > 1). The key idea is to identify whether elements can be connected through their common factors to enable swaps that ultimately sort the array.

---

## Approach 1: Brute Force

### Core Idea:
Check all pairs to find if a swap is possible based on GCD > 1, and perform swaps iteratively to attempt sorting the array. This is naive and inefficient.

### Algorithm:
1. For each pair of elements, check if their GCD > 1.
2. If yes, consider these elements connected and swap them to move towards sorting.
3. Repeat the process until the array is sorted or no further swaps are possible.

### Java Code:
```java
import java.util.*;

public class GCDSort {
    public boolean gcdSort(int[] arr) {
        int n = arr.length;
        boolean swapped;
        int[] sortedArr = arr.clone();
        Arrays.sort(sortedArr);
        
        // Repeat until no swaps or array is sorted
        do {
            swapped = false;
            for (int i = 0; i < n; i++) {
                for (int j = i + 1; j < n; j++) {
                    if (arr[i] > arr[j] && gcd(arr[i], arr[j]) > 1) {
                        // Swap
                        int temp = arr[i];
                        arr[i] = arr[j];
                        arr[j] = temp;
                        swapped = true;
                    }
                }
            }
        } while (swapped);
        return Arrays.equals(arr, sortedArr);
    }

    private int gcd(int a, int b) {
        if (b == 0) return a;
        return gcd(b, a % b);
    }
}
```

### Complexity Analysis:
- **Time Complexity:** **O(N^3 * log(max(arr)))** in worst case, due to nested loops and GCD calculations.
- **Space Complexity:** **O(N)** for array copying.

---

## Approach 2: Using Graph and Union-Find (Optimal)

### Core Idea:
Elements can be connected through common factors > 1. Model these connections as an undirected graph where:
- Each number is a node.
- An edge exists between two numbers if their GCD > 1.
- Use Union-Find to group connected elements.
- Check if, within each group, the elements can be permuted to match the sorted order.

### Algorithm:
1. **Identify connections:** For each pair of elements, compute GCD.
2. **Build Union-Find:** If GCD > 1, union their corresponding nodes.
3. **Group elements:** After union operations, group elements by their root parent.
4. **Compare with sorted array:** For each group, check if the sorted order of the group's elements matches the positions in the sorted array.
5. If all groups can be rearranged to match sorted order, return `true`, else `false`.

### Java Code:
```java
import java.util.*;

public class GCDSort {
    class UnionFind {
        int[] parent;
        public UnionFind(int n) {
            parent = new int[n];
            for (int i = 0; i < n; i++) parent[i] = i;
        }
        public int find(int x) {
            if (parent[x] != x) parent[x] = find(parent[x]);
            return parent[x];
        }
        public void union(int x, int y) {
            parent[find(x)] = find(y);
        }
    }

    public boolean gcdSort(int[] arr) {
        int n = arr.length;
        int[] sortedArr = arr.clone();
        Arrays.sort(sortedArr);
        UnionFind uf = new UnionFind(n);

        // Build graph connections based on GCD
        for (int i = 0; i < n; i++) {
            for (int j = i + 1; j < n; j++) {
                if (gcd(arr[i], arr[j]) > 1) {
                    uf.union(i, j);
                }
            }
        }

        // Map root to list of indices
        Map<Integer, List<Integer>> groups = new HashMap<>();
        for (int i = 0; i < n; i++) {
            int root = uf.find(i);
            groups.computeIfAbsent(root, k -> new ArrayList<>()).add(i);
        }

        // Check if each group's elements can be rearranged to match sorted order
        for (List<Integer> groupIndices : groups.values()) {
            List<Integer> originalValues = new ArrayList<>();
            List<Integer> sortedValues = new ArrayList<>();
            for (int index : groupIndices) {
                originalValues.add(arr[index]);
                sortedValues.add(sortedArr[index]);
            }
            Collections.sort(originalValues);
            if (!originalValues.equals(sortedValues)) {
                return false;
            }
        }
        return true;
    }

    private int gcd(int a, int b) {
        if (b == 0) return a;
        return gcd(b, a % b);
    }
}
```

### Complexity Analysis:
- **Time Complexity:** **O(N^2 * log(max(arr)))** due to pairwise GCD calculations and union operations.
- **Space Complexity:** **O(N)** for Union-Find and auxiliary data structures.

---

## Approach 3: Most Efficient (Using Prime Factors and Union-Find)

### Core Idea:
Instead of pairwise GCD checks, factorize each number into prime factors:
- Connect elements sharing the same prime factor.
- Use a map from prime factors to indices, and union all indices sharing a prime factor.

### Algorithm:
1. For each element:
   - Factorize into prime factors.
   - For each prime factor, union current index with all other indices sharing that prime factor.
2. After processing all elements, group indices by their root parent.
3. For each group, verify if the elements can be rearranged to match the sorted array.

### Java Code:
```java
import java.util.*;

public class GCDSort {
    class UnionFind {
        int[] parent;
        public UnionFind(int n) {
            parent = new int[n];
            for (int i = 0; i < n; i++) parent[i] = i;
        }
        public int find(int x) {
            if (parent[x] != x) parent[x] = find(parent[x]);
            return parent[x];
        }
        public void union(int x, int y) {
            parent[find(x)] = find(y);
        }
    }

    public boolean gcdSort(int[] arr) {
        int n = arr.length;
        int[] sortedArr = arr.clone();
        Arrays.sort(sortedArr);
        UnionFind uf = new UnionFind(n);

        Map<Integer, List<Integer>> primeFactorMap = new HashMap<>();
        for (int i = 0; i < n; i++) {
            List<Integer> primeFactors = factorize(arr[i]);
            for (int prime : primeFactors) {
                if (!primeFactorMap.containsKey(prime)) {
                    primeFactorMap.put(prime, new ArrayList<>());
                }
                for (int index : primeFactorMap.get(prime)) {
                    uf.union(i, index);
                }
                primeFactorMap.get(prime).add(i);
            }
        }

        // Group indices by root parent
        Map<Integer, List<Integer>> groups = new HashMap<>();
        for (int i = 0; i < n; i++) {
            int root = uf.find(i);
            groups.computeIfAbsent(root, k -> new ArrayList<>()).add(i);
        }

        // Verify if each group can be sorted to match the sorted array
        for (List<Integer> groupIndices : groups.values()) {
            List<Integer> originalValues = new ArrayList<>();
            List<Integer> sortedValues = new ArrayList<>();
            for (int index : groupIndices) {
                originalValues.add(arr[index]);
                sortedValues.add(sortedArr[index]);
            }
            Collections.sort(originalValues);
            if (!originalValues.equals(sortedValues)) {
                return false;
            }
        }
        return true;
    }

    private List<Integer> factorize(int num) {
        List<Integer> factors = new ArrayList<>();
        for (int i = 2; i * i <= num; i++) {
            while (num % i == 0) {
                factors.add(i);
                num /= i;
            }
        }
        if (num > 1) {
            factors.add(num);
        }
        return factors;
    }
}
```

### Complexity Analysis:
- **Time Complexity:** **O(N * sqrt(max(arr)))** for factorization + union operations.
- **Space Complexity:** **O(N + P)**, where P is the total number of prime factors.

---

## Summary:
- **Brute Force** is simple but inefficient.
- **Graph + Union-Find** approach reduces complexity by connecting elements based on GCD > 1.
- **Prime Factorization + Union-Find** is the most optimized, connecting elements sharing prime factors, drastically reducing pairwise checks.

Use this guide to understand different strategies for *GCD Sort of an Array*, and select the most efficient approach based on input constraints!
