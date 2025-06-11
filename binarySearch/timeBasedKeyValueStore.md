# Time Based Key-Value Store

---

## Problem Overview

Design a data structure that supports storing key-value pairs along with timestamps, and allows retrieval of the value associated with a key at a specific timestamp.

---

## Approaches to Solve the Problem

### 1. Brute Force Approach

### Core Idea

Store all key-value-timestamp entries in a simple list. To retrieve the value for a key at a specific timestamp, search through the list for the latest timestamp less than or equal to the given timestamp.

### Algorithm

- For each `set(key, value, timestamp)`, append `(key, value, timestamp)` to a list.
- For each `get(key, timestamp)`:
    - Iterate over all entries.
    - For entries matching the key, check if their timestamp <= given timestamp.
    - Keep track of the maximum timestamp satisfying the above.
    - Return the value associated with that maximum timestamp, or "" if none found.

### Java Code

```java
import java.util.*;

class TimeMap {
    List<Map.Entry<String, List<int[]>>> store;

    public TimeMap() {
        store = new ArrayList<>();
    }

    public void set(String key, String value, int timestamp) {
        // Store as list of [timestamp, value]
        store.add(new AbstractMap.SimpleEntry<>(key, new ArrayList<>(Arrays.asList(new int[]{timestamp, value.hashCode()}))));
    }

    public String get(String key, int timestamp) {
        String result = "";
        int maxTimestamp = -1;
        for (Map.Entry<String, List<int[]>> entry : store) {
            if (entry.getKey().equals(key)) {
                for (int[] tsVal : entry.getValue()) {
                    if (tsVal[0] <= timestamp && tsVal[0] > maxTimestamp) {
                        maxTimestamp = tsVal[0];
                        result = String.valueOf(tsVal[1]);
                    }
                }
            }
        }
        return result;
    }
}

```

