---
title: Algorithms - Remove Duplicates from Sorted Array
date: '2024-05-04'
tags: ['algorithms', 'arrays']
draft: false
summary: How to write git commits that are meaningful and provide meaningful context to your changes.
images: []
layout: PostLayout
canonicalUrl:
---

# Problem Description

[LeetCode Problem 26](https://leetcode.com/problems/remove-duplicates-from-sorted-array/)

# Intuition
We'll need to iterate through the array to check for duplicates, but we'll also need to keep track of an index where a unique number is supposed to go.

# Approach
Since the input array is sorted in *non-decreasing* order, we know that the next unique number is going to be somewhere to the right.

In terms of keeping track of the index, we can just store a number. The number can start at 1 because we'll know that the first number (idx = 0) in the array will always be the first unique number in the list.

As we iterate through we can compare the current number and the next number in the list to see if the next number is unique. We'll then set the
array at our tracked index to be the unique number. Finally we can increment the index.

# Complexity
- Time complexity: O(n)

- Space complexity: O(1)

# Code
```
/**
 * @param {number[]} nums
 * @return {number}
 */
const removeDuplicates = (nums) => {
  // Index for which the next unique number will be assigned.
  let index = 1; 

  for (let i = 0; i < nums.length - 1; i++) {
    //Check if the next number is not the same as the currenet number.
    if (nums[i] !== nums[i + 1]) {
      // Assign unique number to it's correct position
      nums[index] = nums[i + 1];

      //increment
      index++;
    }
  }
  return index;
};
```

