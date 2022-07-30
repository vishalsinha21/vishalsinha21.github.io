---
published: true
title: "Next Greater Element"
layout: post
---

Problem statement:
Given an array, print the Next Greater Element (NGE) for every element. The Next greater Element for an element x is the first greater element on the right side of x in the array. Elements for which no greater element exist, consider the next greater element as -1.

Examples:
For an array, the rightmost element always has the next greater element as -1.
For an array that is sorted in decreasing order, all elements have the next greater element as -1.
For the input array [4, 5, 2, 25], the next greater elements for each element are as follows.
```
Element       NGE
4      -->   5
5      -->   25
2      -->   25
25     -->   -1
```
For the input array [13, 7, 6, 12}, the next greater elements for each element are as follows.
```
Element        NGE
13      -->    -1
7       -->     12
6       -->     12
12      -->     -1
```

<iframe src="https://www.codiva.io/p/272950f8-5032-457f-ab8d-6c8247ba5155" style="height: 600px; min-height: 420px; max-height: 90vh; width: 100%; overflow: hidden;"></iframe>
