# 🧠 Dynamic Programming

This repository contains categorized practice problems and solutions for Dynamic Programming (DP) Level 1. Each section includes problems related to specific DP patterns to help reinforce understanding and improve problem-solving skills.

---

## 📌 Table of Contents

-   [Linear DP](#-linear-dp)
-   [2D DP](#-2-dimensional-dp)
-   [DP on Grid](#-dp-on-grid)
-   [Knapsack DP](#-knapsack-dp)
-   [Longest Increasing Subsequence](#-longest-increasing-subsequence)
-   [Longest Common Subsequence](#-longest-common-subsequence)
-   [DP on String](#-dp-on-string)
-   [Cumulative Sum](#-cumulative-sum)
-   [Matrix Chain Multiplication](#-matrix-chain-multiplication)
-   [Kadane’s Algorithm](#️-kadanes-algorithm)
-   [DP on Trees](#-dp-on-trees)
-   [Bitmask DP](#-bitmask-dp)
-   [Digit DP](#-digit-dp)

---

## 🔁 Linear DP

Linear DP problems involve states that can be represented by a single dimension, typically an index `i`, referring to the `i-th` element of an array or the `i-th` step in a sequence.

| Problem Name                        | GitHub Link                                                 | LeetCode Link                                                       | Difficulty |
| :---------------------------------- | :---------------------------------------------------------- | :------------------------------------------------------------------ | :--------- |
| Climbing Stairs                     | [GitHub](https://github.com/Hemantchaurasiya/DSA/blob/main/dp/climbingStairs.md) | [LeetCode](https://leetcode.com/problems/climbing-stairs/)          | Easy       |
| Min Cost Climbing Stairs            | [GitHub](https://github.com/Hemantchaurasiya/DSA/blob/main/dp/minCostClimbingStairs.md) | [LeetCode](https://leetcode.com/problems/min-cost-climbing-stairs/) | Easy       |
| N-th Tribonacci Number              | [GitHub](https://github.com/Hemantchaurasiya/DSA/blob/main/dp/nThTribonacciNumber.md) | [LeetCode](https://leetcode.com/problems/n-th-tribonacci-number/)   | Easy       |
| House Robber                        | [GitHub](https://github.com/Hemantchaurasiya/DSA/blob/main/dp/houseRobber.md) | [LeetCode](https://leetcode.com/problems/house-robber/)             | Medium     |
| House Robber II                     | [GitHub](https://github.com/Hemantchaurasiya/DSA/blob/main/dp/houseRobberII.md) | [LeetCode](https://leetcode.com/problems/house-robber-ii/)          | Medium     |
| Coin Change                         | [GitHub](https://github.com/Hemantchaurasiya/DSA/blob/main/dp/coinChange.md) | [LeetCode](https://leetcode.com/problems/coin-change/)              | Medium     |
| Decode Ways                         | [GitHub](https://github.com/Hemantchaurasiya/DSA/blob/main/dp/decodeWays.md) | [LeetCode](https://leetcode.com/problems/decode-ways/)              | Medium     |
| Perfect Squares                     | [GitHub](https://github.com/Hemantchaurasiya/DSA/blob/main/dp/perfectSquares.md) | [LeetCode](https://leetcode.com/problems/perfect-squares/)          | Medium     |
| Integer Break                       | [GitHub](https://github.com/Hemantchaurasiya/DSA/blob/main/dp/integerBreak.md) | [LeetCode](https://leetcode.com/problems/integer-break/)            | Medium     |
| Best Time to Buy and Sell Stock     | [GitHub](https://github.com/Hemantchaurasiya/DSA/blob/main/dp/bestTimeToBuyAndSellStock.md) | [LeetCode](https://leetcode.com/problems/best-time-to-buy-and-sell-stock/) | Easy       |
| Maximum Total Damage With Spell Casting | [GitHub](https://github.com/Hemantchaurasiya/DSA/blob/main/dp/maximumTotalDamageWithSpellCasting.md) | *(No Direct LeetCode Link Provided in Original)* | Medium     |

---

## 🧮 2 Dimensional DP

2D DP problems involve states represented by two parameters, often `dp[i][j]`, which could refer to considering elements up to index `i` with some property `j`.

| Problem Name                             | GitHub Link                                                                                         | LeetCode Link                                                                                 | Difficulty |
| :--------------------------------------- | :-------------------------------------------------------------------------------------------------- | :------------------------------------------------------------------------------------------ | :--------- |
| Coin Change II                           | [GitHub](https://github.com/Hemantchaurasiya/DSA/blob/main/dp/coinChangeII.md)                      | [LeetCode](https://leetcode.com/problems/coin-change-ii/)                                   | Medium     |
| Partition Equal Subset Sum               | [GitHub](https://github.com/Hemantchaurasiya/DSA/blob/main/dp/partitionEqualSubsetSum.md)           | [LeetCode](https://leetcode.com/problems/partition-equal-subset-sum/)                       | Medium     |
| Longest Arithmetic Subsequence           | [GitHub](https://github.com/Hemantchaurasiya/DSA/blob/main/dp/longestArithmeticSubsequence.md)      | [LeetCode](https://leetcode.com/problems/longest-arithmetic-subsequence/)                   | Medium     |
| Best Time to Buy and Sell Stock with Cooldown | [GitHub](https://github.com/Hemantchaurasiya/DSA/blob/main/dp/bestTimeToBuyAndSellStockWithCooldown.md) | [LeetCode](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-with-cooldown/)    | Medium     |
| Best Time to Buy and Sell Stock IV       | [GitHub](https://github.com/Hemantchaurasiya/DSA/blob/main/dp/bestTimeToBuyAndSellStockIV.md)       | [LeetCode](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-iv/)               | Hard       |
| Best Time to Buy and Sell Stock with Fee | [GitHub](https://github.com/Hemantchaurasiya/DSA/blob/main/dp/bestTimeToBuyAndSellStockWithFee.md) | [LeetCode](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-with-transaction-fee/) | Medium     |
| Stone Game II                            | [GitHub](https://github.com/Hemantchaurasiya/DSA/blob/main/dp/stoneGameII.md)                       | [LeetCode](https://leetcode.com/problems/stone-game-ii/)                                    | Medium     |
| Frog Jump                                | [GitHub](https://github.com/Hemantchaurasiya/DSA/blob/main/dp/frogJump.md)                          | [LeetCode](https://leetcode.com/problems/frog-jump/)                                        | Hard       |
| Stone Game III                           | [GitHub](https://github.com/Hemantchaurasiya/DSA/blob/main/dp/stoneGameIII.md)                      | [LeetCode](https://leetcode.com/problems/stone-game-iii/)                                   | Hard       |

---

## 🧭 DP On Grid

DP on grids involves problems where the state `dp[i][j]` represents a solution for the cell `(i,j)` or a subgrid ending at `(i,j)`. Transitions often come from adjacent cells.

| Problem Name                        | GitHub Link                                                                                       | LeetCode Link                                                                | Difficulty |
| :---------------------------------- | :------------------------------------------------------------------------------------------------ | :--------------------------------------------------------------------------- | :--------- |
| Unique Paths                        | [GitHub](https://github.com/Hemantchaurasiya/DSA/blob/main/dp/uniquePaths.md)                     | [LeetCode](https://leetcode.com/problems/unique-paths/)                      | Medium     |
| Unique Paths II                     | [GitHub](https://github.com/Hemantchaurasiya/DSA/blob/main/dp/uniquePathsII.md)                   | [LeetCode](https://leetcode.com/problems/unique-paths-ii/)                   | Medium     |
| Minimum Path Sum                    | [GitHub](https://github.com/Hemantchaurasiya/DSA/blob/main/dp/minimumPathSum.md)                  | [LeetCode](https://leetcode.com/problems/minimum-path-sum/)                  | Medium     |
| Geek's Training / Ninja's Training  | [GitHub](https://github.com/Hemantchaurasiya/DSA/blob/main/dp/geeksTrainingOrNinjasTraining.md) | *(Similar to [Paint House](https://leetcode.com/problems/paint-house/) or activity selection)* | Medium   |
| Maximum Difference Score in a Grid  | [GitHub](https://github.com/Hemantchaurasiya/DSA/blob/main/dp/maximumDifferenceScoreInAGrid.md) | *(No Direct LeetCode Link Provided in Original)* | Medium     |
| Longest Increasing Path In a Matrix | [GitHub](https://github.com/Hemantchaurasiya/DSA/blob/main/dp/longestIncreasingPathInAMatrix.md) | [LeetCode](https://leetcode.com/problems/longest-increasing-path-in-a-matrix/) | Hard       |
| Cherry Pickup                       | [GitHub](https://github.com/Hemantchaurasiya/DSA/blob/main/dp/cherryPickup.md)                    | [LeetCode](https://leetcode.com/problems/cherry-pickup/)                     | Hard       |
| Number of Paths with Max Score      | [GitHub](https://github.com/Hemantchaurasiya/DSA/blob/main/dp/numberOfPathsWithMaxScore.md)      | [LeetCode](https://leetcode.com/problems/number-of-paths-with-max-score/)    | Hard       |
| Minimum Falling Path Sum II         | [GitHub](https://github.com/Hemantchaurasiya/DSA/blob/main/dp/minimumFallingPathSumII.md)         | [LeetCode](https://leetcode.com/problems/minimum-falling-path-sum-ii/)       | Hard       |

---

## 🎒 Knapsack DP

Knapsack problems deal with selecting items to maximize (or minimize) some value given a constraint on capacity (weight). Variations include 0/1 Knapsack and Unbounded Knapsack.

| Problem Name                 | GitHub Link                                                                                       | LeetCode Link                                                                    | Notes                                      | Difficulty |
| :--------------------------- | :------------------------------------------------------------------------------------------------ | :------------------------------------------------------------------------------- | :----------------------------------------- | :--------- |
| Knapsack - 0/1               | [GitHub](https://github.com/Hemantchaurasiya/DSA/blob/main/dp/knapsack01.md)                     | *(Classic problem, see [Partition Equal Subset Sum](https://leetcode.com/problems/partition-equal-subset-sum/) as a variation)* | Maximize value given weight limit, pick once | Medium     |
| Knapsack - Unbounded         | [GitHub](https://github.com/Hemantchaurasiya/DSA/blob/main/dp/knapsackUnbounded.md)             | *(Classic problem, see [Coin Change](https://leetcode.com/problems/coin-change/) for min items, [Coin Change II](https://leetcode.com/problems/coin-change-ii/) for ways)* | Pick items multiple times                | Medium     |
| Target Sum                   | [GitHub](https://github.com/Hemantchaurasiya/DSA/blob/main/dp/targetSum.md)                     | [LeetCode](https://leetcode.com/problems/target-sum/)                            | Assign +/- to make sum                     | Medium     |
| Combination Sum IV           | [GitHub](https://github.com/Hemantchaurasiya/DSA/blob/main/dp/combinationSumIV.md)               | [LeetCode](https://leetcode.com/problems/combination-sum-iv/)                    | Ways to sum to target, order matters       | Medium     |
| Shopping Offers              | [GitHub](https://github.com/Hemantchaurasiya/DSA/blob/main/dp/shoppingOffers.md)                 | [LeetCode](https://leetcode.com/problems/shopping-offers/)                       |                                            | Medium     |
| Last Stone Weight II         | [GitHub](https://github.com/Hemantchaurasiya/DSA/blob/main/dp/lastStoneWeightII.md)              | [LeetCode](https://leetcode.com/problems/last-stone-weight-ii/)                  | Minimize last stone, subset sum variant  | Medium     |
| Rod Cutting                  | [GitHub](https://github.com/Hemantchaurasiya/DSA/blob/main/dp/rodCutting.md)                     | *(Classic problem, similar to Unbounded Knapsack, e.g., [GFG](https://www.geeksforgeeks.org/rod-cutting-dp-13/))* | Maximize profit from cutting rod         | Medium     |
| Profitable Schemes           | [GitHub](https://github.com/Hemantchaurasiya/DSA/blob/main/dp/profitableSchemes.md)              | [LeetCode](https://leetcode.com/problems/profitable-schemes/)                    | 0/1 Knapsack with multiple constraints     | Hard       |

---

## 📈 Longest Increasing Subsequence

LIS problems focus on finding the longest subsequence of elements in an array such that all elements of the subsequence are sorted in increasing order.

| Problem Name                             | GitHub Link                                                                                         | LeetCode Link                                                                          | Difficulty |
| :--------------------------------------- | :-------------------------------------------------------------------------------------------------- | :------------------------------------------------------------------------------------- | :--------- |
| Longest Increasing Subsequence           | [GitHub](https://github.com/Hemantchaurasiya/DSA/blob/main/dp/longestIncreasingSubsequence.md)      | [LeetCode](https://leetcode.com/problems/longest-increasing-subsequence/)              | Medium     |
| Printing Longest Increasing Subsequence  | [GitHub](https://github.com/Hemantchaurasiya/DSA/blob/main/dp/printingLongestIncreasingSubsequence.md) | *(Follow-up to LIS, reconstruct path)* | Medium     |
| Number of Longest Increasing Subsequence | [GitHub](https://github.com/Hemantchaurasiya/DSA/blob/main/dp/numberOfLongestIncreasingSubsequence.md) | [LeetCode](https://leetcode.com/problems/number-of-longest-increasing-subsequence/)    | Medium     |
| Largest Divisible Subset                 | [GitHub](https://github.com/Hemantchaurasiya/DSA/blob/main/dp/largestDivisibleSubset.md)            | [LeetCode](https://leetcode.com/problems/largest-divisible-subset/)                    | Medium     |
| Maximum Height by Stacking Cuboids       | [GitHub](https://github.com/Hemantchaurasiya/DSA/blob/main/dp/maximumHeightByStackingCuboids.md)    | [LeetCode](https://leetcode.com/problems/maximum-height-by-stacking-cuboids/)        | Hard       |

---

## 🔁 Longest Common Subsequence

LCS problems involve finding the longest subsequence common to two (or more) sequences. It has many applications like diff utilities and bioinformatics.

| Problem Name                                   | GitHub Link                                                                                                 | LeetCode Link                                                                                     | Difficulty |
| :--------------------------------------------- | :---------------------------------------------------------------------------------------------------------- | :---------------------------------------------------------------------------------------------- | :--------- |
| Longest Common Subsequence                     | [GitHub](https://github.com/Hemantchaurasiya/DSA/blob/main/dp/longestCommonSubsequence.md)                  | [LeetCode](https://leetcode.com/problems/longest-common-subsequence/)                           | Medium     |
| Edit Distance                                  | [GitHub](https://github.com/Hemantchaurasiya/DSA/blob/main/dp/editDistance.md)                              | [LeetCode](https://leetcode.com/problems/edit-distance/)                                        | Medium     |
| Print all LCS sequences                        | [GitHub](https://github.com/Hemantchaurasiya/DSA/blob/main/dp/printAllLCSSequences.md)                      | *(Follow-up to LCS, reconstruct all paths)* | Hard       |
| Longest Palindromic Subsequence                | [GitHub](https://github.com/Hemantchaurasiya/DSA/blob/main/dp/longestPalindromicSubsequence.md)             | [LeetCode](https://leetcode.com/problems/longest-palindromic-subsequence/)                    | Medium     |
| Wildcard Matching                              | [GitHub](https://github.com/Hemantchaurasiya/DSA/blob/main/dp/wildcardMatching.md)                          | [LeetCode](https://leetcode.com/problems/wildcard-matching/)                                    | Hard       |
| Shortest Common Supersequence                  | [GitHub](https://github.com/Hemantchaurasiya/DSA/blob/main/dp/shortestCommonSupersequence.md)               | [LeetCode](https://leetcode.com/problems/shortest-common-supersequence/)                      | Hard       |
| Minimum Insertion Steps to Make a Palindrome | [GitHub](https://github.com/Hemantchaurasiya/DSA/blob/main/dp/minimumInsertionStepsToMakeAPalindrome.md) | [LeetCode](https://leetcode.com/problems/minimum-insertion-steps-to-make-a-string-palindrome/) | Hard       |

---

## 🔤 DP on String

This category includes DP problems specifically tailored to string manipulations, often involving subsequences, substrings, or matching.

| Problem Name                    | GitHub Link                                                                                       | LeetCode Link                                                                | Difficulty |
| :------------------------------ | :------------------------------------------------------------------------------------------------ | :--------------------------------------------------------------------------- | :--------- |
| Longest Palindromic Substring   | [GitHub](https://github.com/Hemantchaurasiya/DSA/blob/main/dp/longestPalindromicSubstring.md)   | [LeetCode](https://leetcode.com/problems/longest-palindromic-substring/)     | Medium     |
| Palindromic Substrings          | [GitHub](https://github.com/Hemantchaurasiya/DSA/blob/main/dp/palindromicSubstrings.md)            | [LeetCode](https://leetcode.com/problems/palindromic-substrings/)            | Medium     |
| Word Break                      | [GitHub](https://github.com/Hemantchaurasiya/DSA/blob/main/dp/wordBreak.md)                        | [LeetCode](https://leetcode.com/problems/word-break/)                        | Medium     |
| Interleaving String             | [GitHub](https://github.com/Hemantchaurasiya/DSA/blob/main/dp/interleavingString.md)               | [LeetCode](https://leetcode.com/problems/interleaving-string/)               | Medium     |
| Distinct Subsequences           | [GitHub](https://github.com/Hemantchaurasiya/DSA/blob/main/dp/distinctSubsequences.md)             | [LeetCode](https://leetcode.com/problems/distinct-subsequences/)             | Hard       |
| Word Break II                   | [GitHub](https://github.com/Hemantchaurasiya/DSA/blob/main/dp/wordBreakII.md)                      | [LeetCode](https://leetcode.com/problems/word-break-ii/)                     | Hard       |
| Regular Expression Matching     | [GitHub](https://github.com/Hemantchaurasiya/DSA/blob/main/dp/regularExpressionMatching.md)       | [LeetCode](https://leetcode.com/problems/regular-expression-matching/)       | Hard       |

---

## 📊 Cumulative Sum (Often used with DP)

Problems where pre-calculating cumulative sums (prefix sums) can optimize DP transitions or help define DP states, especially in grid or array problems.

| Problem Name                             | GitHub Link                                                                                     | LeetCode Link                                                                         | Difficulty |
| :--------------------------------------- | :---------------------------------------------------------------------------------------------- | :------------------------------------------------------------------------------------ | :--------- |
| Maximal Square                           | [GitHub](https://github.com/Hemantchaurasiya/DSA/blob/main/dp/maximalSquare.md)                 | [LeetCode](https://leetcode.com/problems/maximal-square/)                             | Medium     |
| Count Square Submatrices with All Ones   | [GitHub](https://github.com/Hemantchaurasiya/DSA/blob/main/dp/countSquareSubmatricesWithAllOnes.md) | [LeetCode](https://leetcode.com/problems/count-square-submatrices-with-all-ones/)     | Medium     |
| Maximal Rectangle                        | [GitHub](https://github.com/Hemantchaurasiya/DSA/blob/main/dp/maximalRectangle.md)              | [LeetCode](https://leetcode.com/problems/maximal-rectangle/)                          | Hard       |
| Number of Submatrices That Sum to Target | [GitHub](https://github.com/Hemantchaurasiya/DSA/blob/main/dp/numberOfSubmatricesThatSumToTarget.md) | [LeetCode](https://leetcode.com/problems/number-of-submatrices-that-sum-to-target/) | Hard       |

---

## ⛓️ Matrix Chain Multiplication (MCM) / Interval DP

MCM is a pattern for problems where you need to find the optimal way to parenthesize an expression or break down a problem into two subproblems with a final operation. This often applies to interval-based problems, including some games.

| Problem Name                             | GitHub Link                                                                                         | LeetCode Link                                                                            | Notes                                     | Difficulty |
| :--------------------------------------- | :-------------------------------------------------------------------------------------------------- | :--------------------------------------------------------------------------------------- | :---------------------------------------- | :--------- |
| Matrix Chain Multiplication              | [GitHub](https://github.com/Hemantchaurasiya/DSA/blob/main/dp/matrixChainMultiplication.md)         | *(Classic problem, see [Burst Balloons](https://leetcode.com/problems/burst-balloons/) as an application)* | Optimal parenthesization                | Hard       |
| Burst Balloons                           | [GitHub](https://github.com/Hemantchaurasiya/DSA/blob/main/dp/burstBalloons.md)                     | [LeetCode](https://leetcode.com/problems/burst-balloons/)                                | MCM variant                               | Hard       |
| Stone Game                               | [GitHub](https://github.com/Hemantchaurasiya/DSA/blob/main/dp/stoneGame.md)                         | [LeetCode](https://leetcode.com/problems/stone-game/)                                    | Interval DP, Game Theory                  | Medium     |
| Minimum Score Triangulation of Polygon   | [GitHub](https://github.com/Hemantchaurasiya/DSA/blob/main/dp/minimumScoreTriangulationOfPolygon.md) | [LeetCode](https://leetcode.com/problems/minimum-score-triangulation-of-polygon/)      | MCM variant                               | Medium     |
| Partition Array for Maximum Sum          | [GitHub](https://github.com/Hemantchaurasiya/DSA/blob/main/dp/partitionArrayForMaximumSum.md)       | [LeetCode](https://leetcode.com/problems/partition-array-for-maximum-sum/)             |                                           | Medium     |
| Palindrome Partitioning II               | [GitHub](https://github.com/Hemantchaurasiya/DSA/blob/main/dp/palindromePartitioningII.md)          | [LeetCode](https://leetcode.com/problems/palindrome-partitioning-ii/)                    |                                           | Hard       |
| Strange Printer                          | [GitHub](https://github.com/Hemantchaurasiya/DSA/blob/main/dp/strangePrinter.md)                    | [LeetCode](https://leetcode.com/problems/strange-printer/)                               |                                           | Hard       |

---

## ⚡ Kadane’s Algorithm

Kadane's algorithm is used to find the maximum sum contiguous subarray. It's a simple yet powerful linear time DP approach.

| Problem Name                             | GitHub Link                                                                                   | LeetCode Link                                                                         | Difficulty |
| :--------------------------------------- | :-------------------------------------------------------------------------------------------- | :------------------------------------------------------------------------------------ | :--------- |
| Maximum Subarray                         | [GitHub](https://github.com/Hemantchaurasiya/DSA/blob/main/dp/maximumSubarray.md)             | [LeetCode](https://leetcode.com/problems/maximum-subarray/)                           | Medium     |
| Maximum Product Subarray                 | [GitHub](https://github.com/Hemantchaurasiya/DSA/blob/main/dp/maximumProductSubarray.md)      | [LeetCode](https://leetcode.com/problems/maximum-product-subarray/)                   | Medium     |
| Maximum Subarray Sum with One Deletion   | [GitHub](https://github.com/Hemantchaurasiya/DSA/blob/main/dp/maximumSubarraySumWithOneDeletion.md) | [LeetCode](https://leetcode.com/problems/maximum-subarray-sum-with-one-deletion/)     | Medium     |
| Length of Longest Fibonacci Subsequence  | [GitHub](https://github.com/Hemantchaurasiya/DSA/blob/main/dp/lengthOfLongestFibonacciSubsequence.md) | [LeetCode](https://leetcode.com/problems/length-of-longest-fibonacci-subsequence/)    | Medium     |

---

## 🌳 DP on Trees

Dynamic Programming on trees involves solving problems by considering subproblems rooted at the children of a node. The state of the DP usually involves information about the subtree rooted at the current node.

| Problem Name                      | GitHub Link                                                                             | LeetCode Link                                                       | Difficulty |
| :-------------------------------- | :-------------------------------------------------------------------------------------- | :------------------------------------------------------------------ | :--------- |
| Diameter of Binary Tree           | [GitHub](https://github.com/Hemantchaurasiya/DSA/blob/main/dp/diameterOfBinaryTree.md)  | [LeetCode](https://leetcode.com/problems/diameter-of-binary-tree/)  | Easy       |
| Binary Tree Maximum Path Sum      | [GitHub](https://github.com/Hemantchaurasiya/DSA/blob/main/dp/binaryTreeMaximumPathSum.md) | [LeetCode](https://leetcode.com/problems/binary-tree-maximum-path-sum/) | Hard       |
| House Robber III                  | [GitHub](https://github.com/Hemantchaurasiya/DSA/blob/main/dp/houseRobberIII.md)         | [LeetCode](https://leetcode.com/problems/house-robber-iii/)         | Medium     |
| Longest Univalue Path             | [GitHub](https://github.com/Hemantchaurasiya/DSA/blob/main/dp/longestUnivaluePath.md)    | [LeetCode](https://leetcode.com/problems/longest-univalue-path/)    | Medium     |
| Sum of Distances in Tree          | [GitHub](https://github.com/Hemantchaurasiya/DSA/blob/main/dp/sumOfDistancesInTree.md) | [LeetCode](https://leetcode.com/problems/sum-of-distances-in-tree/) | Hard       |

---

## 🎭 Bitmask DP

Bitmask DP is used when the state of the DP depends on a subset of items, and the number of items is small enough (usually up to 20-25) to represent the subset as a bitmask (an integer).

| Problem Name                                      | GitHub Link                                                                                                     | LeetCode Link                                                                     | Notes                                             | Difficulty |
| :------------------------------------------------ | :-------------------------------------------------------------------------------------------------------------- | :-------------------------------------------------------------------------------- | :------------------------------------------------ | :--------- |
| Traveling Salesperson Problem                     | [GitHub](https://github.com/Hemantchaurasiya/DSA/blob/main/dp/travelingSalespersonProblem.md)                     | *(Classic problem, often adapted)* | Find shortest tour visiting all cities once     | Hard       |
| Assignment Problem / Min Cost to Assign Tasks     | [GitHub](https://github.com/Hemantchaurasiya/DSA/blob/main/dp/assignmentProblemOrMinCostToAssignTasks.md) | *(e.g., [Assign Cookies](https://leetcode.com/problems/assign-cookies/) - though simpler, or concepts in Smallest Sufficient Team)* | Assign tasks to workers with min cost           | Hard       |
| Smallest Sufficient Team                          | [GitHub](https://github.com/Hemantchaurasiya/DSA/blob/main/dp/smallestSufficientTeam.md)                          | [LeetCode](https://leetcode.com/problems/smallest-sufficient-team/)             |                                                   | Hard       |
| Number of Ways to Wear Different Hats to Each Other | [GitHub](https://github.com/Hemantchaurasiya/DSA/blob/main/dp/numberOfWaysToWearDifferentHatsToEachOther.md) | [LeetCode](https://leetcode.com/problems/number-of-ways-to-wear-different-hats-to-each-other/) |                                                   | Hard       |
| Maximum Compatibility Score Sum                   | [GitHub](https://github.com/Hemantchaurasiya/DSA/blob/main/dp/maximumCompatibilityScoreSum.md)                  | [LeetCode](https://leetcode.com/problems/maximum-compatibility-score-sum/)      |                                                   | Medium     |

---

## 🔢 Digit DP

Digit DP is a technique used to solve problems that ask to count numbers in a given range $[L, R]$ that satisfy a certain property based on their digits. The common approach is to calculate the count for $0 \text{ to } R$ and subtract the count for $0 \text{ to } L-1$.

| Problem Name                                  | GitHub Link                                                                             | LeetCode Link                                                                   | Difficulty |
| :-------------------------------------------- | :-------------------------------------------------------------------------------------- | :------------------------------------------------------------------------------ | :--------- |
| Numbers With Same Consecutive Differences     | [GitHub](https://github.com/Hemantchaurasiya/DSA/blob/main/dp/numbersWithSameConsecutiveDifferences.md) | [LeetCode](https://leetcode.com/problems/numbers-with-same-consecutive-differences/) | Medium     |
| Numbers At Most N Given Digit Set             | [GitHub](https://github.com/Hemantchaurasiya/DSA/blob/main/dp/numbersAtMostNGivenDigitSet.md) | [LeetCode](https://leetcode.com/problems/numbers-at-most-n-given-digit-set/)    | Hard       |
| Count of Integers                             | [GitHub](https://github.com/Hemantchaurasiya/DSA/blob/main/dp/countOfIntegers.md)         | [LeetCode](https://leetcode.com/problems/count-of-integers/)                    | Hard       |
| Find All Good Strings                         | [GitHub](https://github.com/Hemantchaurasiya/DSA/blob/main/dp/findAllGoodStrings.md)      | [LeetCode](https://leetcode.com/problems/find-all-good-strings/)                | Hard       |
| Numbers with Repeated Digits                  | [GitHub](https://github.com/Hemantchaurasiya/DSA/blob/main/dp/numbersWithRepeatedDigits.md) | [LeetCode](https://leetcode.com/problems/numbers-with-repeated-digits/)         | Hard       |

---

> ✅ Note: Update each `[GitHub]()` placeholder with the respective problem solution link once you add the code files.