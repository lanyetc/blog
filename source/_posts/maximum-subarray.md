---
title: Maximum Subarray
date: 2020-9-30 17:36:52
tags: 
- Dynamic Programming
- Algorithm
---

#### Maximum Subarray

Given an integer array `nums`, find the contiguous subarray (containing at least one number) which has the largest sum and return its sum.

**Example:**

```
Input: [-2,1,-3,4,-1,2,1,-5,4],
Output: 6
Explanation: [4,-1,2,1] has the largest sum = 6.
```



**Solution 1: recursive algorithm**

- **Idea**

  For an element A[i] of an array A, the maximun subarray should be at the left of A[i], or the right, or include A[i] (acrossing it). Thus, we can just select maxLeftSub, maxRightSub and maxCenterSub. Our division will be based on the middle.

  For the left and right maximum substrings, we can find them recursively. Because it is an independent array again.

  And for the middle one, its left and right boundaries has to be calculated, that is, accumulate A[0] starting from A[i-1], and see where the maximum is reached. Remember always adding to A[0], comparing and updating the maximum value each time. The same as for the right part. Finally, add A[i] to get maxCenterSub.

  


  ![image-2020042818242232](/images/maxSub-1.png)

- **Analysis**

  The painpoint of recursive algorithms: waste of time - running time is O(nlogn). And also used a lot of extra space.

  *Note*: This method can also figure out what the substring is and where it starts and ends.

- **Implementation**

```javascript
/**
 * @param {number[]} nums
 * @return {number}
 */
var maxSubArray = function(nums) {
    return maxSubSumRec(nums, 0, nums.length-1);
};

function maxSubSumRec(A, left, right) {
    if (left >= right) { // single element
        return A[left];
    }
    let center = Math.floor((left+right) / 2);
    // Recursively calculate the maximum substring on the left
    let maxLeftSum = maxSubSumRec(A, left, center-1);
  	// Recursively calculate the maximum substring on the right
    let maxRightSum = maxSubSumRec(A, center+1, right);
    
  	// Calculate the left margin
    let maxLeftBorderSum = A[center]; 
    let leftBorderSum = maxLeftBorderSum;
    for (let i = center-1; i >= left; i--) {
        leftBorderSum += A[i];
        maxLeftBorderSum = Math.max(maxLeftBorderSum, leftBorderSum);
    }
    // Calculate the right margin
    let maxRightBorderSum = A[center]; 
    let rightBorderSum = maxRightBorderSum;
    for (let i = center+1; i <= right; i++) {
        rightBorderSum += A[i];
        maxRightBorderSum = Math.max(maxRightBorderSum, rightBorderSum);
    }
    let centerSum = maxLeftBorderSum + maxRightBorderSum - A[center];
    return Math.max(maxLeftSum, maxRightSum, centerSum);
}
```



**Solution 2: Dynamic Programming**

- **Idea**

  The principle is that the maximum substring must start with a positive number (if there is one in the array).

  Therefore, when the first positive number is found, we can accumulate from it to back. When sum>0, the latter part is still gaining, and it may continue to increase. But when sum<=0, if it accumulates, it will drag down the latter part, that is, it will decrease. We can look at it this way: Add the following elements one by one to the first position, and sum can be regarded as the beginning of the substring, thus when we look for the largest substring, we must discard the negative part before it.

- **Analysis**

  The time complexity is O(n), because the loop is only done once

- **Implementation**

```javascript
/**
 * @param {number[]} nums
 * @return {number}
 */
var maxSubArray = function(nums) {
    let max = nums[0];
    let sum = 0;
    for(let i = 0; i < nums.length; i++) {
        sum += nums[i];
        max = Math.max(sum, max);
        if (sum <= 0 && i < nums.length-1) { 
          // If sum<=0, it means that the previous substring accumulation has no gain to the back, so the sum is calculated from the next position again
            sum = 0;
        }
    }
    return max;
};

console.log(maxSubArray([-2, 1]));
```



**Another Implementation **

More space-saving: no extra memory is used during the loop (may still be used when calculating max), and each nums[i] stores its maximum accumulated value as the end point. Because the substring must end with a certain element, this is equivalent to calculating all the possibilities, and we just need to find the largest value among them.

```javascript
/**
 * @param {number[]} nums
 * @return {number}
 */
var maxSubArray = function(nums) { 
    for(let i = 1; i < nums.length; i++) {
        if (nums[i-1] > 0) {
            nums[i] += nums[i-1]
        }
    }
    return Math.max(...nums);
};
```




