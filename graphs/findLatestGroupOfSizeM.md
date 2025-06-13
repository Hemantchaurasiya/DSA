# Find Latest Group of Size M

---

## Problem Overview:
Given a sequence of **N** elements (e.g., user IDs, events, or integers), process a series of **union operations** that connect different elements. The goal is to **find the size of the latest (most recent) group** that has exactly **size M** **after each union operation**.

---

## Approach 1: Brute Force

### Core Idea:
Maintain explicit groups or connected components, and after each union, recompute the sizes of all groups to identify if any has size M.

### Algorithm:
1. **Initialize** each element as its own group (disjoint sets).
2. **Maintain** a data structure (like a list of sets) to keep track of groups.
3. For each union operation:
   - Find the groups containing the two elements.
   - Merge the two groups.
   - After merging, iterate over all groups to find if any group has size M.
   - Record whether such a group exists and its latest position (or just the size).

### Java Code:
```java
import java.util.*;

public class FindLatestGroupOfSizeM {
    public List<Integer> findLatestGroup(int n, int m, int[][] unions) {
        List<Set<Integer>> groups = new ArrayList<>();
        Map<Integer, Set<Integer>> elementToGroup = new HashMap<>();
        List<Integer> result = new ArrayList<>();
        int latestPosition = -1; // To track latest occurrence

        for (int i = 0; i < n; i++) {
            // Initialize each element in its own group
            Set<Integer> newGroup = new HashSet<>();
            newGroup.add(i);
            groups.add(newGroup);
            elementToGroup.put(i, newGroup);
        }

        for (int i = 0; i < unions.length; i++) {
            int a = unions[i][0];
            int b = unions[i][1];

            Set<Integer> groupA = elementToGroup.get(a);
            Set<Integer> groupB = elementToGroup.get(b);

            if (groupA != groupB) {
                // Merge groups
                groupA.addAll(groupB);
                for (int elem : groupB) {
                    elementToGroup.put(elem, groupA);
                }
                groups.remove(groupB);
            }
            // Check for group of size M
            boolean found = false;
            for (Set<Integer> g : groups) {
                if (g.size() == m) {
                    latestPosition = i; // record latest position
                    found = true;
                }
            }
            result.add(found ? latestPosition + 1 : -1);
        }
        return result;
    }
}
```

### Complexity Analysis:
- **Time Complexity:** **O(N * G)**, where G is the number of groups; with frequent merging, this could be costly.
- **Space Complexity:** **O(N)** for storing groups and mappings.

---

## Approach 2: Using Union-Find (Disjoint Set Union - DSU)

### Core Idea:
Use **Union-Find data structure** to efficiently **merge sets** and keep track of **group sizes** dynamically. After each union, check if any group has size **M**.

### Algorithm:
1. Initialize **Union-Find** with **N** elements, each in its own set with size 1.
2. Maintain a **frequency map** of group sizes.
3. For each union operation:
   - **Union** the two elements' sets.
   - **Update** the sizes in the map:
     - Remove the old sizes of the two sets.
     - Add the new merged size.
   - Check if **size M** exists in the map.
   - Record the **latest position** where size M exists.

### Java Code:
```java
import java.util.*;

public class FindLatestGroupOfSizeM {
    class UnionFind {
        int[] parent;
        int[] size;
        Map<Integer, Integer> sizeCount;

        public UnionFind(int n) {
            parent = new int[n];
            size = new int[n];
            sizeCount = new HashMap<>();
            for (int i = 0; i < n; i++) {
                parent[i] = i;
                size[i] = 1;
                sizeCount.put(1, sizeCount.getOrDefault(1, 0) + 1);
            }
        }

        public int find(int x) {
            if (parent[x] != x) {
                parent[x] = find(parent[x]);
            }
            return parent[x];
        }

        public boolean union(int x, int y, int m, int[] result, int index) {
            int rootX = find(x);
            int rootY = find(y);
            if (rootX == rootY) return false;

            // Remove old sizes from count map
            decrementSizeCount(size[rootX]);
            decrementSizeCount(size[rootY]);

            // Union by size
            if (size[rootX] < size[rootY]) {
                parent[rootX] = rootY;
                size[rootY] += size[rootX];
                incrementSizeCount(size[rootY]);
            } else {
                parent[rootY] = rootX;
                size[rootX] += size[rootY];
                incrementSizeCount(size[rootX]);
            }

            // Check if size M exists
            if (sizeCount.getOrDefault(m, 0) > 0) {
                result[index] = index + 1; // Latest position
            } else {
                result[index] = -1;
            }

            return true;
        }

        private void decrementSizeCount(int sizeVal) {
            int count = sizeCount.get(sizeVal);
            if (count == 1) {
                sizeCount.remove(sizeVal);
            } else {
                sizeCount.put(sizeVal, count - 1);
            }
        }

        private void incrementSizeCount(int sizeVal) {
            sizeCount.put(sizeVal, sizeCount.getOrDefault(sizeVal, 0) + 1);
        }
    }

    public List<Integer> findLatestGroup(int n, int m, int[][] unions) {
        UnionFind uf = new UnionFind(n);
        int[] result = new int[unions.length];

        for (int i = 0; i < unions.length; i++) {
            uf.union(unions[i][0], unions[i][1], m, result, i);
        }

        List<Integer> output = new ArrayList<>();
        for (int res : result) {
            output.add(res);
        }
        return output;
    }
}
```

