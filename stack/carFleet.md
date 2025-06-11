# Car Fleet

---

## Problem Overview
Given the positions and speeds of cars on a straight road, a car fleet is formed when cars collide and travel together at the same speed.  
**Objective:** Determine the number of car fleets that will arrive at the destination.

**Input:**
- `target`: the destination point
- `position[]`: array of starting positions of cars
- `speed[]`: array of speeds corresponding to each car

---

## Approach 1: Brute Force (Naive Simulation)

### **Core Idea**
- Simulate the movement of each car over time until they reach the target.
- Detect when cars catch up to each other, forming a fleet.
- Count the number of distinct fleets.

### **Algorithm**
1. For each car, calculate the time to reach the target: `time = (target - position) / speed`.
2. Store these times along with positions.
3. Sort cars based on their starting positions in descending order.
4. Iterate over sorted cars:
   - If a car's time is greater than the previous fleet's time, it forms a new fleet.
   - Else, it joins the existing fleet.
5. Count the number of fleets formed.

### **Limitations**
- Inefficient as it involves sorting and simulating movement.
- Not practical for large inputs.

---

## Approach 2: Greedy with Sorting (Most Efficient)

### **Core Idea**
- Cars that start behind but are faster can catch up to the cars ahead, forming fleets.
- Process cars from the one closest to the destination backward.
- Use the time each car takes to reach the target as the key metric.

### **Algorithm**
1. Pair each car's position with its speed.
2. Sort cars based on position in descending order.
3. Initialize `fleets = 0` and `prevTime = 0`.
4. Traverse sorted cars:
   - Calculate time to reach the target: `time = (target - position) / speed`.
   - If `time > prevTime`, it means this car forms a new fleet.
     - Increment fleet count.
     - Update `prevTime` to current car's time.
   - Else, the car joins the existing fleet.
5. Return the total number of fleets.

### **Java Code**
```java
import java.util.Arrays;
import java.util.Comparator;

public class CarFleet {
    public int carFleet(int target, int[] position, int[] speed) {
        int n = position.length;
        // Pair position and speed
        int[][] cars = new int[n][2];
        for (int i = 0; i < n; i++) {
            cars[i][0] = position[i];
            cars[i][1] = speed[i];
        }
        // Sort based on position in descending order
        Arrays.sort(cars, (a, b) -> b[0] - a[0]);

        int fleets = 0;
        double prevTime = 0;
        for (int i = 0; i < n; i++) {
            double time = (double)(target - cars[i][0]) / cars[i][1];
            if (time > prevTime) {
                fleets++;
                prevTime = time;
            }
            // Else, car joins the fleet ahead
        }
        return fleets;
    }
}
```

### **Complexity Analysis**
- **Time Complexity:** *O(n log n)* — due to sorting the cars based on position.
- **Space Complexity:** *O(n)* — for storing position-speed pairs.

---

## **Dry Run Example**

**Input:**
```
target = 12
position = [10, 8, 0, 5, 3]
speed = [2, 4, 1, 1, 3]
```

| Step | Car (Position, Speed) | Distance to Target | Time to Reach | Action                          | Fleet Count | prevTime | Explanation                                           |
|-------|------------------------|---------------------|--------------|---------------------------------|--------------|----------|-------------------------------------------------------|
| 1     | (10, 2)                | 2                   | 1.0          | New fleet (fleets=1)             | 1            | 1.0      | First car forms a fleet                              |
| 2     | (8, 4)                 | 4                   | 1.0          | Same time as previous, joins fleet | 1            | 1.0      | Joins existing fleet                                |
| 3     | (5, 1)                 | 7                   | 7.0          | > prevTime, new fleet (fleets=2) | 2            | 7.0      | New fleet formed                                    |
| 4     | (3, 3)                 | 9                   | 3.0          | < prevTime, joins fleet          | 2            | 7.0      | Joins previous fleet                                |
| 5     | (0, 1)                 | 12                  | 12.0         | > prevTime, new fleet (fleets=3) | 3            | 12.0     | New fleet formed                                    |

**Result:** 3 fleets.

---

## **Summary Table**

| Approach                         | Time Complexity     | Space Complexity | Comments                                         |
|----------------------------------|---------------------|------------------|--------------------------------------------------|
| Naive Simulation                 | O(n^2)             | O(n)             | Not suitable for large inputs                   |
| Sorting + Greedy (Optimal)       | O(n log n)         | O(n)             | Efficient, widely used for this problem        |

---

## **Final Tips for Interviews**
- Focus on sorting cars based on position.
- Use the concept of "time to reach target" to determine fleet formation.
- Process cars from closest to the target backward.
- Remember, if a car takes longer or equal time than the previous, it forms a new fleet.
- Test with different scenarios: all cars ahead, all behind, mixed speeds, etc.

---

**Happy coding!**
