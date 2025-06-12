# Single Threaded CPU

The **Single Threaded CPU** problem involves scheduling tasks based on their enqueue times and processing durations, ensuring tasks are executed as per rules, and returning the order of task execution.

---

## Problem Statement
Given arrays `tasks` (each task has an ID and a processing time) and `enqueTimes` (when each task becomes available), simulate a CPU that processes tasks:
- Tasks are picked from available tasks (those with enqueue time ≤ current time).
- If multiple are available, pick the one with the shortest processing time.
- If there's a tie, pick the task with the smaller task ID.
- The CPU is single-threaded, so it processes tasks one by one.
- Return the order of task IDs as they are processed.

---

## Approach Overview

---

### 1. **Brute Force (Naive) Approach**

**Core Idea:**
- At each step, scan all tasks to find the next task to execute based on the rules.
- Update current time based on task processing.
- Repeat until all tasks are processed.

**Algorithm Steps:**
- Keep a list of all tasks with their enqueue times.
- Initialize current time to the minimum enqueue time.
- Repeatedly:
  - Find all tasks that have enqueue time ≤ current time and are not yet processed.
  - Select the task with the shortest processing time; if tie, smallest ID.
  - Process the task: add its ID to result, update current time.
  - Mark task as processed.
- Continue until all tasks are scheduled.

**Java Code:**
```java
import java.util.*;

class Task {
    int id;
    int enqueueTime;
    int processingTime;
    Task(int id, int enqueueTime, int processingTime) {
        this.id = id;
        this.enqueueTime = enqueueTime;
        this.processingTime = processingTime;
    }
}

public class Solution {
    public int[] getOrder(int[][] tasksArr, int[] enqueueTimeArr) {
        int n = tasksArr.length;
        List<Task> tasks = new ArrayList<>();
        for (int i = 0; i < n; i++) {
            tasks.add(new Task(i, enqueueTimeArr[i], tasksArr[i][1]));
        }
        boolean[] processed = new boolean[n];
        int[] result = new int[n];
        int count = 0;
        int currentTime = Integer.MAX_VALUE;

        // Find initial min enqueue time
        for (int t : enqueueTimeArr) {
            currentTime = Math.min(currentTime, t);
        }

        while (count < n) {
            // Find available tasks
            int minProcessingTime = Integer.MAX_VALUE;
            int minId = Integer.MAX_VALUE;
            int taskIndex = -1;
            for (int i = 0; i < n; i++) {
                if (!processed[i] && tasks.get(i).enqueueTime <= currentTime) {
                    if (tasks.get(i).processingTime < minProcessingTime ||
                        (tasks.get(i).processingTime == minProcessingTime && tasks.get(i).id < minId)) {
                        minProcessingTime = tasks.get(i).processingTime;
                        minId = tasks.get(i).id;
                        taskIndex = i;
                    }
                }
            }
            if (taskIndex == -1) {
                // No available task, move time forward
                int nextEnqueue = Integer.MAX_VALUE;
                for (int i = 0; i < n; i++) {
                    if (!processed[i]) {
                        nextEnqueue = Math.min(nextEnqueue, tasks.get(i).enqueueTime);
                    }
                }
                currentTime = nextEnqueue;
                continue;
            }
            // Process selected task
            processed[taskIndex] = true;
            result[count++] = tasks.get(taskIndex).id;
            currentTime += tasks.get(taskIndex).processingTime;
        }
        return result;
    }
}
```

### **Complexity Analysis:**
- **Time Complexity:** O(N^2), since at each step, scanning all tasks to find the next task.
- **Space Complexity:** O(N), for storing task details and processed flags.

---

### 2. **Optimized Approach: Using Min-Heaps and Sorting**

**Core Idea:**
- Sort all tasks by enqueue time.
- Use a min-heap to select the next task based on processing time and ID.
- Simulate time progression:
  - Add all tasks that become available at current time to the heap.
  - Pop the top task (shortest processing time, smallest ID).
  - Process it and update current time.
  - Repeat until all tasks are processed.

**Algorithm Steps:**
- Create a list of tasks with their enqueue time, processing time, and ID.
- Sort tasks by enqueue time.
- Use a min-heap (priority queue) ordered by processing time, then by ID.
- Maintain an index to track tasks that are yet to be added to the heap.
- Initialize current time to the earliest enqueue time.
- While there are unprocessed tasks:
  - Add all tasks that have enqueue time ≤ current time to the heap.
  - If heap is empty, move current time to the next task's enqueue time.
  - Pop the task with minimum processing time (and ID).
  - Add task ID to result.
  - Update current time += task's processing time.

---

### **Java Code:**
```java
import java.util.*;

public class Solution {
    public int[] getOrder(int[][] tasksArr, int[] enqueueTimeArr) {
        int n = tasksArr.length;
        List<int[]> tasks = new ArrayList<>();
        for (int i = 0; i < n; i++) {
            tasks.add(new int[]{i, enqueueTimeArr[i], tasksArr[i][1]});
        }
        // Sort tasks by enqueue time
        tasks.sort(Comparator.comparingInt(a -> a[1]));
        
        PriorityQueue<int[]> minHeap = new PriorityQueue<>(
            (a, b) -> (a[2] == b[2]) ? a[0] - b[0] : a[2] - b[2]
        );
        
        int[] result = new int[n];
        int time = 0;
        int index = 0;
        int count = 0;
        
        // Initialize current time to earliest enqueue time
        time = tasks.get(0)[1];
        
        while (count < n) {
            // Add all tasks available at current time
            while (index < n && tasks.get(index)[1] <= time) {
                int[] task = tasks.get(index);
                minHeap.offer(new int[]{task[0], task[1], task[2]});
                index++;
            }
            if (minHeap.isEmpty()) {
                // No tasks available, jump to next enqueue time
                if (index < n) {
                    time = tasks.get(index)[1];
                    continue;
                }
            } else {
                int[] currentTask = minHeap.poll();
                result[count++] = currentTask[0];
                time += currentTask[2];  // move current time forward
            }
        }
        return result;
    }
}
```

### **Complexity Analysis:**
- **Time Complexity:** O(N log N) due to sorting and heap operations.
- **Space Complexity:** O(N), for storing tasks and heap.

---

### 3. **Most Optimal Approach: Using Sorting and a Min-Heap with Efficient Time Management**

This approach is similar to approach 2 but emphasizes efficient handling of time jumps and task selection, ensuring minimal overhead.

---

## **Summary Table**

| Approach | Core Idea | Data Structures | Time Complexity | Space Complexity | Notes |
|------------|--------------|-------------------|-------------------|------------------|--------|
| Naive | Scan all tasks repeatedly | Arrays + Linear Search | O(N^2) | O(N) | Not scalable |
| Sorting + Min-Heap | Sort tasks, process in order | Arrays + PriorityQueue | O(N log N) | O(N) | Efficient for large inputs |
| Optimal | Time-optimized, handling time jumps | Sorting + PriorityQueue | O(N log N) | O(N) | Best performance |

---

## **Final Tips:**
- Sorting tasks by enqueue time simplifies selecting available tasks.
- Use a priority queue to always pick the shortest task quickly.
- Manage current time carefully:
  - If no tasks are available, jump to the next task's enqueue time.
  - Otherwise, process the shortest available task and update the current time accordingly.
- This approach ensures an efficient and optimal solution suitable for large datasets.

---

**End of DSA Revision Note for Single Threaded CPU**