### Complexity Analysis:
- **Time Complexity:** **O(N * α(N))** per union (amortized), where α(N) is the inverse Ackermann function.
- **Space Complexity:** **O(N)** for parent, size arrays, and maps.

---

## Approach 3: Most Optimal Solution

### Core Idea:
- Use **Union-Find** with **size tracking** and **efficient size count updates**.
- After each union, quickly determine if any group has size **M** by maintaining a **global count** of groups of size M.

### Algorithm:
- Similar to Approach 2, but optimize by maintaining a **global counter** for the number of groups of size M.
- When a group's size changes:
  - If it was previously size M, decrement the counter.
  - If it becomes size M, increment the counter.
- The latest union operation where the counter > 0 indicates the latest position.

### Java Code:
*(Refer to Approach 2, with added global counter management)*

```java
import java.util.*;

public class FindLatestGroupOfSizeM {
    class UnionFind {
        int[] parent;
        int[] size;
        int countSizeM; // Global count of groups with size M

        public UnionFind(int n, int m) {
            parent = new int[n];
            size = new int[n];
            for (int i = 0; i < n; i++) {
                parent[i] = i;
                size[i] = 1;
            }
            countSizeM = n; // Initially, all are size 1
        }

        public int find(int x) {
            if (parent[x] != x)
                parent[x] = find(parent[x]);
            return parent[x];
        }

        public boolean union(int x, int y, int m, int[] result, int index) {
            int rootX = find(x);
            int rootY = find(y);
            if (rootX == rootY) {
                result[index] = (countSizeM > 0) ? index + 1 : -1;
                return false;
            }

            // Before union, check if sizes are size M
            if (size[rootX] == m) countSizeM--;
            if (size[rootY] == m) countSizeM--;

            // Union by size
            if (size[rootX] < size[rootY]) {
                parent[rootX] = rootY;
                size[rootY] += size[rootX];
                if (size[rootY] == m) countSizeM++;
            } else {
                parent[rootY] = rootX;
                size[rootX] += size[rootY];
                if (size[rootX] == m) countSizeM++;
            }

            result[index] = (countSizeM > 0) ? index + 1 : -1;
            return true;
        }
    }

    public List<Integer> findLatestGroup(int n, int m, int[][] unions) {
        UnionFind uf = new UnionFind(n, m);
        int[] result = new int[unions.length];

        for (int i = 0; i < unions.length; i++) {
            uf.union(unions[i][0], unions[i][1], m, result, i);
        }

        List<Integer> output = new ArrayList<>();
        for (int res : result) {
            output.add(res);
        }
        return output;
    }
}
```

### Complexity Analysis:
- **Time Complexity:** **O(N * α(N))**, where **α(N)** is the inverse Ackermann function.
- **Space Complexity:** **O(N)** for parent and size arrays.

---

## Summary:
- **Brute Force** is simple but inefficient for large inputs.
- **Union-Find (Disjoint Set Union)** provides an **efficient** way to dynamically manage groups and sizes.
- **Maintaining a global counter** for groups of size M yields the **most optimal** solution for quick updates and retrieval.

---

Use this guide to understand and implement solutions efficiently for "Find Latest Group of Size M" and similar dynamic connectivity problems!
