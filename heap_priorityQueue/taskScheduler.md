# Task Scheduler

This guide covers multiple approaches to solving the **"Task Scheduler"** problem, starting from brute-force to optimized solutions. Each approach includes the core idea, detailed algorithm steps, Java implementation, complexity analysis, and a dry run example for clear understanding and quick revision, ideal for interview preparation.

---

## Problem Statement
Given a list of tasks represented by characters (e.g., `['A', 'A', 'A', 'B', 'B', 'B']`) and a non-negative integer `n` representing the cooling interval, schedule tasks such that the same task is separated by at least `n` units of time. Return the minimum number of units of times that the CPU will take to finish all tasks.

---

## Approach 1: Brute Force (Simulation)

### **Core Idea:**
Repeatedly pick tasks with the highest remaining counts, respecting the cooling period, and simulate the process step-by-step.

---

### **Algorithm:**
1. Count the frequency of each task using a `HashMap`.
2. While there are tasks remaining:
   - Pick the top `available` tasks (up to `n+1`) with the highest remaining counts.
   - Execute these tasks, decrement their counts.
   - If tasks still have remaining counts, add them back to the pool.
   - Count each interval as 1 unit of time.
   - If less than `n+1` tasks are available in a cycle, idle (add idle time).
3. Continue until all tasks are scheduled.

*Note:* This approach is complex to implement efficiently and often involves simulation with priority queues.

---

### **Java Code:**
*(Not optimized, illustrative purpose)*

```java
import java.util.*;

public class TaskSchedulerBruteForce {
    public int leastInterval(char[] tasks, int n) {
        Map<Character, Integer> taskCounts = new HashMap<>();
        for (char task : tasks) {
            taskCounts.put(task, taskCounts.getOrDefault(task, 0) + 1);
        }

        int time = 0;
        PriorityQueue<Integer> maxHeap = new PriorityQueue<>(Collections.reverseOrder());
        maxHeap.addAll(taskCounts.values());

        while (!maxHeap.isEmpty()) {
            List<Integer> temp = new ArrayList<>();
            int cycle = 0;
            for (int i = 0; i <= n; i++) {
                if (!maxHeap.isEmpty()) {
                    int count = maxHeap.poll();
                    count--;
                    if (count > 0)
                        temp.add(count);
                    cycle++;
                } else {
                    // Idle time
                }
                time++;
                if (maxHeap.isEmpty() && temp.isEmpty())
                    break;
            }
            maxHeap.addAll(temp);
        }

        return time;
    }
}
```

### **Complexity Analysis:**
- **Time Complexity:**  
  `O(N * (N + K))` in worst case, where `N` is number of tasks, because at each step, we process tasks and potentially re-insert them multiple times.

- **Space Complexity:**  
  `O(N)` for task counts and priority queue.

---

### **Dry Run (Example):**
- Input: `tasks = ['A','A','A','B','B','B']`, `n=2`

| Step | Remaining Tasks | Processed Tasks | Time Spent | Comments                          |
|-------|-------------------|-----------------|------------|----------------------------------|
| 1     | A=3, B=3          | Select A, B     | 2          | Both tasks executed, counts 2 |
| 2     | A=2, B=2          | Select A, B     | 4          | Counts decreased, repeat       |
| 3     | A=1, B=1          | Select A, B     | 6          | All tasks completed in 6 units|

---

## Approach 2: Greedy with Priority Queue (Most Efficient)

### **Core Idea:**
Use a max-heap (priority queue) to always pick tasks with the highest remaining counts, enforce the cooling period via a queue, and count the total intervals.

---

### **Algorithm:**
1. Count task frequencies with a `HashMap`.
2. Insert all task counts into a max-heap.
3. Use a queue to hold tasks during the cooling period.
4. While heap is not empty:
   - Pop up to `n+1` tasks from the heap to process in one cycle.
   - For each task:
     - Execute (decrement count).
     - If still remaining, add to a temporary list.
   - After processing:
     - Re-insert remaining tasks back into the heap.
     - Count total time units, including idle if no tasks are available.
5. Continue until all tasks are scheduled.

---

### **Java Code:**
```java
import java.util.*;

public class TaskSchedulerOptimized {
    public int leastInterval(char[] tasks, int n) {
        Map<Character, Integer> taskCounts = new HashMap<>();
        for (char task : tasks) {
            taskCounts.put(task, taskCounts.getOrDefault(task, 0) + 1);
        }

        PriorityQueue<Integer> maxHeap = new PriorityQueue<>(Collections.reverseOrder());
        maxHeap.addAll(taskCounts.values());

        int totalTime = 0;

        while (!maxHeap.isEmpty()) {
            int cycleTime = 0;
            List<Integer> temp = new ArrayList<>();

            for (int i = 0; i <= n; i++) {
                if (!maxHeap.isEmpty()) {
                    int count = maxHeap.poll();
                    count--;
                    if (count > 0)
                        temp.add(count);
                    cycleTime++;
                }
            }
            // Re-insert remaining tasks
            maxHeap.addAll(temp);

            totalTime += maxHeap.isEmpty() ? cycleTime : (n + 1);
        }

        return totalTime;
    }
}
```

### **Complexity Analysis:**
- **Time Complexity:**  
  `O(N log K)` where `K` is the number of distinct tasks, since each task is pushed/popped from the priority queue multiple times.

- **Space Complexity:**  
  `O(N)` for task counts and priority queue.

---

### **Dry Run (Example):**
- Input: `tasks = ['A','A','A','B','B','B']`, `n=2`

| Step | Heap contents (after selection) | Processed | Time units | Comments                         |
|-------|----------------------------------|--------------|--------------|----------------------------------|
| 1     | [A=2,B=2]                        | Select A,B   | 3            | After processing, counts are 2,2 |
| 2     | [A=1,B=1]                        | Select A,B   | 3            | Counts after decrement           |
| 3     | []                               | All done     | 6            | Total time units = 6           |

---

## Summary Table

| Approach                    | Data Structures Used                     | Time Complexity       | Space Complexity | Best Use Case                          |
|------------------------------|------------------------------------------|-----------------------|------------------|----------------------------------------|
| Brute Force (Simulation)     | PriorityQueue, HashMap                  | O(N^2) (worst)        | O(N)             | Small input sizes, conceptual understanding |
| Greedy with Priority Queue   | Max-heap + Queue                       | O(N log N)            | O(N)             | Large input sizes, performance critical |

---

## Final Tips:
- The **most optimal** approach generally uses a max-heap and greedy scheduling.
- For large inputs, prioritize solutions with `O(N log K)` complexity.
- Remember to handle idle times correctly: if tasks remain, add idle units; otherwise, complete early.

---

**End of Revision Note**