*(Note: The above code is simplified; in practice, you'd store data differently)*

---

### Complexity Analysis

- **Time Complexity:**
    - `set()`: O(1)
    - `get()`: O(N) (scanning all entries)
- **Space Complexity:** O(N), where N is the number of entries stored.

---

### Dry Run

Suppose:

```
set("foo", "bar", 1)
set("foo", "baz", 3)
get("foo", 2)  // should return "bar"
get("foo", 3)  // should return "baz"

```

| Operation | Data Structure State | Result |
| --- | --- | --- |
| set("foo", "bar", 1) | `[("foo", [(1, "bar")])]` |  |
| set("foo", "baz", 3) | `[("foo", [(1, "bar"), (3, "baz")])]` |  |
| get("foo", 2) | Search: `(1, "bar")` matches timestamp <= 2, max timestamp=1, value="bar" | Returns `"bar"` |
| get("foo", 3) | Search: `(1, "bar")` and `(3, "baz")` timestamp <= 3, max=3, value="baz" | Returns `"baz"` |

---

### 2. Better Solution: Hash Map + Sorted List

### Core Idea

Store each key with a list of `(timestamp, value)` pairs sorted by timestamp. For retrieval, use binary search to find the latest timestamp less than or equal to the given timestamp.

### Algorithm

- For `set(key, value, timestamp)`:
    - Append `(timestamp, value)` to the list for the key.
- For `get(key, timestamp)`:
    - Use binary search on the list of `(timestamp, value)` pairs for the key to find the rightmost timestamp <= given timestamp.
    - Return the associated value or "" if none found.

### Java Code

```java
import java.util.*;

class TimeMap {
    private Map<String, List<int[]>> map;

    public TimeMap() {
        map = new HashMap<>();
    }

    public void set(String key, String value, int timestamp) {
        map.putIfAbsent(key, new ArrayList<>());
        map.get(key).add(new int[]{timestamp, value.hashCode()}); // Store value as hash code for simplicity
    }

    public String get(String key, int timestamp) {
        if (!map.containsKey(key)) return "";
        List<int[]> list = map.get(key);
        int left = 0, right = list.size() - 1;
        int resIndex = -1;

        while (left <= right) {
            int mid = left + (right - left) / 2;
            if (list.get(mid)[0] <= timestamp) {
                resIndex = mid;
                left = mid + 1; // search right for the latest timestamp
            } else {
                right = mid - 1;
            }
        }

        if (resIndex == -1) return "";
        return String.valueOf(list.get(resIndex)[1]);
    }
}

```

*(Note: For actual string storage, use a parallel list or a custom class instead of hash codes)*

---

### Complexity Analysis

- **Time Complexity:**
    - `set()`: O(1) amortized (append operation)
    - `get()`: O(log N) due to binary search, where N is number of timestamps for that key
- **Space Complexity:** O(N), where N is total number of set operations

---

### Dry Run

Input:

```
set("foo", "bar", 1)
set("foo", "baz", 3)
get("foo", 2)  // should return "bar"
get("foo", 3)  // should return "baz"

```

| Step | Data Structure State | Explanation |
| --- | --- | --- |
| set("foo", "bar", 1) | `{"foo": [(1, "bar")]}` | Append (1, "bar") |
| set("foo", "baz", 3) | `{"foo": [(1, "bar"), (3, "baz")]}` | Append (3, "baz") |
| get("foo", 2) | Binary search: mid=1, check timestamp 1 <= 2 → yes, resIndex=0 | Return value at index 0: "bar" |
| get("foo", 3) | Binary search: mid=1, check timestamp 3 <= 3 → yes, resIndex=1 | Return value at index 1: "baz" |

---

### 3. Most Optimal Solution: Hash Map + Binary Search with Built-in Data Structures

- Use a `HashMap<String, TreeMap<Integer, String>>`.
- `set()`: Insert into TreeMap.
- `get()`: Use `floorKey()` method to find the greatest key <= timestamp.

### Algorithm

- For `set(key, value, timestamp)`:
    - Insert `(timestamp, value)` into the TreeMap for the key.
- For `get(key, timestamp)`:
    - Retrieve the TreeMap.
    - Use `floorKey(timestamp)` to find the closest timestamp <= the given timestamp.
    - Return the corresponding value or "" if no such key exists.

### Java Code

```java
import java.util.*;

class TimeMap {
    private Map<String, TreeMap<Integer, String>> map;

    public TimeMap() {
        map = new HashMap<>();
    }

    public void set(String key, String value, int timestamp) {
        map.putIfAbsent(key, new TreeMap<>());
        map.get(key).put(timestamp, value);
    }

    public String get(String key, int timestamp) {
        if (!map.containsKey(key)) return "";
        TreeMap<Integer, String> treeMap = map.get(key);
        Integer floorKey = treeMap.floorKey(timestamp);
        return floorKey == null ? "" : treeMap.get(floorKey);
    }
}

```

---

### Complexity Analysis

- **Time Complexity:**
    - `set()`: O(log N) due to TreeMap insertion.
    - `get()`: O(log N) due to `floorKey()` operation.
- **Space Complexity:** O(N), total number of key-value-timestamp entries.

---

### Dry Run

Input:

```
set("foo", "bar", 1)
set("foo", "baz", 3)
get("foo", 2)  // should return "bar"
get("foo", 3)  // should return "baz"

```

| Operation | Data Structure State | Explanation |
| --- | --- | --- |
| set("foo", "bar", 1) | Map: `{"foo": {1: "bar"}}` | Insert timestamp 1 with value "bar" |
| set("foo", "baz", 3) | Map: `{"foo": {1: "bar", 3: "baz"}}` | Insert timestamp 3 with value "baz" |
| get("foo", 2) | `floorKey(2) = 1`, value = "bar" | Return "bar" |
| get("foo", 3) | `floorKey(3) = 3`, value = "baz" | Return "baz" |

---

## Summary Table

| Approach | Data Structures | Time Complexity (get) | Space Complexity | Notes |
| --- | --- | --- | --- | --- |
| Brute Force | List of all entries | O(N) | O(N) | Naive, slow, not suitable for large data |
| Sorted List + Binary Search | HashMap + List of `(timestamp, value)` | O(log N) | O(N) | Efficient, requires sorted insertion or appending |
| HashMap + TreeMap | HashMap + TreeMap | O(log N) | O(N) | Most optimal for retrieval with `floorKey()` method |

---

## Final Tips for Interviews

- Always consider the constraints: number of operations and data size.
- For fast retrieval, a `TreeMap` or binary search approach is preferred.
- Think about trade-offs: insertion complexity vs. retrieval speed.
- Practice edge cases:
    - When key does not exist.
    - When no timestamp <= given timestamp exists.

---

**End of Revision Note**
