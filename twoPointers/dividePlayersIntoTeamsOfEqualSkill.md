# Divide Players Into Teams of Equal Skill

This problem involves partitioning players into teams such that each team has **equal total skill level**. Given an array of players' skill levels, the goal is to determine whether it's possible to divide them into such teams.

---

## 1. Brute Force Approach

### **Approach Name:**  
Backtracking / Exhaustive Search

### **Core Idea:**  
Try all possible partitions of players into teams with the same total skill sum. Check whether such a partition exists.

### **Algorithm:**  
1. Calculate the total sum of all players’ skills.
2. For each possible number of teams (`k`), check if total sum is divisible by `k`.  
3. If divisible, the target sum per team is `totalSum / k`.
4. Use backtracking to assign players to teams such that each team sums to the target:
   - Maintain an array `teamSums` of size `k`, initialized to zero.
   - Recursively try to assign each player to a team:
     - Skip if adding the player exceeds the target sum.
     - If all players are assigned successfully, return `true`.
5. If no valid partitioning is found, return `false`.

### **Java Code:**
```java
public class DividePlayersIntoTeams {
    public boolean canPartition(int[] skills, int k) {
        int totalSum = 0;
        for (int skill : skills) totalSum += skill;
        if (totalSum % k != 0) return false;
        int target = totalSum / k;
        boolean[] used = new boolean[skills.length];
        return backtrack(skills, used, k, 0, 0, target);
    }

    private boolean backtrack(int[] skills, boolean[] used, int k, int start, int currentSum, int target) {
        if (k == 1) return true; // Only one team left
        if (currentSum == target) // One team formed, move to next
            return backtrack(skills, used, k - 1, 0, 0, target);
        for (int i = start; i < skills.length; i++) {
            if (!used[i]) {
                if (currentSum + skills[i] > target) continue;
                used[i] = true;
                if (backtrack(skills, used, k, i + 1, currentSum + skills[i], target))
                    return true;
                used[i] = false;
            }
        }
        return false;
    }
}
```

### **Complexity Analysis:**  
- **Time Complexity:** **Exponential (O(k^n))** in worst case, due to exploring all subset arrangements.
- **Space Complexity:** **O(n)** for recursion stack and used array.

### **Dry Run:**  
Suppose:
- `skills = [2, 4, 2, 4]`
- `k=2`

Steps:
- Total sum = 12
- Target per team = 6
- Assign players:
  - Try to form first team with `[2, 4]` (sum=6), success.
  - Remaining `[2, 4]` forms second team.
- Return `true`.

---

## 2. Better Approach: Sorting + Greedy Backtracking

### **Approach Name:**  
Sorting + Pruning

### **Core Idea:**  
Sort players in descending order to place the largest skill players first, which helps prune invalid assignments early.

### **Algorithm:**  
1. Sort the array in descending order.
2. Apply the same backtracking approach as above.
3. During backtracking:
   - Always try to assign the largest remaining player first.
   - If at any point a player cannot fit into any team, prune and backtrack.

### **Java Code:**
```java
import java.util.Arrays;

public class DividePlayersIntoTeams {
    public boolean canPartition(int[] skills, int k) {
        int totalSum = 0;
        for (int skill : skills) totalSum += skill;
        if (totalSum % k != 0) return false;
        int target = totalSum / k;
        Arrays.sort(skills);
        reverse(skills);
        boolean[] used = new boolean[skills.length];
        return backtrack(skills, used, k, 0, 0, target);
    }

    private boolean backtrack(int[] skills, boolean[] used, int k, int start, int currentSum, int target) {
        if (k == 1) return true;
        if (currentSum == target) {
            return backtrack(skills, used, k - 1, 0, 0, target);
        }
        for (int i = start; i < skills.length; i++) {
            if (!used[i]) {
                if (currentSum + skills[i] > target) continue;
                used[i] = true;
                if (backtrack(skills, used, k, i + 1, currentSum + skills[i], target))
                    return true;
                used[i] = false;
                // Optimization: if first element cannot fit, no need to try further
                if (currentSum == 0) break;
            }
        }
        return false;
    }

    private void reverse(int[] arr) {
        int left = 0, right = arr.length - 1;
        while (left < right) {
            int temp = arr[left];
            arr[left] = arr[right];
            arr[right] = temp;
            left++;
            right--;
        }
    }
}
```

### **Complexity Analysis:**  
- **Time Complexity:** Still exponential but with better pruning, often significantly faster than brute-force.
- **Space Complexity:** **O(n)** for recursion and used array.

---

## 3. Most Optimal Approach: Dynamic Programming / Bit Masking (for small n)

### **Approach Name:**  
Bitmask DP for subset partitioning

### **Core Idea:**  
Use bitmasking to represent subsets of players, and dynamic programming to check if subsets can form valid teams.

### **Algorithm:**  
1. Compute total sum and check divisibility.
2. Generate all subset sums using bitmask representation.
3. Use DP to check if the array can be partitioned into `k` subsets each summing to `target`.
4. For small input sizes, this approach is feasible.

*Note:* This approach is complex to implement and suitable for small `n` (up to 20).

### **Java Code:**  
(Example for small `n`, omitted for brevity)

### **Complexity Analysis:**  
- **Time Complexity:** **O(2^n * n)**, exponential but optimized using DP.
- **Space Complexity:** **O(2^n)** for DP array.

---

## **Summary & Key Points:**

| Approach                     | Time Complexity                | Space Complexity | Description                                              |
|------------------------------|--------------------------------|------------------|----------------------------------------------------------|
| Backtracking (Brute Force)    | Exponential (O(k^n))          | O(n)             | Try all possible partitions recursively.                |
| Sorting + Pruning            | Still exponential but faster   | O(n)             | Sort players to prune invalid options early.           |
| Bitmask DP                   | O(2^n * n) for small n       | O(2^n)          | Use DP with bitmasks for small input sizes.             |

---

## **Final Tips for Interview:**
- For small input sizes, backtracking with pruning is effective.
- Always check total sum divisibility before proceeding.
- Sorting helps prune the search space.
- For large inputs, this problem is generally NP-complete; focus on heuristic or approximate solutions if needed.
- Clarify whether the problem asks for a boolean answer or actual team partitioning.

Master both recursive and optimized approaches to handle various input constraints!
