# Word Ladder

---

## Problem Overview
Given two words (`beginWord` and `endWord`) and a dictionary (`wordList`), find the **shortest transformation sequence** from `beginWord` to `endWord`. Each transformation must change exactly one letter, and each intermediate word must exist in the dictionary.

- **Input:**  
  - `beginWord`: starting word  
  - `endWord`: target word  
  - `wordList`: list of valid intermediate words

- **Output:**  
  - The minimum number of steps to transform `beginWord` into `endWord`, or 0 if impossible.

---

## Approach 1: Brute Force (Backtracking)  
*(Note: Not practical for large datasets; mainly for understanding)*

### Core Idea
Attempt all possible transformations recursively, exploring all paths until reaching the `endWord`. This approach is exponential and often infeasible.

### Algorithm
1. Start from `beginWord`.
2. For each word in `wordList`, if it differs by exactly one letter and is unused:
   - Recursively attempt to find a path to `endWord`.
3. Keep track of the minimum path length that reaches `endWord`.
4. Return the shortest path length found or 0 if none.

### Java Code (Conceptual)
```java
public class WordLadderBruteForce {
    int minSteps = Integer.MAX_VALUE;
    public int ladderLength(String beginWord, String endWord, List<String> wordList) {
        boolean[] used = new boolean[wordList.size()];
        dfs(beginWord, endWord, wordList, used, 1);
        return minSteps == Integer.MAX_VALUE ? 0 : minSteps;
    }

    private void dfs(String current, String endWord, List<String> wordList, boolean[] used, int depth) {
        if (current.equals(endWord)) {
            minSteps = Math.min(minSteps, depth);
            return;
        }
        for (int i = 0; i < wordList.size(); i++) {
            if (!used[i] && isOneLetterDiff(current, wordList.get(i))) {
                used[i] = true;
                dfs(wordList.get(i), endWord, wordList, used, depth + 1);
                used[i] = false;
            }
        }
    }

    private boolean isOneLetterDiff(String a, String b) {
        int diffCount = 0;
        for (int i = 0; i < a.length(); i++) {
            if (a.charAt(i) != b.charAt(i))
                diffCount++;
            if (diffCount > 1)
                return false;
        }
        return diffCount == 1;
    }
}
```

### Complexity Analysis
- **Time Complexity:** O(N!), since it explores permutations of words in worst case.
- **Space Complexity:** O(N), for recursion stack and used array.

---

## Approach 2: BFS (Most Efficient and Common Solution)
### Core Idea
Use **Breadth-First Search (BFS)** to explore all possible transformations level by level, ensuring the shortest path is found.

### Algorithm
1. Convert `wordList` into a HashSet for O(1) lookup.
2. Initialize a queue with `beginWord`.
3. Maintain a `visited` set to avoid revisiting words.
4. For each word in the queue:
   - Generate all possible one-letter transformations.
   - If a transformation matches `endWord`, return the current depth + 1.
   - If the transformed word exists in the dictionary and is unvisited:
     - Add it to the queue and mark visited.
5. Continue until the queue is empty or `endWord` is found.

### Java Code
```java
public class WordLadderBFS {
    public int ladderLength(String beginWord, String endWord, List<String> wordList) {
        Set<String> wordSet = new HashSet<>(wordList);
        if (!wordSet.contains(endWord)) return 0;

        Queue<String> queue = new LinkedList<>();
        queue.offer(beginWord);
        Set<String> visited = new HashSet<>();
        visited.add(beginWord);

        int level = 1;

        while (!queue.isEmpty()) {
            int size = queue.size();
            for (int i = 0; i < size; i++) {
                String current = queue.poll();

                if (current.equals(endWord))
                    return level;

                for (String neighbor : getNextWords(current, wordSet)) {
                    if (!visited.contains(neighbor)) {
                        visited.add(neighbor);
                        queue.offer(neighbor);
                    }
                }
            }
            level++;
        }
        return 0;
    }

    private List<String> getNextWords(String word, Set<String> wordSet) {
        List<String> neighbors = new ArrayList<>();
        char[] chArr = word.toCharArray();

        for (int i = 0; i < chArr.length; i++) {
            char originalChar = chArr[i];

            for (char c = 'a'; c <= 'z'; c++) {
                if (c == originalChar) continue;
                chArr[i] = c;
                String newWord = new String(chArr);
                if (wordSet.contains(newWord))
                    neighbors.add(newWord);
            }
            chArr[i] = originalChar;
        }
        return neighbors;
    }
}
```

