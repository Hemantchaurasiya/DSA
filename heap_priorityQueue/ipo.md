# IPO (Maximum Capital)

This guide covers multiple approaches to solving the **IPO** problem, which involves selecting projects to maximize profit given initial capital, project costs, and profits. It progresses from naive to optimal solutions, including detailed explanations, code, complexity analysis, and dry runs for quick understanding.

---

## Problem Overview
- You are given:
  - An initial capital `W`.
  - Two arrays:
    - `Profits[]` where each element represents the profit from a project.
    - `Capital[]` where each element represents the minimum capital required to start that project.
- You can complete **at most `k` projects**.
- You can only select projects where the `capital[i]` ≤ current available capital.
- After completing a project, your capital increases by its profit.
- The goal is to maximize the final capital after completing up to `k` projects.

---

## 1. Brute Force Approach

### **Core Idea**
Check all possible project sequences to find the maximum capital after `k` projects. This is highly inefficient but illustrates the problem's brute-force nature.

### **Algorithm**
1. For each of `k` iterations:
   - Find the project with the maximum profit among those whose `capital` requirement is ≤ current capital.
   - Select this project, update the current capital by adding the project’s profit.
2. Repeat until `k` projects are done or no projects are affordable.

### **Java Code**
```java
public class IPO {
    public int findMaximizedCapital(int k, int W, int[] Profits, int[] Capital) {
        int n = Profits.length;
        for (int i = 0; i < k; i++) {
            int maxProfit = -1;
            int projectIndex = -1;
            for (int j = 0; j < n; j++) {
                if (Capital[j] <= W && Profits[j] > maxProfit) {
                    maxProfit = Profits[j];
                    projectIndex = j;
                }
            }
            if (projectIndex == -1) break; // no project affordable
            W += Profits[projectIndex];
        }
        return W;
    }
}
```

### **Complexity Analysis**
- **Time Complexity:**  
  `O(k * n)` because for each of the `k` iterations, we scan all projects.
- **Space Complexity:**  
  `O(1)` (no extra space apart from input arrays).

---

### **Dry Run with Sample Input**
| Input | k=2, W=0, Profits=[1,2,3], Capital=[0,1,1] |
|--------|-----------------------------------------|
| Iteration 1: | Max profit among projects with Capital ≤ 0: project 0 (profit=1), W=0+1=1 |
| Iteration 2: | Now W=1; projects 1 and 2 are affordable. Project 2 (profit=3): W=1+3=4 |

---

## 2. Better Solution: Sorting + Priority Queue

### **Core Idea**
Sort projects by their capital requirement. Use a min-heap for projects sorted by capital, and a max-heap for project profits. This improves efficiency by avoiding repeated scans.

### **Algorithm**
1. Create a list of projects with their `capital` and `profit`.
2. Sort projects by `capital`.
3. Use a min-heap (`capitalHeap`) for project `capital`.
4. Use a max-heap (`profitHeap`) for project `profit`.
5. For each iteration:
   - Add all projects whose `capital` requirement is ≤ current capital into `profitHeap`.
   - Extract the max profit from `profitHeap`.
   - Update current capital.
6. Repeat up to `k` times or until no projects are available.

### **Java Code**
```java
import java.util.*;

public class IPO {
    public int findMaximizedCapital(int k, int W, int[] Profits, int[] Capital) {
        int n = Profits.length;
        List<int[]> projects = new ArrayList<>();
        for (int i = 0; i < n; i++) {
            projects.add(new int[]{Capital[i], Profits[i]});
        }
        projects.sort(Comparator.comparingInt(a -> a[0]));

        PriorityQueue<Integer> maxProfitHeap = new PriorityQueue<>(Collections.reverseOrder());
        PriorityQueue<int[]> capitalHeap = new PriorityQueue<>(Comparator.comparingInt(a -> a[0]));

        for (int[] p : projects) {
            capitalHeap.offer(p);
        }

        for (int i = 0; i < k; i++) {
            while (!capitalHeap.isEmpty() && capitalHeap.peek()[0] <= W) {
                maxProfitHeap.offer(capitalHeap.poll()[1]);
            }
            if (maxProfitHeap.isEmpty()) break;
            W += maxProfitHeap.poll();
        }

        return W;
    }
}
```

### **Complexity Analysis**
- **Time Complexity:**  
  `O(n log n + k log n)` due to sorting and heap operations.
- **Space Complexity:**  
  `O(n)` for storing project data and heaps.

---

### **Dry Run with Sample Input**
| Initial W=0, k=2, Profits=[1,2,3], Capital=[0,1,1] |
|-----------------------------------------------------|
| Sorted projects: [(0,1), (1,2), (1,3)] |
| Iteration 1: | Add projects with capital ≤ 0: [(0,1)] | Extract max profit=1, W=0+1=1 |
| Iteration 2: | Add projects with capital ≤ 1: [(1,2), (1,3)] | Extract max profit=3, W=1+3=4 |

---

## 3. Most Optimal Solution: Max Heap + Priority Queue (Greedy)

### **Core Idea**
Combine sorting with a max heap for profits, and process projects efficiently, avoiding unnecessary scans.

### **Algorithm Summary**
- Sort projects by capital.
- Use a max heap for profits.
- For each project selection:
  - Add all projects affordable at current capital into the max heap.
  - Select the project with the maximum profit.
  - Increase current capital.
- Repeat up to `k` times or until no more projects are available.

### **This approach is an extension of the previous method** and is considered optimal for large inputs.

---

## **Summary Table**

| Approach                        | Best Use Case                    | Time Complexity                | Space Complexity | Notes                                           |
|--------------------------------|----------------------------------|--------------------------------|------------------|------------------------------------------------|
| Brute Force                     | Small input sizes                | `O(k * n)`                     | `O(1)`           | Inefficient, scans all projects repeatedly    |
| Sorting + Priority Queue        | Moderate sizes, better performance | `O(n log n + k log n)`    | `O(n)`           | Efficient for typical constraints               |

---

## **Key Takeaways**
- Use sorting to organize projects based on requirement.
- Use heaps to efficiently select projects with maximum profit.
- Greedy approach ensures optimal choices at each step.

This completes the detailed revision for **IPO**, covering multiple approaches with their intuition, implementation, and complexity.
