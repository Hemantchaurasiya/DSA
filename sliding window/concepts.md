

Algorithms: (Fixed size sliding window)
1. Hit the window with calculation
2. slide the window
    - increase the left pointer of the window
    - increase the right pointer of the window
    - calculate ans

```java
public int fixedSizeSlidingWindow(int[] nums, int windowSize){
    int windowSum = 0;
    int maxSum = 0;

    for(int i=0; i < windowSize; i++){
        windowSum += nums[i];
    }
    maxSum = windowSum;

    for (int right=windowSize; right < windowSize; ++right){
        int left = right - windowSize;
        // remove left data from window
        windowSum -= nums[left];
        // add right data in the window
        windowSum += nums[right];

        maxSum = Math.max(maxSum, windowSum);
    }
    return maxSum;
}
```


1.Max Sum Subarray of size K
Link: https://www.geeksforgeeks.org/problems/max-sum-subarray-of-size-k5313/1
Given an array of integers arr[]  and a number k. Return the maximum sum of a subarray of size k.

Note: A subarray is a contiguous part of any given array.

Examples:

Input: arr[] = [100, 200, 300, 400] , k = 2
Output: 700
Explanation: arr3  + arr4 = 700, which is maximum.

```java
class Solution {
    public int maximumSumSubarray(int[] arr, int k) {
        int windowSize = k;
        int windowSum = 0;
        int maxSum = 0;
        
        // Hit the window
        for(int i=0; i<windowSize; i++){
            windowSum += arr[i];
        }
        
        maxSum = windowSum;
        
        // Slide the window
        for(int right= windowSize; right< arr.length; ++right){
            // find the left pointer of the window
            int left = right - windowSize;

            // remove left pointer data from the window
            windowSum -= arr[left];

            // Add right pointer data in the window
            windowSum += arr[right];

            // Find answer of the current window
            maxSum = Math.max(maxSum, windowSum);
        }
        return maxSum;
    }
}
```

2. 

```java
private static T slidingWindowFlexibleLongest(List<T> input) {
    initialize window, ans
    int left = 0;
    for (int right = 0; right < input.size(); ++right) {
        append input.get(right) to window
        while (invalid(window)) {         // update left until window is valid again
            remove input.get(left) from window
            ++left;
        }
        ans = Math.max(ans, window); // window is guaranteed to be valid here
    }
    return ans;
}
```