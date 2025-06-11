# Binary Watch

---

## Problem Statement
Given a non-negative integer `turnedOn` representing the number of LEDs that are currently on, return all possible times the binary watch could represent. The watch has:

- 4 LEDs for hours (0-11)
- 6 LEDs for minutes (0-59)

The LEDs are binary, and the number of LEDs lit corresponds to the number of `1`s in the binary representation of the hour and minute.

---

## Approach 1: Brute Force

### Core Idea
Check every possible time (from 0:00 to 11:59) and count the number of LEDs lit in the binary representation of hours and minutes. If the total matches `turnedOn`, include this time in the result.

### Algorithm
1. Loop over hours from 0 to 11.
2. Loop over minutes from 0 to 59.
3. For each time:
   - Convert hour and minute to binary.
   - Count the total number of `1`s in binary forms.
   - If total `1`s equals `turnedOn`, format the time (e.g., "h:mm") and add to results.
   
### Recursion Tree Diagram
(Not applicable for brute-force iteration, but conceptually it checks all pairs `(hour, minute)`; total 12 * 60 = 720 checks.)

### Java Code
```java
import java.util.*;

public class BinaryWatch {
    public List<String> readBinaryWatch(int turnedOn) {
        List<String> result = new ArrayList<>();
        for (int h = 0; h < 12; h++) {
            for (int m = 0; m < 60; m++) {
                if (Integer.bitCount(h) + Integer.bitCount(m) == turnedOn) {
                    result.add(String.format("%d:%02d", h, m));
                }
            }
        }
        return result;
    }
}
```

### Complexity Analysis
- **Time Complexity:** **O(12 * 60) = O(720)**, constant time for fixed constraints.
- **Space Complexity:** **O(1)**, ignoring the output list, as no extra space grows with input size.

### Dry Run Example
Input: `turnedOn = 1`

- Loop starts:
  - Hour = 0:
    - Minute = 0 (binary 000000): `bitCount = 0`, total = 0 → not equal to 1.
    - Minute = 1 (binary 000001): `bitCount = 1`, total = 1 → add "0:01".
    - ...
  - Hour = 1:
    - Minute = 0 (binary 000000): total = 1 → add "1:00".
    - Minute = 2 (binary 000010): total = 1 → add "1:02".
    - ...
- Continue similarly for all hours and minutes.

---

## Approach 2: Using Bit Manipulation and Preprocessing

### Core Idea
Leverage **bit manipulation** to efficiently count set bits, and iterate over only those combinations where the total `1`s match `turnedOn`.

### Algorithm
1. For each hour (0-11), precompute the number of set bits.
2. For each minute (0-59), precompute the number of set bits.
3. For each combination `(hour, minute)`:
   - Check if `bitCount(hour) + bitCount(minute) == turnedOn`.
   - If yes, format and add to results.

This approach is similar to brute-force but emphasizes bit manipulation for efficiency, especially in languages where bit counting is optimized.

### Java Code
```java
import java.util.*;

public class BinaryWatch {
    public List<String> readBinaryWatch(int turnedOn) {
        List<String> result = new ArrayList<>();
        for (int h = 0; h < 12; h++) {
            int hourBits = Integer.bitCount(h);
            for (int m = 0; m < 60; m++) {
                int minBits = Integer.bitCount(m);
                if (hourBits + minBits == turnedOn) {
                    result.add(String.format("%d:%02d", h, m));
                }
            }
        }
        return result;
    }
}
```

### Complexity Analysis
- Same as Approach 1: **O(720)** overall, with slight optimization.

---

## Approach 3: **Backtracking / Combinatorial Approach** (Most Optimal)

### Core Idea
Instead of checking all times, generate all possible combinations of LEDs that match the number `turnedOn`. Use backtracking to select LEDs for hours and minutes separately, and combine them to form valid times.

### Intuition
- The total number of LEDs lit is `turnedOn`.
- Distribute these LEDs between hours and minutes:
  - For each possible number of LEDs in hours (`i`), the remaining (`turnedOn - i`) LEDs are in minutes.
- Generate all numbers with `i` bits set for hours (from 0-11).
- Generate all numbers with `turnedOn - i` bits set for minutes (from 0-59).
- Combine each valid hour and minute to form times.

---

### Algorithm
1. For each `i` from 0 to `turnedOn`:
   - Generate all `hour` values with `i` bits set (where 0 ≤ `hour` ≤ 11).
   - Generate all `minute` values with `turnedOn - i` bits set (where 0 ≤ `minute` ≤ 59).
2. For each pair `(hour, minute)`:
   - Format and add to results if valid.

### Generating Numbers with `k` bits set
- Use combinatorial generation or bit manipulation techniques to produce all numbers with exactly `k` bits set within a range.

### Java Code
```java
import java.util.*;

public class BinaryWatch {
    // Helper method to generate numbers with 'bitsCount' bits set within a range
    private void generateNumbers(int max, int bitsCount, int start, List<Integer> result) {
        generateNumbersHelper(max, bitsCount, 0, 0, result);
    }

    private void generateNumbersHelper(int max, int bitsCount, int current, int start, List<Integer> result) {
        if (bitsCount == 0) {
            if (current <= max) {
                result.add(current);
            }
            return;
        }
        for (int i = start; i <= 31; i++) {
            int newCurrent = current | (1 << i);
            generateNumbersHelper(max, bitsCount - 1, newCurrent, i + 1, result);
        }
    }

    public List<String> readBinaryWatch(int turnedOn) {
        List<String> result = new ArrayList<>();
        for (int hourBits = 0; hourBits <= turnedOn; hourBits++) {
            int minBits = turnedOn - hourBits;
            List<Integer> hours = new ArrayList<>();
            List<Integer> minutes = new ArrayList<>();
            generateNumbers(11, hourBits, 0, hours); // Generate hours with 'hourBits' set
            generateNumbers(59, minBits, 0, minutes); // Generate minutes with 'minBits' set

            for (int h : hours) {
                for (int m : minutes) {
                    result.add(String.format("%d:%02d", h, m));
                }
            }
        }
        return result;
    }
}
```

### Complexity Analysis
- **Time Complexity:** O(`C(4, hourBits) * C(6, minBits)`) per distribution, with total roughly manageable given small constraints.
- **Space Complexity:** O(number of combinations generated), which is small due to limited ranges.

---

## Summary

| Approach | Core Idea | Advantages | Disadvantages | Time Complexity | Space Complexity |
|------------|--------------|--------------|------------------|------------------|------------------|
| **Brute Force** | Check all times from 0:00 to 11:59 | Simple, straightforward | Checks unnecessary times | O(720) | O(1) (excluding output) |
| **Bit Manipulation & Preprocessing** | Use `Integer.bitCount` to count bits | Slightly optimized | Still checks all times | O(720) | O(1) |
| **Backtracking / Combinatorial** | Generate combinations with fixed bits set | Most efficient, avoids unnecessary checks | Slightly complex to implement | O(C(4, hourBits)*C(6, minBits)) | Small, manageable |


---

## Final Tips for Interview Preparation
- Understand **bit manipulation** for counting set bits.
- Recognize that the problem involves **combinatorial generation** of numbers with specific bit counts.
- Be able to **explain the trade-offs** between checking all times vs. generating combinations.
- Practice implementing the **backtracking approach** for similar combinatorial problems.

---

**End of Revision Notes**
