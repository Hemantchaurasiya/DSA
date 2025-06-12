# Best Time to Buy and Sell Stock

---

## Problem Overview
Given an array `prices` where `prices[i]` is the price of a stock on day `i`, **find the maximum profit** you can achieve by choosing a single day to buy and a different day to sell the stock afterward.  
**Note:** You cannot sell before you buy.

---

## Approach 1: Brute Force

### **Core Idea**
Examine all possible pairs of buy and sell days to find the maximum profit.

### **Algorithm**
1. Initialize `maxProfit` to 0.
2. Loop through each day `i` (buy day).
3. For each `i`, loop through subsequent days `j` (sell day).
4. Calculate profit as `prices[j] - prices[i]`.
5. If profit > `maxProfit`, update `maxProfit`.
6. After examining all pairs, return `maxProfit`.

### **Java Code**
```java
public int maxProfit(int[] prices) {
    int maxProfit = 0;
    int n = prices.length;
    for (int i = 0; i < n - 1; i++) {
        for (int j = i + 1; j < n; j++) {
            int profit = prices[j] - prices[i];
            if (profit > maxProfit) {
                maxProfit = profit;
            }
        }
    }
    return maxProfit;
}
```

### **Complexity Analysis**
- **Time Complexity:** **O(n^2)**, as it checks all pairs.
- **Space Complexity:** **O(1)**, no extra space used.

### **Dry Run**
- Input: `prices = [7, 1, 5, 3, 6, 4]`

| i | prices[i] | j | prices[j] | Profit | MaxProfit Update? |
|---|------------|---|-----------|--------|-------------------|
| 0 | 7          | 1 | 1         | -6     | No                |
|   |            | 2 | 5         | -2     | No                |
|   |            | 3 | 3         | -4     | No                |
|   |            | 4 | 6         | -1     | No                |
|   |            | 5 | 4         | -3     | No                |
| 1 | 1          | 2 | 5         | 4      | Yes → maxProfit=4 |
|   |            | 3 | 3         | 2      | No                |
|   |            | 4 | 6         | 5      | Yes → maxProfit=5 |
|   |            | 5 | 4         | 3      | No                |
| 2 | 5          | 3 | 3         | -2     | No                |
|   |            | 4 | 6         | 1      | No                |
|   |            | 5 | 4         | -1     | No                |
| 3 | 3          | 4 | 6         | 3      | Yes → maxProfit=5 (unchanged) |
|   |            | 5 | 4         | 1      | No                |
| 4 | 6          | 5 | 4         | -2     | No                |
| Final answer: **5** |

---

## Approach 2: One Pass / Optimized Solution

### **Core Idea**
Track the minimum price seen so far while iterating through the array, and compute potential profit at each step. The maximum profit found during this process is the answer.

### **Algorithm**
1. Initialize `minPrice` to a very large value (e.g., `Integer.MAX_VALUE`).
2. Initialize `maxProfit` to 0.
3. Loop through each price in the array:
   - Update `minPrice` if current price is lower.
   - Calculate potential profit as `currentPrice - minPrice`.
   - If the potential profit is greater than `maxProfit`, update `maxProfit`.
4. Return `maxProfit`.

### **Java Code**
```java
public int maxProfit(int[] prices) {
    int minPrice = Integer.MAX_VALUE;
    int maxProfit = 0;
    for (int price : prices) {
        if (price < minPrice) {
            minPrice = price;
        } else if (price - minPrice > maxProfit) {
            maxProfit = price - minPrice;
        }
    }
    return maxProfit;
}
```

### **Complexity Analysis**
- **Time Complexity:** **O(n)**, single pass through the array.
- **Space Complexity:** **O(1)**, only constant extra space.

### **Dry Run**
- Input: `prices = [7, 1, 5, 3, 6, 4]`

| i | price | minPrice | maxProfit | Calculation | Explanation |
|---|--------|----------|------------|--------------|--------------|
| 0 | 7      | 7        | 0          | minPrice=7   | Initial min price |
| 1 | 1      | 1        | 0          | minPrice=1   | Update minPrice to 1 |
| 2 | 5      | 1        | 4          | 5 - 1=4 > 0 | Update maxProfit to 4 |
| 3 | 3      | 1        | 4          | 3 - 1=2 < 4 | No change |
| 4 | 6      | 1        | 5          | 6 - 1=5 > 4 | Update maxProfit to 5 |
| 5 | 4      | 1        | 5          | 4 - 1=3 < 5 | No change |

**Result:** Max profit = **5**

---

## Summary
| Method | Approach | Time Complexity | Space Complexity | Key Points |
|---------|--------------|-------------------|---------------------|--------------|
| 1 | Brute Force | **O(n^2)** | **O(1)** | Checks all pairs, inefficient for large inputs. |
| 2 | One Pass / Greedy | **O(n)** | **O(1)** | Efficient, tracks min price and max profit dynamically. |

---

## Final Tips for Interviews
- Always aim for the **O(n)** solution with a single pass.
- Understand the intuition: **tracking minimum price seen so far** helps to evaluate potential profit efficiently.
- Practice variations like multiple transactions, or maximizing profit with cooldowns, to deepen understanding.

---

**Happy Coding!**
