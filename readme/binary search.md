# ✅ Binary Search Patterns with Key Interview Problems

Master binary search and its advanced variations with a categorized list of must-practice interview problems from LeetCode and GFG.

---

## 🧠 Table of Contents

1. [Classic Binary Search](#-pattern-1-classic-binary-search)
2. [Binary Search on Answers](#-pattern-2-binary-search-on-answers)
3. [Lower Bound / Upper Bound](#-pattern-3-lower-bound--upper-bound)
4. [Search in Rotated Sorted Array](#-pattern-4-search-in-rotated-sorted-array)
5. [Peak Element / Bitonic Search](#-pattern-5-peak-element--bitonic-search)
6. [2D Binary Search](#-pattern-6-2d-binary-search)
7. [Modified Binary Search](#-pattern-7-modified-binary-search)

---

## 🔍 Pattern 1: Classic Binary Search

**When to Use:** Searching for a specific value in a sorted array.

**Core Idea:** Repeatedly divide the search interval in half until the target is found or the interval is empty.

| # | Problem Name | GitHub Solution | LeetCode / GFG Link | Difficulty |
|--:|--------------------------------------|---------------------------------------------------------------------------------|----------------------|------------|
| 1 | Binary Search | [Solution](https://github.com/Hemantchaurasiya/DSA/blob/main/binarySearch/binarySearch.md) | [Link](https://leetcode.com/problems/binary-search/) | Easy |
| 2 | Search Insert Position | [Solution](https://github.com/Hemantchaurasiya/DSA/blob/main/binarySearch/searchInsertPosition.md) | [Link](https://leetcode.com/problems/search-insert-position/) | Easy |
| 3 | Find Smallest Letter Greater Than Target | [Solution](https://github.com/Hemantchaurasiya/DSA/blob/main/binarySearch/findSmallestLetterGreaterThanTarget.md) | [Link](https://leetcode.com/problems/find-smallest-letter-greater-than-target/) | Easy |
| 4 | Count Negative Numbers in a Sorted Matrix | [Solution](https://github.com/Hemantchaurasiya/DSA/blob/main/binarySearch/countNegativeNumbersInASortedMatrix.md) | [Link](https://leetcode.com/problems/count-negative-numbers-in-a-sorted-matrix/) | Easy |
| 5 | Find First and Last Position of Element in Sorted Array | [Solution](https://github.com/Hemantchaurasiya/DSA/blob/main/binarySearch/findFirstAndLastPositionOfElementInSortedArray.md) | [Link](https://leetcode.com/problems/find-first-and-last-position-of-element-in-sorted-array/) | Medium |
| 6 | Find Right Interval | [Solution](https://github.com/Hemantchaurasiya/DSA/blob/main/binarySearch/findRightInterval.md) | [Link](https://leetcode.com/problems/find-right-interval/) | Medium |
| 7 | Time Based Key-Value Store | [Solution](https://github.com/Hemantchaurasiya/DSA/blob/main/binarySearch/timeBasedKeyValueStore.md) | [Link](https://leetcode.com/problems/time-based-key-value-store/) | Medium |
| 8 | Snapshot Array | [Solution](https://github.com/Hemantchaurasiya/DSA/blob/main/binarySearch/snapshotArray.md) | [Link](https://leetcode.com/problems/snapshot-array/) | Medium |
| 9 | Search in a Sorted Array of Unknown Size | [Solution](https://github.com/Hemantchaurasiya/DSA/blob/main/binarySearch/searchInASortedArrayOfUnknownSize.md) | [Link](https://leetcode.com/problems/search-in-a-sorted-array-of-unknown-size/) | Medium |
| 10| Guess Number Higher or Lower | [Solution](https://github.com/Hemantchaurasiya/DSA/blob/main/binarySearch/guessNumberHigherOrLower.md) | [Link](https://leetcode.com/problems/guess-number-higher-or-lower/) | Easy |
| 11| First Bad Version | [Solution](https://github.com/Hemantchaurasiya/DSA/blob/main/binarySearch/firstBadVersion.md) | [Link](https://leetcode.com/problems/first-bad-version/) | Easy |

---

## 🧮 Pattern 2: Binary Search on Answers

**When to Use:** Problems where the answer lies in a numeric range and you must **optimize or satisfy a condition**.

**Core Idea:** Apply binary search to the **search space of potential answers**.

| # | Problem Name | GitHub Solution | LeetCode Link | Difficulty |
|--:|--------------------------------------|-------------------------------------------------------------------------------------------|---------------|------------|
| 1 | Valid Perfect Square | [Solution](https://github.com/Hemantchaurasiya/DSA/blob/main/binarySearch/validPerfectSquare.md) | [Link](https://leetcode.com/problems/valid-perfect-square/) | Easy |
| 2 | Sqrt(x) | [Solution](https://github.com/Hemantchaurasiya/DSA/blob/main/binarySearch/sqrtX.md) | [Link](https://leetcode.com/problems/sqrtx/) | Easy |
| 3 | Arranging Coins | [Solution](https://github.com/Hemantchaurasiya/DSA/blob/main/binarySearch/arrangingCoins.md) | [Link](https://leetcode.com/problems/arranging-coins/) | Easy |
| 4 | Kth Missing Positive Number | [Solution](https://github.com/Hemantchaurasiya/DSA/blob/main/binarySearch/kthMissingPositiveNumber.md) | [Link](https://leetcode.com/problems/kth-missing-positive-number/) | Easy |
| 5 | H-Index II | [Solution](https://github.com/Hemantchaurasiya/DSA/blob/main/binarySearch/hIndexII.md) | [Link](https://leetcode.com/problems/h-index-ii/) | Medium |
| 6 | Single Element in a Sorted Array | [Solution](https://github.com/Hemantchaurasiya/DSA/blob/main/binarySearch/singleElementInASortedArray.md) | [Link](https://leetcode.com/problems/single-element-in-a-sorted-array/) | Medium |
| 7 | Find K Closest Elements | [Solution](https://github.com/Hemantchaurasiya/DSA/blob/main/binarySearch/findKClosestElements.md) | [Link](https://leetcode.com/problems/find-k-closest-elements/) | Medium |
| 8 | Valid Triangle Number | [Solution](https://github.com/Hemantchaurasiya/DSA/blob/main/binarySearch/validTriangleNumber.md) | [Link](https://leetcode.com/problems/valid-triangle-number/) | Medium |
| 9 | Successful Pairs of Spells and Potions | [Solution](https://github.com/Hemantchaurasiya/DSA/blob/main/binarySearch/successfulPairsOfSpellsAndPotions.md) | [Link](https://leetcode.com/problems/successful-pairs-of-spells-and-potions/) | Medium |
|10 | Number of Subsequences That Satisfy the Given Sum Condition | [Solution](https://github.com/Hemantchaurasiya/DSA/blob/main/binarySearch/numberOfSubsequencesThatSatisfyTheGivenSumCondition.md) | [Link](https://leetcode.com/problems/number-of-subsequences-that-satisfy-the-given-sum-condition/) | Medium |
|11 | Random Pick with Weight | [Solution](https://github.com/Hemantchaurasiya/DSA/blob/main/binarySearch/randomPickWithWeight.md) | [Link](https://leetcode.com/problems/random-pick-with-weight/) | Medium |
|12 | Minimum Number of Days to Make m Bouquets | [Solution](https://github.com/Hemantchaurasiya/DSA/blob/main/binarySearch/minimumNumberOfDaysToMakeMBouquets.md) | [Link](https://leetcode.com/problems/minimum-number-of-days-to-make-m-bouquets/) | Medium |
|13 | Koko Eating Bananas | [Solution](https://github.com/Hemantchaurasiya/DSA/blob/main/binarySearch/kokoEatingBananas.md) | [Link](https://leetcode.com/problems/koko-eating-bananas/) | Medium |
|14 | Capacity To Ship Packages Within D Days | [Solution](https://github.com/Hemantchaurasiya/DSA/blob/main/binarySearch/capacityToShipPackagesWithinDDays.md) | [Link](https://leetcode.com/problems/capacity-to-ship-packages-within-d-days/) | Medium |
|15 | Median of Two Sorted Arrays | [Solution](https://github.com/Hemantchaurasiya/DSA/blob/main/binarySearch/medianOfTwoSortedArrays.md) | [Link](https://leetcode.com/problems/median-of-two-sorted-arrays/) | Hard |

---

## 🎯 Pattern 3: Lower Bound / Upper Bound

**When to Use:** When the problem is about finding **first or last** occurrence or determining insertion points.

**Core Idea:** Binary search with boundaries — often used when duplicates exist.

| # | Problem Name | GitHub Solution | LeetCode Link | Difficulty |
|--:|--------------------------------------|---------------------------------------------------------------------------------|---------------|------------|
| 1 | Search Insert Position | [Solution](https://github.com/Hemantchaurasiya/DSA/blob/main/binarySearch/searchInsertPosition.md) | [Link](https://leetcode.com/problems/search-insert-position/) | Easy |
| 2 | Find First and Last Position of Element in Sorted Array | [Solution](https://github.com/Hemantchaurasiya/DSA/blob/main/binarySearch/findFirstAndLastPositionOfElementInSortedArray.md) | [Link](https://leetcode.com/problems/find-first-and-last-position-of-element-in-sorted-array/) | Medium |
| 3 | Find Smallest Letter Greater Than Target | [Solution](https://github.com/Hemantchaurasiya/DSA/blob/main/binarySearch/findSmallestLetterGreaterThanTarget.md) | [Link](https://leetcode.com/problems/find-smallest-letter-greater-than-target/) | Easy |

---

## 🔄 Pattern 4: Search in Rotated Sorted Array

**When to Use:** If the sorted array has been **rotated** at some pivot.

**Core Idea:** Modify binary search to check which half is sorted and adjust accordingly.

| # | Problem Name | GitHub Solution | LeetCode / GFG Link | Difficulty |
|--:|--------------------------------------|-------------------------------------------------------------------------------|----------------------|------------|
| 1 | Search in Rotated Sorted Array | [Solution](https://github.com/Hemantchaurasiya/DSA/blob/main/binarySearch/searchInRotatedSortedArray.md) | [Link](https://leetcode.com/problems/search-in-rotated-sorted-array/) | Medium |
| 2 | Find Minimum in Rotated Sorted Array | [Solution](https://github.com/Hemantchaurasiya/DSA/blob/main/binarySearch/findMinimumInRotatedSortedArray.md) | [Link](https://leetcode.com/problems/find-minimum-in-rotated-sorted-array/) | Medium |
| 3 | Find Minimum in Rotated Sorted Array II | [Solution](https://github.com/Hemantchaurasiya/DSA/blob/main/binarySearch/findMinimumInRotatedSortedArrayII.md) | [Link](https://leetcode.com/problems/find-minimum-in-rotated-sorted-array-ii/) | Hard |
| 4 | Rotation Count | [Solution](https://github.com/Hemantchaurasiya/DSA/blob/main/binarySearch/rotationCount.md) | [Link](https://www.geeksforgeeks.org/find-rotation-count-rotated-sorted-array/) | Medium |

---

## ⛰️ Pattern 5: Peak Element / Bitonic Search

**When to Use:** For **unimodal arrays** or arrays where you're asked to find a peak or max element.

**Core Idea:** Binary search based on slope direction — ideal for **peak finding**.

| # | Problem Name | GitHub Solution | LeetCode / GFG Link | Difficulty |
|--:|--------------------------------------|-----------------------------------------------------------------------------|----------------------|------------|
| 1 | Peak Index in a Mountain Array | [Solution](https://github.com/Hemantchaurasiya/DSA/blob/main/binarySearch/peakIndexInAMountainArray.md) | [Link](https://leetcode.com/problems/peak-index-in-a-mountain-array/) | Medium |
| 2 | Find Peak Element | [Solution](https://github.com/Hemantchaurasiya/DSA/blob/main/binarySearch/findPeakElement.md) | [Link](https://leetcode.com/problems/find-peak-element/) | Medium |
| 3 | Bitonic Array Maximum | [Solution](https://github.com/Hemantchaurasiya/DSA/blob/main/binarySearch/bitonicArrayMaximum.md) | [Link](https://www.geeksforgeeks.org/find-maximum-element-bitonic-array/) | Easy |
| 4 | Search Bitonic Array (Challenge) | [Solution](https://github.com/Hemantchaurasiya/DSA/blob/main/binarySearch/searchBitonicArrayChallenge.md) | Custom Problem | Medium |

---

## 🧱 Pattern 6: 2D Binary Search

**When to Use:** For matrix-based problems with **row or column-wise sorted data**.

**Core Idea:** Treat matrix like a 1D array or apply binary search on rows and columns.

| # | Problem Name | GitHub Solution | LeetCode Link | Difficulty |
|--:|--------------------------------------|---------------------------------------------------------------------------------|---------------|------------|
| 1 | Search a 2D Matrix | [Solution](https://github.com/Hemantchaurasiya/DSA/blob/main/binarySearch/searchA2DMatrix.md) | [Link](https://leetcode.com/problems/search-a-2d-matrix/) | Medium |
| 2 | Count Negative Numbers in a Sorted Matrix | [Solution](https://github.com/Hemantchaurasiya/DSA/blob/main/binarySearch/countNegativeNumbersInASortedMatrix.md) | [Link](https://leetcode.com/problems/count-negative-numbers-in-a-sorted-matrix/) | Easy |

---

## 🧪 Pattern 7: Modified Binary Search

**When to Use:** When data or array conditions are **non-standard** (e.g., order-agnostic, infinite arrays, characters, etc.)

**Core Idea:** Customize standard binary search for unique scenarios.

| # | Problem Name | GitHub Solution | LeetCode / GFG Link | Difficulty |
|--:|--------------------------------------|------------------------------------------------------------------------------|----------------------|------------|
| 1 | Order-agnostic Binary Search | [Solution](https://github.com/Hemantchaurasiya/DSA/blob/main/binarySearch/orderAgnosticBinarySearch.md) | [Link](https://www.geeksforgeeks.org/order-agnostic-binary-search/) | Easy |
| 2 | Ceiling of a Number | [Solution](https://github.com/Hemantchaurasiya/DSA/blob/main/binarySearch/ceilingOfANumber.md) | [Link](https://www.geeksforgeeks.org/ceiling-in-a-sorted-array/) | Medium |
| 3 | Floor of a Number | [Solution](https://github.com/Hemantchaurasiya/DSA/blob/main/binarySearch/floorOfANumber.md) | [Link](https://www.geeksforgeeks.org/floor-in-a-sorted-array/) | Medium |
| 4 | Next Letter | [Solution](https://github.com/Hemantchaurasiya/DSA/blob/main/binarySearch/nextLetter.md) | [Link](https://leetcode.com/problems/find-smallest-letter-greater-than-target/) | Medium |
| 5 | Number Range | [Solution](https://github.com/Hemantchaurasiya/DSA/blob/main/binarySearch/numberRange.md) | [Link](https://leetcode.com/problems/find-first-and-last-position-of-element-in-sorted-array/) | Medium |
| 6 | Search in a Sorted Infinite Array | [Solution](https://github.com/Hemantchaurasiya/DSA/blob/main/binarySearch/searchInASortedInfiniteArray.md) | [Link](https://leetcode.com/problems/search-in-a-sorted-array-of-unknown-size/) | Medium |
| 7 | Minimum Difference Element (Custom) | [Solution](https://github.com/Hemantchaurasiya/DSA/blob/main/binarySearch/minimumDifferenceElementCustom.md) | Custom Problem | Medium |

---

## 📌 Tips for Mastery

- Focus on understanding the **conditions to apply binary search**.
- Practice converting problems to **binary search on range**, not just array index.
- Identify **monotonic properties** in problems.
- Learn how to code **both lower and upper bound variants**.
- Visualize the **binary search decision space** to understand convergence.

---