### Complexity Analysis
- **Time Complexity:** O(M * N), where M is the length of words and N is the size of the word list, because each word generates at most 26 * M transformations.
- **Space Complexity:** O(N), for the `visited` set and queue.

---

## Approach 3: Bidirectional BFS (Most Optimal)
### Core Idea
Run BFS simultaneously from both `beginWord` and `endWord`, reducing the search space and improving performance.

### Algorithm
1. Initialize two sets: `beginSet` containing `beginWord`, and `endSet` containing `endWord`.
2. Maintain a `visited` set.
3. While both sets are non-empty:
   - Always expand the smaller set for efficiency.
   - Generate all possible transformations from the current frontier.
   - If any transformation intersects with the opposite frontier, a shortest path is found.
   - Update the frontier with newly generated words.
4. Increment the step count with each level.

### Java Code
```java
public class WordLadderBidirectional {
    public int ladderLength(String beginWord, String endWord, List<String> wordList) {
        Set<String> wordSet = new HashSet<>(wordList);
        if (!wordSet.contains(endWord)) return 0;

        Set<String> beginSet = new HashSet<>();
        Set<String> endSet = new HashSet<>();
        Set<String> visited = new HashSet<>();

        beginSet.add(beginWord);
        endSet.add(endWord);
        int level = 1;

        while (!beginSet.isEmpty() && !endSet.isEmpty()) {
            if (beginSet.size() > endSet.size()) {
                // Swap to expand smaller set
                Set<String> temp = beginSet;
                beginSet = endSet;
                endSet = temp;
            }

            Set<String> tempSet = new HashSet<>();
            for (String word : beginSet) {
                for (String neighbor : getNextWords(word, wordSet)) {
                    if (endSet.contains(neighbor))
                        return level + 1;
                    if (!visited.contains(neighbor)) {
                        visited.add(neighbor);
                        tempSet.add(neighbor);
                    }
                }
            }
            beginSet = tempSet;
            level++;
        }
        return 0;
    }

    private List<String> getNextWords(String word, Set<String> wordSet) {
        List<String> neighbors = new ArrayList<>();
        char[] chArr = word.toCharArray();

        for (int i = 0; i < chArr.length; i++) {
            char originalChar = chArr[i];

            for (char c = 'a'; c <= 'z'; c++) {
                if (c == originalChar) continue;
                chArr[i] = c;
                String newWord = new String(chArr);
                if (wordSet.contains(newWord))
                    neighbors.add(newWord);
            }
            chArr[i] = originalChar;
        }
        return neighbors;
    }
}
```

### Complexity Analysis
- **Time Complexity:** O(M * N), similar to BFS, but generally faster due to simultaneous search.
- **Space Complexity:** O(N), for storing the sets and visited words.

---

## Summary
| Approach | Use Case | Pros | Cons |
|------------|--------------|-------|-------|
| Brute Force | Small datasets, initial understanding | Simple | Very slow, exponential time |
| BFS | Large datasets, straightforward implementation | Efficient for moderate size | Still can be slow for very large datasets |
| Bidirectional BFS | Large datasets, optimal performance | Much faster, reduces search space | Slightly more complex to implement |

---

## Final Recommendation
For practical, efficient solutions, **Bidirectional BFS** is preferred, especially when the word list is large.

---

**Happy Revising!**
