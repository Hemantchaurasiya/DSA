# Rotate Image In-Place

This problem asks us to rotate a given n x n 2D matrix by 90 degrees clockwise **in-place**, i.e., without using extra space for another matrix.

---

## Approach 1: Brute Force (Using Extra Space)

### Algorithm:

- Create a new matrix `res` of the same size as the original.
- For each element `(i, j)` in the original matrix:
    - Place it in the position `(j, n - 1 - i)` in the new matrix.
- Copy the `res` matrix back into the original matrix.

### Java Code:

```java
public void rotate(int[][] matrix) {
    int n = matrix.length;
    int[][] res = new int[n][n];

    for (int i = 0; i < n; i++) {
        for (int j = 0; j < n; j++) {
            res[j][n - 1 - i] = matrix[i][j];
        }
    }

    // Copy back to original matrix
    for (int i = 0; i < n; i++) {
        for (int j = 0; j < n; j++) {
            matrix[i][j] = res[i][j];
        }
    }
}

```

### Complexity Analysis:

- **Time Complexity:** `O(n^2)` because we traverse all elements once.
- **Space Complexity:** `O(n^2)` due to the auxiliary matrix `res`.

### Dry Run:

| Input matrix (n=3): | 1 2 3 |<br> | 4 5 6 |<br> | 7 8 9 |<br>

|---------------------|-------|-------|-------|

| After rotation in `res`: | 7 4 1 |<br> | 8 5 2 |<br> | 9 6 3 |

| Copy back to original matrix | 7 4 1 |<br> | 8 5 2 |<br> | 9 6 3 |

---

## Approach 2: Rotate Layer by Layer (In-Place without Extra Space)

### Algorithm:

- The matrix can be thought of as layers or rings.
- For each layer (from outermost to innermost):
    - For each element in the current layer:
        - Swap four elements in a cycle:
            - top -> right -> bottom -> left -> top
- This operation rotates the layer by 90 degrees.

### Steps:

1. Loop over each layer `layer` from `0` to `n/2 - 1`.
2. For each element `i` in the layer:
    - Save the top element.
    - Move left to top.
    - Move bottom to left.
    - Move right to bottom.
    - Assign saved top to right.

### Java Code:

```java
public void rotate(int[][] matrix) {
    int n = matrix.length;
    for (int layer = 0; layer < n / 2; layer++) {
        int first = layer;
        int last = n - 1 - layer;
        for (int i = first; i < last; i++) {
            int offset = i - first;

            // Save top
            int top = matrix[first][i];

            // Left -> Top
            matrix[first][i] = matrix[last - offset][first];

            // Bottom -> Left
            matrix[last - offset][first] = matrix[last][last - offset];

            // Right -> Bottom
            matrix[last][last - offset] = matrix[i][last];

            // Top -> Right
            matrix[i][last] = top;
        }
    }
}

```

### Complexity Analysis:

- **Time Complexity:** `O(n^2)` because each element is moved once.
- **Space Complexity:** `O(1)` as the rotation is done in-place with a few variables.

### Dry Run:

**Input:**

```
1 2 3
4 5 6
7 8 9

```

**Process:**

- Layer 0 (`layer=0`):
    - `first=0`, `last=2`
    - i=0:
        - top=1
        - Move `matrix[2][0]` (7) to `matrix[0][0]`
        - Move `matrix[2][2]` (9) to `matrix[2][0]`
        - Move `matrix[0][2]` (3) to `matrix[2][2]`
        - Assign saved top (1) to `matrix[0][2]`
    - i=1:
        - top=2
        - Move `matrix[1][0]` (4) to `matrix[0][1]`
        - Move `matrix[2][1]` (8) to `matrix[1][0]`
        - Move `matrix[1][2]` (6) to `matrix[2][1]`
        - Assign saved top (2) to `matrix[1][2]`

**Result after processing:**

```
7 4 1
8 5 2
9 6 3

```

---

## Approach 3: Transpose and Reverse (Most Optimal)

### Algorithm:

- **Step 1: Transpose** the matrix (swap `matrix[i][j]` with `matrix[j][i]`).
- **Step 2: Reverse** each row to get the rotated matrix.

This approach is succinct and leverages matrix properties.

### Java Code:

```java
public void rotate(int[][] matrix) {
    int n = matrix.length;

    // Transpose
    for (int i = 0; i < n; i++) {
        for (int j = i; j < n; j++) {
            int temp = matrix[i][j];
            matrix[i][j] = matrix[j][i];
            matrix[j][i] = temp;
        }
    }

    // Reverse each row
    for (int i = 0; i < n; i++) {
        int start = 0, end = n - 1;
        while (start < end) {
            int temp = matrix[i][start];
            matrix[i][start] = matrix[i][end];
            matrix[i][end] = temp;
            start++;
            end--;
        }
    }
}

```

### Complexity Analysis:

- **Time Complexity:** `O(n^2)`:
    - Transposing takes `O(n^2)`.
    - Reversing each row takes `O(n^2)`.
- **Space Complexity:** `O(1)` (in-place).

### Dry Run:

**Input:**

```
1 2 3
4 5 6
7 8 9

```

**Transpose:**

```
1 4 7
2 5 8
3 6 9

```

**Reverse each row:**

```
7 4 1
8 5 2
9 6 3

```

---

# Summary

| Approach | Method | In-Place? | Code Complexity | Best Use Case |
| --- | --- | --- | --- | --- |
| **1** | Extra matrix | No | O(n^2) time, O(n^2) space | When space isn't constrained |
| **2** | Layer by layer rotation | Yes | O(n^2) time, O(1) space | Efficient in-place rotation |
| **3** | Transpose + Reverse | Yes | O(n^2) time, O(1) space | Concise and elegant solution |

---

## Final Tips for Interviews:

- Understand the matrix properties and how rotation relates to transpose/reverse.
- Practice in-place transformations for space efficiency.
- Clearly explain your approach, especially how you handle indices.
- Always analyze time and space complexities.

---

Feel free to revisit these approaches during your revision!
