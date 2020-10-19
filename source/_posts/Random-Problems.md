---
title: Random Problems
date: 2020-10-03 18:44:16
categories: 
- Algorithm
---



#### Single Number

Given a **non-empty** array of integers, every element appears *twice* except for one. Find that single one.

**Note:**

Your algorithm should have a linear runtime complexity. Could you implement it without using extra memory?

**My solution :**

Sort and loop

Question: the sort() method implements on quick sort, but when I wrote a quick sort by myself, it said "time limit exceed", why???

```java
class Solution {
    public int singleNumber(int[] nums) {
        int singleNumIdx = 0;
        Arrays.sort(nums);
        for (int i = 0; i < nums.length; i++) {
            if ((i == 0 && (nums.length == 1 || nums[i] < nums[i+1])) 
                || (i == nums.length-1 && nums[i] > nums[i-1])
                || (nums[i] < nums[i+1] && nums[i] > nums[i-1]) ) {
                singleNumIdx = i;
                break;
            }
        }
        return nums[singleNumIdx];
    }  
}
```

**Solution:**

1. Math

   Concept: 2(a+b+c) - (a+a+b+b+c) = c

   Time complexity : O(n)

   Space complexity: O(n)

   ```Java
   class Solution {
     public int singleNumber(int[] nums) {
       int sumOfSet = 0, sumOfNums = 0;
       Set<Integer> set = new HashSet();
   
       for (int num : nums) {
         if (!set.contains(num)) {
           set.add(num);
           sumOfSet += num;
         }
         sumOfNums += num;
       }
       return 2 * sumOfSet - sumOfNums;
     }
   }
   ```



2. Bit Manipulation

   Concept: 

   * *a*⊕0=*a*; 
   * *a*⊕*a*=*0*

   Time complexity: O(n)

   Space complexity : O(1)

   ```java
   class Solution {
     public int singleNumber(int[] nums) {
       int a = 0;
       for (int i : nums) {
         a ^= i;
       }
       return a;
     }
   }
   ```

   

#### Happy Number

Write an algorithm to determine if a number `n` is "happy".

A happy number is a number defined by the following process: Starting with any positive integer, replace the number by the sum of the squares of its digits, and repeat the process until the number equals 1 (where it will stay), or it **loops endlessly in a cycle** which does not include 1. Those numbers for which this process **ends in 1** are happy numbers.

Return True if `n` is a happy number, and False if not.

**Example:** 

```
Input: 19
Output: true
Explanation: 
12 + 92 = 82
82 + 22 = 68
62 + 82 = 100
12 + 02 + 02 = 1
```



- **Idea**：

  Convert the number to a string first, then use split to cut each bit into the array, calculate the sum, and recurse when the sum is not 1. Save each sum, and stop when the sum starts to repeat.

* **Implemetation**

  arr.reduce(function, initialValue): Combine the array according to the function method. If arr.length == 1, and no initialValue is specified, the single element value of the original array is returned directly without executing the callback method.

  Can be used to achieve accumulation.

```javascript
/**
 * @param {number} n
 * @return {boolean}
 */
const set = new Set();
var isHappy = function(n) {
    let sum = splitAndPlus(n);
    while (sum !== 1) {
        if(!set.has(sum)) {
            set.add(sum);
            sum = splitAndPlus(sum);
            continue;
        }
        return false;
    }
    return true;
};

function splitAndPlus(n) {
    let numArray = n.toString().split("").map(Number);
    return numArray.reduce((acc,cur) => acc += cur*cur, 0);
}
```



