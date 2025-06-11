# Snapshot Array

This guide provides a comprehensive overview of the **Snapshot Array** problem, covering multiple approaches from brute-force to optimal solutions, complete with explanations, code, complexity analysis, and dry runs for better understanding.

---

## Problem Statement (Summary)

Implement a data structure that supports **the following operations**:

- `void set(index, val)`: Sets the element at `index` to `val`.
- `int snap()`: Takes a snapshot of the current array and returns a snapshot id.
- `int get(index, snap_id)`: Returns the value at `index` from the snapshot with id `snap_id`.

---

## 1. **Brute Force Approach**

### Core Idea

Maintain an array of arrays or list of copies; each snapshot is an independent copy of the entire array.

### Algorithm

- On each `snap()`, create a deep copy of the current array and store it in a list.
- On `get()`, directly access the stored snapshot at the given `snap_id`.
- On `set()`, update the current array.

### Java Code

```java
import java.util.*;

class SnapshotArray {
    private List<int[]> snapshots;
    private int[] currentArray;
    private int snapId;

    public SnapshotArray(int length) {
        currentArray = new int[length];
        snapshots = new ArrayList<>();
        snapId = 0;
    }

    public void set(int index, int val) {
        currentArray[index] = val;
    }

    public int snap() {
        snapshots.add(currentArray.clone());
        return snapId++;
    }

    public int get(int index, int snap_id) {
        return snapshots.get(snap_id)[index];
    }
}

```

### Complexity Analysis

- **Time Complexity**
    - `set()`: O(1)
    - `snap()`: O(n) (copy of entire array)
    - `get()`: O(1)
- **Space Complexity**: O(n * number_of_snapshots), as each snapshot stores a full copy of the array.

### Dry Run

Suppose:

```
length = 3
Operations:
set(0, 5)
set(1, 3)
snap() -> returns 0
set(0, 2)
get(0, 0) -> should return 5

```

Tracking:

| Operation | Array State | Snapshots | Snap ID | get(0, 0) |
| --- | --- | --- | --- | --- |
| set(0,5) | [5,0,0] | - | - | - |
| set(1,3) | [5,3,0] | - | - | - |
| snap() | - | [[5,3,0]] | 0 | - |
| set(0,2) | [2,3,0] | - | - | - |
| get(0,0) | - | - | - | 5 |

---

## 2. **Better Solution: Store Changes with Timestamps**

### Core Idea

Instead of copying entire array, store only changes with their corresponding snapshot id.

### Algorithm

- For each index, maintain a list of `(snap_id, value)` pairs, sorted by `snap_id`.
- On `set()`, update the list for that index with the current `snap_id`.
- On `snap()`, increment `snap_id`.
- On `get()`, perform a binary search on the list for that index to find the latest value with `snap_id` ≤ current.

### Java Code

```java
import java.util.*;

class SnapshotArray {
    private int snapId;
    private List<TreeMap<Integer, Integer>> indexMaps;

    public SnapshotArray(int length) {
        snapId = 0;
        indexMaps = new ArrayList<>();
        for (int i = 0; i < length; i++) {
            TreeMap<Integer, Integer> map = new TreeMap<>();
            map.put(0, 0); // default value
            indexMaps.add(map);
        }
    }

    public void set(int index, int val) {
        indexMaps.get(index).put(snapId, val);
    }

    public int snap() {
        return snapId++;
    }

    public int get(int index, int snap_id) {
        TreeMap<Integer, Integer> map = indexMaps.get(index);
        // Find the greatest key <= snap_id
        Integer key = map.floorKey(snap_id);
        return map.get(key);
    }
}

```

### Complexity Analysis

- **Time Complexity**
    - `set()`: O(log n) (TreeMap insertion)
    - `snap()`: O(1)
    - `get()`: O(log n) (binary search in TreeMap)
- **Space Complexity**: O(n * m), where `m` is the number of snapshots, as each index stores multiple `(snap_id, value)` pairs.

### Dry Run

Suppose:

```
length = 2
Operations:
set(0, 5)
set(1, 10)
snap() -> 0
set(0, 6)
get(0, 0) -> should return 5
get(1, 0) -> should return 10

```

Tracking:

| Index | Changes (snap_id -> value) | After operations |
| --- | --- | --- |
| 0 | {0: 5} (initial), {0: 6} after set(0,6) |  |
| 1 | {0: 10} |  |

Queries:

- `get(0, 0)` -> floorKey(0) in index 0's map is 0 → value 5
- `get(1, 0)` -> floorKey(0) in index 1's map is 0 → value 10

---

## 3. **Most Optimal Approach: Using Binary Search with Versioned Storage**

### Core Idea

Leverage the second approach but optimize data structures for faster lookups, usually via **list of snapshots** with binary searches.

### Algorithm

- For each index, store a list of `(snap_id, value)` pairs.
- On `set()`, append the new `(current_snap_id, val)` to the index's list.
- On `snap()`, just increment the `snap_id`.
- On `get()`, perform a binary search on the index's list to find the value with the largest `snap_id` ≤ requested `snap_id`.

### Java Code

```java
import java.util.*;

class SnapshotArray {
    private int snapId;
    private List<List<Pair>> snaps;

    private static class Pair {
        int snapId;
        int val;

        Pair(int snapId, int val) {
            this.snapId = snapId;
            this.val = val;
        }
    }

    public SnapshotArray(int length) {
        snapId = 0;
        snaps = new ArrayList<>();
        for (int i = 0; i < length; i++) {
            List<Pair> list = new ArrayList<>();
            list.add(new Pair(0, 0)); // default value
            snaps.add(list);
        }
    }

    public void set(int index, int val) {
        List<Pair> list = snaps.get(index);
        list.add(new Pair(snapId, val));
    }

    public int snap() {
        return snapId++;
    }

    public int get(int index, int snap_id) {
        List<Pair> list = snaps.get(index);
        int left = 0, right = list.size() - 1;
        int res = 0;
        while (left <= right) {
            int mid = left + (right - left) / 2;
            if (list.get(mid).snapId <= snap_id) {
                res = list.get(mid).val;
                left = mid + 1;
            } else {
                right = mid - 1;
            }
        }
        return res;
    }
}

```

### Complexity Analysis

- **Time Complexity**
    - `set()`: O(1) amortized (append operation)
    - `snap()`: O(1)
    - `get()`: O(log n) (binary search)
- **Space Complexity**: O(n * m), where `m` is the number of snaps, as each index can have up to `m` entries.

### Dry Run

Input:

```
length = 2
Operations:
set(0, 5)
set(1, 10)
snap() -> 0
set(0, 6)
get(0, 0) -> 5
get(1, 0) -> 10

```

Tracking:

| Index | Changes (snap_id, value) | After operations |
| --- | --- | --- |
| 0 | (0,5), (0,6) |  |
| 1 | (0,10) |  |

Queries:

- `get(0, 0)` -> binary search finds `(0,5)`, returns 5.
- `get(1, 0)` -> binary search finds `(0,10)`, returns 10.

---

## Final Tips for Interview Preparation

- Understand the trade-offs: full copying (inefficient), differential storage with binary search (more efficient).
- Practice binary search on custom data structures.
- Focus on optimizing space and time depending on constraints.

---

This concludes the detailed revision note for **Snapshot Array**. Use this as a quick reference for understanding different approaches and their implementation nuances.
