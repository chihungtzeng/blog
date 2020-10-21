---
title: Classic leetcode problems
date: 2020-10-14 19:08:07
tags:
categories:
---

This post is my collection of what I've learned from leetcode.

- 146 LRU Cache
- 460 LFU Cache
  Use Iterator to put/get cached items in O(1) time.

- 471 Encode String with Shortest Length
  Find repeative patterns in a string

- 719 Find K-th Smallest Pair Distance
  Use binary search to reduce the O(n^2) search space to O(n*log n).

- 1192 Critical Connections in a Network
  Application of DFS' back edges

- 1231 Divide Chocolate
  Advanced binary search

- 1312 Minimum Insertion Steps to Make a String Palindrome
  Learn how to reduce DP space complexity from O(n^2) to O(n)

- 1334 Find the City With the Smallest Number of Neighbors at a Threshold Distance
  Minimum distance with constraints.

- 1376 Time Needed to Inform All Employees
  Bottom-up DFS

- 1382 Balance a Binary Search Tree
  See discussion section about how to do it.

- 1401 Circle and Rectangle Overlapping
  Use clamp function to find nearest point to solve the problem.

- 1438 Longest Continuous Subarray With Absolute Diff Less Than or Equal to Limit
  Keep min/max in a moving window efficiently to solve the problem in O(n) time.

- 1483 Kth Ancestor of a Tree Node
  Maintain a jump table to go to parents far away.

- 1494 Parallel Courses II
  Iterate all submask of mask i
  ```
  for (int j = i; j != 0; j = (j - 1) & i) {
  ```

- 1606 Find Servers That Handled Most Number of Requests
  Making circular array into a linear array

