
# Conversions

## ✅ 1. Adjacency List ➡️ Adjacency Matrix

### 🔹 Core Idea

- Use a `V x V` matrix initialized to `0`.
- For each node `u`, for every neighbor `v` in `adjList.get(u)`, set `matrix[u][v] = 1`.

### 🔹 Java Code

```java
java
CopyEdit
int[][] adjListToMatrix(List<List<Integer>> adjList) {
    int V = adjList.size();
    int[][] matrix = new int[V][V];

    for (int u = 0; u < V; u++) {
        for (int v : adjList.get(u)) {
            matrix[u][v] = 1;
        }
    }
    return matrix;
}

```

### 🔹 Time & Space Complexity

- Time: O(V + E)
- Space: O(V²)

---

## ✅ 2. Adjacency List ➡️ Edge List

### 🔹 Core Idea

- Flatten the adjacency list: for each `(u, v)` pair in the list, add to edge list.

### 🔹 Java Code

```java
java
CopyEdit
List<int[]> adjListToEdgeList(List<List<Integer>> adjList) {
    List<int[]> edgeList = new ArrayList<>();

    for (int u = 0; u < adjList.size(); u++) {
        for (int v : adjList.get(u)) {
            edgeList.add(new int[]{u, v});
        }
    }
    return edgeList;
}

```

### 🔹 Time & Space Complexity

- Time: O(V + E)
- Space: O(E)

---

## ✅ 3. Adjacency Matrix ➡️ Adjacency List

### 🔹 Core Idea

- For every `matrix[i][j] == 1`, add `j` to `adjList.get(i)`.

### 🔹 Java Code

```java
java
CopyEdit
List<List<Integer>> matrixToAdjList(int[][] matrix) {
    int V = matrix.length;
    List<List<Integer>> adjList = new ArrayList<>();

    for (int i = 0; i < V; i++) {
        adjList.add(new ArrayList<>());
        for (int j = 0; j < V; j++) {
            if (matrix[i][j] == 1) {
                adjList.get(i).add(j);
            }
        }
    }
    return adjList;
}

```

### 🔹 Time & Space Complexity

- Time: O(V²)
- Space: O(V + E)

---

## ✅ 4. Edge List ➡️ Adjacency List

### 🔹 Core Idea

- For each edge `(u, v)`, add `v` to `adjList[u]`.

### 🔹 Java Code

```java
java
CopyEdit
List<List<Integer>> edgeListToAdjList(List<int[]> edgeList, int V) {
    List<List<Integer>> adjList = new ArrayList<>();
    for (int i = 0; i < V; i++) adjList.add(new ArrayList<>());

    for (int[] edge : edgeList) {
        int u = edge[0], v = edge[1];
        adjList.get(u).add(v);
    }
    return adjList;
}

```

### 🔹 Time & Space Complexity

- Time: O(E)
- Space: O(V + E)

## ✅ 5. Edge List ➡️ Adjacency Matrix

### 🔹 Core Idea

For each edge `(u, v)`, mark `matrix[u][v] = 1`.

### 🔹 Use Case

Quick edge checks from a raw edge input.

### 🔹 Algorithm

- Create matrix.
- For each `(u, v)`, update `matrix[u][v] = 1`.

### 🔹 Java Code

```java
java
CopyEdit
int[][] edgeListToMatrix(List<int[]> edgeList, int V) {
    int[][] matrix = new int[V][V];

    for (int[] edge : edgeList) {
        int u = edge[0], v = edge[1];
        matrix[u][v] = 1;
    }
    return matrix;
}

```

### 🔹 Time & Space Complexity

- Time: O(E)
- Space: O(V²)

## ✅ 6. Adjacency Matrix ➡️ Edge List

### 🔹 Core Idea

For every `matrix[i][j] == 1`, add edge `(i, j)`.

### 🔹 Use Case

Useful for quickly converting to a format usable by Kruskal’s or Prim’s algorithm.

### 🔹 Algorithm

- Iterate over all `i, j`.
- If matrix entry is `1`, store `(i, j)`.

### 🔹 Java Code

```java
java
CopyEdit
List<int[]> matrixToEdgeList(int[][] matrix) {
    List<int[]> edgeList = new ArrayList<>();
    int V = matrix.length;

    for (int i = 0; i < V; i++) {
        for (int j = 0; j < V; j++) {
            if (matrix[i][j] == 1) {
                edgeList.add(new int[]{i, j});
            }
        }
    }
    return edgeList;
}

```

### 🔹 Time & Space Complexity

- Time: O(V²)
- Space: O(E)
