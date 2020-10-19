---
title: Best Time to Buy and Sell Stock
date: 2020-10-1 18:09:06
tags:
---



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



- **Idea**

  It's similar to the largest substring, looping once to find the day when the profit is the most to sell. To store the previous lowest cost and highest profit.

- **Implementation**

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





#### Best Time to Buy and Sell Stock II

Say you have an array `prices` for which the *i*th element is the price of a given stock on day *i*.

Design an algorithm to find the maximum profit. You may complete as many transactions as you like (i.e., buy one and sell one share of the stock multiple times).

**Note:** You may not engage in multiple transactions at the same time (i.e., you must sell the stock before you buy again).

**Example 1:**

```
Input: [7,1,5,3,6,4]
Output: 7
Explanation: Buy on day 2 (price = 1) and sell on day 3 (price = 5), profit = 5-1 = 4.
             Then buy on day 4 (price = 3) and sell on day 5 (price = 6), profit = 6-3 = 3.
```

（There is no limit to the number of transactions, but the existing ones must be sold before the next purchase）

- **Idea**

  This is to find the maximum profit. If the elements of the array are drawn on the coordinate system and connected as a line, it is easy to understand, that is, to add up all the growth. Go to the bottom of the valley and the peak of the valley. When you encounter a valley of the bottom A[i], go to the next valley and peak A[j] to sell. Because as long as the price continues to rise, greater profits will be made. Once it starts to fall, wait until the bottom to buy.

  ![image-20200428182422320](images/best-time-buy-sell-2.png)

  

- **Implementation**

  ```javascript
  /**
   * @param {number[]} prices
   * @return {number}
   */
  var maxProfit = function(prices) {
      let maxProfit = 0;
      for (let i = 0; i < prices.length; i++) {
          if (i+1< prices.length && prices[i+1] > prices[i]) {
              maxProfit += prices[i+1] - prices[i];
          }
      }
      return maxProfit;
  };
  ```

  

#### Best Time to Buy and Sell Stock with Cooldown

Say you have an array for which the *i*th element is the price of a given stock on day *i*.

Design an algorithm to find the maximum profit. You may complete as many transactions as you like (ie, buy one and sell one share of the stock multiple times) with the following restrictions:

- You may not engage in multiple transactions at the same time (ie, you must sell the stock before you buy again).
- After you sell your stock, you cannot buy stock on next day. (ie, cooldown 1 day)

**Example:**

```
Input: [1,2,3,0,2]
Output: 3 
Explanation: transactions = [buy, sell, cooldown, buy, sell]
```

- **Idea**

  Cooldown means sell can only goes to rest state，not buy state。

- **Implementation**

  ```javascript
  /**
   * @param {number[]} prices
   * @return {number}
   */
  var maxProfit = function(prices) {
      let T_i_k_0 = 0;
      let T_i_k_1 = -prices[0];
      let T_i_1_k_0 = 0;
      let T_i_2_k_0 = -prices[0];
      for (let i = 0; i < prices.length; i++) {
          T_i_k_0 = Math.max(T_i_1_k_0, T_i_k_1 + prices[i]);
          T_i_k_1 = Math.max(T_i_k_1, T_i_2_k_0 - prices[i]);
          T_i_2_k_0 = T_i_1_k_0;
          T_i_1_k_0 = T_i_k_0;
      }
      return T_i_k_0;
  };
  ```

  



#### Summary

For the best time to buy and sell problem, the following factors affect the best interests:

1. We are currently at the i-th day.
2. How many transactions we are allowed to do
3. After the end of the day, the inventory we have on hand (because we cannot be involved in two transactions at the same time, that is, we must empty the inventory before we can continue to purchase)

There are also three kinds of actions we can take：buy, rest, and sell

We assume the following variables：

1. prices is the array of a series price, prices[i]represents the price on the i-th day.

2. k represents the number of transactions allowed

3. T[i] [k] represents the maximum profit that can be obtained on the i-th day under the limit of the maximum number of transactions k. However, since two transactions cannot be involved at the same time, the actions we can do are different for situations where there is and no inventory on hand after the transaction ends. Therefore, the calculation method of T[i] [k] will also change:

   - After the transaction ends, the on-hand inventory is 0. It may be that the inventory on the i-1 day is 0 and no transaction occurs on the i day, or the inventory on the i-1 day is 1 and the sell operation is performed on the i day.

     > T[i] [k] [0] = max(T[i-1] [k] [0], T[i-1] [k] [1] + prices[i] )

   - After the transaction ends, the on-hand inventory is 1. It may be that the inventory on the i-1 day is 1 and no transaction occurs on the i day, or the inventory on the i-1 day is 0 and the buy operation is performed on the i day.

     > T[i] [k] [1] = max(T[i-1] [k] [1], T[i-1] [k+1] [0] - prices[i] )

Therefore, to calculate the maximum profit maxProfit, we only need to loop the convenience prices array, update the values of T[i] [k] [0] and T[i] [k] [1], and finally get T[i] [k] The value of [0] is maxProfit. As for why it’s not T[i] [k] [1].... of course it's because the max profit can only be made by selling them all!



#### Reference

- [General wyas for stock problems](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-with-cooldown/discuss/75924/Most-consistent-ways-of-dealing-with-the-series-of-stock-problems)

