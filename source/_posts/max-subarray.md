---
layout: maximum
title: Maximum subarray with related best buy and sell problems
date: 2020-04-27 13:11:41
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



**Solution 1: 递归求解**

- **思路**

  对于数组A里的某一个元素A[i]，A的最大子串要不在A[i]的左边，要不在A[i]的右边，要不就包含A[i] （横跨了它）。因此，只要选择maxLeftSub, maxRightSub和maxCenterSub即可。我们的分割就以中间为基准做分割啦。

  对于左右的最大子串呢，就可以递归地去找啦。因为又是独立的数组了。

  而中间的就要去计算它的左右边界，也就是把从A[i-1]开始A[0]累加，看加到哪里为止有最大值。要一直加到A[0]哦，每次都比较和更新最大值。右边也是一样。最后再加上A[i]就得到maxCenterSub。
  

  ![](/images/maxSub-1.png)

- **分析**

  递归算法共同的痛：浪费时间，运行时间是O(nlogn)。而且还用了超多额外空间的。

  *Note*: 这个方法能同时得出子串是什么，以及起止位置

- **实现**

```javascript
/**
 * @param {number[]} nums
 * @return {number}
 */
var maxSubArray = function(nums) {
    return maxSubSumRec(nums, 0, nums.length-1);
};

function maxSubSumRec(A, left, right) {
    if (left >= right) { // 单个元素
        return A[left];
    }
    let center = Math.floor((left+right) / 2);
    // 递归计算左边的最大子串
    let maxLeftSum = maxSubSumRec(A, left, center-1);
  	// 递归计算右边的最大子串
    let maxRightSum = maxSubSumRec(A, center+1, right);
    
  	// 计算左边边界
    let maxLeftBorderSum = A[center]; 
    let leftBorderSum = maxLeftBorderSum;
    for (let i = center-1; i >= left; i--) {
        leftBorderSum += A[i];
        maxLeftBorderSum = Math.max(maxLeftBorderSum, leftBorderSum);
    }
    // 计算右边边界
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



**Solution 2: 动态规划**

- **思路**

  原理来说就是一个最大的子串一定会以正数开头（如果数组里存在正数的话）。

  因此当找到第一个正数的时候从前往后去累加，当sum>0时，对于后面的部分还是增益的，还有可能继续增长。但当sum<=0时，再累加的话对后面的部分就是拖累了，也就是会减小。可以这么看，把后面的元素一个个往第一个位置加，所以sum可以看成是子串的开头，我们在往下寻找最大子串的时候，肯定要舍弃前面为负的部分。

- **分析**

  时间复杂度是O(n)，因为只做了一遍循环

- **实现**

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
          // 如果sum<=0，说明前面的子串累加对后面没有增益了，因此重新从下一个位置开始计算sum
            sum = 0;
        }
    }
    return max;
};

console.log(maxSubArray([-2, 1]));
```



**另一种实现**

更加节省空间：在循环的时候不占用额外内存（在计算max的时候可能还是用了），每一个nums[i]存储的都是它为终点的最大累加值。因为子串肯定以某一个元素作为终点，所以这样相当于算了所有可能性，只要再去找其中最大的值就可以了。

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





#### Best Time to Buy and Sell Stock

Say you have an array for which the *i*th element is the price of a given stock on day *i*.

If you were only permitted to complete at most one transaction (i.e., buy one and sell one share of the stock), design an algorithm to find the maximum profit.

Note that you cannot sell a stock before you buy one.

**Example 1:**

```
Input: [7,1,5,3,6,4]
Output: 5
Explanation: Buy on day 2 (price = 1) and sell on day 5 (price = 6), profit = 6-1 = 5.
             Not 7-1 = 6, as selling price needs to be larger than buying price.
```

**Example 2:**

```
Input: [7,6,4,3,1]
Output: 0
Explanation: In this case, no transaction is done, i.e. max profit = 0.
```



- **思路**

  就跟最大子串那个很像，循环一次去找在哪一天卖出利益最大。要存储之前的最低成本和最高利润。

- **实现**

  ```javascript
  /**
   * @param {number[]} prices
   * @return {number}
   */
  var maxProfit = function(prices) {
      let minCost = prices[0];
      let maxProfit = 0;
      for (let i = 1; i < prices.length; i++) {
          minCost = prices[i-1] < minCost ? prices[i-1] : minCost;
          if (prices[i] > minCost) {
              maxProfit = (prices[i]-minCost) > maxProfit ? prices[i]-minCost : maxProfit;
          }
      }
      return maxProfit;
  };
  ```
